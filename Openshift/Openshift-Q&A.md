Below is a **clean, interview‑ready set of OpenShift Q\&A for all levels (Beginner → Intermediate → Advanced)**.  
**No sources, no URLs, no references — just pure Q\&A**, exactly as you requested.

***

# ✅ **OpenShift Interview Q\&A for DevOps (All Levels)**

**Beginner + Intermediate + Advanced**

***

# **🔵 BEGINNER LEVEL (Fundamentals)**

### **1. What is OpenShift?**

OpenShift is an enterprise Kubernetes platform by Red Hat that includes additional features like built‑in CI/CD, enhanced security, RBAC, an internal registry, Operators, and a developer-friendly web console.

### **2. How is OpenShift different from Kubernetes?**

Kubernetes is a container orchestration engine; OpenShift is a complete enterprise platform built on Kubernetes with added security policies, S2I, built‑in registry, Operators, and opinionated defaults.

### **3. What is a Project in OpenShift?**

A Project is OpenShift’s enhanced version of a Kubernetes namespace. It includes RBAC, quotas, controls, isolation, and self‑service provisioning.

### **4. What is a Pod?**

A pod is the smallest deployable unit containing one or more containers sharing network and storage.

### **5. What is a Deployment?**

A Deployment manages application rollout, scaling, and rollback of pods.

### **6. What is a Route?**

It exposes services externally using the OpenShift router (HAProxy). Similar to Kubernetes Ingress.

### **7. What is the internal registry?**

A built‑in container image registry that stores application images used by OpenShift deployments.

### **8. What is the OpenShift Web Console?**

A UI to manage projects, workloads, networking, build pipelines, and cluster resources.

***

# **🟠 INTERMEDIATE LEVEL (Hands‑On DevOps Concepts)**

### **9. What is BuildConfig in OpenShift?**

A BuildConfig defines how OpenShift builds a container image — source, strategy, triggers, and output.

### **10. What is S2I (Source‑to‑Image)?**

S2I builds images directly from source code using builder images. It automates the process and ensures repeatable builds.

### **11. What are ImageStreams?**

ImageStreams track image changes and allow automatic redeployments when new versions are pushed.

### **12. Explain the OpenShift Architecture.**

*   **Master (Control Plane):** API server, scheduler, controller-manager, etcd.
*   **Worker Nodes:** Run pods.
*   **OpenShift Add‑ons:** Router, registry, Operators, OLM.

### **13. What is an Operator in OpenShift?**

Operators automate application lifecycle tasks like installation, upgrades, config, failover, etc.

### **14. What is OLM (Operator Lifecycle Manager)?**

OLM manages Operators — installation, updates, versioning, and dependencies.

### **15. What are SCCs (Security Context Constraints)?**

SCCs enforce security restrictions such as runAsUser, privileged containers, file system access, etc.  
OpenShift has stricter defaults than Kubernetes.

### **16. How do you scale an application in OpenShift?**

`oc scale deployment <name> --replicas=<count>`

### **17. What is a Route vs Service?**

*   **Service:** Internal communication (ClusterIP, NodePort).
*   **Route:** External access for HTTP/HTTPS traffic.

### **18. What is a DaemonSet used for?**

To run a pod on every node (e.g., logs, monitoring agents).

***

# **🔴 ADVANCED LEVEL (Real‑World Ops, Troubleshooting, CI/CD, Networking)**

### **19. Explain the SDN model in OpenShift.**

It uses Software Defined Networking (OVN‑Kubernetes or OpenShift SDN) providing:

*   Pod‑to‑pod networking
*   Isolation between projects
*   NetworkPolicies
*   East‑west and north‑south traffic control

### **20. How do you troubleshoot a failing Pod?**

Steps:

1.  `oc describe pod <pod>` (Events, YAML issues)
2.  `oc logs <pod>` (container logs)
3.  Check imagePullSecrets, SCC restrictions
4.  Check liveliness and readiness probes
5.  Check node status (`oc get nodes`)

### **21. How does OpenShift handle Persistent Storage?**

Using **PersistentVolumes (PV)** and **PersistentVolumeClaims (PVC)**.  
Supported storage: NFS, Ceph, AWS EBS, Azure Disk, GCP PD, OCS, etc.

### **22. What are Deployment Strategies in OpenShift?**

*   **Rolling** (default)
*   **Recreate**
*   **Blue‑Green**
*   **Canary**
*   **A/B testing (via Routes)**

### **23. How do you perform a rollout and rollback?**

*   Rollout: `oc rollout status deployment/<name>`
*   Rollback: `oc rollout undo deployment/<name>`

### **24. What is CRI‑O and why does OpenShift use it?**

CRI‑O is a lightweight container runtime optimized for Kubernetes.  
Replaces Docker for better security, reliability, and performance.

### **25. Explain the Build Strategies in OpenShift.**

*   **S2I**
*   **Docker strategy**
*   **Pipeline strategy (Jenkins)**
*   **Custom strategy**

### **26. How is OpenShift used in CI/CD pipelines?**

*   BuildConfigs trigger image builds
*   ImageStreams trigger deployments
*   Jenkins or Tekton pipelines interact with OpenShift APIs
*   Deployment automation with Operators and GitOps (ArgoCD)

### **27. How do you secure OpenShift Applications?**

*   SCCs
*   RBAC
*   NetworkPolicies
*   Image signing
*   Internal registry with RBAC
*   TLS Routes
*   Secrets & ConfigMaps

### **28. How do you monitor OpenShift?**

*   Prometheus & Alertmanager (built‑in)
*   Grafana dashboards
*   EFK/ELK stack for logging
*   Cluster Insights & Metrics API

### **29. What is the purpose of the Router (HAProxy)?**

Handles external traffic routing using Routes. Supports:

*   TLS termination
*   Edge, Passthrough, Reencrypt
*   Blue‑Green deployments

### **30. What is GitOps in OpenShift?**

Using ArgoCD or Tekton to manage clusters and applications declaratively stored in Git.  
Automates deployments, rollback, and config drift detection.

***

# **🟣 EXPERT / SCENARIO‑BASED QUESTIONS**

### **31. Your pod keeps restarting. How do you debug it?**

*   Check container logs
*   Check probes failing (liveness/readiness)
*   Check resource limits causing OOM
*   Check image permission issues (SCC)
*   Check env variables & config injection
*   Check node resource pressure

### **32. A Route is not accessible from outside. What do you check?**

*   Router Pod status
*   Route configuration
*   TLS termination issues
*   Service targetPort & selector
*   Firewall rules / Load balancer
*   DNS resolution

### **33. BuildConfig keeps failing. Steps to fix?**

*   Check build logs
*   Check Git credentials
*   Check S2I builder image version
*   Check resource quotas
*   Check registry push permissions

### **34. PVC stuck in Pending. What does it mean?**

No PV satisfies the requirements.  
Fix:

*   Create a PV
*   Adjust PVC size, accessMode, storageClass
*   Validate storage backend

### **35. Cluster upgrade failed. How to proceed?**

*   Check Operator status
*   Check MachineConfig errors
*   Check etcd member health
*   Check drain failures
*   Review pre‑upgrade validations
*   Rollback using cluster‑version‑operator if needed

***
