## 1) Pods stuck in `CrashLoopBackOff` after a new image deploy

**Problem:** New deployment rolls out; pods keep restarting.  
**Likely causes:** Misconfigured env vars, failing readiness/liveness probes, bad image tag, permission/SCC issues.  
**How to debug & fix:**

```bash
oc describe pod <pod>                      # events, probe failures
oc logs <pod> -c <container>              # container logs
oc set env deployment/<app> KEY=VALUE     # fix bad env var
oc rollout undo deployment/<app>          # rollback to last working replica set
```

**Tips:** Validate probes locally; pin image tags; use `ImageStream` with promotion gates.

***

## 2) Deployment stuck on “ProgressDeadlineExceeded”

**Problem:** `oc get deploy` shows progressing timeout.  
**Likely causes:** Insufficient resources, failing probes, image pull issues.  
**Resolution:**

```bash
oc describe deploy/<app>
oc get events --sort-by=.lastTimestamp
oc get is/<image> -n <ns>
oc set resources deploy/<app> --limits=cpu=500m,memory=512Mi --requests=...  # adjust
```

**Tip:** Lower `maxUnavailable` or increase `progressDeadlineSeconds`.

***

## 3) PVC in `Pending` state; app cannot start

**Problem:** A PVC never binds.  
**Likely causes:** No matching PV, wrong `storageClass`, access mode mismatch.  
**Fix:**

```bash
oc get pvc,pv,sc
# Patch request size or storageClass:
oc patch pvc <name> -p '{"spec":{"resources":{"requests":{"storage":"10Gi"}}}}'
oc patch pvc <name> -p '{"spec":{"storageClassName":"ocs-storagecluster-ceph-rbd"}}'
```

**Tip:** Ensure access modes (RWO/RWX) match the storage backend.

***

## 4) Route returns 503; service seems healthy

**Problem:** External URL shows 503, pods are running.  
**Likely causes:** Service selector mismatch, wrong targetPort, TLS mismatch.  
**Steps:**

```bash
oc get svc <svc> -o yaml | grep -E 'selector|targetPort'
oc get endpoints <svc> -o wide
oc describe route <route>                 # check termination (edge/passthrough/reencrypt)
```

**Fix:** Align `Service` selectors with pod labels; correct `targetPort`; verify certs.

***

## 5) Blue‑Green deployment with zero downtime via Routes

**Goal:** Shift traffic safely between v1 and v2.  
**Approach:**

*   Deploy `app-v2` alongside `app-v1`.
*   Create a new `Service` for v2; update Route to point to v2 when ready.

```bash
oc set route-backends route/app app-v1=0 app-v2=100     # weighted if needed
```

**Rollback:** Flip weights back to v1.

***

## 6) Canary rollout using weighted backends

**Goal:** Send 10% traffic to new version.

```bash
oc set route-backends route/app app-stable=90 app-canary=10
oc adm router --stats-port=1936 # ensure router exposes stats/metrics for validation
```

**Validate** with metrics and error rates; increase weights gradually.

***

## 7) Jenkins/Tekton pipeline fails at `oc apply` with RBAC error

**Problem:** Pipeline service account lacks permissions.  
**Fix:**

```bash
# Grant least-privilege edit or a role binding scoped to the project:
oc adm policy add-role-to-user edit system:serviceaccount:<ns>:<sa> -n <ns>
# Or a granular Role/RoleBinding for specific verbs/resources
```

**Tip:** Store kubeconfig/service account token in a sealed secret; rotate regularly.

***

## 8) ImagePullBackOff from internal registry

**Problem:** Pulling image from OpenShift registry fails.  
**Causes:** Missing imagePullSecret, wrong pull spec, policy.  
**Steps:**

```bash
oc get is -n <ns>
oc get secret | grep dockercfg
oc set image deploy/<app> <container>=image-registry.openshift-image-registry.svc:5000/<ns>/<image>:<tag>
oc secrets link default <pull-secret> --for=pull
```

