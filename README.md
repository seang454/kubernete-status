# 🚀 The Master Kubernetes Pod Statuses, Errors & Exit Codes Encyclopedia

> **The Definitive, 100% Complete Troubleshooting Encyclopedia for Kubernetes (v1.20 - v1.31+)**  
> Covering every single Kubernetes Pod Phase, Container State, Waiting/Terminated Reason, Scheduling Gate, Admission Webhook Rejection, ResourceQuota Constraint, In-Place Resize State, CNI/DNS Network Glitch, Storage/CSI Error, Probe Failure, Lifecycle Hook Error, Kernel Signal, Workload Controller Deadlock (StatefulSets/Deployments), Autoscaler Failure (HPA/KEDA/Cluster Autoscaler), CronJob/DaemonSet Failure, Ingress/Gateway API Glitch, TLS/Certificate Expiry, Windows Container Error, Disruption Target, and Container Exit Code with **Real-World Scenarios**, **Deep Root Causes**, **Exact Diagnostic Commands**, and **Step-by-Step Fixes**.

---

## 📌 Table of Contents

- [🧠 0. Pod Lifecycle Architecture: Phases, Container States & Conditions](#-0-pod-lifecycle-architecture-phases-container-states--conditions)
- [🛠️ Diagnostic Master-Kit: 12 Essential Commands](#️-diagnostic-master-kit-12-essential-commands)
- [📑 1. Scheduling, Placement & Admission Control Errors](#-1-scheduling-placement--admission-control-errors)
  - [1.1 `Pending` (Generic Unscheduled)](#11-pending-generic-unscheduled)
  - [1.2 `FailedScheduling` (Insufficient CPU / Memory / GPU)](#12-failedscheduling-insufficient-cpu--memory--gpu)
  - [1.3 `SchedulingGated` (Kubernetes 1.26+ Dynamic Scheduling Gates)](#13-schedulinggated-kubernetes-126-dynamic-scheduling-gates)
  - [1.4 `ResourceQuotaExceeded` (`Forbidden: exceeded quota`)](#14-resourcequotaexceeded-forbidden-exceeded-quota)
  - [1.5 `LimitRangeViolation` (`Forbidden: minimum/maximum constraint`)](#15-limitrangeviolation-forbidden-minimummaximum-constraint)
  - [1.6 `AdmissionWebhookDenied` / `WebhookTimeout`](#16-admissionwebhookdenied--webhooktimeout)
  - [1.7 `MatchNodeSelector` / `NodeAffinity` Failure](#17-matchnodeselector--nodeaffinity-failure)
  - [1.8 `UntoleratedTaint` (`NoSchedule` / `NoExecute`)](#18-untoleratedtaint-noschedule--noexecute)
  - [1.9 `PodTopologySpreadFilter` / `PodAntiAffinity` Conflict](#19-podtopologyspreadfilter--podantiaffinity-conflict)
  - [1.10 `VolumeZoneConflict` (Cross-AZ Disk Attachment)](#110-volumezoneconflict-cross-az-disk-attachment)
  - [1.11 `HostPortConflict` (Port Already Bound on Node)](#111-hostportconflict-port-already-bound-on-node)
  - [1.12 `MaxPodsExceeded` / `NodeUnschedulable` (`Cordoned`)](#112-maxpodsexceeded--nodeunschedulable-cordoned)
- [🌐 2. Pod Sandbox & CNI Network Initialization Errors](#-2-pod-sandbox--cni-network-initialization-errors)
  - [2.1 `PodInitializing`](#21-podinitializing)
  - [2.2 `CreatePodSandboxError` / `FailedCreatePodSandBox`](#22-createpodsandboxerror--failedcreatepodsandbox)
  - [2.3 `NetworkPluginNotReady` / `NetworkNotReady`](#23-networkpluginnotready--networknotready)
  - [2.4 `PodSubnetExhaustion` (No Free IPs in Node/Cluster CIDR)](#24-podsubnetexhaustion-no-free-ips-in-nodecluster-cidr)
  - [2.5 `FailedSync` (Kubelet Sync Loop Failure)](#25-failedsync-kubelet-sync-loop-failure)
  - [2.6 `ServiceAccountNotFound` / `kube-api-access` Mount Failure](#26-serviceaccountnotfound--kube-api-access-mount-failure)
- [📦 3. Image, OCI Registry & Container Runtime (CRI) Errors](#-3-image-oci-registry--container-runtime-cri-errors)
  - [3.1 `ErrImagePull`](#31-errimagepull)
  - [3.2 `ImagePullBackOff`](#32-imagepullbackoff)
  - [3.3 `InvalidImageName`](#33-invalidimagename)
  - [3.4 `ErrImageNeverPull`](#34-errimageneverpull)
  - [3.5 `ImageInspectError` / `ErrImageInspect`](#35-imageinspecterror--errimageinspect)
  - [3.6 `ImageArchitectureMismatch` (`exec format error`)](#36-imagearchitecturemismatch-exec-format-error)
  - [3.7 `RegistryUnauthorized` (`401 Unauthorized` / `403 Forbidden`)](#37-registryunauthorized-401-unauthorized--403-forbidden)
  - [3.8 `DockerHubRateLimit` (`HTTP 429 Too Many Requests`)](#38-dockerhubratelimit-http-429-too-many-requests)
  - [3.9 `ImagePullTimeout` (Gigantic Layers / Slow Link)](#39-imagepulltimeout-gigantic-layers--slow-link)
- [🔒 4. Security Context, Kernel Profiles & Privilege Errors](#-4-security-context-kernel-profiles--privilege-errors)
  - [4.1 `RunAsNonRootError` / `MustRunAsNonRoot`](#41-runasnonrooterror--mustrunasnonroot)
  - [4.2 `ContainerCannotRun` (AppArmor / Seccomp / SELinux Denied)](#42-containercannotrun-apparmor--seccomp--selinux-denied)
  - [4.3 `ReadOnlyFilesystemError`](#43-readonlyfilesystemerror)
  - [4.4 `SysctlNotAllowed` / `ForbiddenSysctl`](#44-sysctlnotallowed--forbiddensysctl)
  - [4.5 `HostSecurityViolation` (`HostPID` / `HostNetwork` / `HostIPC` Forbidden)](#45-hostsecurityviolation-hostpid--hostnetwork--hostipc-forbidden)
  - [4.6 `DevicePluginFailed` / `GPUAllocationFailed`](#46-devicepluginfailed--gpuallocationfailed)
- [⚙️ 5. Container Configuration & Lifecycle Hook Errors](#️-5-container-configuration--lifecycle-hook-errors)
  - [5.1 `CreateContainerConfigError`](#51-createcontainerconfigerror)
  - [5.2 `CreateContainerError`](#52-createcontainererror)
  - [5.3 `RunContainerError`](#53-runcontainererror)
  - [5.4 `VolumeSubpathInitializationFailed`](#54-volumesubpathinitializationfailed)
  - [5.5 `ProjectedVolumeError` / `TokenRequestFailed`](#55-projectedvolumeerror--tokenrequestfailed)
  - [5.6 `PostStartHookError` & `PreStopHookFailed`](#56-poststarthookerror--prestophookfailed)
- [💾 6. Storage, CSI Driver & Volume Mounting Errors](#-6-storage-csi-driver--volume-mounting-errors)
  - [6.1 `ContainerCreating` (Stuck on Volume Attachment)](#61-containercreating-stuck-on-volume-attachment)
  - [6.2 `FailedMount` & `FailedAttachVolume`](#62-failedmount--failedattachvolume)
  - [6.3 `PersistentVolumeClaimNotBound` / `ProvisioningFailed`](#63-persistentvolumeclaimnotbound--provisioningfailed)
  - [6.4 `Multi-Attach error for volume` (ReadWriteOnce Conflict)](#64-multi-attach-error-for-volume-readwriteonce-conflict)
  - [6.5 `VolumeResizeFailed` / `FileSystemResizeFailed`](#65-volumeresizefailed--filesystemresizefailed)
  - [6.6 `VolumeConditionAbnormal` (CSI Volume Health Monitoring)](#66-volumeconditionabnormal-csi-volume-health-monitoring)
  - [6.7 `NFSStaleFileHandle` / `StorageNodeDown`](#67-nfsstalefilehandle--storagenodedown)
- [🔄 7. Init Containers, Sidecars & Ephemeral Debuggers](#-7-init-containers-sidecars--ephemeral-debuggers)
  - [7.1 `Init:0/N` / `Init:1/N` (Stuck Waiting on Dependencies)](#71-init0n--init1n-stuck-waiting-on-dependencies)
  - [7.2 `Init:CrashLoopBackOff` & `Init:Error`](#72-initcrashloopbackoff--initerror)
  - [7.3 `Init:ImagePullBackOff`](#73-initimagepullbackoff)
  - [7.4 `Init:CreateContainerConfigError` & `Init:CreateContainerError`](#74-initcreatecontainerconfigerror--initcreatecontainererror)
  - [7.5 `Init:OOMKilled` (Exit Code 137 in Init Phase)](#75-initoomkilled-exit-code-137-in-init-phase)
  - [7.6 `ServiceMeshSidecarNotReady` (Istio / Linkerd Startup Delay)](#76-servicemeshsidecarnotready-istio--linkerd-startup-delay)
  - [7.7 `EphemeralContainersFailed` / `EphemeralContainerNotReady`](#77-ephemeralcontainersfailed--ephemeralcontainernotready)
- [💥 8. Runtime Crashes, Memory & Resource Starvation](#-8-runtime-crashes-memory--resource-starvation)
  - [8.1 `Error` (Non-Zero Exit Code)](#81-error-non-zero-exit-code)
  - [8.2 `CrashLoopBackOff`](#82-crashloopbackoff)
  - [8.3 `OOMKilled` (Exit Code 137 - Out of Memory)](#83-oomkilled-exit-code-137---out-of-memory)
  - [8.4 `Completed` (Exit Code 0 on Backgrounded Server)](#84-completed-exit-code-0-on-backgrounded-server)
  - [8.5 `ContainerStatusUnknown`](#85-containerstatusunknown)
  - [8.6 `DeadlineExceeded` (`activeDeadlineSeconds` Reached)](#86-deadlineexceeded-activedeadlineseconds-reached)
  - [8.7 `BackoffLimitExceeded` (Batch Job Failure)](#87-backofflimitexceeded-batch-job-failure)
  - [8.8 `JobSuspended` (`spec.suspend: true`)](#88-jobsuspended-specsuspend-true)
- [🔧 9. In-Place Pod Resizing & Dynamic Resource Allocation (K8s 1.27+)](#-9-in-place-pod-resizing--dynamic-resource-allocation-k8s-127)
  - [9.1 In-Place Resize States: `Proposed`, `InProgress`, `Deferred`, `Infeasible`](#91-in-place-resize-states-proposed-inprogress-deferred-infeasible)
  - [9.2 `DynamicResourceAllocationPending` (DRA Hardware Claims)](#92-dynamicresourceallocationpending-dra-hardware-claims)
- [🏥 10. Health Checks (Probes) & Custom Readiness Gates](#-10-health-checks-probes--custom-readiness-gates)
  - [10.1 `Unhealthy` (Liveness Probe Failed)](#101-unhealthy-liveness-probe-failed)
  - [10.2 `ReadinessProbeFailed` (Traffic Cut Off)](#102-readinessprobefailed-traffic-cut-off)
  - [10.3 `StartupProbeFailed` (Slow Boot Timeout)](#103-startupprobefailed-slow-boot-timeout)
  - [10.4 `ExecProbeTimeout` / `ProbeWarning`](#104-execprobetimeout--probewarning)
  - [10.5 `ReadinessGatesFailed` / `ReadinessGatesNotReady` (Cloud Load Balancers)](#105-readinessgatesfailed--readinessgatesnotready-cloud-load-balancers)
- [🌐 11. DNS, CoreDNS & Cluster Networking Failures](#-11-dns-coredns--cluster-networking-failures)
  - [11.1 `CoreDNS CrashLoopBackOff` (Forwarding Loop Detected)](#111-coredns-crashloopbackoff-forwarding-loop-detected)
  - [11.2 `NameResolutionFailure` (`ndots:5` Latency & NXDOMAIN)](#112-nameresolutionfailure-ndots5-latency--nxdomain)
  - [11.3 `ServiceEndpointsMissing` (No Pods Match Selector)](#113-serviceendpointsmissing-no-pods-match-selector)
- [🚪 12. Node Pressure, Eviction, Preemption & Teardown Errors](#-12-node-pressure-eviction-preemption--teardown-errors)
  - [12.1 `Evicted` (`DiskPressure`, `MemoryPressure`, `PIDPressure`)](#121-evicted-diskpressure-memorypressure-pidpressure)
  - [12.2 `Evicted` (`DisruptionTarget`: `EvictionByEvictionAPI`, `TerminationByKubelet`, `DeletionByPodGC`)](#122-evicted-disruptiontarget-evictionbyevictionapi-terminationbykubelet-deletionbypodgc)
  - [12.3 `TaintManagerEviction` (`NoExecute` Taint Applied)](#123-taintmanagereviction-noexecute-taint-applied)
  - [12.4 `TerminatedDueToNodeShutdown` (Graceful Node Shutdown)](#124-terminatedduetonodeshutdown-graceful-node-shutdown)
  - [12.5 `Preempted` / `Preempting` (`PriorityClass`)](#125-preempted--preempting-priorityclass)
  - [12.6 `Terminating` (Stuck on Finalizers or Storage Unmount)](#126-terminating-stuck-on-finalizers-or-storage-unmount)
  - [12.7 `GracefulTerminationTimeout` (App Ignored SIGTERM)](#127-gracefulterminationtimeout-app-ignored-sigterm)
  - [12.8 `Unknown` / `NodeLost` / `NodeNotReady`](#128-unknown--nodelost--nodenotready)
- [🔄 13. Workload Controller Deadlocks & Autoscaler Failures](#-13-workload-controller-deadlocks--autoscaler-failures)
  - [13.1 `ProgressDeadlineExceeded` (Deployment Rollout Stuck)](#131-progressdeadlineexceeded-deployment-rollout-stuck)
  - [13.2 `StatefulSetOrdinalDeadlock` (StatefulSet Pod Order Blocked)](#132-statefulsetordinaldeadlock-statefulset-pod-order-blocked)
  - [13.3 `PodDisruptionBudgetViolation` (`Cannot evict pod: PDB violated`)](#133-poddisruptionbudgetviolation-cannot-evict-pod-pdb-violated)
  - [13.4 `HPAUnableToComputeMetrics` / `ScalingLimited`](#134-hpaunabletocomputemetrics--scalinglimited)
  - [13.5 `KEDATriggerError` (External Event Source Down)](#135-kedatriggererror-external-event-source-down)
  - [13.6 `ClusterAutoscalerScaleUpFailed` (Cloud Quota Reached)](#136-clusterautoscalerscaleupfailed-cloud-quota-reached)
- [🛡️ 14. Specialized Workload & Infrastructure Failures](#️-14-specialized-workload--infrastructure-failures)
  - [14.1 `CronJobMissedSchedule` / `CannotDetermineTimeZone`](#141-cronjobmissedschedule--cannotdeterminetimezone)
  - [14.2 `DaemonSetRolloutBlocked` (Node Taints / Master Node Exclusion)](#142-daemonsetrolloutblocked-node-taints--master-node-exclusion)
  - [14.3 `GatewayAPI / Ingress 502 / 504 Bad Gateway`](#143-gatewayapi--ingress-502--504-bad-gateway)
  - [14.4 `x509: CertificateExpired` / `Certificate Signed by Unknown Authority`](#144-x509-certificateexpired--certificate-signed-by-unknown-authority)
  - [14.5 `IPv6DualStackAllocationFailed`](#145-ipv6dualstackallocationfailed)
  - [14.6 `WindowsNodeContainerFailed` (HNS Network & Isolation Mismatch)](#146-windowsnodecontainerfailed-hns-network--isolation-mismatch)
- [🔢 15. Master Container Exit Codes & OS Signals Table](#-15-master-container-exit-codes--os-signals-table)
- [🗺️ 16. The Ultimate 60-Second Diagnostic Decision Tree](#️-16-the-ultimate-60-second-diagnostic-decision-tree)
- [📋 17. Master Quick Reference Action Matrix](#-17-master-quick-reference-action-matrix)

---

## 🧠 0. Pod Lifecycle Architecture: Phases, Container States & Conditions

Understanding Kubernetes troubleshooting requires distinguishing between the **3 Architectural Layers of Status**:

```
Layer 1: Pod Phase (High-level summary: Pending, Running, Succeeded, Failed, Unknown)
   │
   ├── Layer 2: Pod Conditions (Boolean health gates: PodScheduled, Initialized, ContainersReady, Ready, DisruptionTarget)
   │
   └── Layer 3: Container States (Low-level status: Waiting, Running, Terminated + Specific Reasons)
```

```
[ Pod API Object Created ]
           │
           ▼
 ┌─────────────────────────────────────────────────────────────┐
 │ 1. Phase: PENDING                                           │
 │    Condition: PodScheduled = False ──► Scheduler filters    │
 │    Condition: PodScheduled = True                           │
 │    Condition: Initialized = False  ──► Init containers run  │
 │    Condition: Initialized = True                            │
 └──────────────────────────────┬──────────────────────────────┘
                                │
                                ▼
 ┌─────────────────────────────────────────────────────────────┐
 │ 2. Phase: RUNNING                                           │
 │    Container State: Waiting ──► Pulling image / creating    │
 │    Container State: Running ──► Startup Probe passes        │
 │    Condition: ContainersReady = True                        │
 │    Condition: Ready = True  ──► Traffic routed via Service  │
 └──────────────────────────────┬──────────────────────────────┘
                                │
        ┌───────────────────────┴───────────────────────┐
        ▼                                               ▼
 ┌─────────────────────────────┐         ┌─────────────────────────────┐
 │ 3. Phase: SUCCEEDED         │         │ 4. Phase: FAILED            │
 │    Container State:         │         │    Container State:         │
 │    Terminated (Exit 0)      │         │    Terminated (Exit != 0)   │
 │    (Batch Jobs / CronJobs)  │         │    (Crashes / OOMKilled)    │
 └─────────────────────────────┘         └─────────────────────────────┘
```

---

## 🛠️ Diagnostic Master-Kit: 12 Essential Commands

```bash
# 1. Quick overview of all pods, nodes, IP addresses, and restarts
kubectl get pods -o wide -A

# 2. The #1 command: Detailed events, state reasons, and probe failures
kubectl describe pod <pod-name> -n <namespace>

# 3. View live application logs
kubectl logs <pod-name> -n <namespace> -c <container-name>

# 4. View logs of the container BEFORE it crashed
kubectl logs <pod-name> -n <namespace> -c <container-name> --previous

# 5. Stream live logs with timestamps
kubectl logs -f <pod-name> -n <namespace> --timestamps

# 6. Stream logs of an Init Container
kubectl logs <pod-name> -n <namespace> -c <init-container-name>

# 7. List recent cluster warnings and scheduling failures sorted by time
kubectl get events -n <namespace> --sort-by='.metadata.creationTimestamp'

# 8. Check node resource consumption (CPU / Memory pressure)
kubectl top nodes
kubectl top pods -n <namespace>

# 9. Dump full JSON/YAML status with exact condition timestamps
kubectl get pod <pod-name> -n <namespace> -o yaml

# 10. Launch an interactive debug container directly into the Pod's network/namespace
kubectl debug -it <pod-name> --image=nicolaka/netshoot --target=<container-name>

# 11. Check namespace resource quota limits and current usage
kubectl get resourcequota -n <namespace> -o yaml

# 12. Check PVC & StorageClass status
kubectl get pvc,pv,sc -A
```

---

## 📑 1. Scheduling, Placement & Admission Control Errors

---

### 1.1 `Pending` (Generic Unscheduled)
* **📖 What It Means:** The Pod definition has been accepted by the Kubernetes API server, but it cannot be assigned to any worker node or cannot begin container creation.
* 🎭 **Real-World Scenario:** You create a deployment requesting `8 GB` RAM per replica with 5 replicas. Your cluster only has 3 worker nodes with `4 GB` RAM each. The scheduler evaluates every node, finds 0 eligible nodes, and keeps the Pods in `Pending` forever.
* 🔬 **Root Causes:**
  - Cluster compute exhaustion (No node has enough free unallocated CPU or RAM).
  - Required PersistentVolumeClaim (PVC) is unbound.
  - NodeSelector / Affinity conditions cannot be satisfied.
  - Node Taints exist without matching Pod Tolerations.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  ```
  *Output under `Events`:*
  ```text
  Warning  FailedScheduling  default-scheduler  0/3 nodes available: 3 Insufficient memory.
  ```
* 🛠️ **Step-by-Step Fix:**
  1. Review container requests in YAML:
     ```yaml
     resources:
       requests:
         cpu: "250m"      # Lower from unrealistic values
         memory: "512Mi"
     ```
  2. Enable Cluster Autoscaler on cloud (EKS/GKE/AKS) to add worker nodes automatically.
  3. Verify PVC status: `kubectl get pvc -n <namespace>`.

---

### 1.2 `FailedScheduling` (Insufficient CPU / Memory / GPU)
* **📖 What It Means:** The Scheduler ran its filter plugins against all nodes in the cluster and every single node failed the resource capacity check.
* 🎭 **Real-World Scenario:** An AI training job specifies `resources.limits: { "nvidia.com/gpu": "2" }`. Your cluster nodes only have 1 physical GPU each, or the GPU node drivers (NVIDIA device plugin) are not installed.
* 🔬 **Root Causes:**
  - `Insufficient cpu`, `Insufficient memory`, or `Insufficient nvidia.com/gpu`.
  - Node allocatable capacity is completely booked by other pods' `requests`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe nodes | grep -A 8 "Allocated resources:"
  ```
* 🛠️ **Step-by-Step Fix:**
  - Add dedicated GPU nodes or reduce request footprints.
  - Audit cluster pod requests using `kubectl top nodes` vs allocated requests.

---

### 1.3 `SchedulingGated` (Kubernetes 1.26+ Dynamic Scheduling Gates)
* **📖 What It Means:** The Pod is intentionally placed in a waiting gate by an external controller (e.g. Karpenter node autoscaler, Dynamic Resource Allocation, or custom admission controllers) before the standard scheduler is permitted to place it.
* 🎭 **Real-World Scenario:** Karpenter detects that a Pod requires a special instance type. It attaches a scheduling gate `spec.schedulingGates: [{ name: "karpenter.sh/provisioning" }]` to the Pod so `kube-scheduler` ignores it while Karpenter provisions a new AWS EC2 VM. Once the VM boots, Karpenter removes the gate.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get pod <pod-name> -o yaml | grep -A 3 "schedulingGates:"
  ```
* 🛠️ **Step-by-Step Fix:**
  - If a gate is stuck because an external controller failed:
    ```bash
    kubectl patch pod <pod-name> -p '{"spec":{"schedulingGates":[]}}'
    ```

---

### 1.4 `ResourceQuotaExceeded` (`Forbidden: exceeded quota`)
* **📖 What It Means:** The Kubernetes Admission Controller rejected the Pod creation because the namespace's cumulative `ResourceQuota` limit has been exceeded.
* 🎭 **Real-World Scenario:** The DevOps team set a namespace quota of `requests.cpu: 10`. You deploy a new deployment with 4 replicas requesting `3 CPU` each (Total: 12 CPU). The API server blocks the Pod creation immediately: `pods "api-xxx" is forbidden: exceeded quota: compute-quota, requested: requests.cpu=3, used: requests.cpu=8, limited: requests.cpu=10`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe resourcequota -n <namespace>
  ```
* 🛠️ **Step-by-Step Fix:**
  - Scale down other unused deployments in the namespace.
  - Request a quota increase from cluster administrators:
    ```yaml
    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: compute-quota
    spec:
      hard:
        requests.cpu: "20"
        requests.memory: "50Gi"
    ```

---

### 1.5 `LimitRangeViolation` (`Forbidden: minimum/maximum constraint`)
* **📖 What It Means:** The container resource specifications violate the namespace's `LimitRange` rules (e.g., minimum CPU, maximum memory, or ratio between request and limit).
* 🎭 **Real-World Scenario:** The namespace specifies a `LimitRange` stating no single container may request more than `4Gi` memory. You specify `limits.memory: 8Gi`. The API server rejects Pod creation.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe limitrange -n <namespace>
  ```
* 🛠️ **Step-by-Step Fix:** Adjust the container's `resources.requests` and `limits` to fall within the `[min, max]` boundaries defined by the `LimitRange`.

---

### 1.6 `AdmissionWebhookDenied` / `WebhookTimeout`
* **📖 What It Means:** A Validating or Mutating Admission Webhook (e.g., Kyverno, OPA Gatekeeper, Istio Sidecar Injector, Cert-Manager) rejected the Pod creation request or timed out trying to reach the webhook server.
* 🎭 **Real-World Scenario:** OPA Gatekeeper policy mandates that all production Pods must have a `cost-center` and `owner` label. You deploy without these labels, and API server returns: `admission webhook "validation.gatekeeper.sh" denied the request: [mandatory-labels] You must provide label <cost-center>`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations
  kubectl get pods -n gatekeeper-system  # or kyverno
  ```
* 🛠️ **Step-by-Step Fix:**
  - Supply the required labels/annotations in your Pod YAML.
  - If a dead webhook is blocking the entire cluster, temporarily delete or fix the webhook service:
    ```bash
    kubectl delete validatingwebhookconfiguration <broken-webhook-name>
    ```

---

### 1.7 `MatchNodeSelector` / `NodeAffinity` Failure
* **📖 What It Means:** The Pod explicitly specifies that it must run on a node with particular key-value labels, but no active node has those labels.
* 🎭 **Real-World Scenario:** You deploy a Redis cache with `nodeSelector: { disktype: ssd, environment: production }`. In your new staging cluster, the nodes were labeled `env: prod` and `storage: nvme`. Because the labels do not match character-for-character, the scheduler rejects all nodes.
* 🔬 **Root Causes:**
  - Typo in node label key or value.
  - Labeled nodes are currently drained, cordoned, or powered off.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get nodes --show-labels
  kubectl describe pod <pod-name> | grep -A 5 "Node-Selectors:"
  ```
* 🛠️ **Step-by-Step Fix:**
  1. Label the target worker node:
     ```bash
     kubectl label nodes worker-node-01 disktype=ssd environment=production
     ```
  2. Or switch from hard affinity (`requiredDuringSchedulingIgnoredDuringExecution`) to soft affinity (`preferredDuringSchedulingIgnoredDuringExecution`).

---

### 1.8 `UntoleratedTaint` (`NoSchedule` / `NoExecute`)
* **📖 What It Means:** Worker nodes have a **Taint** (a repellent barrier) applied to reserve them for special workloads, and your Pod lacks the matching **Toleration** (key/pass).
* 🎭 **Real-World Scenario:** Master/Control-plane nodes are tainted with `node-role.kubernetes.io/control-plane:NoSchedule`. If all worker nodes crash, your pods cannot be scheduled onto the master nodes because they lack tolerations.
* 🔬 **Root Causes:**
  - Custom taints applied to dedicated nodes (e.g., `gpu=true:NoSchedule` or `dedicated=database:NoSchedule`).
  - Automatic node problem taints (e.g., `node.kubernetes.io/unreachable`, `node.kubernetes.io/disk-pressure`).
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe nodes | grep -i taints
  ```
* 🛠️ **Step-by-Step Fix:**
  - Add the toleration to your Pod YAML:
    ```yaml
    tolerations:
    - key: "dedicated"
      operator: "Equal"
      value: "database"
      effect: "NoSchedule"
    ```

---

### 1.9 `PodTopologySpreadFilter` / `PodAntiAffinity` Conflict
* **📖 What It Means:** The scheduler cannot place the Pod without violating high-availability spreading rules (e.g. "Do not place two replicas in the same Availability Zone or on the same Node").
* 🎭 **Real-World Scenario:** You configure `podAntiAffinity` with `requiredDuringScheduling` so that no two database replicas run on the same physical host. You request 4 replicas, but your cluster only has 3 worker nodes. The 4th replica stays `Pending`.
* 🔬 **Root Causes:**
  - Strict anti-affinity rules exceed physical node/zone count.
  - Strict `topologySpreadConstraints` with `whenUnsatisfiable: DoNotSchedule`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  ```
  *Event:* `0/3 nodes available: 3 node(s) had pod anti-affinity rules`.
* 🛠️ **Step-by-Step Fix:**
  - Change `whenUnsatisfiable: DoNotSchedule` to `ScheduleAnyway`.
  - Use `preferredDuringSchedulingIgnoredDuringExecution` instead of `required`.

---

### 1.10 `VolumeZoneConflict` (Cross-AZ Disk Attachment)
* **📖 What It Means:** The Pod's PersistentVolume is locked in Availability Zone `A`, but the scheduler attempted to place the Pod onto a node in Availability Zone `B`.
* 🎭 **Real-World Scenario:** In AWS EKS, an EBS volume PV was created in `us-east-1a`. All worker nodes in `us-east-1a` are currently at 100% capacity. The scheduler tries to place the pod on a node in `us-east-1b`, but EBS volumes cannot cross availability zone boundaries.
* 🔬 **Root Causes:**
  - StorageClass was created without `volumeBindingMode: WaitForFirstConsumer`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  ```
  *Event:* `1 node(s) had volume node affinity conflict`.
* 🛠️ **Step-by-Step Fix:**
  - Always configure your StorageClass with topology-aware binding:
    ```yaml
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: ebs-gp3-sc
    provisioner: ebs.csi.aws.com
    volumeBindingMode: WaitForFirstConsumer
    ```

---

### 1.11 `HostPortConflict` (Port Already Bound on Node)
* **📖 What It Means:** The Pod spec defines `hostPort: 80`, but another Pod running on that specific node is already binding port 80 of the host network.
* 🎭 **Real-World Scenario:** You deploy an Ingress Controller with 3 replicas requesting `hostPort: 80` on a 2-node cluster. The 3rd replica cannot be scheduled because both nodes already have port 80 occupied.
* 🛠️ **Step-by-Step Fix:**
  - Use Kubernetes `Service` with `type: LoadBalancer` or `type: NodePort` instead of hardcoding `hostPort`.
  - Or use a `DaemonSet` ensuring only 1 replica per node.

---

### 1.12 `MaxPodsExceeded` / `NodeUnschedulable` (`Cordoned`)
* **📖 What It Means:**
  - `MaxPodsExceeded`: The worker node has reached its hard limit for running pods (default is 110 pods per node).
  - `NodeUnschedulable`: The node has been marked `cordoned` (`SchedulingDisabled`) during maintenance.
* 🎭 **Real-World Scenario:** A DevOps engineer ran `kubectl cordon worker-01` before a kernel upgrade and forgot to uncordon it. New pods cannot be scheduled onto `worker-01`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get nodes
  # Look for: "Ready,SchedulingDisabled"
  ```
* 🛠️ **Step-by-Step Fix:**
  - Uncordon the node:
    ```bash
    kubectl uncordon worker-01
    ```
  - For `MaxPodsExceeded`, clean up dead completed pods or increase `--max-pods` in kubelet config.

---

## 🌐 2. Pod Sandbox & CNI Network Initialization Errors

---

### 2.1 `PodInitializing`
* **📖 What It Means:** The Pod is currently executing Init Containers, creating the pause network sandbox, or mounting secrets/ConfigMaps before launching application containers.
* 🎭 **Real-World Scenario:** You have 3 Init Containers that run sequentially. While they execute, the Pod status displays `PodInitializing` or `Init:X/Y`.
* 🛠️ **Step-by-Step Fix:** Normal transitional state. If stuck, check Init Container logs: `kubectl logs <pod-name> -c <init-container>`.

---

### 2.2 `CreatePodSandboxError` / `FailedCreatePodSandBox`
* **📖 What It Means:** The Kubelet called the Container Runtime Interface (CRI / containerd) to allocate the Linux network namespace and pause container, but the CNI plugin failed.
* 🎭 **Real-World Scenario:** In AWS EKS using AWS VPC CNI (`aws-node`), the node ran out of secondary IP addresses because the EC2 instance type (`t3.medium`) only supports 18 IPs. Pod sandbox creation fails with `FailedCreatePodSandBox: CNI failed to set up pod network: add cmd: failed to assign an IP address`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  kubectl logs -n kube-system -l k8s-app=aws-node
  ```
* 🛠️ **Step-by-Step Fix:**
  - Enable Prefix Delegation in AWS VPC CNI to support hundreds of IPs per node.
  - Restart CNI daemonset pods: `kubectl rollout restart ds/aws-node -n kube-system`.

---

### 2.3 `NetworkPluginNotReady` / `NetworkNotReady`
* **📖 What It Means:** The worker node's Kubelet is running, but the CNI network plugin (Calico, Flannel, Cilium) is dead, uninstalled, or misconfigured.
* 🎭 **Real-World Scenario:** A new node joins the cluster via `kubeadm join`. Before you apply the Calico CNI manifest, the node shows `NotReady` and pods fail with `network plugin is not ready: cni config uninitialized`.
* 🛠️ **Step-by-Step Fix:** Apply or reinstall your CNI provider (e.g., `kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml`).

---

### 2.4 `PodSubnetExhaustion` (No Free IPs in Node/Cluster CIDR)
* **📖 What It Means:** The cluster CIDR (e.g. `10.244.0.0/16`) or node `/24` pod subnet has exhausted all available IP addresses.
* 🎭 **Real-World Scenario:** You scaled your workload to 500 pods in a small `/24` subnet (max 254 IPs). New pods get stuck in `CreatePodSandboxError`.
* 🛠️ **Step-by-Step Fix:** Expand cluster CIDR or configure IP pools with larger subnets in Calico / Cilium.

---

### 2.5 `FailedSync` (Kubelet Sync Loop Failure)
* **📖 What It Means:** The Kubelet's internal reconciliation loop (`syncPod`) encountered an unexpected runtime exception while trying to create or delete pod resources.
* 🎭 **Real-World Scenario:** System disk `/var/lib/kubelet` ran out of disk space or an invalid Docker daemon configuration caused the CRI gRPC socket to hang.
* 🛠️ **Step-by-Step Fix:** Inspect node system logs: `sudo journalctl -u kubelet -e`.

---

### 2.6 `ServiceAccountNotFound` / `kube-api-access` Mount Failure
* **📖 What It Means:** The Pod specifies a `serviceAccountName`, but that ServiceAccount does not exist in the namespace. Kubelet cannot project the API token volume `kube-api-access-xxx`.
* 🎭 **Real-World Scenario:** Your deployment specifies `serviceAccountName: vault-auth-sa`. You deployed to a new namespace where `vault-auth-sa` was never created.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  # Warning FailedMount MountVolume.SetUp failed for volume "kube-api-access-xxx" : serviceaccount "vault-auth-sa" not found
  ```
* 🛠️ **Step-by-Step Fix:** Create the ServiceAccount in the matching namespace: `kubectl create sa vault-auth-sa -n <namespace>`.

---

## 📦 3. Image, OCI Registry & Container Runtime (CRI) Errors

---

### 3.1 `ErrImagePull`
* **📖 What It Means:** The worker node's container runtime failed on its first attempt to pull the container image from the remote registry.
* 🎭 **Real-World Scenario:** You pushed your Docker image as `registry.gitlab.com/my-org/auth:v1.4.2`, but in the Kubernetes YAML you made a typo and wrote `auth:1.4.2` (missing `v`). The registry responds with HTTP 404 Not Found.
* 🔬 **Root Causes:**
  - Image tag or repository name typo.
  - Private registry credentials missing or expired.
  - Node DNS or internet connection failure.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  ```
  *Event:* `Failed to pull image "...": rpc error: code = NotFound desc = failed to pull and unpack image`.
* 🛠️ **Step-by-Step Fix:**
  1. Test image pull locally: `docker pull <full-image-name>:<tag>`.
  2. If private, create `imagePullSecrets`:
     ```bash
     kubectl create secret docker-registry my-registry-secret \
       --docker-server=https://index.docker.io/v1/ \
       --docker-username=myuser \
       --docker-password=mypassword \
       --docker-email=me@example.com
     ```
  3. Reference it in your Pod YAML:
     ```yaml
     spec:
       imagePullSecrets:
       - name: my-registry-secret
     ```

---

### 3.2 `ImagePullBackOff`
* **📖 What It Means:** Kubernetes failed to pull the image and is now waiting exponentially (10s, 20s, 40s... up to 5 mins) before trying again to prevent overwhelming the registry and network.
* 🎭 **Real-World Scenario:** Your deployment has 20 pods that hit an image pull error. Kubernetes keeps backing off and retrying.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  ```
* 🛠️ **Step-by-Step Fix:**
  - Fix the underlying `ErrImagePull` root cause.
  - Force immediate retry after fixing:
    ```bash
    kubectl rollout restart deployment <deployment-name>
    ```

---

### 3.3 `InvalidImageName`
* **📖 What It Means:** The image path violates Docker/OCI syntax specifications.
* 🎭 **Real-World Scenario:** An unrendered CI/CD template variable resulted in `image: "docker.io/repo/app:${BUILD_ID}"` or contained uppercase characters like `MyCompany/API:v1`.
* 🔬 **Root Causes:** Uppercase letters in repository names, spaces, or illegal punctuation.
* 🛠️ **Step-by-Step Fix:** Convert repository names to lowercase and verify variable substitution in CI pipelines.

---

### 3.4 `ErrImageNeverPull`
* **📖 What It Means:** The Pod specifies `imagePullPolicy: Never`, requiring the image to be pre-cached on the node, but the image is absent.
* 🎭 **Real-World Scenario:** In local development (Minikube / Kind), you built `my-app:dev` on your host laptop Docker engine, but did not load it into the Minikube/Kind VM node.
* 🛠️ **Step-by-Step Fix:**
  - Load into Kind: `kind load docker-image my-app:dev --name <cluster-name>`.
  - Load into Minikube: `minikube image load my-app:dev`.
  - Or change `imagePullPolicy` to `IfNotPresent`.

---

### 3.5 `ImageInspectError` / `ErrImageInspect`
* **📖 What It Means:** The container runtime pulled the image layers, but could not parse the image config manifest or tar metadata.
* 🎭 **Real-World Scenario:** The worker node experienced disk corruption or ran out of disk space halfway through decompressing a 5 GB layer in `/var/lib/containerd`.
* 🛠️ **Step-by-Step Fix:**
  - SSH into the node and remove cached corrupted image:
    ```bash
    sudo crictl rmi <image-id>
    ```
  - Re-trigger pod: `kubectl delete pod <pod-name>`.

---

### 3.6 `ImageArchitectureMismatch` (`exec format error`)
* **📖 What It Means:** The container image was compiled for a CPU architecture different from the worker node CPU (e.g. ARM64 vs x86_64 AMD64).
* 🎭 **Real-World Scenario:** A developer on an Apple Silicon Mac (M1/M2/M3 - ARM64) built a Docker image using `docker build -t app:v1 .` and pushed it. The production Kubernetes cluster runs on Intel Xeon x86_64 machines. The container crashes immediately with: `standard_init_linux.go: exec user process caused "exec format error"`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl logs <pod-name> --previous
  # Look for: "exec format error"
  ```
* 🛠️ **Step-by-Step Fix:**
  - Build multi-architecture images using Docker Buildx:
    ```bash
    docker buildx build --platform linux/amd64,linux/arm64 -t myorg/app:v1 --push .
    ```

---

### 3.7 `RegistryUnauthorized` (`401 Unauthorized` / `403 Forbidden`)
* **📖 What It Means:** The registry rejected the pull request due to missing, invalid, or expired credentials.
* 🎭 **Real-World Scenario:** AWS ECR authorization tokens expire every 12 hours. If your cluster uses a cron job to refresh ECR tokens and the job fails, all new pod image pulls fail with `401 Unauthorized`.
* 🛠️ **Step-by-Step Fix:**
  - Refresh ECR / GCP Artifact Registry credentials or configure AWS IRSA (IAM Roles for Service Accounts) with Amazon EKS.

---

### 3.8 `DockerHubRateLimit` (`HTTP 429 Too Many Requests`)
* **📖 What It Means:** Anonymous image pulls from Docker Hub exceeded 100 pulls per 6 hours per IP address.
* 🎭 **Real-World Scenario:** An entire office or shared cloud NAT gateway IP pulls public base images (`python:3.11`, `node:18`). Docker Hub blocks requests with HTTP 429.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  # Look for: "toomanyrequests: You have reached your unauthenticated pull rate limit"
  ```
* 🛠️ **Step-by-Step Fix:**
  - Add an authenticated Docker Hub secret or mirror public images to your private internal registry (e.g. AWS ECR / Harbor).

---

### 3.9 `ImagePullTimeout` (Gigantic Layers / Slow Link)
* **📖 What It Means:** The image download exceeded the Kubelet `--image-pull-progress-deadline` (default 1-2 minutes without progress).
* 🎭 **Real-World Scenario:** An LLM / Machine Learning container image is 25 GB. On a slow node internet connection, the CRI times out and aborts the pull.
* 🛠️ **Step-by-Step Fix:**
  - Pre-warm images on nodes using DaemonSets.
  - Mount model weights from external S3/NFS volumes instead of baking 20 GB into the container image.

---

## 🔒 4. Security Context, Kernel Profiles & Privilege Errors

---

### 4.1 `RunAsNonRootError` / `MustRunAsNonRoot`
* **📖 What It Means:** Your Pod specifies `runAsNonRoot: true`, but the Docker image specifies `USER root` (UID 0) and has no numeric non-root user defined.
* 🎭 **Real-World Scenario:** For enterprise compliance, you configure:
  ```yaml
  securityContext:
    runAsNonRoot: true
  ```
  The container image is standard `nginx:latest`, which runs as root by default. Kubernetes halts execution with `container has runAsNonRoot and image will run as root`.
* 🛠️ **Step-by-Step Fix:**
  - Add explicit UID/GID in your Pod YAML:
    ```yaml
    securityContext:
      runAsNonRoot: true
      runAsUser: 10001
      runAsGroup: 10001
    ```
  - Or update the `Dockerfile` with `USER 10001`.

---

### 4.2 `ContainerCannotRun` (AppArmor / Seccomp / SELinux Denied)
* **📖 What It Means:** The Linux kernel security module (AppArmor, SELinux, or Seccomp profile) blocked a restricted system call (e.g. `ptrace`, `setuid`, `sys_admin`).
* 🎭 **Real-World Scenario:** Your container process tries to change network routing rules using `iptables` without `CAP_NET_ADMIN` capability. The kernel halts the process.
* 🔍 **How to Inspect:**
  ```bash
  sudo dmesg | grep -i "apparmor"
  sudo audit2why < /var/log/audit/audit.log
  ```
* 🛠️ **Step-by-Step Fix:** Add necessary Linux capabilities in `securityContext.capabilities.add` or adjust AppArmor/Seccomp profiles.

---

### 4.3 `ReadOnlyFilesystemError`
* **📖 What It Means:** The Pod specifies `readOnlyRootFilesystem: true`, and the container process attempted to write to a directory on disk that is not mounted as a writable volume.
* 🎭 **Real-World Scenario:** A Java or Python app tries to create temporary files in `/tmp` or write logs to `/var/log/app.log`. The OS returns `Read-only file system (errno 30)` and crashes.
* 🛠️ **Step-by-Step Fix:**
  - Mount an in-memory `emptyDir` volume specifically for temporary directories:
    ```yaml
    volumeMounts:
    - name: tmp-volume
      mountPath: /tmp
    volumes:
    - name: tmp-volume
      emptyDir: {}
    ```

---

### 4.4 `SysctlNotAllowed` / `ForbiddenSysctl`
* **📖 What It Means:** The Pod attempts to tune unsafe Linux kernel sysctls (e.g., `net.core.somaxconn`, `net.ipv4.tcp_tw_reuse`) that are not whitelisted in the Kubelet `--allowed-unsafe-sysctls` flag.
* 🛠️ **Step-by-Step Fix:** Whitelist the sysctls in `/var/lib/kubelet/config.yaml` on worker nodes.

---

### 4.5 `HostSecurityViolation` (`HostPID` / `HostNetwork` / `HostIPC` Forbidden)
* **📖 What It Means:** The Pod specifies `hostNetwork: true` or `hostPID: true`, but Pod Security Admission (PSA) enforces the `restricted` or `baseline` profile, blocking execution.
* 🛠️ **Step-by-Step Fix:** Remove host namespace flags or label the namespace for `privileged` profile:
  ```bash
  kubectl label ns <namespace> pod-security.kubernetes.io/enforce=privileged
  ```

---

### 4.6 `DevicePluginFailed` / `GPUAllocationFailed`
* **📖 What It Means:** The container requested specialized hardware (`nvidia.com/gpu`, SR-IOV NIC), but the node device plugin crashed or lost communication with hardware drivers.
* 🛠️ **Step-by-Step Fix:** Restart the device plugin daemonset: `kubectl rollout restart ds/nvidia-device-plugin-daemonset -n kube-system`.

---

## ⚙️ 5. Container Configuration & Lifecycle Hook Errors

---

### 5.1 `CreateContainerConfigError`
* **📖 What It Means:** Kubernetes cannot configure container environment variables or volume mounts because a referenced `ConfigMap` or `Secret` does not exist in the same namespace.
* 🎭 **Real-World Scenario:** Your deployment YAML references a ConfigMap for environment configuration:
  ```yaml
  envFrom:
    - configMapRef:
        name: api-config  # <-- Typo! The actual ConfigMap was named api-configuration
  ```
  Kubernetes blocks container creation.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  # Look for: Error: configmap "api-config" not found
  ```
* 🛠️ **Step-by-Step Fix:**
  1. Check existing resources: `kubectl get configmap,secret -n <namespace>`.
  2. Fix the typo in YAML or create the missing ConfigMap/Secret.

---

### 5.2 `CreateContainerError`
* **📖 What It Means:** The low-level container runtime (`containerd`/`runc`) failed while constructing the container sandbox.
* 🎭 **Real-World Scenario:** You declared two volume mounts pointing to the exact same `mountPath: /var/log`, or specified conflicting security capabilities.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  ```
* 🛠️ **Step-by-Step Fix:**
  - Ensure every volume mount has a unique, non-overlapping `mountPath`.
  - Validate `securityContext` settings.

---

### 5.3 `RunContainerError`
* **📖 What It Means:** The container sandbox was created, but execution failed at the moment of starting the process.
* 🎭 **Real-World Scenario:** You wrote an `entrypoint.sh` script on Windows with Windows `CRLF` newlines and forgot `chmod +x`. When Linux tries to run it, `/bin/sh^M: bad interpreter: No such file or directory` or `permission denied` occurs.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  # Look for: failed to start container: exec: "/app/start.sh": permission denied
  ```
* 🛠️ **Step-by-Step Fix:**
  - In `Dockerfile`: Add `RUN chmod +x /app/entrypoint.sh`.
  - Convert line endings: `dos2unix entrypoint.sh`.

---

### 5.4 `VolumeSubpathInitializationFailed`
* **📖 What It Means:** You used `subPath` in `volumeMounts` to mount a single file from a ConfigMap/Secret or Persistent Volume, but the specified key or directory path does not exist.
* 🎭 **Real-World Scenario:**
  ```yaml
  volumeMounts:
  - name: config
    mountPath: /app/config.json
    subPath: configuration.json  # <-- Typo! The ConfigMap key was named config.json
  ```
* 🛠️ **Step-by-Step Fix:**
  - Verify the exact key name inside the ConfigMap: `kubectl get configmap <name> -o yaml`.

---

### 5.5 `ProjectedVolumeError` / `TokenRequestFailed`
* **📖 What It Means:** The Kubelet failed to generate a projected volume containing bound service account tokens or certificate bundles.
* 🛠️ **Step-by-Step Fix:** Verify that the API server TokenRequest feature is enabled and the ServiceAccount is valid.

---

### 5.6 `PostStartHookError` & `PreStopHookFailed`
* **📖 What It Means:**
  - `PostStartHookError`: The lifecycle `postStart` command or HTTP request failed with a non-zero exit code. Kubernetes terminates the container immediately.
  - `PreStopHookFailed`: The lifecycle `preStop` handler hung or timed out during container shutdown.
* 🎭 **Real-World Scenario:** You configure a `postStart` command to warm up cache: `command: ["curl", "http://localhost:8080/warmup"]`. The web server has not finished binding to port 8080 when curl executes, causing curl to exit with code 7 (Connection refused) and killing the container.
* 🛠️ **Step-by-Step Fix:**
  - Ensure `postStart` handlers are resilient, non-blocking, or include retry loops:
    ```yaml
    lifecycle:
      postStart:
        exec:
          command: ["/bin/sh", "-c", "until nc -z localhost 8080; do sleep 1; done"]
    ```

---

## 💾 6. Storage, CSI Driver & Volume Mounting Errors

---

### 6.1 `ContainerCreating` (Stuck on Volume Attachment)
* **📖 What It Means:** The Pod is stuck in `ContainerCreating` state for over 5 minutes because a Persistent Volume cannot be attached to the node or network interface setup is blocked.
* 🎭 **Real-World Scenario:** A worker node was terminated abruptly. Its AWS EBS volume remains registered as "In-Use" on the AWS EC2 management plane. When Kubernetes recreates the Pod on a healthy node, AWS refuses to attach the volume.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  ```
  *Event:* `Warning FailedAttachVolume VolumeAttachment "csi-xxx" is still attached to node "worker-02"`.
* 🛠️ **Step-by-Step Fix:**
  - Wait for the CSI driver attach-detach controller timeout (usually 6-8 minutes).
  - Manually detach the stuck volume in your cloud console if urgent.

---

### 6.2 `FailedMount` & `FailedAttachVolume`
* **📖 What It Means:** 
  - `FailedAttachVolume`: The cloud storage provider failed to connect the virtual disk to the VM instance.
  - `FailedMount`: The disk is attached to the VM, but the OS failed to format or mount the filesystem (e.g. ext4/xfs).
* 🎭 **Real-World Scenario:** Worker node IAM role is missing `ec2:AttachVolume` permission in AWS, or disk filesystem is corrupted.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  kubectl get pv,pvc -A
  ```
* 🛠️ **Step-by-Step Fix:**
  - Verify Cloud IAM policy permissions attached to worker node instances.
  - Check node dmesg logs: `sudo dmesg | grep -i "filesystem"`.

---

### 6.3 `PersistentVolumeClaimNotBound` / `ProvisioningFailed`
* **📖 What It Means:** The Pod requests a PVC, but the PVC status is `Pending` because no matching PersistentVolume exists and dynamic provisioning failed.
* 🎭 **Real-World Scenario:** Your PVC requests `storageClassName: standard-ssd`, but the cluster only defines a StorageClass named `gp3`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get sc
  kubectl describe pvc <pvc-name> -n <namespace>
  ```
* 🛠️ **Step-by-Step Fix:**
  - Align the `storageClassName` in your PVC with the available StorageClasses in `kubectl get sc`.

---

### 6.4 `Multi-Attach error for volume` (ReadWriteOnce Conflict)
* **📖 What It Means:** A PersistentVolume with access mode `ReadWriteOnce` (RWO) is mounted by a Pod on Node 1, and another Pod on Node 2 tries to mount the same volume simultaneously.
* 🎭 **Real-World Scenario:** You trigger a RollingUpdate deployment with `replicas: 1` attached to an RWO volume. The new Pod starts on Node 2 while the old Pod is still terminating on Node 1. Node 2 gets blocked with `Multi-Attach error`.
* 🛠️ **Step-by-Step Fix:**
  - Use `strategy: { type: Recreate }` for Single-Replica Stateful Deployments:
    ```yaml
    spec:
      strategy:
        type: Recreate
    ```
  - Or use `ReadWriteMany` (RWX) storage (e.g. AWS EFS, NFS, CephFS).

---

### 6.5 `VolumeResizeFailed` / `FileSystemResizeFailed`
* **📖 What It Means:** You edited a PVC to increase its capacity from `50Gi` to `100Gi`, but the CSI driver or OS filesystem resize operation (`resize2fs`/`xfs_growfs`) failed.
* 🛠️ **Step-by-Step Fix:** Verify the StorageClass has `allowVolumeExpansion: true`. Restart the consumer Pod to trigger online filesystem expansion.

---

### 6.6 `VolumeConditionAbnormal` (CSI Volume Health Monitoring)
* **📖 What It Means:** The CSI Volume Health Monitor detected physical storage faults (e.g. disk sector corruption, storage array I/O degradation) on the underlying block device.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pvc <pvc-name>
  ```
* 🛠️ **Step-by-Step Fix:** Check the storage backend array status or migrate the workload to a new PVC snapshot.

---

### 6.7 `NFSStaleFileHandle` / `StorageNodeDown`
* **📖 What It Means:** An NFS server or Ceph cluster restarted, causing file handles on worker nodes to become stale. Pod read/write operations block forever in uninterruptible sleep (D-state).
* 🛠️ **Step-by-Step Fix:** Unmount the stale mount on the worker node with `sudo umount -f -l <mountpoint>`.

---

## 🔄 7. Init Containers, Sidecars & Ephemeral Debuggers

---

### 7.1 `Init:0/N` / `Init:1/N` (Stuck Waiting on Dependencies)
* **📖 What It Means:** The Pod is waiting for Init Container #1 or #2 to complete before it can start the application container.
* 🎭 **Real-World Scenario:** You have an init container running:
  ```bash
  until nc -z postgres-service 5432; do echo waiting for db; sleep 2; done
  ```
  The database is down, so the Init Container loops forever and stays in `Init:0/1`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl logs <pod-name> -c <init-container-name>
  ```
* 🛠️ **Step-by-Step Fix:**
  - Identify which dependency is failing by inspecting the init container logs.
  - Verify network connectivity to the target service.

---

### 7.2 `Init:CrashLoopBackOff` & `Init:Error`
* **📖 What It Means:** The Init container crashed with a non-zero exit code (e.g. exit code 1). Main app containers will **never** start.
* 🎭 **Real-World Scenario:** An Init Container runs database schema migrations (`flyway migrate` or `prisma migrate deploy`). The database credentials in the secret are wrong, causing the migration to abort with an authentication error.
* 🔍 **How to Inspect:**
  ```bash
  kubectl logs <pod-name> -c <init-container-name> --previous
  ```
* 🛠️ **Step-by-Step Fix:**
  - Fix the database migration SQL script or credentials.

---

### 7.3 `Init:ImagePullBackOff`
* **📖 What It Means:** The Init Container's image cannot be downloaded from the container registry.
* 🛠️ **Step-by-Step Fix:** Refer to Section 3.1 to resolve registry access or image tag typos.

---

### 7.4 `Init:CreateContainerConfigError` & `Init:CreateContainerError`
* **📖 What It Means:** The Init Container references a ConfigMap, Secret, or volume mount path that is missing or conflicting.
* 🛠️ **Step-by-Step Fix:** Verify all ConfigMaps and Secrets referenced by the Init Container exist in the namespace.

---

### 7.5 `Init:OOMKilled` (Exit Code 137 in Init Phase)
* **📖 What It Means:** An Init Container performing data decompression, asset compilation, or DB migrations exceeded its memory limit and was killed by the kernel.
* 🛠️ **Step-by-Step Fix:** Increase `resources.limits.memory` specifically on the `initContainers` block.

---

### 7.6 `ServiceMeshSidecarNotReady` (Istio / Linkerd Startup Delay)
* **📖 What It Means:** In clusters with Service Mesh auto-injection (Istio / Linkerd / Consul), the main app container starts and attempts outbound network requests before the Envoy sidecar proxy is fully initialized, causing immediate network failures.
* 🎭 **Real-World Scenario:** A Spring Boot app boots up, tries to connect to PostgreSQL immediately on startup, and fails with `Connection refused` because Istio Envoy proxy (`istio-proxy`) is still booting.
* 🛠️ **Step-by-Step Fix:**
  - In Kubernetes 1.28+, use native sidecar containers (`restartPolicy: Always` in `initContainers`).
  - In Istio: Enable `holdApplicationUntilProxyStarts: true` in Istio mesh config.

---

### 7.7 `EphemeralContainersFailed` / `EphemeralContainerNotReady`
* **📖 What It Means:** An interactive debugging container launched via `kubectl debug` failed to attach or crashed.
* 🛠️ **Step-by-Step Fix:** Check the image name used in `kubectl debug` (e.g. `nicolaka/netshoot` or `busybox`).

---

## 💥 8. Runtime Crashes, Memory & Resource Starvation

---

### 8.1 `Error` (Non-Zero Exit Code)
* **📖 What It Means:** The container started, executed application code, and exited with an error status (e.g. exit code 1).
* 🎭 **Real-World Scenario:** A Node.js backend starts up, attempts to read `process.env.JWT_SECRET`, finds it undefined, and throws an unhandled exception: `TypeError: Cannot read property 'length' of undefined`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl logs <pod-name> --previous
  ```
* 🛠️ **Step-by-Step Fix:**
  - Check the stack trace in logs and supply the missing environment variable or configuration.

---

### 8.2 `CrashLoopBackOff`
* **📖 What It Means:** The container keeps crashing immediately after startup. Kubernetes applies exponential backoff delay (10s → 20s → 40s → 80s → up to 300s) to avoid thrashing the CPU.
* 🎭 **Real-World Scenario:** A Python service connects to Redis on startup. If Redis is unreachable, the Python code calls `sys.exit(1)`. Every time Kubernetes restarts the pod, it crashes again.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  # Check "Last State" -> Reason and Exit Code
  kubectl logs <pod-name> --previous
  ```
* 🛠️ **Step-by-Step Fix:**
  - Determine whether the crash is application code (Exit 1), out of memory (Exit 137), or binary not found (Exit 127).

---

### 8.3 `OOMKilled` (Exit Code 137 - Out of Memory)
* **📖 What It Means:** The container exceeded its `resources.limits.memory` threshold, and the Linux kernel Out-Of-Memory Killer immediately sent a `SIGKILL` (Signal 9) to protect host stability.
* 🎭 **Real-World Scenario:** A PDF generation service receives a 100-page document. Memory usage spikes from 200 MB to 1.2 GB. The container memory limit was set to `512Mi`. The Linux kernel kills the container instantly.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  ```
  *Output:*
  ```text
  State:          Terminated
    Reason:       OOMKilled
    Exit Code:    137
  ```
* 🛠️ **Step-by-Step Fix:**
  1. Increase `resources.limits.memory` in your deployment YAML:
     ```yaml
     resources:
       requests:
         memory: "512Mi"
       limits:
         memory: "2Gi"
     ```
  2. For Java applications: Configure JVM heap options (`-XX:MaxRAMPercentage=75.0` or `-Xmx1500m`).
  3. Inspect memory leaks using heap profilers.

---

### 8.4 `Completed` (Exit Code 0 on Backgrounded Server)
* **📖 What It Means:** The container process finished and exited cleanly with status code `0`.
* 🎭 **Real-World Scenario:** You containerized Nginx or an Express app, but the entrypoint command was `systemctl start nginx` or `node index.js &` (background daemon). Because the backgrounded process detaches, the main foreground PID 1 process terminates immediately with code 0.
* 🛠️ **Step-by-Step Fix:**
  - Ensure the main container process runs in the **foreground** as PID 1:
    - Nginx: `CMD ["nginx", "-g", "daemon off;"]`
    - Node.js: `CMD ["node", "index.js"]`

---

### 8.5 `ContainerStatusUnknown`
* **📖 What It Means:** Kubernetes is completely in the dark about the container. The Kubelet on the worker node or the Kubernetes API server cannot determine whether the container is currently `Waiting`, `Running`, or `Terminated`. The container runtime (`containerd` / `CRI-O`) failed to answer status queries.
* 🎭 **Real-World Scenario:** A high-throughput database worker node suffers a host-level Out-Of-Memory event on the OS layer. The Linux kernel's host OOM killer terminates the `containerd` daemon process itself to free memory. Because `containerd` is dead, the node `kubelet` cannot query container status via the `/run/containerd/containerd.sock` UNIX domain socket. When you run `kubectl get pods`, the pod status displays `ContainerStatusUnknown`.
* 🔬 **Deep Root Causes:**
  - Container runtime (`containerd`, `CRI-O`) crashed, froze, or was killed by the host OS kernel.
  - The Kubelet's gRPC connection to the CRI socket (`/run/containerd/containerd.sock`) timed out due to extreme host CPU throttling or 100% disk I/O lockup.
  - The worker node's root filesystem `/var/lib/containerd` or `/var/lib/kubelet` became corrupted or entered a read-only state.
  - An abrupt host VM reset occurred while containers were in the middle of state transitions.
* 🔍 **How to Inspect & Diagnose:**
  ```bash
  # 1. Check Pod status and which worker node it resides on
  kubectl get pod <pod-name> -o wide

  # 2. Check the condition of the host node
  kubectl describe node <node-name>

  # 3. SSH into the worker node and inspect CRI runtime & Kubelet health
  sudo systemctl status containerd
  sudo systemctl status kubelet

  # 4. Check if the container runtime responds to CLI queries
  sudo crictl ps -a
  sudo crictl info

  # 5. Check Kubelet and Containerd system logs for gRPC socket errors
  sudo journalctl -u containerd -n 50 --no-pager
  sudo journalctl -u kubelet -n 50 --no-pager
  ```
* 🛠️ **Step-by-Step Fix:**
  1. **Restart Container Runtime on the Node:**
     ```bash
     sudo systemctl restart containerd
     sudo systemctl restart kubelet
     ```
  2. **If the Node is permanently unresponsive / dead:** Force delete the stuck pod so the Kubernetes controller can recreate it on a healthy worker node:
     ```bash
     kubectl delete pod <pod-name> -n <namespace> --grace-period=0 --force
     ```
  3. **Prevent Host OOM on daemons:** Configure `system-reserved` and `kube-reserved` memory in `/var/lib/kubelet/config.yaml` so the Linux kernel never kills `containerd` or `kubelet`:
     ```yaml
     systemReserved:
       cpu: "500m"
       memory: "1Gi"
     kubeReserved:
       cpu: "500m"
       memory: "1Gi"
     ```

---

### 8.6 `DeadlineExceeded` (`activeDeadlineSeconds` Reached)
* **📖 What It Means:** The Pod was terminated because its total execution time exceeded the specified `activeDeadlineSeconds` configuration.
* 🎭 **Real-World Scenario:** You configured a data processing Job with `activeDeadlineSeconds: 600` (10 minutes). A network bottleneck causes the job to take 12 minutes. At the 10-minute mark, Kubernetes terminates the Pod with `DeadlineExceeded`.
* 🛠️ **Step-by-Step Fix:** Increase `activeDeadlineSeconds` in your Pod/Job specification or optimize the workload throughput.

---

### 8.7 `BackoffLimitExceeded` (Batch Job Failure)
* **📖 What It Means:** A Kubernetes `Job` has reached its maximum restart retry count (`spec.backoffLimit`, default 6) and has been permanently marked as failed.
* 🛠️ **Step-by-Step Fix:** Inspect the Job pod failure logs: `kubectl logs job/<job-name>`.

---

### 8.8 `JobSuspended` (`spec.suspend: true`)
* **📖 What It Means:** The Kubernetes Job has been placed on hold. No pods will be created until `spec.suspend` is set back to `false`.
* 🛠️ **Step-by-Step Fix:** Resume the job with `kubectl patch job <job-name> -p '{"spec":{"suspend":false}}'`.

---

## 🔧 9. In-Place Pod Resizing & Dynamic Resource Allocation (K8s 1.27+)

---

### 9.1 In-Place Resize States: `Proposed`, `InProgress`, `Deferred`, `Infeasible`
* **📖 What It Means:** In Kubernetes 1.27+, you can resize CPU/Memory of a live running container without restarting the Pod. The `status.resize` field indicates the state:
  - **`Proposed`**: The resize request was acknowledged by the API server.
  - **`InProgress`**: The Kubelet is currently adjusting cgroups v2 resource limits on the node.
  - **`Deferred`**: The requested CPU/RAM is currently unavailable on this node; Kubelet will apply it as soon as other pods free capacity.
  - **`Infeasible`**: The node cannot physically satisfy the resize request (e.g. asking for 64 GB on a 32 GB node).
* 🔍 **How to Inspect:**
  ```bash
  kubectl get pod <pod-name> -o jsonpath='{.status.containerStatuses[*].allocatedResources}'
  ```
* 🛠️ **Step-by-Step Fix:** If `Infeasible`, adjust requests or recreate the Pod to allow scheduling on a larger worker node.

---

### 9.2 `DynamicResourceAllocationPending` (DRA Hardware Claims)
* **📖 What It Means:** The Pod references a Dynamic Resource Allocation (DRA) `ResourceClaim` (e.g. customized FPGA, specialized TPU, or dynamic GPU slices), but the DRA driver has not yet bound the resource.
* 🛠️ **Step-by-Step Fix:** Check the health of your DRA driver daemonset and verify `ResourceClaimParameters` CRDs.

---

## 🏥 10. Health Checks (Probes) & Custom Readiness Gates

---

### 10.1 `Unhealthy` (Liveness Probe Failed)
* **📖 What It Means:** The Liveness Probe failed consecutive checks equal to `failureThreshold`. Kubernetes assumes the process is deadlocked or permanently broken and **kills & restarts the container**.
* 🎭 **Real-World Scenario:** A backend API experiences a database thread pool exhaustion. The `/healthz` endpoint blocks trying to acquire a DB connection and times out after 3 seconds. Kubernetes restarts the container repeatedly.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  # Warning  Unhealthy  Liveness probe failed: HTTP probe failed with statuscode: 500
  ```
* 🛠️ **Step-by-Step Fix:**
  - Keep Liveness Probes lightweight (check local server status, avoid deep external DB queries).
  - Tune probe thresholds:
    ```yaml
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 30
      timeoutSeconds: 5
      failureThreshold: 3
    ```

---

### 10.2 `ReadinessProbeFailed` (Traffic Cut Off)
* **📖 What It Means:** The Readiness Probe failed. The container is **NOT restarted**, but Kubernetes immediately removes the Pod IP from the Service endpoints load balancer so users receive no errors.
* 🎭 **Real-World Scenario:** An e-commerce API is warming up local caches. During this 20-second warmup, the `/ready` probe returns HTTP 503. Kubernetes routes user traffic only to existing healthy replicas.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe pod <pod-name>
  kubectl get endpoints <service-name>
  ```
* 🛠️ **Step-by-Step Fix:**
  - Check why `/ready` is returning non-200 in app logs.
  - Increase `initialDelaySeconds` to give the app adequate time to boot.

---

### 10.3 `StartupProbeFailed` (Slow Boot Timeout)
* **📖 What It Means:** The container failed to respond successfully to the Startup Probe within the total allotted startup window. Kubernetes restarts the container.
* 🎭 **Real-World Scenario:** A large Java Spring Boot monolithic app takes 110 seconds to start on cold boot. The liveness probe starts checking after 30 seconds and kills the app before it ever finishes booting.
* 🛠️ **Step-by-Step Fix:**
  - Add a dedicated `startupProbe` to disable liveness checks during boot:
    ```yaml
    startupProbe:
      httpGet:
        path: /healthz
        port: 8080
      failureThreshold: 30
      periodSeconds: 10   # Gives up to 30 * 10s = 300 seconds (5 minutes) to start
    ```

---

### 10.4 `ExecProbeTimeout` / `ProbeWarning`
* **📖 What It Means:** A probe using `exec: command: [...]` hung inside the container because the command process became deadlocked or took longer than `timeoutSeconds`.
* 🛠️ **Step-by-Step Fix:** Optimize the probe script or increase `timeoutSeconds`.

---

### 10.5 `ReadinessGatesFailed` / `ReadinessGatesNotReady` (Cloud Load Balancers)
* **📖 What It Means:** The Pod's containers are ready, but an external cloud controller (e.g. AWS Load Balancer Controller registering target into ALB Target Group) has not marked the custom readiness gate as `True`.
* 🎭 **Real-World Scenario:** AWS ALB Target Group health check takes 45 seconds to verify target health. Rolling updates pause until AWS ALB responds with HTTP 200.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get pod <pod-name> -o jsonpath='{.status.conditions}'
  ```
* 🛠️ **Step-by-Step Fix:** Verify AWS Target Group health check path and security group firewall rules.

---

## 🌐 11. DNS, CoreDNS & Cluster Networking Failures

---

### 11.1 `CoreDNS CrashLoopBackOff` (Forwarding Loop Detected)
* **📖 What It Means:** The cluster DNS resolver (`coredns`) is crashing, causing all pods to fail when resolving domain names or service names (`mysql-service.default.svc.cluster.local`).
* 🎭 **Real-World Scenario:** The host node `/etc/resolv.conf` contains an upstream nameserver `127.0.0.53` (systemd-resolved loop). CoreDNS inherits this and detects a forwarding loop, crashing immediately to prevent infinite DNS loops.
* 🔍 **How to Inspect:**
  ```bash
  kubectl logs -n kube-system -l k8s-app=kube-dns
  # Look for: "Loop ... detected in plugin/loop"
  ```
* 🛠️ **Step-by-Step Fix:**
  - Fix host `/etc/resolv.conf` nameservers to point directly to upstream DNS (e.g. `8.8.8.8` or internal gateway).

---

### 11.2 `NameResolutionFailure` (`ndots:5` Latency & NXDOMAIN)
* **📖 What It Means:** Container application fails with `getaddrinfo EAI_AGAIN` or DNS timeouts when resolving external domains like `api.stripe.com`.
* 🎭 **Real-World Scenario:** Default Kubernetes DNS config specifies `ndots:5`. Resolving `api.stripe.com` generates 5 recursive queries (`api.stripe.com.default.svc.cluster.local`, etc.), flooding CoreDNS and causing random timeouts.
* 🛠️ **Step-by-Step Fix:**
  - Append a trailing dot to external domains: `api.stripe.com.`
  - Or configure NodeLocal DNSCache in the cluster.

---

### 11.3 `ServiceEndpointsMissing` (No Pods Match Selector)
* **📖 What It Means:** Traffic to a Kubernetes Service fails with connection timeouts because `kubectl get endpoints <service>` is `<none>`.
* 🎭 **Real-World Scenario:** Your Service has `selector: { app: api }`, but your Deployment Pod template has `labels: { app: api-service }`. The selector fails to match any pods.
* 🛠️ **Step-by-Step Fix:** Align the Service `spec.selector` labels with the Pod `metadata.labels`.

---

## 🚪 12. Node Pressure, Eviction, Preemption & Teardown Errors

---

### 12.1 `Evicted` (`DiskPressure`, `MemoryPressure`, `PIDPressure`)
* **📖 What It Means:** The `kubelet` forcefully evicted the Pod from the worker node to prevent the host machine from freezing or crashing.
* 🎭 **Real-World Scenario:** An unrotated application log file filled the node root filesystem to 96%. The kubelet triggers `DiskPressure` and begins evicting `BestEffort` pods.
* 🔬 **Root Causes:**
  - `DiskPressure`: Host filesystem usage > 85%.
  - `MemoryPressure`: Host allocatable RAM exhausted.
  - `PIDPressure`: Linux process table full (too many threads/processes).
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe node <node-name>
  kubectl describe pod <pod-name> | grep -i reason
  ```
* 🛠️ **Step-by-Step Fix:**
  1. Clean unused images: `crictl rmi --prune`.
  2. Set `ephemeral-storage` requests and limits in Pod YAML:
     ```yaml
     resources:
       limits:
         ephemeral-storage: "2Gi"
     ```
  3. Clean up evicted pod records:
     ```bash
     kubectl get pods -A | grep Evicted | awk '{print $2 " -n " $1}' | xargs -r kubectl delete pod
     ```

---

### 12.2 `Evicted` (`DisruptionTarget`: `EvictionByEvictionAPI`, `TerminationByKubelet`, `DeletionByPodGC`)
* **📖 What It Means:** In Kubernetes 1.25+, when a Pod is targeted for termination due to cluster lifecycle events, the `DisruptionTarget` condition records the exact sub-reason:
  - **`EvictionByEvictionAPI`**: Triggered via `kubectl drain` or cluster autoscaler node scale-down.
  - **`PreemptionByKubeScheduler`**: Preempted to make room for a higher-priority pod.
  - **`TerminationByKubelet`**: Kubelet evicted the pod due to node resource pressure or graceful shutdown.
  - **`DeletionByPodGC`**: Orphaned or terminating pod cleaned up by Pod Garbage Collector.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get pod <pod-name> -o jsonpath='{.status.conditions[?(@.type=="DisruptionTarget")]}'
  ```

---

### 12.3 `TaintManagerEviction` (`NoExecute` Taint Applied)
* **📖 What It Means:** A `NoExecute` taint was added to the node, and the Pod's `tolerationSeconds` timer expired, causing the TaintManager controller to evict the Pod.
* 🛠️ **Step-by-Step Fix:** Add toleration with higher `tolerationSeconds` if the pod must survive brief network partitions:
  ```yaml
  tolerations:
  - key: "node.kubernetes.io/unreachable"
    operator: "Exists"
    effect: "NoExecute"
    tolerationSeconds: 300
  ```

---

### 12.4 `TerminatedDueToNodeShutdown` (Graceful Node Shutdown)
* **📖 What It Means:** The host operating system received a shutdown/reboot signal (`systemd-inhibit`), and Kubelet terminated pods gracefully according to priority.
* 🛠️ **Step-by-Step Fix:** Normal during server reboots. Workloads will automatically reschedule on other active nodes.

---

### 12.5 `Preempted` / `Preempting` (`PriorityClass`)
* **📖 What It Means:** A high-priority Pod needed node resources, and Kubernetes evicted your lower-priority Pod to make room.
* 🎭 **Real-World Scenario:** A critical payment-gateway pod with `priorityClassName: high-priority` scales up during peak traffic. Low-priority batch worker pods are preempted.
* 🛠️ **Step-by-Step Fix:**
  - Assign explicit `PriorityClass` objects to production workloads:
    ```yaml
    apiVersion: scheduling.k8s.io/v1
    kind: PriorityClass
    metadata:
      name: high-priority
    value: 1000000
    globalDefault: false
    ```

---

### 12.6 `Terminating` (Stuck on Finalizers or Storage Unmount)
* **📖 What It Means:** The Pod received a delete command, but cannot finish terminating because a **Finalizer** is waiting on an external controller, or the storage CSI driver cannot unmount the volume.
* 🎭 **Real-World Scenario:** A storage driver crashed while unmounting an NFS volume. The Linux kernel process is stuck in Uninterruptible Sleep (D-state) waiting for I/O.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get pod <pod-name> -n <namespace> -o yaml | grep -A 5 "finalizers:"
  ```
* 🛠️ **Step-by-Step Fix:**
  1. Remove blocking finalizers:
     ```bash
     kubectl patch pod <pod-name> -n <namespace> -p '{"metadata":{"finalizers":null}}'
     ```
  2. Force deletion if the underlying container is already dead:
     ```bash
     kubectl delete pod <pod-name> -n <namespace> --grace-period=0 --force
     ```

---

### 12.7 `GracefulTerminationTimeout` (App Ignored SIGTERM)
* **📖 What It Means:** The container was sent a `SIGTERM` signal during shutdown, but failed to exit before `terminationGracePeriodSeconds` (default 30s) elapsed. The Kubelet sent a forceful `SIGKILL` (Exit 137).
* 🛠️ **Step-by-Step Fix:**
  - Add `SIGTERM` signal listeners in application code to close active connections promptly:
    ```javascript
    process.on('SIGTERM', () => {
      server.close(() => process.exit(0));
    });
    ```
  - Or increase `terminationGracePeriodSeconds: 60` in the Pod spec.

---

### 12.8 `Unknown` / `NodeLost` / `NodeNotReady`
* **📖 What It Means:** The Kubernetes control plane has lost contact with the worker node's `kubelet` for more than `node-monitor-grace-period` (default 40s).
* 🎭 **Real-World Scenario:** A physical server lost power, a network switch failed, or the cloud provider abruptly stopped a spot instance.
* 🛠️ **Step-by-Step Fix:**
  - Check physical node / VM status in cloud console.
  - Restart kubelet on the node: `sudo systemctl restart kubelet`.

---

## 🔄 13. Workload Controller Deadlocks & Autoscaler Failures

---

### 13.1 `ProgressDeadlineExceeded` (Deployment Rollout Stuck)
* **📖 What It Means:** A Deployment rollout failed to make progress within `progressDeadlineSeconds` (default 600 seconds / 10 minutes) because new pods are crashing in `CrashLoopBackOff` or stuck in `Pending`.
* 🎭 **Real-World Scenario:** You deploy a bad container image with a critical syntax error. The ReplicaSet launches 2 new pods that immediately crash. The old pods continue running, and after 10 minutes the Deployment status is marked `ProgressDeadlineExceeded`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl rollout status deployment/<deployment-name>
  ```
* 🛠️ **Step-by-Step Fix:**
  - Roll back to previous healthy revision:
    ```bash
    kubectl rollout undo deployment/<deployment-name>
    ```

---

### 13.2 `StatefulSetOrdinalDeadlock` (StatefulSet Pod Order Blocked)
* **📖 What It Means:** By default, StatefulSets use `podManagementPolicy: OrderedReady`, meaning Pod `app-1` will **NEVER** be created or started until Pod `app-0` is fully `Running` and `Ready`. If Pod 0 fails, the entire StatefulSet halts.
* 🎭 **Real-World Scenario:** In a 3-node MongoDB cluster (`mongo-0`, `mongo-1`, `mongo-2`), `mongo-0` fails to mount its PVC. `mongo-1` and `mongo-2` are never created.
* 🛠️ **Step-by-Step Fix:**
  - Fix the underlying issue on Pod 0 (`kubectl describe pod mongo-0`).
  - Or switch to parallel creation: `podManagementPolicy: Parallel`.

---

### 13.3 `PodDisruptionBudgetViolation` (`Cannot evict pod: PDB violated`)
* **📖 What It Means:** A node drain (`kubectl drain`) or cluster autoscaler scale-down is rejected by the API server because evicting the Pod would violate its `PodDisruptionBudget` (`minAvailable` / `maxUnavailable`).
* 🎭 **Real-World Scenario:** You have 2 replicas of Elasticsearch with a PDB requiring `minAvailable: 2`. One replica crashed, leaving only 1 available. When you try to drain the node hosting the healthy replica, `kubectl drain` fails with `Cannot evict pod as it would violate the pod's disruption budget`.
* 🛠️ **Step-by-Step Fix:**
  - Bring other replicas up to health before draining.
  - Or temporarily delete the blocking PDB: `kubectl delete pdb <pdb-name>`.

---

### 13.4 `HPAUnableToComputeMetrics` / `ScalingLimited`
* **📖 What It Means:** The Horizontal Pod Autoscaler (HPA) cannot calculate target CPU/Memory metrics and refuses to scale up or down because `metrics-server` is down or containers lack `resources.requests`.
* 🎭 **Real-World Scenario:** You create an HPA targeting `50%` CPU utilization, but in your Deployment YAML you did not define `resources.requests.cpu`. The HPA displays `TARGETS: <unknown>/50%` and never scales.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe hpa <hpa-name>
  ```
* 🛠️ **Step-by-Step Fix:** Always configure `resources.requests.cpu` and `resources.requests.memory` on all containers managed by an HPA.

---

### 13.5 `KEDATriggerError` (External Event Source Down)
* **📖 What It Means:** KEDA (Kubernetes Event-driven Autoscaling) failed to query an external scaler (e.g. RabbitMQ queue depth, Kafka topic lag, AWS SQS) due to authentication or network timeouts.
* 🛠️ **Step-by-Step Fix:** Inspect KEDA ScaledObject: `kubectl describe scaledobject <name>`.

---

### 13.6 `ClusterAutoscalerScaleUpFailed` (Cloud Quota Reached)
* **📖 What It Means:** The Cluster Autoscaler tried to add new cloud VM worker nodes to accommodate `Pending` pods, but the cloud provider (AWS/GCP/Azure) rejected the request due to cloud account vCPU quota limits or instance type unavailability.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get configmap cluster-autoscaler-status -n kube-system -o yaml
  ```
* 🛠️ **Step-by-Step Fix:** Request a service quota increase for vCPUs in your cloud provider management console.

---

## 🛡️ 14. Specialized Workload & Infrastructure Failures

---

### 14.1 `CronJobMissedSchedule` / `CannotDetermineTimeZone`
* **📖 What It Means:** A CronJob failed to trigger at its scheduled time because the previous job execution took too long (with `concurrencyPolicy: Forbid` or `Replace`), or timezone parsing failed.
* 🎭 **Real-World Scenario:** A report generation CronJob runs every hour with `concurrencyPolicy: Forbid`. A heavy query causes the 2:00 AM job to run for 80 minutes. The 3:00 AM job execution is skipped and marked `MissedSchedule`.
* 🛠️ **Step-by-Step Fix:** Set `startingDeadlineSeconds: 300` on the CronJob spec or optimize the underlying script.

---

### 14.2 `DaemonSetRolloutBlocked` (Node Taints / Master Node Exclusion)
* **📖 What It Means:** A DaemonSet (e.g. FluentBit, Datadog, Calico node agent) is not running on new worker nodes or control-plane nodes because it lacks the necessary node tolerations.
* 🛠️ **Step-by-Step Fix:** Add tolerations for `node-role.kubernetes.io/control-plane:NoSchedule` and `CriticalAddonsOnly`.

---

### 14.3 `GatewayAPI / Ingress 502 / 504 Bad Gateway`
* **📖 What It Means:** The Ingress controller (Nginx, Traefik) or Gateway API HTTPRoute accepted the external request, but the target Pod IP timed out (`504`) or immediately refused connection (`502`).
* 🎭 **Real-World Scenario:** An Nginx Ingress route targets an Express app on port 3000. In the Kubernetes Service YAML, `targetPort` was set to 8080 by mistake. Ingress returns `502 Bad Gateway`.
* 🛠️ **Step-by-Step Fix:** Verify that the Service `spec.ports[*].targetPort` matches the container's actual listening port.

---

### 14.4 `x509: CertificateExpired` / `Certificate Signed by Unknown Authority`
* **📖 What It Means:** The TLS certificates used by Kubelet, API Server admission webhooks, or Cert-Manager expired or have an invalid CA root certificate bundle.
* 🎭 **Real-World Scenario:** After 1 year of cluster operation, the Kubeadm client certificate `/etc/kubernetes/pki/apiserver.crt` expires. `kubectl` commands and pod creations fail with `x509: certificate has expired`.
* 🛠️ **Step-by-Step Fix:** Renew Kubeadm certs: `sudo kubeadm certs renew all` and restart control-plane static pods.

---

### 14.5 `IPv6DualStackAllocationFailed`
* **📖 What It Means:** In an IPv4/IPv6 Dual-Stack cluster, the CNI failed to allocate both IPv4 and IPv6 IP addresses to the Pod sandbox.
* 🛠️ **Step-by-Step Fix:** Verify node dual-stack CIDR assignment with `kubectl get node <name> -o yaml | grep -A 4 podCIDRs`.

---

### 14.6 `WindowsNodeContainerFailed` (HNS Network & Isolation Mismatch)
* **📖 What It Means:** On Windows Server worker nodes, the Windows Host Networking Service (HNS) failed to initialize, or the container OS build version does not match the host Windows OS build version.
* 🎭 **Real-World Scenario:** You deploy a Windows container built with Windows Server 2022 image (`ltsc2022`) onto a node running Windows Server 2019 (`ltsc2019`). Windows process isolation fails.
* 🛠️ **Step-by-Step Fix:** Match Windows container base image OS version with the host node OS build or use Hyper-V isolation (`isolation: hyperv`).

---

## 🔢 15. Master Container Exit Codes & OS Signals Table

When inspecting `kubectl describe pod <name>`, inspect the **Last State** section to find the **Exit Code**.

> 💡 **The Standard Linux Signal Formula:**  
> $$\text{Exit Code} = 128 + \text{Signal Number}$$

| Exit Code | Signal / Name | Real Meaning & Root Cause | Real-World Scenario & Exact Fix |
| :---: | :---: | :--- | :--- |
| **`0`** | `SUCCESS` | Process completed all tasks and exited cleanly. | Normal for Jobs/CronJobs. If a web server exits with 0, ensure it runs in the foreground (`daemon off;`). |
| **`1`** | `GENERAL ERROR` | Unhandled application error, exception, or missing config. | Python `KeyError`, Java `NullPointerException`, Node.js unhandled promise rejection. Inspect `kubectl logs --previous`. |
| **`2`** | `MISUSE OF SHELL` | Invalid argument or syntax error in Pod YAML `command:` or `args:`. | Missing quotes, illegal flags in bash command string (e.g. `sh -c "echo hello`). |
| **`126`** | `CANNOT EXECUTE` | Target binary/script was found, but lacks executable permissions (`chmod +x`). | Entrypoint script `/entrypoint.sh` lacks execution bit. Fix in Dockerfile: `RUN chmod +x /entrypoint.sh`. |
| **`127`** | `COMMAND NOT FOUND` | Executable binary specified in command/entrypoint does not exist in image. | Specifying `command: ["bash"]` in an alpine or scratch image that only provides `/bin/sh`. |
| **`128`** | `INVALID EXIT CODE` | Application called `exit(n)` with an invalid exit argument outside `0-255`. | Bug inside custom exit handling code. |
| **`130`** | `SIGINT (128+2)` | Process terminated by interrupt signal (equivalent to `Ctrl + C`). | Container received an interrupt signal from manual debug session. |
| **`134`** | `SIGABRT (128+6)` | Process aborted itself via `abort()` call. | Fatal assertion failure in C/C++/Go runtime or JVM crash. Check crash dump logs. |
| **`137`** | `SIGKILL (128+9)` | Container forcefully terminated by Linux kernel or Kubelet (`kill -9`). | **1.** `OOMKilled` (exceeded memory limit). Increase `limits.memory`.<br>**2.** Container ignored `SIGTERM` past `terminationGracePeriodSeconds` (default 30s) and was hard-killed. |
| **`139`** | `SIGSEGV (128+11)` | Segmentation Fault (Attempted to access invalid memory address). | Memory corruption or incompatible native C-bindings (e.g. `node-gyp`, `scipy`, `grpc`). |
| **`143`** | `SIGTERM (128+15)` | Container received graceful termination signal from Kubernetes. | Normal during deployments or scaling down. Ensure application handles `SIGTERM` to close database connections gracefully. |
| **`255`** | `EXIT OUT OF RANGE` | Entrypoint script failed with an unhandled exit code outside standard range. | Inspect container entrypoint script for uncaught script errors. |

---

## 🗺️ 16. The Ultimate 60-Second Diagnostic Decision Tree

```
                                [ Pod Is Not Healthy ]
                                          │
                                          ▼
                         Run: `kubectl describe pod <name>`
                                          │
        ┌─────────────────────────────────┼─────────────────────────────────┐
        ▼                                 ▼                                 ▼
   [ SCHEDULE & ADMISSION ]         [ IMAGE & CRI ERROR ]            [ RUNTIME ERROR ]
   • Pending                        • ErrImagePull                   • CrashLoopBackOff
   • FailedScheduling               • ImagePullBackOff               • OOMKilled (137)
   • SchedulingGated                • InvalidImageName               • Error (1)
   • ResourceQuotaExceeded          • exec format error (Arch)       • Completed (0)
        │                                 │                                 │
   ├─► Check CPU/RAM requests       ├─► Check tag spelling           ├─► Run `kubectl logs --previous`
   ├─► Check Node Taints & Labels   ├─► Check imagePullSecrets       ├─► Exit 137? -> Increase RAM
   └─► Check ResourceQuota / Limits └─► Multi-arch build (amd64)     └─► Exit 1? -> Fix app bug
                                                                     
        ┌─────────────────────────────────┼─────────────────────────────────┐
        ▼                                 ▼                                 ▼
   [ STORAGE & CSI ERROR ]          [ PROBE & NETWORK ]              [ TEARDOWN ERROR ]
   • ContainerCreating (Stuck)      • Unhealthy (Liveness)           • Terminating (Stuck)
   • FailedMount                    • ReadinessProbeFailed           • Evicted (DiskPressure)
   • Multi-Attach error             • CoreDNS CrashLoopBackOff       • NodeLost / Unknown
        │                                 │                                 │
   ├─► Check PVC/PV binding         ├─► Increase timeout/delays      ├─► Remove finalizers
   ├─► Check AZ match (us-east-1a)  ├─► Check app CPU deadlock       ├─► Prune node images
   └─► Check CSI driver logs        └─► Fix CoreDNS resolv.conf      └─► Restart kubelet
```

---

## 📋 17. Master Quick Reference Action Matrix

| When Pod Status Displays... | Immediate Primary Suspect | Exact Diagnostic & Recovery Command |
| :--- | :--- | :--- |
| **`Pending`** | CPU/RAM exhaustion, Taints, unbound PVC | `kubectl describe pod <pod>` |
| **`SchedulingGated`** | External autoscaler (Karpenter/DRA) gating | `kubectl get pod <pod> -o yaml \| grep schedulingGates` |
| **`Forbidden: exceeded quota`** | Namespace ResourceQuota full | `kubectl describe resourcequota -n <ns>` |
| **`CreatePodSandboxError`** | CNI plugin error or IP exhaustion | `kubectl logs -n kube-system -l k8s-app=aws-node` |
| **`ImagePullBackOff`** | Image tag typo, missing Secret, or Rate Limit | `kubectl get secrets` & verify repository |
| **`exec format error`** | CPU architecture mismatch (ARM64 vs AMD64) | Rebuild image with `--platform linux/amd64` |
| **`CreateContainerConfigError`** | Referenced ConfigMap or Secret missing | `kubectl get configmap,secret -n <ns>` |
| **`CrashLoopBackOff`** | Application crash on boot | `kubectl logs <pod> --previous` |
| **`OOMKilled` (Exit 137)** | Container exceeded memory limit | Increase `resources.limits.memory` |
| **`ContainerStatusUnknown`** | Host OOM on containerd / socket hang | `sudo systemctl restart containerd kubelet` |
| **`Unhealthy` (Liveness)** | App deadlock or probe timeout | Increase `initialDelaySeconds` & timeout |
| **`ReadinessProbeFailed`** | Backend dependencies down (DB/Redis) | Check downstream service connectivity |
| **`ContainerCreating` (Stuck)** | Cloud volume attachment lock | Check PVC status & cloud disk console |
| **`Terminating` (Stuck)** | Blocking Finalizers or unmount hang | `kubectl patch pod <pod> -p '{"metadata":{"finalizers":null}}'` |
| **`Evicted` (DiskPressure)** | Worker node disk usage > 85% | `kubectl describe node <node>` & prune images |
| **`CoreDNS CrashLoopBackOff`** | DNS forwarding loop in `/etc/resolv.conf` | Fix nameservers in host `/etc/resolv.conf` |
| **`ProgressDeadlineExceeded`** | Deployment rollout stuck past 10 mins | `kubectl rollout undo deployment/<name>` |
| **`PDB Violation`** | Eviction blocked by PodDisruptionBudget | Check healthy replicas before draining |
| **`x509: CertificateExpired`** | Kubeadm or Webhook TLS expired | `sudo kubeadm certs renew all` |

---
*Created with ❤️ for Kubernetes developers, SREs, platform engineers, and DevOps professionals.*
