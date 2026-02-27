# ✅ OpenShift Error Types — On‑Call Cheat Sheet

## 1) Pod / Container STATUS (what you see in `oc get pods`)

### 🔥 Startup / Crash

*   **CrashLoopBackOff**  
    **Meaning:** Container starts → crashes → restarts repeatedly (with increasing delay).  
    **Most common causes:** bad config/env, missing dependency (DB/LDAP/API), bad command/args, probe misconfig, OOMKilled.  
    **First checks:** `oc logs --previous`, `oc describe pod` (Events + probes).

*   **Error / Failed**  
    **Meaning:** Container exited non‑zero and won’t recover automatically (very common for Jobs).  
    **First checks:** `oc logs`, `oc describe pod` (Exit code, Events).

### 📦 Image / Registry

*   **ErrImagePull**  
    **Meaning:** A pull attempt failed right now.  
    **Common causes:** wrong image/tag, auth required, registry/DNS/egress down.

*   **ImagePullBackOff**  
    **Meaning:** Repeated image pull failures → kubelet backs off retries.  
    **First checks:** `oc describe pod` (Events show the real reason), verify pull secret and image reference.

### 🧱 Container Create / Init

*   **CreateContainerError**  
    **Meaning:** Kubelet couldn’t create the container.  
    **Common causes:** missing Secret/ConfigMap, volume mount issues, permission/SCC, invalid command, bad image entrypoint.  
    **First checks:** `oc describe pod` (Events), check mounts + security context.

*   **Init:0/1, Init:1/2, Init:Error**  
    **Meaning:** Init containers failing → app container never starts.  
    **First checks:** `oc logs -c <init-container>`, `oc describe pod` (init section).

### ⏳ Scheduling / Placement

*   **Pending**  
    **Meaning:** Pod accepted but not scheduled or waiting on prerequisites.  
    **Common causes:** insufficient CPU/mem, taints/affinity mismatch, PVC not bound, quotas.  
    **First checks:** `oc describe pod` (look for FailedScheduling), `oc get nodes`, PVC status.

### 🧹 Deletion / Cleanup

*   **Terminating (stuck)**  
    **Meaning:** Pod deletion requested but it won’t complete.  
    **Common causes:** node unreachable, CSI detach stuck, finalizers, runtime issues.  
    **First checks:** `oc describe pod`, node health, volume attachments/snapshots.

### ❓ Unknown State

*   **Unknown / ContainerStatusUnknown**  
    **Meaning:** Control plane can’t reliably determine pod/container state.  
    **Common causes:** node connectivity issues, kubelet/CRI runtime (e.g., CRI‑O) misbehaving.  
    **First checks:** `oc get nodes`, node events, runtime logs if accessible.

***

## 2) Container “Reason” (seen in `oc describe pod` → container state)

These explain *why* a pod is failing.

*   **OOMKilled** → memory limit exceeded (often leads to CrashLoopBackOff).
*   **Probe failures**
    *   **Readiness failed** → Running but not Ready (no traffic via Service)
    *   **Liveness failed** → restarts
    *   **Startup failed** → never becomes healthy, restarts/backoff

***

## 3) Storage Error Types (PVC/PV/CSI/ODF/Ceph)

Usually show up as Pending/CreateContainerError/CrashLoopBackOff.

*   **PVC Pending** → storage class/provisioning/capacity issue.
*   **FailedMount / Attach timeout** → volume can’t attach/mount to node.
*   **Read-only filesystem / permission denied** → app can’t write → crashes/jobs fail.

**Fast checks**

*   `oc get pvc -n <ns>`
*   `oc describe pod <pod> -n <ns>` (Events: mount/attach failures)

***

## 4) Node Error Types (cluster-wide impact)

*   **NotReady / Unreachable** → pods may go Unknown; scheduling disrupted.
*   **Ready,SchedulingDisabled / cordoned** → node is healthy but won’t accept new pods.
*   **DiskPressure / MemoryPressure / PIDPressure** → evictions, failures, instability.

**Fast checks**

*   `oc get nodes -o wide`
*   `oc describe node <node>`

***

## 5) Operator / Platform Health (OpenShift-specific)

Check with `oc get co`:

*   **Degraded=True** → operator can’t reconcile; feature/component unhealthy.
*   **Progressing=True** → rollout/update not completing.
*   **Available=False** → operator’s component unavailable (can be critical).
*   **Upgrade blocked** → cluster version reconciliation can fail when key operators degraded.

**Fast checks**

*   `oc get co`
*   `oc describe co <name>`

***

## 6) Networking Error Types (CNI/SDN/DNS/Ingress/Route)

*   **CNI setup failure** → pods stuck ContainerCreating; events mention sandbox/network setup.
*   **DNS/service reachability issues** → pods Running but unreachable.
*   **Ingress/Route issues** → app works internally but fails externally (TLS, route target port, ingress controller).

**Fast checks**

*   Pod events: `oc describe pod`
*   Ingress: `oc get co ingress`, `oc get pods -n openshift-ingress`
*   Routes: `oc get route -A`

***

## 7) Build & CI/CD Errors (if using OpenShift builds)

*   **Build pod Failed/Error** → dependency download, auth, network/proxy, Dockerfile/S2I script failures.
*   Downstream symptom: deployments later hit **ImagePullBackOff** if image never got built/pushed.

**Fast checks**

*   `oc get builds -n <ns>`
*   `oc logs <build-pod>`

***

## 8) Jobs/CronJobs (automation failures)

*   **Job pod Error** → script/config wrong, missing mounts, auth, network or storage issues.
*   Shows up as **KubeJobFailed** alerts and pods in Error.

**Fast checks**

*   `oc get jobs,cronjobs -n <ns>`
*   `oc logs <job-pod>`

***

# 🚑 Universal 60‑second triage commands

```bash
# 1) Find non-healthy pods
oc get pods -A | grep -v Running | grep -v Completed

# 2) See the real reason (Events are gold)
oc -n <ns> describe pod <pod>

# 3) Logs (previous is key for CrashLoopBackOff)
oc -n <ns> logs <pod> --tail=200
oc -n <ns> logs <pod> --previous --tail=200

# 4) Node + Operator health (cluster-wide)
oc get nodes -o wide
oc get co
```

***