***

## 9) Probe flapping causes rolling restarts

**Problem:** Readiness toggles; router removes/adds endpoints.  
**Fix:** Increase `initialDelaySeconds`, `timeoutSeconds`, add a lightweight `/healthz` endpoint; confirm CPU/memory headroom.

```yaml
readinessProbe:
  httpGet: { path: /healthz, port: 8080 }
  initialDelaySeconds: 20
  timeoutSeconds: 2
  periodSeconds: 5
  failureThreshold: 3
```

***

## 10) Container needs to write to filesystem; permission denied

**Problem:** Default OpenShift random UID breaks writes to root‑owned paths.  
**Fix:** Make image UID‑agnostic and use writable paths.

```dockerfile
RUN mkdir -p /opt/app && chgrp -R 0 /opt/app && chmod -R g=u /opt/app
USER 1001
```

**Alternative:** Use an SCC only if justified and approved.

***

## 11) NetworkPolicy blocks traffic after adoption

**Problem:** After enabling NetworkPolicies, services can’t talk.  
**Fix:** Start with explicit allow for intra‑namespace and required egress.

```yaml
kind: NetworkPolicy
spec:
  podSelector: {}
  ingress:
  - from: [ { podSelector: {} } ]
  egress:
  - to: [ { namespaceSelector: { matchLabels: { name: logging } } } ]
  policyTypes: ["Ingress","Egress"]
```

**Tip:** Add label‑driven whitelists between tiers (api → db).

***

## 12) Cluster‑wide 5xx spikes after rollout

**Problem:** Router/HAProxy shows surge of 5xx.  
**Debug:**

*   Check router logs and `haproxy` stats.
*   Verify backend readiness and mTLS settings.  
    **Rollback:** `oc rollout undo deployment/<app>`; throttle `maxUnavailable`.

***

## 13) Pods OOMKilled under load

**Problem:** Memory limits too tight.  
**Fix:** Set realistic requests/limits; add VPA or autoscaling.

```bash
oc set resources deploy/<app> --requests=memory=512Mi --limits=memory=1Gi
oc autoscale deploy/<app> --min=2 --max=10 --cpu-percent=70
```

***

## 14) `oc exec` and `oc logs` are slow/unresponsive

**Causes:** API server pressure, network issues, node pressure.  
**Checks:**

```bash
oc get nodes
oc adm top nodes
oc get clusterversion
oc get co                                  # cluster operators health
```

**Fix:** Drain problematic nodes; scale control plane if needed; investigate SDN latency.

***

## 15) PVC expansion without downtime

**Goal:** Increase storage for a stateful app.  
**Steps:**

1.  Ensure StorageClass supports expansion.
2.  Patch PVC size; allow filesystem resize in the pod.

```bash
oc patch pvc <pvc> -p '{"spec":{"resources":{"requests":{"storage":"50Gi"}}}}'
oc describe pvc <pvc>  # wait until Bound+Resized
```

**Tip:** Verify app supports online resize; otherwise plan a controlled restart.

***

## 16) Backup & restore of app resources (GitOps‑first)

**Approach:**

*   Store all manifests in Git (Deployments, Services, Routes, RBAC).
*   Use **OADP/Velero** for cluster objects + PV snapshots.  
    **Restore:**

1.  Recreate namespace and apply Git manifests.
2.  Restore PVs from snapshots with Velero.
3.  Verify Secrets/ConfigMaps and Routes.

***

## 17) Jenkins agent pods frequently crash

**Problem:** Agent images outdated, insufficient resources, or wrong JNLP config.  
**Fix:** Pin a maintained agent image, set requests/limits, validate JNLP secret and URL.

```bash
oc logs <agent-pod>
oc describe pod <agent-pod>
oc delete pod <agent-pod>   # deployment will recreate
```

***

## 18) Canary failed—fast rollback strategy

**Steps:**

```bash
oc set route-backends route/app stable=100 canary=0
oc rollout undo deploy/app-canary
```

**Then:** Capture error rates, logs; fix and retest in lower env.

***

## 19) ImageStream never triggers a new rollout

**Problem:** Tag not updated or trigger disabled.  
**Fix:**

```bash
oc get is <image> -o yaml
oc set triggers deploy/<app> --from-image=<ns>/<image>:<tag> -c <container>
oc tag <ns>/<image>@<digest> <ns>/<image>:<tag>
```

***

## 20) S2I build fails pulling dependencies

**Problem:** Proxy/cert or missing builder toolchain.  
**Fix:** Configure proxy in BuildConfig and add needed packages to builder.

```yaml
spec:
  strategy:
    sourceStrategy:
      env:
      - name: HTTP_PROXY
        value: http://proxy:8080
```

**Validate** cert chain inside builder with `curl -v`.

***

## 21) Multi‑tenancy: Prevent cross‑project access

**Goal:** Isolate teams.  
**Actions:**

*   Use separate Projects; strict RBAC Roles/Bindings.
*   Apply NetworkPolicies to restrict east‑west traffic.
*   Define ResourceQuotas and LimitRanges per namespace.

***

## 22) “Permission denied” pulling private images from external registry

**Fix:** Create and link a Docker registry secret to SA used by deployments.

```bash
oc create secret docker-registry regcred \
  --docker-server=<registry> --docker-username=<u> --docker-password=<p> --docker-email=<e>
oc secrets link default regcred --for=pull
```

***

## 23) Ingress TLS termination choice (edge vs passthrough vs reencrypt)

**Scenario:** App manages TLS itself.  
**Solution:** Use **passthrough** so router forwards encrypted traffic; for mutual TLS to backends, use **reencrypt** with proper destination CA.

***

## 24) Node drain before maintenance

**Goal:** Safely move workloads off a node.  
**Steps:**

```bash
oc adm cordon <node>
oc adm drain <node> --ignore-daemonsets --delete-emptydir-data --force
# After maintenance:
oc adm uncordon <node>
```

**Tip:** Respect PDBs to avoid service disruption.

***

## 25) Cluster upgrade planning

**Checklist:**

*   Read release notes; confirm Operator compatibility.
*   Backup etcd (or ensure control plane backups).
*   Quiesce high‑risk workloads.  
    **Commands:**

```bash
oc adm upgrade
oc get clusterversion
oc get co  # operators progressing/degraded?
```

***

## 26) High router CPU during traffic spikes

**Fixes:**

*   Add more router replicas; tune route sharding.
*   Enable HTTP/2 only if needed; compress carefully.

```bash
oc -n openshift-ingress scale deploy/router-default --replicas=4
```

***

## 27) Tekton pipeline can’t access cluster resources in target namespace

**Problem:** SA bound only in CI namespace.  
**Fix:** Create a `Role` in target namespace and a `RoleBinding` to the CI SA.

```bash
oc -n target-ns adm policy add-role-to-user edit system:serviceaccount:ci-ns:tekton-sa
```

***

## 28) Logging not capturing application logs

**Checks:**

*   App logs to stdout/stderr (12‑factor).
*   For sidecar log agents, verify volume mounts and file paths.
*   Confirm cluster logging stack deployed and index patterns available.

***

## 29) Secret rotation without downtime

**Approach:**

*   Mount secrets as env/volumes; trigger rolling restart on change using annotation.

```bash
oc set env deploy/<app> SECRET_ROTATED=$(date +%s)
```

**Tip:** Use sealed secrets or external secret operators.

***

## 30) Enforcing image security and provenance

**Goal:** Only allow signed images from approved registries.  
**Actions:**

*   Configure ImagePolicy/Admission to block unapproved registries.
*   Scan images and fail pipelines on critical CVEs.
*   Use ImageStreams to control promotion from `:dev` → `:prod`.

***
