# 🚀 The Master Kubernetes Troubleshooting Encyclopedia & Deep-Dive Guide

> **The Definitive, 360° Zero-to-Hero Kubernetes Troubleshooting Manual (v1.20 – v1.31+)**  
> Every single Kubernetes Pod Phase, Container State, Scheduler Constraint, Admission Webhook, CNI/DNS Network Glitch, CSI Storage Error, Health Probe Failure, Linux Kernel Signal, and Controller Rollout Deadlock explained with **Beginner Analogies**, **Internal Kernel/Control-Plane Mechanics**, **Realistic Production Incident Stories**, **Simulated Terminal Logs**, **Step-by-Step Diagnostic Workflows**, **Before-vs-After YAML Fixes**, and **Production Prevention Checklists**.

---

## 📌 Table of Contents

- [🧠 0. The 3 Architectural Layers of Kubernetes Pod Statuses](#-0-the-3-architectural-layers-of-kubernetes-pod-statuses)
- [🛠️ Diagnostic Command Center: 12 Essential Triage Commands](#️-diagnostic-command-center-12-essential-triage-commands)
- [📑 1. Scheduling, Placement & Admission Control Errors](#-1-scheduling-placement--admission-control-errors)
  - [1.1 `Pending` (Generic Unscheduled Pod)](#11-pending-generic-unscheduled-pod)
  - [1.2 `FailedScheduling` (Resource Starvation: CPU / Memory / GPU)](#12-failedscheduling-resource-starvation-cpu--memory--gpu)
  - [1.3 `SchedulingGated` (Kubernetes 1.26+ Dynamic Scheduling Gates)](#13-schedulinggated-kubernetes-126-dynamic-scheduling-gates)
  - [1.4 `ResourceQuotaExceeded` (`Forbidden: exceeded quota`)](#14-resourcequotaexceeded-forbidden-exceeded-quota)
  - [1.5 `LimitRangeViolation` (`Forbidden: min/max constraint`)](#15-limitrangeviolation-forbidden-minmax-constraint)
  - [1.6 `AdmissionWebhookDenied` / `WebhookTimeout`](#16-admissionwebhookdenied--webhooktimeout)
  - [1.7 `MatchNodeSelector` / `NodeAffinity` Failure](#17-matchnodeselector--nodeaffinity-failure)
  - [1.8 `UntoleratedTaint` (`NoSchedule` / `NoExecute`)](#18-untoleratedtaint-noschedule--noexecute)
  - [1.9 `PodTopologySpreadFilter` / `PodAntiAffinity` Conflict](#19-podtopologyspreadfilter--podantiaffinity-conflict)
  - [1.10 `VolumeZoneConflict` (Cross-Availability-Zone Disk Lock)](#110-volumezoneconflict-cross-availability-zone-disk-lock)
  - [1.11 `HostPortConflict` (Node Port Already Bound)](#111-hostportconflict-node-port-already-bound)
  - [1.12 `MaxPodsExceeded` / `NodeUnschedulable` (`Cordoned`)](#112-maxpodsexceeded--nodeunschedulable-cordoned)
- [🌐 2. Pod Sandbox & CNI Network Initialization Errors](#-2-pod-sandbox--cni-network-initialization-errors)
  - [2.1 `PodInitializing`](#21-podinitializing)
  - [2.2 `CreatePodSandboxError` / `FailedCreatePodSandBox`](#22-createpodsandboxerror--failedcreatepodsandbox)
  - [2.3 `NetworkPluginNotReady` / `NetworkNotReady`](#23-networkpluginnotready--networknotready)
  - [2.4 `PodSubnetExhaustion` (IP Pool Depleted)](#24-podsubnetexhaustion-ip-pool-depleted)
  - [2.5 `FailedSync` (Kubelet Sync Loop Runtime Exception)](#25-failedsync-kubelet-sync-loop-runtime-exception)
  - [2.6 `ServiceAccountNotFound` / `kube-api-access` Mount Failure](#26-serviceaccountnotfound--kube-api-access-mount-failure)
- [📦 3. Image, OCI Registry & Container Runtime (CRI) Errors](#-3-image-oci-registry--container-runtime-cri-errors)
  - [3.1 `ErrImagePull`](#31-errimagepull)
  - [3.2 `ImagePullBackOff`](#32-imagepullbackoff)
  - [3.3 `InvalidImageName`](#33-invalidimagename)
  - [3.4 `ErrImageNeverPull`](#34-errimageneverpull)
  - [3.5 `ImageInspectError` / `ErrImageInspect`](#35-imageinspecterror--errimageinspect)
  - [3.6 `ImageArchitectureMismatch` (`exec format error` - ARM64 vs AMD64)](#36-imagearchitecturemismatch-exec-format-error---arm64-vs-amd64)
  - [3.7 `RegistryUnauthorized` (`401 Unauthorized` / `403 Forbidden`)](#37-registryunauthorized-401-unauthorized--403-forbidden)
  - [3.8 `DockerHubRateLimit` (`HTTP 429 Too Many Requests`)](#38-dockerhubratelimit-http-429-too-many-requests)
  - [3.9 `ImagePullTimeout` (Gigantic Layers / Slow Network)](#39-imagepulltimeout-gigantic-layers--slow-network)
- [🔒 4. Security Context, Kernel Profiles & Privilege Violations](#-4-security-context-kernel-profiles--privilege-violations)
  - [4.1 `RunAsNonRootError` / `MustRunAsNonRoot`](#41-runasnonrooterror--mustrunasnonroot)
  - [4.2 `ContainerCannotRun` (AppArmor / Seccomp / SELinux Denied)](#42-containercannotrun-apparmor--seccomp--selinux-denied)
  - [4.3 `ReadOnlyFilesystemError` (Write Denied on Root FS)](#43-readonlyfilesystemerror-write-denied-on-root-fs)
  - [4.4 `SysctlNotAllowed` / `ForbiddenSysctl`](#44-sysctlnotallowed--forbiddensysctl)
  - [4.5 `HostSecurityViolation` (`HostPID` / `HostNetwork` / `HostIPC` Blocked)](#45-hostsecurityviolation-hostpid--hostnetwork--hostipc-blocked)
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
  - [7.1 `Init:0/N` / `Init:1/N` (Waiting on Downstream Service)](#71-init0n--init1n-waiting-on-downstream-service)
  - [7.2 `Init:CrashLoopBackOff` & `Init:Error`](#72-initcrashloopbackoff--initerror)
  - [7.3 `Init:ImagePullBackOff`](#73-initimagepullbackoff)
  - [7.4 `Init:CreateContainerConfigError` & `Init:CreateContainerError`](#74-initcreatecontainerconfigerror--initcreatecontainererror)
  - [7.5 `Init:OOMKilled` (Exit Code 137 in Init Phase)](#75-initoomkilled-exit-code-137-in-init-phase)
  - [7.6 `ServiceMeshSidecarNotReady` (Istio / Linkerd Startup Race Condition)](#76-servicemeshsidecarnotready-istio--linkerd-startup-race-condition)
  - [7.7 `EphemeralContainersFailed` / `EphemeralContainerNotReady`](#77-ephemeralcontainersfailed--ephemeralcontainernotready)
- [💥 8. Runtime Crashes, Memory & Resource Starvation](#-8-runtime-crashes-memory--resource-starvation)
  - [8.1 `Error` (Non-Zero Application Exit Code)](#81-error-non-zero-application-exit-code)
  - [8.2 `CrashLoopBackOff`](#82-crashloopbackoff)
  - [8.3 `OOMKilled` (Exit Code 137 - Out of Memory)](#83-oomkilled-exit-code-137---out-of-memory)
  - [8.4 `Completed` (Exit Code 0 on Detached Background Process)](#84-completed-exit-code-0-on-detached-background-process)
  - [8.5 `ContainerStatusUnknown` (Kubelet & Containerd Communication Lost)](#85-containerstatusunknown-kubelet--containerd-communication-lost)
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
  - [11.2 `NameResolutionFailure` (`ndots:5` Latency & NXDOMAIN Storms)](#112-nameresolutionfailure-ndots5-latency--nxdomain-storms)
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

## 🧠 0. The 3 Architectural Layers of Kubernetes Pod Statuses

To troubleshoot Kubernetes effectively, you must understand that "Pod Status" is **not a single string**. It is composed of **3 distinct architectural layers**:

```
Layer 1: Pod Phase (High-level lifecycle state: Pending, Running, Succeeded, Failed, Unknown)
   │
   ├── Layer 2: Pod Conditions (Boolean health gates: PodScheduled, Initialized, ContainersReady, Ready, DisruptionTarget)
   │
   └── Layer 3: Container States (Low-level status: Waiting, Running, Terminated + Specific Reason & Exit Code)
```

```
[ 1. Pod Manifest Submitted via API / kubectl ]
                     │
                     ▼
 ┌─────────────────────────────────────────────────────────────┐
 │ Layer 1: Phase = PENDING                                    │
 │ ├─ Condition: PodScheduled = False  (kube-scheduler queue)  │
 │ ├─ Condition: PodScheduled = True   (Node selected)         │
 │ ├─ Condition: Initialized  = False  (Init containers run)   │
 │ └─ Condition: Initialized  = True   (Init containers done)  │
 └──────────────────────────────┬──────────────────────────────┘
                                │
                                ▼
 ┌─────────────────────────────────────────────────────────────┐
 │ Layer 1: Phase = RUNNING                                    │
 │ ├─ Container State: Waiting     (CRI pulling image/create)  │
 │ ├─ Container State: Running     (Process executing)         │
 │ ├─ Condition: ContainersReady   (Startup Probe passed)      │
 │ └─ Condition: Ready = True      (Readiness Probe passed)    │
 │    ──► Endpoint Controller adds Pod IP to Service Backends  │
 └──────────────────────────────┬──────────────────────────────┘
                                │
        ┌───────────────────────┴───────────────────────┐
        ▼                                               ▼
 ┌─────────────────────────────┐         ┌─────────────────────────────┐
 │ Layer 1: Phase = SUCCEEDED  │         │ Layer 1: Phase = FAILED     │
 │ ├─ Container: Terminated    │         │ ├─ Container: Terminated    │
 │ └─ Exit Code: 0             │         │ ├─ Exit Code != 0 (Crash)   │
 │    (Completed Batch Jobs)   │         │ └─ Reason: OOMKilled/Error  │
 └─────────────────────────────┘         └─────────────────────────────┘
```

---

## 🛠️ Diagnostic Command Center: 12 Essential Triage Commands

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

### 1.1 `Pending` (Generic Unscheduled Pod)
* **📖 Plain English Explanation & Beginner Analogy:**  
  Think of Kubernetes like a hotel booking system. You ask for a suite with 8 king beds (`8 CPU, 16 GB RAM`). If every room in the hotel only has 2 beds, the front desk places your reservation on "Hold" (`Pending`). The hotel receptionist will keep checking every few seconds until a matching room opens up or new rooms are built.
* **⚙️ What Happens Under the Hood:**  
  When you create a Pod, `kube-apiserver` writes the Pod manifest into `etcd` with `spec.nodeName: ""` (empty). The `kube-scheduler` watches for unbound pods and runs its scheduling pipeline (Filter Plugins $\rightarrow$ Score Plugins $\rightarrow$ Reserve Plugins $\rightarrow$ Permit Plugins $\rightarrow$ Bind Plugins). If every node is disqualified during the Filter phase, the Pod stays in `Pending` with condition `PodScheduled: False`.
* **🎭 Real-World Production Incident:**  
  During a Friday evening traffic surge, an e-commerce platform triggers a Horizontal Pod Autoscaler (HPA) to scale the payment service from 5 to 40 replicas. Each replica requests `2 CPU` and `4 GB RAM`. The AWS EKS cluster only had 5 worker nodes with `8 CPU` each. After scheduling 15 replicas, the cluster runs completely out of allocatable CPU. The remaining 25 replicas remain stuck in `Pending`, causing payment checkout latency to spike from 200ms to 45 seconds.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl get pods -n production
  NAME                              READY   STATUS    RESTARTS   AGE
  payment-service-59f7b98d9-29xkl   0/1     Pending   0          18m
  payment-service-59f7b98d9-48vzt   0/1     Pending   0          18m

  $ kubectl describe pod payment-service-59f7b98d9-29xkl -n production
  Events:
    Type     Reason            Age   From               Message
    ----     ------            ----  ----               -------
    Warning  FailedScheduling  18m   default-scheduler  0/5 nodes available: 5 Insufficient cpu.
  ```
* **🔍 Step-by-Step Diagnostic Plan:**  
  1. Check what the scheduler is complaining about: `kubectl describe pod <pod-name> -n <namespace>`
  2. Inspect total allocated CPU/memory across all nodes: `kubectl describe nodes | grep -A 8 "Allocated resources:"`
  3. Check if any PersistentVolumeClaims (PVCs) are stuck: `kubectl get pvc -n <namespace>`
* **🛠️ Step-by-Step Fix (Before vs After YAML):**  
  ```yaml
  # ❌ PROBLEMATIC YAML (Unrealistic requests that exhaust cluster)
  spec:
    containers:
    - name: payment-api
      image: myorg/payment:v2.1
      resources:
        requests:
          cpu: "4000m"     # Requesting 4 Full Cores per pod!
          memory: "8Gi"
  ```
  ```yaml
  # ✅ FIXED YAML (Realistic right-sized baseline requests)
  spec:
    containers:
    - name: payment-api
      image: myorg/payment:v2.1
      resources:
        requests:
          cpu: "250m"      # 0.25 core baseline
          memory: "512Mi"
        limits:
          cpu: "1000m"
          memory: "2Gi"
  ```
* **🛡️ Production Prevention Checklist:**  
  - Always install and configure **Cluster Autoscaler** or **Karpenter** on cloud clusters (EKS/GKE/AKS) to provision nodes automatically when pods enter `Pending`.
  - Use `Vertical Pod Autoscaler (VPA)` in recommendation mode to right-size CPU/RAM requests based on actual telemetry.

---

### 1.2 `FailedScheduling` (Resource Starvation: CPU / Memory / GPU)
* **📖 Plain English Explanation & Beginner Analogy:**  
  You walk into an arcade asking to play an exclusive virtual-reality game that requires an NVIDIA RTX 4090 GPU machine. The arcade has 10 standard consoles, but 0 VR GPU consoles. The arcade referee tells you: "Failed to seat: 0/10 machines match hardware requirements."
* **⚙️ What Happens Under the Hood:**  
  The `NodeResourcesFit` scheduler plugin compares `sum(allocated_requests) + pod_requests` against the node's `status.allocatable` pool. If `Allocatable - Requested < PodRequest`, the node score drops to 0 and the filter fails. For GPUs, the Kubelet device plugin must advertise socket capacity under `status.allocatable["nvidia.com/gpu"]`.
* **🎭 Real-World Production Incident:**  
  A data science team deploys a PyTorch model training Job requiring `nvidia.com/gpu: 2`. The Kubernetes node group contains AWS `g4dn.xlarge` instances, each possessing only **1 physical GPU**. The scheduler evaluates every node and immediately rejects all of them with `0/8 nodes available: 8 Insufficient nvidia.com/gpu`.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl describe pod pytorch-trainer-job-44fxa
  Events:
    Type     Reason            Age   From               Message
    ----     ------            ----  ----               -------
    Warning  FailedScheduling  5m    default-scheduler  0/8 nodes available: 8 Insufficient nvidia.com/gpu.
  ```
* **🔍 Step-by-Step Diagnostic Plan:**  
  ```bash
  # Check available GPUs across all cluster nodes
  kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\tGPU: "}{.status.allocatable.nvidia\.com/gpu}{"\n"}{end}'
  ```
* **🛠️ Step-by-Step Fix:**  
  Update the node group to multi-GPU instance types (e.g. AWS `g4dn.12xlarge` with 4 GPUs) or update the job spec to request 1 GPU per replica with distributed Data-Parallel training (DDP).

---

### 1.3 `SchedulingGated` (Kubernetes 1.26+ Dynamic Scheduling Gates)
* **📖 Plain English Explanation & Beginner Analogy:**  
  Imagine waiting in an amusement park line with a "VIP Ticket Check" gatekeeper standing in front of the ride. Even though seats are available, the gatekeeper blocks you until your security wristband is scanned. Once scanned, the gate opens and you are seated immediately.
* **⚙️ What Happens Under the Hood:**  
  Kubernetes 1.26 introduced `spec.schedulingGates`. When a Pod is created with a gate declared in its spec (e.g., `name: "karpenter.sh/provisioning"` or `name: "external-security-scanner"`), the `kube-scheduler` completely skips scheduling calculations for that Pod. The Pod remains in `Pending` without throwing `FailedScheduling` errors until an external controller patches the Pod to empty the `schedulingGates` array.
* **🎭 Real-World Production Incident:**  
  A company uses Karpenter for Just-In-Time node autoscaling. Karpenter intercepts a large batch job and attaches a scheduling gate `karpenter.sh/provisioning`. However, Karpenter crashes due to an AWS IAM permission error (`ec2:RunInstances` denied). Because Karpenter crashed, it never provisions the node and never removes the gate. The batch job remains stuck in `SchedulingGated` for hours.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl get pod ml-worker-01 -o yaml
  spec:
    schedulingGates:
    - name: karpenter.sh/provisioning
  status:
    conditions:
    - type: PodScheduled
      status: "False"
      reason: SchedulingGated
      message: "Pod was blocked by scheduling gates: [karpenter.sh/provisioning]"
  ```
* **🔍 Step-by-Step Diagnostic Plan:**  
  ```bash
  kubectl get pod <pod-name> -o jsonpath='{.spec.schedulingGates}'
  kubectl logs -n karpenter -l app.kubernetes.io/name=karpenter
  ```
* **🛠️ Step-by-Step Fix:**  
  1. Fix the underlying controller (e.g. grant AWS IAM permissions).
  2. If emergency manual intervention is needed to allow standard nodes to schedule the Pod:
     ```bash
     kubectl patch pod <pod-name> -p '{"spec":{"schedulingGates":[]}}'
     ```

---

### 1.4 `ResourceQuotaExceeded` (`Forbidden: exceeded quota`)
* **📖 Plain English Explanation & Beginner Analogy:**  
  Each department in a company is given a corporate credit card with a strict \$5,000 monthly spending limit (`ResourceQuota`). When the mobile team attempts to charge \$6,000, the bank declines the transaction instantly at the point of sale.
* **⚙️ What Happens Under the Hood:**  
  The `ResourceQuota` Admission Controller intercepts the `POST /api/v1/namespaces/{ns}/pods` request before it is ever written to `etcd`. It queries the current resource consumption in the namespace, calculates `current_usage + new_pod_requests`, and rejects the API transaction with HTTP 403 Forbidden if the sum exceeds the quota hard limit.
* **🎭 Real-World Scenario:**  
  The QA staging namespace has a quota of `requests.cpu: 16`. A developer deploys a load-testing suite with 10 replicas requesting `2 CPU` each (Total: 20 CPU). The deployment replica set fails to create pods.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl describe replicaset load-test-api-7b89f
  Events:
    Type     Reason        Age   From                   Message
    ----     ------        ----  ----                   -------
    Warning  FailedCreate  2m    replicaset-controller  pods "load-test-api-7b89f-8xz8" is forbidden: exceeded quota: compute-quota, requested: requests.cpu=2, used: requests.cpu=15, limited: requests.cpu=16
  ```
* **🔍 Step-by-Step Diagnostic Plan:**  
  ```bash
  kubectl describe resourcequota -n <namespace>
  ```
* **🛠️ Step-by-Step Fix:**  
  Increase the quota or clean up inactive pods/jobs in the namespace:
  ```bash
  kubectl delete pod -n <namespace> --field-selector=status.phase=Succeeded
  ```

---

### 1.5 `LimitRangeViolation` (`Forbidden: min/max constraint`)
* **📖 Plain English Explanation & Beginner Analogy:**  
  A luggage check-in rule at an airport states: "Bags must weigh at least 1 kg and no more than 32 kg." If you try to check in a 50 kg trunk or a feather weighing 0.01 kg, the counter agent rejects it immediately.
* **⚙️ What Happens Under the Hood:**  
  The `LimitRanger` admission plugin validates that every individual container's `requests` and `limits` conform to the `min`, `max`, and `maxLimitRequestRatio` constraints specified in the namespace's `LimitRange` object.
* **🛠️ Step-by-Step Fix:**  
  Inspect the namespace rules with `kubectl describe limitrange -n <namespace>` and ensure your container YAML requests and limits fall strictly within the minimum and maximum boundaries.

---

### 1.6 `AdmissionWebhookDenied` / `WebhookTimeout`
* **📖 Plain English Explanation & Beginner Analogy:**  
  A security guard at the gate of a secure research building inspects your badge. The guard scans your ID against an external security database. If your badge lacks required clearance badges (`Denied`), or if the security scanner loses Wi-Fi connection and freezes for 30 seconds (`WebhookTimeout`), the guard refuses to open the gate.
* **⚙️ What Happens Under the Hood:**  
  Before persisting objects, the API server executes registered `MutatingWebhookConfiguration` and `ValidatingWebhookConfiguration` webhooks (e.g. OPA Gatekeeper, Kyverno, Istio Sidecar Injector). The API server sends an `AdmissionReview` JSON payload over HTTPS to the webhook service. If the service returns `allowed: false` or fails to respond before `timeoutSeconds` (default 10s), the API call is aborted.
* **🎭 Real-World Scenario:**  
  An enterprise enforces a security policy requiring all container images to originate from the private registry `ecr.mycompany.com/`. A developer tests an open-source image `docker.io/library/redis:alpine`. The Validating Webhook immediately rejects the pod.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl apply -f redis-pod.yaml
  Error from server (Forbidden): error when creating "redis-pod.yaml": admission webhook "validate-registry.company.internal" denied the request: Image docker.io/library/redis:alpine comes from an untrusted registry. Only ecr.mycompany.com is allowed.
  ```
* **🛠️ Step-by-Step Fix:**  
  - Align your YAML with company security policies (e.g. mirror the image to your private registry).
  - If a broken webhook service is down and paralyzing the entire cluster:
    ```bash
    kubectl get validatingwebhookconfigurations
    kubectl delete validatingwebhookconfiguration <broken-webhook-name>
    ```

---

### 1.7 `MatchNodeSelector` / `NodeAffinity` Failure
* **📖 Plain English Explanation & Beginner Analogy:**  
  You request a hotel room specifically with "Ocean View and Balcony" (`nodeSelector: { view: ocean, balcony: true }`). If the only available rooms have "Street View" or "Courtyard View", you will not be checked into any room.
* **⚙️ What Happens Under the Hood:**  
  The `NodeAffinity` scheduler plugin matches the Pod's `nodeSelector` or `nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution` expressions against the labels stored in `Node.metadata.labels`. If no node satisfies all boolean requirements, the node is filtered out.
* **🎭 Real-World Scenario:**  
  A developer specifies `nodeSelector: { "kubernetes.io/arch": "arm64" }` to save cost on AWS Graviton instances. However, the cluster only has Intel x86_64 worker nodes (`amd64`). The Pod stays `Pending`.
* **🔍 Step-by-Step Diagnostic Plan:**  
  ```bash
  kubectl get nodes --show-labels
  kubectl describe pod <pod-name> | grep -A 6 "Node-Selectors:"
  ```
* **🛠️ Step-by-Step Fix:**  
  Label the target node:
  ```bash
  kubectl label nodes worker-node-01 kubernetes.io/arch=arm64 --overwrite
  ```
  Or change hard affinity (`required`) to soft affinity (`preferredDuringSchedulingIgnoredDuringExecution`).

---

### 1.8 `UntoleratedTaint` (`NoSchedule` / `NoExecute`)
* **📖 Plain English Explanation & Beginner Analogy:**  
  A hospital operating room has a "Sterile Zone: Medical Staff Only" sign (**Taint**). If you are a visitor, you are repelled and cannot enter. You can only enter if you are wearing a full surgeon scrub pass (**Toleration**).
* **⚙️ What Happens Under the Hood:**  
  Nodes have `spec.taints` with 3 possible effects:
  1. `NoSchedule`: Scheduler will never place a pod without matching toleration.
  2. `PreferNoSchedule`: Scheduler tries to avoid placing, but will if no other nodes exist.
  3. `NoExecute`: Pods currently running on the node that lack toleration are **evicted immediately**.
* **🎭 Real-World Scenario:**  
  Dedicated database nodes are tainted with `dedicated=postgres:NoSchedule` to prevent frontend web apps from taking DB CPU. You deploy a Redis cache without tolerations, and it cannot schedule.
* **🛠️ Step-by-Step Fix (Before vs After YAML):**  
  ```yaml
  # ✅ Add matching toleration to your Pod YAML
  spec:
    tolerations:
    - key: "dedicated"
      operator: "Equal"
      value: "postgres"
      effect: "NoSchedule"
  ```

---

### 1.9 `PodTopologySpreadFilter` / `PodAntiAffinity` Conflict
* **📖 Plain English Explanation & Beginner Analogy:**  
  A company policy states: "No two executive board members may fly on the same airplane." If there are 4 executives but only 3 flights available, the 4th executive cannot book a seat.
* **⚙️ What Happens Under the Hood:**  
  `PodAntiAffinity` prevents placing pods with matching label selectors on the same topology domain (e.g. `topologyKey: kubernetes.io/hostname` or `topologyKey: topology.kubernetes.io/zone`). If all nodes in the topology already host a matching pod, scheduler filter rejects placement.
* **🛠️ Step-by-Step Fix:**  
  Switch strict `requiredDuringSchedulingIgnoredDuringExecution` to flexible `preferredDuringSchedulingIgnoredDuringExecution` so Kubernetes can schedule replicas on the same node if the cluster runs out of separate hosts.

---

### 1.10 `VolumeZoneConflict` (Cross-Availability-Zone Disk Lock)
* **📖 Plain English Explanation & Beginner Analogy:**  
  You rent a physical storage locker in New York City. You cannot access that physical locker while standing in London. You must travel to New York to open it.
* **⚙️ What Happens Under the Hood:**  
  Cloud block storage volumes (AWS EBS, GCP Persistent Disk, Azure Disk) exist strictly inside a single physical datacenter Availability Zone (e.g. `us-east-1a`). When a PersistentVolume (PV) is bound in `us-east-1a`, the `VolumeZone` scheduler plugin automatically filters out any worker nodes located in `us-east-1b` or `us-east-1c`. If all nodes in `us-east-1a` are full, the Pod is stranded.
* **🛠️ Step-by-Step Fix:**  
  Configure your StorageClass with `volumeBindingMode: WaitForFirstConsumer` so the cloud disk is created in the exact AZ where the Pod is scheduled, rather than pre-creating it in a random zone.

---

### 1.11 `HostPortConflict` (Node Port Already Bound)
* **📖 Plain English Explanation & Beginner Analogy:**  
  Two different businesses try to register the exact same physical street address and door number. The postal service refuses to register the second business at the duplicate door.
* **⚙️ What Happens Under the Hood:**  
  When a container specifies `hostPort: 80`, the Kubelet binds port 80 directly on the host node's network interface. The scheduler `NodePorts` plugin ensures no two pods requesting the same `hostPort` and protocol are scheduled onto the same physical worker node.
* **🛠️ Step-by-Step Fix:**  
  Avoid `hostPort`. Use a standard Kubernetes `Service` (`type: ClusterIP`, `NodePort`, or `LoadBalancer`) with Ingress controllers.

---

### 1.12 `MaxPodsExceeded` / `NodeUnschedulable` (`Cordoned`)
* **📖 Plain English Explanation & Beginner Analogy:**  
  - `MaxPodsExceeded`: A bus has a maximum legal capacity of 110 passengers. The driver closes the doors once the 110th passenger steps in.
  - `NodeUnschedulable`: A maintenance crew puts an "Out of Service" cone in front of an escalator (`kubectl cordon`).
* **🛠️ Step-by-Step Fix:**  
  - Uncordon node: `kubectl uncordon <node-name>`.
  - Clean up dead completed pods to free up pod slots: `kubectl delete pod --field-selector=status.phase=Succeeded -A`.

---

## 🌐 2. Pod Sandbox & CNI Network Initialization Errors

---

### 2.1 `PodInitializing`
* **📖 Plain English Explanation:**  
  The stage where Kubernetes has reserved the node, and is currently setting up the container's private Linux namespaces (networking, IPC, PID), mounting volumes, injecting secrets, and executing Init Containers.
* **🛠️ Step-by-Step Fix:** Normal transitional state. If it stays stuck for more than 2 minutes, check init container logs: `kubectl logs <pod-name> -c <init-container-name>`.

---

### 2.2 `CreatePodSandboxError` / `FailedCreatePodSandBox`
* **📖 Plain English Explanation & Beginner Analogy:**  
  Before you can move furniture into a new house, the foundation and plumbing (network & IP address) must be set up. If the city utility company fails to assign a water line to your lot, construction halts completely.
* **⚙️ What Happens Under the Hood:**  
  The Kubelet calls the Container Runtime Interface (`RunPodSandbox` gRPC). The CRI (containerd) creates a pause container (`registry.k8s.io/pause:3.9`) and invokes the CNI plugin (e.g. Calico, Cilium, AWS VPC CNI) via CNI ADD command. The CNI creates a veth pair, attaches one end to the host bridge/eBPF map, moves the other end into the container network namespace, and assigns an IP address. If IP allocation fails or the CNI binary returns an error code, Kubelet reports `CreatePodSandboxError`.
* **🎭 Real-World Production Incident:**  
  In AWS EKS using AWS VPC CNI, an EC2 `m5.large` instance supports a maximum of 29 secondary IP addresses. A sudden deployment creates 35 pods on that node. The 30th pod through 35th pod fail with: `FailedCreatePodSandBox: rpc error: code = Unknown desc = failed to setup network for sandbox: add cmd: failed to assign an IP address`.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl describe pod api-backend-6784d5f-9xj2b
  Events:
    Type     Reason                  Age   From     Message
    ----     ------                  ----  ----     -------
    Warning  FailedCreatePodSandBox  4m    kubelet  Failed to create pod sandbox: rpc error: code = Unknown desc = failed to set up sandbox container: CNI failed to assign IP address
  ```
* **🔍 Step-by-Step Diagnostic Plan:**  
  ```bash
  # Check CNI agent logs in kube-system
  kubectl logs -n kube-system -l k8s-app=aws-node --tail=100
  kubectl logs -n kube-system -l k8s-app=calico-node --tail=100
  ```
* **🛠️ Step-by-Step Fix:**  
  - For AWS VPC CNI: Enable Prefix Delegation to expand IP capacity from 29 to over 250 IPs per node:
    ```bash
    kubectl set env daemonset aws-node -n kube-system ENABLE_PREFIX_DELEGATION=true
    ```
  - Restart CNI daemonset: `kubectl rollout restart ds/aws-node -n kube-system`.

---

### 2.3 `NetworkPluginNotReady` / `NetworkNotReady`
* **📖 Plain English Explanation:**  
  The worker node's Kubelet is running, but the CNI network daemonset has not yet initialized `/etc/cni/net.d/` network configuration files on the node filesystem.
* **🛠️ Step-by-Step Fix:**  
  Install or reinstall your CNI provider manifest (Calico, Flannel, or Cilium).

---

### 2.4 `PodSubnetExhaustion` (IP Pool Depleted)
* **📖 Plain English Explanation:**  
  The entire IP address block assigned to your Kubernetes cluster or node (e.g. `10.244.0.0/16`) has run out of unassigned IP addresses.
* **🛠️ Step-by-Step Fix:**  
  Add secondary CIDR blocks to your VPC / cluster network pool.

---

### 2.5 `FailedSync` (Kubelet Sync Loop Runtime Exception)
* **📖 Plain English Explanation:**  
  The Kubelet's internal reconciliation engine (`syncPod`) crashed or threw an unhandled exception while attempting to establish the desired pod state on the node.
* **🛠️ Step-by-Step Fix:**  
  Inspect node daemon logs with `sudo journalctl -u kubelet -e --no-pager`. Check if disk space under `/var/lib/kubelet` is full (`df -h`).

---

### 2.6 `ServiceAccountNotFound` / `kube-api-access` Mount Failure
* **📖 Plain English Explanation & Beginner Analogy:**  
  Your Pod requests a corporate ID badge named `finance-service-account` so it can talk to the Kubernetes API. The badge issuance office checks the registry and discovers no badge by that name was ever registered in that department.
* **⚙️ What Happens Under the Hood:**  
  Every Pod automatically mounts a projected volume named `kube-api-access-xxxxx` containing the ServiceAccount token, CA certificate, and namespace. If `spec.serviceAccountName` does not exist in the namespace, Kubelet's volume manager fails during `MountVolume.SetUp`.
* **🛠️ Step-by-Step Fix:**  
  ```bash
  kubectl create serviceaccount <service-account-name> -n <namespace>
  ```

---

## 📦 3. Image, OCI Registry & Container Runtime (CRI) Errors

---

### 3.1 `ErrImagePull`
* **📖 Plain English Explanation & Beginner Analogy:**  
  You order a book from an online warehouse with tracking ID `BK-9921`. The delivery courier looks on the warehouse shelves, finds no package with that ID, and calls you saying: "Package Not Found / 404".
* **⚙️ What Happens Under the Hood:**  
  Kubelet calls `PullImage` gRPC on `containerd`. The runtime contacts the OCI registry over HTTPS, queries `/v2/<name>/manifests/<tag>`, and receives HTTP 404 (Not Found), HTTP 401 (Unauthorized), or a DNS failure.
* **🎭 Real-World Scenario:**  
  A developer commits code with tag `v1.0.4`. In the Helm values file, they type `tag: 1.0.4` (missing `v`). The deployment immediately throws `ErrImagePull`.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl describe pod auth-api-5c94d8b99-kldp2
  Events:
    Type     Reason     Age   From     Message
    ----     ------     ----  ----     -------
    Normal   BackOff    12s   kubelet  Back-off pulling image "registry.gitlab.com/company/auth-api:1.0.4"
    Warning  Failed     12s   kubelet  Error: ErrImagePull
  ```
* **🛠️ Step-by-Step Fix:**  
  1. Verify image locally: `docker pull registry.gitlab.com/company/auth-api:1.0.4`
  2. For private registries, attach `imagePullSecrets`:
     ```yaml
     spec:
       imagePullSecrets:
       - name: gitlab-registry-secret
     ```

---

### 3.2 `ImagePullBackOff`
* **📖 Plain English Explanation:**  
  Direct continuation of `ErrImagePull`. Kubernetes pauses and waits with exponential backoff (10s $\rightarrow$ 20s $\rightarrow$ 40s $\rightarrow$ 80s $\rightarrow$ 300s) before re-attempting image downloads to prevent crashing the registry.
* **🛠️ Step-by-Step Fix:**  
  Fix the underlying `ErrImagePull` issue and force an immediate restart:
  ```bash
  kubectl rollout restart deployment <deployment-name>
  ```

---

### 3.3 `InvalidImageName`
* **📖 Plain English Explanation:**  
  The image string violates OCI / Docker naming rules (e.g. contains uppercase characters like `MyCompany/App:v1`, unrendered CI template variables `${TAG}`, or illegal spaces).
* **🛠️ Step-by-Step Fix:**  
  Change image repository names to all-lowercase characters and verify CI/CD template variable replacement.

---

### 3.4 `ErrImageNeverPull`
* **📖 Plain English Explanation:**  
  You specified `imagePullPolicy: Never`, promising Kubernetes that the image was pre-cached on the node's hard drive, but the image is missing from the local containerd cache.
* **🛠️ Step-by-Step Fix:**  
  - Load into Kind: `kind load docker-image <image> --name <cluster>`
  - Load into Minikube: `minikube image load <image>`
  - Or change `imagePullPolicy` to `IfNotPresent`.

---

### 3.5 `ImageInspectError` / `ErrImageInspect`
* **📖 Plain English Explanation:**  
  The container runtime downloaded the image layers from the registry, but the local disk corrupted the layer tar archive or ran out of disk space during decompression in `/var/lib/containerd`.
* **🛠️ Step-by-Step Fix:**  
  SSH into the worker node and prune corrupted layers:
  ```bash
  sudo crictl rmi <image-id>
  ```

---

### 3.6 `ImageArchitectureMismatch` (`exec format error` - ARM64 vs AMD64)
* **📖 Plain English Explanation & Beginner Analogy:**  
  You try to run an Apple Mac macOS software program directly on an old Intel Windows 98 computer. The operating system CPU rejects the machine instructions as unreadable gibberish.
* **⚙️ What Happens Under the Hood:**  
  The binary inside the container was compiled for ARM64 CPU instruction set (e.g. Apple Silicon M1/M2/M3 or AWS Graviton), but the worker node CPU is Intel/AMD x86_64 (`amd64`). The Linux kernel `execve` syscall fails with error code `ENOEXEC` (Exec format error).
* **🎭 Real-World Scenario:**  
  A developer builds a Docker image on their MacBook Pro (`docker build -t api:v1 .`) and pushes it. In production on AWS Intel EC2 nodes, the container crashes with exit code `1` or `127` and the log displays `exec /app/server: exec format error`.
* **🛠️ Step-by-Step Fix:**  
  Build multi-architecture container images using Docker Buildx:
  ```bash
  docker buildx build --platform linux/amd64,linux/arm64 -t myorg/api:v1 --push .
  ```

---

### 3.7 `RegistryUnauthorized` (`401 Unauthorized` / `403 Forbidden`)
* **📖 Plain English Explanation:**  
  The container registry requires authentication credentials, and the provided `imagePullSecrets` is missing, expired, or lacking read permissions.
* **🛠️ Step-by-Step Fix:**  
  Recreate the Docker registry secret with valid credentials:
  ```bash
  kubectl create secret docker-registry regcred \
    --docker-server=https://index.docker.io/v1/ \
    --docker-username=<user> \
    --docker-password=<pass> \
    --docker-email=<email> -n <namespace>
  ```

---

### 3.8 `DockerHubRateLimit` (`HTTP 429 Too Many Requests`)
* **📖 Plain English Explanation:**  
  Docker Hub limits anonymous unauthenticated image pulls to 100 requests per 6 hours per IP address. When an entire office or NAT gateway hits this ceiling, Docker Hub blocks all image downloads with HTTP 429.
* **🛠️ Step-by-Step Fix:**  
  Authenticate with a paid Docker Hub account or mirror public base images to an internal enterprise registry (AWS ECR, GCP Artifact Registry, Harbor).

---

### 3.9 `ImagePullTimeout` (Gigantic Layers / Slow Network)
* **📖 Plain English Explanation:**  
  The container image is extremely large (e.g. 20 GB AI/LLM model weights or PyTorch packages) and took longer than Kubelet's `--image-pull-progress-deadline` (default 1-2 minutes without progress).
* **🛠️ Step-by-Step Fix:**  
  Mount weights dynamically from S3/NFS volumes instead of baking 20 GB into Docker image layers.

---

## 🔒 4. Security Context, Kernel Profiles & Privilege Violations

---

### 4.1 `RunAsNonRootError` / `MustRunAsNonRoot`
* **📖 Plain English Explanation & Beginner Analogy:**  
  A bank vault has a strict rule: "No one may enter with the Master Master-Key (`root` user UID 0)." Your container walks up to the door wearing the master key badge. The vault security guard blocks entry instantly.
* **⚙️ What Happens Under the Hood:**  
  When `securityContext.runAsNonRoot: true` is configured, Kubelet inspects the image metadata. If `USER` in the Dockerfile is `root` (UID 0) and `runAsUser` is not defined in the Pod spec, Kubelet refuses to create the container container process to prevent privilege escalation.
* **🛠️ Step-by-Step Fix (Before vs After YAML):**  
  ```yaml
  # ✅ Specify explicit non-root UID/GID in Pod YAML
  spec:
    securityContext:
      runAsNonRoot: true
      runAsUser: 10001
      runAsGroup: 10001
      fsGroup: 10001
  ```

---

### 4.2 `ContainerCannotRun` (AppArmor / Seccomp / SELinux Denied)
* **📖 Plain English Explanation:**  
  The Linux kernel security sub-system (AppArmor, Seccomp, or SELinux) intercepted an unauthorized system call (e.g. attempting to mount a filesystem or invoke raw kernel network sockets).
* **🛠️ Step-by-Step Fix:**  
  Grant required Linux capabilities in `securityContext.capabilities.add` (e.g. `CAP_NET_ADMIN`, `CAP_SYS_PTRACE`) or adjust the Seccomp profile to `RuntimeDefault`.

---

### 4.3 `ReadOnlyFilesystemError` (Write Denied on Root FS)
* **📖 Plain English Explanation:**  
  Your Pod specifies `readOnlyRootFilesystem: true` for immutability and security. Your application attempts to write temporary log files or cache files to `/tmp` or `/app/logs` and crashes with `Read-only file system (errno 30)`.
* **🛠️ Step-by-Step Fix:**  
  Mount a writable in-memory `emptyDir` volume specifically on `/tmp`:
  ```yaml
  volumeMounts:
  - name: writable-tmp
    mountPath: /tmp
  volumes:
  - name: writable-tmp
    emptyDir: {}
  ```

---

### 4.4 `SysctlNotAllowed` / `ForbiddenSysctl`
* **📖 Plain English Explanation:**  
  The Pod attempts to configure unsafe kernel parameters (e.g. `net.ipv4.ip_forward`, `net.core.somaxconn`) that are not whitelisted in the Kubelet configuration `--allowed-unsafe-sysctls`.
* **🛠️ Step-by-Step Fix:**  
  Add the sysctl name to `/var/lib/kubelet/config.yaml` on worker nodes.

---

### 4.5 `HostSecurityViolation` (`HostPID` / `HostNetwork` / `HostIPC` Blocked)
* **📖 Plain English Explanation:**  
  The Pod specifies `hostNetwork: true` or `hostPID: true`, but the namespace enforces the Kubernetes Pod Security Admission (PSA) `restricted` or `baseline` profile.
* **🛠️ Step-by-Step Fix:**  
  Remove host namespace access or grant `privileged` profile to the namespace:
  ```bash
  kubectl label ns <namespace> pod-security.kubernetes.io/enforce=privileged
  ```

---

### 4.6 `DevicePluginFailed` / `GPUAllocationFailed`
* **📖 Plain English Explanation:**  
  The NVIDIA or SR-IOV device plugin daemonset crashed or lost communication with host hardware kernel drivers (`/dev/nvidiactl`).
* **🛠️ Step-by-Step Fix:**  
  Restart the NVIDIA device plugin: `kubectl rollout restart ds/nvidia-device-plugin-daemonset -n kube-system`.

---

## ⚙️ 5. Container Configuration & Lifecycle Hook Errors

---

### 5.1 `CreateContainerConfigError`
* **📖 Plain English Explanation & Beginner Analogy:**  
  A chef begins cooking a recipe that requires "Secret Spice #4" (`Secret: db-password`). The chef opens the spice cabinet and discovers the spice jar does not exist. The chef halts cooking before ever turning on the stove.
* **⚙️ What Happens Under the Hood:**  
  Before creating the container sandbox, Kubelet resolves all `envFrom.configMapRef`, `env.valueFrom.secretKeyRef`, and volume references. If any referenced ConfigMap, Secret, or Secret key is missing in the namespace, Kubelet fails with `CreateContainerConfigError`.
* **🎭 Real-World Scenario:**  
  You deploy a new version of an authentication service that references a secret `jwt-encryption-key`. You forgot to run `kubectl apply -f secret.yaml` in the production namespace. The Pod refuses to start.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl describe pod auth-api-7b89f-29xkl
  Events:
    Type     Reason                    Age   From     Message
    ----     ------                    ----  ----     -------
    Warning  Failed                    15s   kubelet  Error: secret "jwt-encryption-key" not found
  ```
* **🛠️ Step-by-Step Fix:**  
  Create the missing Secret or ConfigMap:
  ```bash
  kubectl create secret generic jwt-encryption-key --from-literal=secret=mySuperSecretKey -n <namespace>
  ```

---

### 5.2 `CreateContainerError`
* **📖 Plain English Explanation:**  
  The low-level container runtime (`containerd` / `runc`) failed while constructing the container sandbox (e.g. duplicate `mountPath` declarations, conflicting volume mounts, or invalid Linux capabilities).
* **🛠️ Step-by-Step Fix:**  
  Ensure all volume mounts inside the container have unique destination directories.

---

### 5.3 `RunContainerError`
* **📖 Plain English Explanation & Beginner Analogy:**  
  The container was constructed, but the moment Linux tried to execute `/entrypoint.sh`, Linux threw a "Permission Denied" error because the script did not have executable permissions (`chmod +x`), or threw a "Bad Interpreter" error due to Windows `CRLF` carriage returns.
* **🛠️ Step-by-Step Fix:**  
  In your Dockerfile, ensure execution bits:
  ```dockerfile
  RUN chmod +x /app/entrypoint.sh
  ```
  Convert line endings from CRLF to LF: `dos2unix entrypoint.sh`.

---

### 5.4 `VolumeSubpathInitializationFailed`
* **📖 Plain English Explanation:**  
  You used `subPath: config.json` in `volumeMounts` to mount a single file from a ConfigMap, but the key inside the ConfigMap was spelled `configuration.json`.
* **🛠️ Step-by-Step Fix:**  
  Verify the exact key name inside the ConfigMap with `kubectl get configmap <name> -o yaml`.

---

### 5.5 `ProjectedVolumeError` / `TokenRequestFailed`
* **📖 Plain English Explanation:**  
  The Kubelet failed to request a bound service account token from the Kubernetes API server TokenRequest API.
* **🛠️ Step-by-Step Fix:**  
  Verify API server health and check if the ServiceAccount exists.

---

### 5.6 `PostStartHookError` & `PreStopHookFailed`
* **📖 Plain English Explanation:**  
  - `PostStartHookError`: The lifecycle `postStart` script failed with a non-zero exit code. Kubernetes kills the container immediately.
  - `PreStopHookFailed`: The lifecycle `preStop` handler hung and exceeded `terminationGracePeriodSeconds`.
* **🛠️ Step-by-Step Fix:**  
  Make `postStart` hooks non-blocking and ensure pre-stop scripts exit quickly.

---

## 💾 6. Storage, CSI Driver & Volume Mounting Errors

---

### 6.1 `ContainerCreating` (Stuck on Volume Attachment)
* **📖 Plain English Explanation & Beginner Analogy:**  
  Worker Node A crashes abruptly. The AWS EBS disk is still physically plugged into the dead server. When Kubernetes starts the Pod on Worker Node B, AWS cloud refuses to attach the disk because AWS thinks Node A still has the exclusive lock.
* **⚙️ What Happens Under the Hood:**  
  Cloud block storage drivers manage `VolumeAttachment` objects. When a node dies without clean teardown, the cloud provider volume attachment lock persists. The Kubelet attach-detach controller on the master node must wait for timeout (6-8 minutes) before forcefully detaching the volume.
* **🛠️ Step-by-Step Fix:**  
  Wait for CSI timeout or manually detach the stuck volume in the AWS EC2 / GCP console.

---

### 6.2 `FailedMount` & `FailedAttachVolume`
* **📖 Plain English Explanation:**  
  - `FailedAttachVolume`: Cloud provider failed to attach disk to VM (IAM permissions missing).
  - `FailedMount`: Disk is attached to VM, but OS filesystem format/mount failed (corrupted ext4/xfs).
* **🛠️ Step-by-Step Fix:**  
  Verify IAM role permissions on worker node EC2 instance profiles (`ec2:AttachVolume`).

---

### 6.3 `PersistentVolumeClaimNotBound` / `ProvisioningFailed`
* **📖 Plain English Explanation:**  
  The Pod requests a PersistentVolumeClaim, but the PVC status is `Pending` because no matching StorageClass provisioner exists in the cluster.
* **🛠️ Step-by-Step Fix:**  
  Check available StorageClasses with `kubectl get sc` and update `storageClassName` in your PVC.

---

### 6.4 `Multi-Attach error for volume` (ReadWriteOnce Conflict)
* **📖 Plain English Explanation:**  
  A PersistentVolume configured with `ReadWriteOnce` (RWO) cannot be mounted by two pods on two different physical worker nodes at the same time. During a rolling update, the new pod starts on Node 2 while the old pod is still terminating on Node 1.
* **🛠️ Step-by-Step Fix:**  
  Change deployment strategy to `Recreate` for single-replica stateful apps:
  ```yaml
  spec:
    strategy:
      type: Recreate
  ```

---

### 6.5 `VolumeResizeFailed` / `FileSystemResizeFailed`
* **📖 Plain English Explanation:**  
  You increased PVC storage from `50Gi` to `100Gi`, but the CSI driver failed to expand the live filesystem (`resize2fs` / `xfs_growfs`).
* **🛠️ Step-by-Step Fix:**  
  Ensure StorageClass has `allowVolumeExpansion: true`. Restart the consumer Pod.

---

### 6.6 `VolumeConditionAbnormal` (CSI Volume Health Monitoring)
* **📖 Plain English Explanation:**  
  The CSI Volume Health Monitor detected physical hardware degradation, bad blocks, or I/O latency spikes on the underlying storage SAN.
* **🛠️ Step-by-Step Fix:**  
  Snapshot the volume and migrate data to a fresh PVC.

---

### 6.7 `NFSStaleFileHandle` / `StorageNodeDown`
* **📖 Plain English Explanation:**  
  An NFS server rebooted, causing worker node Linux kernel NFS mounts to enter Uninterruptible Sleep (D-state). Pods hung on file I/O cannot be terminated normally.
* **🛠️ Step-by-Step Fix:**  
  Force unmount on worker node: `sudo umount -f -l <mountpoint>`.

---

## 🔄 7. Init Containers, Sidecars & Ephemeral Debuggers

---

### 7.1 `Init:0/N` / `Init:1/N` (Waiting on Downstream Service)
* **📖 Plain English Explanation & Beginner Analogy:**  
  A construction crew is waiting for the concrete foundation to cure before building the house walls. If the concrete mixer truck gets stuck in traffic, construction is on indefinite standby (`Init:0/1`).
* **⚙️ What Happens Under the Hood:**  
  Init Containers execute sequentially from index `0` to `N-1`. Each Init Container must exit cleanly with status code `0` before the next container begins. The main app container process is never created until all Init Containers succeed.
* **🎭 Real-World Scenario:**  
  An Init container runs `until nc -z postgres-db 5432; do sleep 2; done`. The PostgreSQL database is offline. The Pod stays in `Init:0/1` forever.
* **🛠️ Step-by-Step Fix:**  
  Inspect the Init container logs: `kubectl logs <pod-name> -c <init-container-name>`. Fix the database connection.

---

### 7.2 `Init:CrashLoopBackOff` & `Init:Error`
* **📖 Plain English Explanation:**  
  The Init Container executed and crashed with a non-zero exit code (e.g. database migration script failed with SQL syntax error). Kubernetes enters exponential restart backoff.
* **🛠️ Step-by-Step Fix:**  
  Check previous crash logs: `kubectl logs <pod-name> -c <init-container-name> --previous`. Fix the migration script or DB credentials.

---

### 7.3 `Init:ImagePullBackOff`
* **📖 Plain English Explanation:**  
  The container image for the Init container cannot be pulled from the registry.
* **🛠️ Step-by-Step Fix:**  
  Fix image tag spelling or registry secret.

---

### 7.4 `Init:CreateContainerConfigError` & `Init:CreateContainerError`
* **📖 Plain English Explanation:**  
  The Init Container references a ConfigMap, Secret, or volume mount that is missing or duplicate.
* **🛠️ Step-by-Step Fix:**  
  Verify ConfigMaps and Secrets referenced in the `initContainers` block exist.

---

### 7.5 `Init:OOMKilled` (Exit Code 137 in Init Phase)
* **📖 Plain English Explanation:**  
  An Init Container performing data unpacking, file downloading, or database migrations exceeded its memory limit and was killed by the Linux kernel.
* **🛠️ Step-by-Step Fix:**  
  Increase `resources.limits.memory` on the specific `initContainers` block.

---

### 7.6 `ServiceMeshSidecarNotReady` (Istio / Linkerd Startup Race Condition)
* **📖 Plain English Explanation & Beginner Analogy:**  
  A VIP diplomat moves into a high-security zone. The diplomat's security bodyguard (**Envoy Sidecar Proxy**) is still putting on their bulletproof vest. The diplomat tries to walk outside and gets blocked immediately.
* **⚙️ What Happens Under the Hood:**  
  In Service Mesh environments, the app container and the Envoy sidecar container start in parallel. If the app boots faster than Envoy (which takes 2-4 seconds to sync xDS routes), the app's initial database connection fails with `Connection Refused`.
* **🛠️ Step-by-Step Fix:**  
  - In Kubernetes 1.28+, use native sidecar containers (`restartPolicy: Always` inside `initContainers`).
  - In Istio: Set `holdApplicationUntilProxyStarts: true` in mesh config.

---

### 7.7 `EphemeralContainersFailed` / `EphemeralContainerNotReady`
* **📖 Plain English Explanation:**  
  An interactive debug container launched via `kubectl debug -it <pod> --image=nicolaka/netshoot` failed to start or crashed.
* **🛠️ Step-by-Step Fix:**  
  Verify the debug image name exists and is accessible.

---

## 💥 8. Runtime Crashes, Memory & Resource Starvation

---

### 8.1 `Error` (Non-Zero Application Exit Code)
* **📖 Plain English Explanation & Beginner Analogy:**  
  A student opens an exam paper, encounters an impossible question with no instructions, throws up their hands, and walks out of the exam room (Exit Code 1).
* **⚙️ What Happens Under the Hood:**  
  The application process was launched as PID 1 inside the container, encountered an unhandled exception (e.g. Python `ZeroDivisionError`, Node.js unhandled promise rejection, Java `NullPointerException`), and exited with a status code other than `0`.
* **🛠️ Step-by-Step Fix:**  
  Inspect the application stack trace: `kubectl logs <pod-name> --previous`. Fix the code bug or provide missing environment variables.

---

### 8.2 `CrashLoopBackOff`
* **📖 Plain English Explanation & Beginner Analogy:**  
  A car engine has a dead battery. Every time you turn the key, the engine sputters for 1 second and dies. If you keep turning the key rapidly every 0.1 seconds, you will burn out the starter motor. Kubernetes enforces a cooling-off waiting period (10s, 20s, 40s... up to 5 minutes) between restart attempts.
* **⚙️ What Happens Under the Hood:**  
  When a container exits with a non-zero status, Kubelet increments the restart counter and calculates the backoff formula: $T = \min(10 \times 2^{\text{restarts}}, 300)\text{ seconds}$. The container state becomes `Waiting` with reason `CrashLoopBackOff`.
* **🎭 Real-World Scenario:**  
  A microservice connects to Redis during boot. In production, the Redis DNS name was changed, but the deployment config was not updated. The app crashes immediately upon boot.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl get pod api-69f8b4d99-kldp2
  NAME                      READY   STATUS             RESTARTS      AGE
  api-69f8b4d99-kldp2       0/1     CrashLoopBackOff   8 (3m ago)    22m
  ```
* **🔍 Step-by-Step Diagnostic Plan:**  
  ```bash
  # Check logs of the crashed container instance BEFORE it restarted
  kubectl logs <pod-name> --previous
  ```
* **🛠️ Step-by-Step Fix:**  
  Identify whether the exit code is:
  - **Exit 1**: Application code bug $\rightarrow$ Fix code/env.
  - **Exit 137**: OOMKilled $\rightarrow$ Increase RAM limits.
  - **Exit 127**: Missing binary $\rightarrow$ Fix Dockerfile entrypoint.

---

### 8.3 `OOMKilled` (Exit Code 137 - Out of Memory)
* **📖 Plain English Explanation & Beginner Analogy:**  
  You rent a storage unit that has a hard ceiling of 500 boxes (`limits.memory: 500Mi`). You try to cram the 501st box into the unit. The facility security guard (**Linux Kernel OOM Killer**) immediately executes an emergency eviction order and terminates your lease with prejudice (`SIGKILL / Signal 9`).
* **⚙️ What Happens Under the Hood:**  
  Every container's memory limit is enforced by Linux Kernel `cgroups` (`memory.max` in cgroups v2 or `memory.limit_in_bytes` in cgroups v1). When container memory consumption exceeds the limit, the Linux kernel Out-Of-Memory Killer sends an uncatchable `SIGKILL` (Signal 9) to PID 1. The container termination exit code is recorded as $128 + 9 = 137$.
* **🎭 Real-World Scenario:**  
  A PDF generation microservice receives a 500-page document upload. The Node.js buffer inflates memory usage from 250 MB to 1.8 GB. The Pod memory limit was set to `1Gi`. The Linux kernel immediately kills the container.
* **💻 Realistic Simulated Terminal Output:**  
  ```bash
  $ kubectl describe pod pdf-generator-7b89f-29xkl
  Containers:
    pdf-service:
      State:          Terminated
        Reason:       OOMKilled
        Exit Code:    137
        Started:      Fri, 17 Sep 2026 01:10:00 +0000
        Finished:     Fri, 17 Sep 2026 01:14:22 +0000
  ```
* **🛠️ Step-by-Step Fix (Before vs After YAML):**  
  ```yaml
  # ❌ PROBLEMATIC YAML (Memory limit too low for peak batch operations)
  resources:
    limits:
      memory: "512Mi"
  ```
  ```yaml
  # ✅ FIXED YAML (Right-sized memory with safety headroom)
  resources:
    requests:
      memory: "1Gi"
    limits:
      memory: "3Gi"
  ```
  - For Java JVM: Configure JVM container awareness flags: `-XX:MaxRAMPercentage=75.0`.

---

### 8.4 `Completed` (Exit Code 0 on Detached Background Process)
* **📖 Plain English Explanation:**  
  The container entrypoint process finished its work and exited cleanly with status code `0`.
* **Is this an error?**
  - **Healthy** for Kubernetes `Jobs` or `CronJobs`.
  - **Error** if this was supposed to be a continuous web server (Nginx/Express). It means your startup command launched in the background (detached `&`), leaving PID 1 to exit immediately.
* **🛠️ Step-by-Step Fix:**  
  Ensure web servers run in the **foreground**:
  - Nginx: `CMD ["nginx", "-g", "daemon off;"]`
  - Node: `CMD ["node", "server.js"]`

---

### 8.5 `ContainerStatusUnknown` (Kubelet & Containerd Communication Lost)
* **📖 Plain English Explanation & Beginner Analogy:**  
  The flight control tower tries to radio an aircraft to ask its altitude. The radio transmitter is completely dead and hears only static. The tower marks the aircraft status as "Unknown" until communication is restored.
* **⚙️ What Happens Under the Hood:**  
  The Kubernetes control plane (`kube-apiserver`) queries Kubelet on the worker node for container status. If the container runtime (`containerd` / `CRI-O`) crashed, was OOM-killed on the host level, or the UNIX domain socket `/run/containerd/containerd.sock` timed out, Kubelet cannot determine whether containers are running or dead.
* **🛠️ Step-by-Step Fix:**  
  1. SSH into the worker node and restart containerd & kubelet:
     ```bash
     sudo systemctl restart containerd
     sudo systemctl restart kubelet
     ```
  2. If the node is permanently destroyed: Force delete the stuck pod:
     ```bash
     kubectl delete pod <pod-name> -n <namespace> --grace-period=0 --force
     ```

---

### 8.6 `DeadlineExceeded` (`activeDeadlineSeconds` Reached)
* **📖 Plain English Explanation:**  
  A data-processing Job specifies `activeDeadlineSeconds: 600` (10 minutes max). The job took 11 minutes, so Kubernetes terminated the Pod.
* **🛠️ Step-by-Step Fix:** Increase `activeDeadlineSeconds` or optimize query performance.

---

### 8.7 `BackoffLimitExceeded` (Batch Job Failure)
* **📖 Plain English Explanation:**  
  A Kubernetes `Job` retried failed pod executions up to `spec.backoffLimit` (default 6 retries) and failed all attempts.
* **🛠️ Step-by-Step Fix:** Check job pod logs with `kubectl logs job/<job-name>` and fix the application error.

---

### 8.8 `JobSuspended` (`spec.suspend: true`)
* **📖 Plain English Explanation:**  
  The batch Job has been placed on pause. No pods will be scheduled until `spec.suspend` is set to `false`.
* **🛠️ Step-by-Step Fix:** Resume the job:
  ```bash
  kubectl patch job <job-name> -p '{"spec":{"suspend":false}}'
  ```

---

## 🔧 9. In-Place Pod Resizing & Dynamic Resource Allocation (K8s 1.27+)

---

### 9.1 In-Place Resize States: `Proposed`, `InProgress`, `Deferred`, `Infeasible`
* **📖 Plain English Explanation:**  
  In Kubernetes 1.27+, you can change a container's CPU/RAM without restarting the Pod. The `status.resize` field indicates the state:
  - **`Proposed`**: API server accepted the change.
  - **`InProgress`**: Kubelet is actively reconfiguring cgroups v2 limits on the node.
  - **`Deferred`**: The requested CPU/RAM is currently occupied by other workloads on the node; Kubelet will apply it as soon as capacity frees up.
  - **`Infeasible`**: The requested size exceeds the physical node hardware (e.g. requesting 128 GB RAM on a 64 GB RAM server).
* **🛠️ Step-by-Step Fix:** If `Infeasible`, recreate the Pod so it can be scheduled on a larger machine.

---

### 9.2 `DynamicResourceAllocationPending` (DRA Hardware Claims)
* **📖 Plain English Explanation:**  
  The Pod requests specialized hardware (FPGAs, custom TPU slices) via Dynamic Resource Allocation (`ResourceClaim`), but the vendor DRA driver has not yet bound the hardware.
* **🛠️ Step-by-Step Fix:** Verify the health of the DRA driver daemonset.

---

## 🏥 10. Health Checks (Probes) & Custom Readiness Gates

---

### 10.1 `Unhealthy` (Liveness Probe Failed)
* **📖 Plain English Explanation & Beginner Analogy:**  
  A doctor checks a patient's pulse every 10 seconds. If the doctor detects 0 pulse 3 times in a row, the doctor applies CPR (**Kubelet kills and restarts the container**).
* **⚙️ What Happens Under the Hood:**  
  Kubelet executes the configured `livenessProbe` (HTTP GET, TCP Socket, or Exec command). If the probe fails `failureThreshold` consecutive times (default 3), Kubelet terminates the container process and increments the restart counter.
* **🎭 Real-World Scenario:**  
  An API service suffers a database connection pool exhaustion. When Kubelet queries `/healthz`, the endpoint blocks waiting for a DB connection and times out after 3 seconds. Kubelet kills and restarts the container in an endless loop.
* **🛠️ Step-by-Step Fix:**  
  - Ensure Liveness Probes check **only in-memory health** (do not query downstream databases in liveness probes).
  - Increase `initialDelaySeconds` and `timeoutSeconds`:
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
* **📖 Plain English Explanation & Beginner Analogy:**  
  A restaurant waiter turns the "OPEN" sign on the front door to "BUSY". The kitchen staff is not fired (**Container is NOT restarted**), but new customers are prevented from entering the dining room until the kitchen catches up.
* **⚙️ What Happens Under the Hood:**  
  When a Readiness Probe fails, the EndpointSlice controller removes the Pod's IP address from all matching Kubernetes `Service` backends. The container remains running, but receives **0 incoming user traffic**.
* **🛠️ Step-by-Step Fix:**  
  Investigate downstream dependencies (e.g. Redis cache or database availability) and increase `initialDelaySeconds`.

---

### 10.3 `StartupProbeFailed` (Slow Boot Timeout)
* **📖 Plain English Explanation:**  
  Legacy enterprise monolithic apps (like Java Spring Boot) may take 90-120 seconds to boot on cold start. A `startupProbe` disables Liveness Probes until the application has fully initialized.
* **🛠️ Step-by-Step Fix:**  
  Add a `startupProbe` granting up to 5 minutes to start:
  ```yaml
  startupProbe:
    httpGet:
      path: /healthz
      port: 8080
    failureThreshold: 30
    periodSeconds: 10   # 30 * 10s = 300 seconds (5 minutes)
  ```

---

### 10.4 `ExecProbeTimeout` / `ProbeWarning`
* **📖 Plain English Explanation:**  
  A probe configured with `exec: command: [...]` hung inside the container because the command process deadlocked.
* **🛠️ Step-by-Step Fix:** Use lightweight HTTP or TCP probes instead of heavy shell scripts.

---

### 10.5 `ReadinessGatesFailed` / `ReadinessGatesNotReady` (Cloud Load Balancers)
* **📖 Plain English Explanation:**  
  The Pod is ready inside Kubernetes, but an external cloud controller (e.g. AWS ALB Target Group registration) has not verified target health.
* **🛠️ Step-by-Step Fix:** Verify cloud security group firewall rules and health check paths on the AWS ALB Target Group.

---

## 🌐 11. DNS, CoreDNS & Cluster Networking Failures

---

### 11.1 `CoreDNS CrashLoopBackOff` (Forwarding Loop Detected)
* **📖 Plain English Explanation & Beginner Analogy:**  
  Person A asks Person B for directions. Person B tells Person A to ask Person A. The two people loop endlessly talking to each other until exhaustion.
* **⚙️ What Happens Under the Hood:**  
  If the host worker node's `/etc/resolv.conf` contains an upstream nameserver `127.0.0.53` (Ubuntu `systemd-resolved`), CoreDNS detects that it is forwarding queries back to itself and intentionally crashes with `plugin/loop: Loop detected` to protect the CPU.
* **🛠️ Step-by-Step Fix:**  
  Fix host `/etc/resolv.conf` nameservers to point directly to upstream DNS (e.g. `8.8.8.8` or internal gateway).

---

### 11.2 `NameResolutionFailure` (`ndots:5` Latency & NXDOMAIN Storms)
* **📖 Plain English Explanation:**  
  Default Kubernetes DNS uses `ndots:5`. When resolving external domains like `api.stripe.com` (which has 2 dots), Kubernetes sends 5 recursive search queries (`api.stripe.com.default.svc.cluster.local`, etc.), flooding CoreDNS and causing random connection drops.
* **🛠️ Step-by-Step Fix:**  
  Append a trailing dot to external URLs: `https://api.stripe.com./` or deploy NodeLocal DNSCache.

---

### 11.3 `ServiceEndpointsMissing` (No Pods Match Selector)
* **📖 Plain English Explanation:**  
  A Kubernetes Service has `selector: { app: payment }`, but the Deployment Pod labels are spelled `app: payment-api`. The Service has `0` endpoints.
* **🛠️ Step-by-Step Fix:**  
  Align the Service `spec.selector` labels with the Pod `metadata.labels`.

---

## 🚪 12. Node Pressure, Eviction, Preemption & Teardown Errors

---

### 12.1 `Evicted` (`DiskPressure`, `MemoryPressure`, `PIDPressure`)
* **📖 Plain English Explanation & Beginner Analogy:**  
  A submarine takes on water and approaches maximum depth limit. The captain orders non-essential cargo to be thrown overboard (**Kubelet evicts BestEffort pods**) to prevent the submarine from sinking (**Node freeze/kernel panic**).
* **⚙️ What Happens Under the Hood:**  
  When node root filesystem utilization exceeds threshold (usually >85%), Kubelet triggers `DiskPressure`. It evicts pods ranked by Quality of Service (QoS):
  1. `BestEffort` (no requests/limits) $\rightarrow$ Evicted first.
  2. `Burstable` (requests < limits) $\rightarrow$ Evicted second.
  3. `Guaranteed` (requests == limits) $\rightarrow$ Evicted last.
* **🛠️ Step-by-Step Fix:**  
  - Clean unused container images: `sudo crictl rmi --prune`.
  - Set `ephemeral-storage` limits on pods to prevent unrotated logs from filling the host disk.
  - Delete evicted pod records:
    ```bash
    kubectl get pods -A | grep Evicted | awk '{print $2 " -n " $1}' | xargs -r kubectl delete pod
    ```

---

### 12.2 `Evicted` (`DisruptionTarget`: `EvictionByEvictionAPI`, `TerminationByKubelet`, `DeletionByPodGC`)
* **📖 Plain English Explanation:**  
  In Kubernetes 1.25+, the `DisruptionTarget` condition records the exact sub-reason:
  - **`EvictionByEvictionAPI`**: Node was drained via `kubectl drain`.
  - **`PreemptionByKubeScheduler`**: Evicted to seat a higher-priority pod.
  - **`TerminationByKubelet`**: Evicted due to host resource starvation.
  - **`DeletionByPodGC`**: Orphaned pod cleaned up by the garbage collector.

---

### 12.3 `TaintManagerEviction` (`NoExecute` Taint Applied)
* **📖 Plain English Explanation:**  
  A `NoExecute` taint was added to a node, and the Pod's `tolerationSeconds` countdown expired.
* **🛠️ Step-by-Step Fix:** Add toleration with higher `tolerationSeconds` if pods must survive temporary network partitions.

---

### 12.4 `TerminatedDueToNodeShutdown` (Graceful Node Shutdown)
* **📖 Plain English Explanation:**  
  The host server received an OS shutdown signal (`systemd-inhibit`), and Kubelet terminated pods in priority order before power-off.

---

### 12.5 `Preempted` / `Preempting` (`PriorityClass`)
* **📖 Plain English Explanation:**  
  A critical production pod with `priorityClassName: high-priority` required node capacity, so Kubernetes evicted a lower-priority background batch worker pod.
* **🛠️ Step-by-Step Fix:** Assign `PriorityClass` objects to mission-critical workloads.

---

### 12.6 `Terminating` (Stuck on Finalizers or Storage Unmount)
* **📖 Plain English Explanation & Beginner Analogy:**  
  You try to close a bank account. The bank teller says: "You cannot close this account until your pending loan paperwork is cleared (**Finalizer**)."
* **⚙️ What Happens Under the Hood:**  
  When you delete a Pod, API server sets `metadata.deletionTimestamp`. If `metadata.finalizers` contains items or an NFS storage CSI driver hangs unmounting the disk, the Pod remains in `Terminating` indefinitely.
* **🛠️ Step-by-Step Fix:**  
  Remove blocking finalizers:
  ```bash
  kubectl patch pod <pod-name> -n <namespace> -p '{"metadata":{"finalizers":null}}'
  ```
  Force delete if the process is confirmed dead:
  ```bash
  kubectl delete pod <pod-name> -n <namespace> --grace-period=0 --force
  ```

---

### 12.7 `GracefulTerminationTimeout` (App Ignored SIGTERM)
* **📖 Plain English Explanation:**  
  During pod deletion, Kubelet sends `SIGTERM` (Signal 15) asking the application to close database connections and shut down cleanly. The app ignored the signal. After `terminationGracePeriodSeconds` (default 30s), Kubelet sent a forceful `SIGKILL` (Exit 137).
* **🛠️ Step-by-Step Fix:**  
  Add `SIGTERM` signal handlers in your application code:
  ```javascript
  process.on('SIGTERM', () => {
    server.close(() => process.exit(0));
  });
  ```

---

### 12.8 `Unknown` / `NodeLost` / `NodeNotReady`
* **📖 Plain English Explanation:**  
  The Kubernetes control plane stopped receiving heartbeat pings from the worker node for more than 40 seconds.
* **🛠️ Step-by-Step Fix:**  
  Check physical VM status in cloud console and restart Kubelet: `sudo systemctl restart kubelet`.

---

## 🔄 13. Workload Controller Deadlocks & Autoscaler Failures

---

### 13.1 `ProgressDeadlineExceeded` (Deployment Rollout Stuck)
* **📖 Plain English Explanation:**  
  A Deployment rollout failed to achieve ready status within `progressDeadlineSeconds` (default 600s / 10 mins) because new pods keep crashing.
* **🛠️ Step-by-Step Fix:**  
  Roll back to the previous stable revision:
  ```bash
  kubectl rollout undo deployment/<deployment-name>
  ```

---

### 13.2 `StatefulSetOrdinalDeadlock` (StatefulSet Pod Order Blocked)
* **📖 Plain English Explanation & Beginner Analogy:**  
  A relay race where Runner #2 cannot start running until Runner #1 hands over the baton. If Runner #1 falls down, the entire race halts.
* **⚙️ What Happens Under the Hood:**  
  By default, StatefulSets enforce `podManagementPolicy: OrderedReady`. Pod `app-1` will **NEVER** be created until Pod `app-0` is fully `Running` and `Ready`.
* **🛠️ Step-by-Step Fix:**  
  Fix the issue on Pod 0 (`kubectl describe pod app-0`) or switch to `podManagementPolicy: Parallel`.

---

### 13.3 `PodDisruptionBudgetViolation` (`Cannot evict pod: PDB violated`)
* **📖 Plain English Explanation:**  
  `kubectl drain` is rejected by the API server because evicting the pod would drop available replicas below the minimum defined in the `PodDisruptionBudget`.
* **🛠️ Step-by-Step Fix:**  
  Ensure other replicas are healthy before draining the node.

---

### 13.4 `HPAUnableToComputeMetrics` / `ScalingLimited`
* **📖 Plain English Explanation:**  
  Horizontal Pod Autoscaler displays `TARGETS: <unknown>/50%` because `metrics-server` is not installed or container specs lack `resources.requests.cpu`.
* **🛠️ Step-by-Step Fix:**  
  Always define `resources.requests.cpu` on all containers managed by HPA.

---

### 13.5 `KEDATriggerError` (External Event Source Down)
* **📖 Plain English Explanation:**  
  KEDA autoscaler failed to query external metrics (RabbitMQ queue depth, Kafka lag, AWS SQS) due to network or authentication errors.
* **🛠️ Step-by-Step Fix:**  
  Inspect KEDA ScaledObject: `kubectl describe scaledobject <name>`.

---

### 13.6 `ClusterAutoscalerScaleUpFailed` (Cloud Quota Reached)
* **📖 Plain English Explanation:**  
  Cluster Autoscaler tried to launch new cloud VMs for `Pending` pods, but the cloud provider (AWS/GCP/Azure) rejected the API call due to cloud account vCPU limits.
* **🛠️ Step-by-Step Fix:**  
  Request a vCPU quota increase in your cloud provider management console.

---

## 🛡️ 14. Specialized Workload & Infrastructure Failures

---

### 14.1 `CronJobMissedSchedule` / `CannotDetermineTimeZone`
* **📖 Plain English Explanation:**  
  A CronJob missed its scheduled execution time because the previous job run took too long (with `concurrencyPolicy: Forbid`) or timezone parsing failed.
* **🛠️ Step-by-Step Fix:**  
  Set `startingDeadlineSeconds: 300` on the CronJob spec.

---

### 14.2 `DaemonSetRolloutBlocked` (Node Taints / Master Node Exclusion)
* **📖 Plain English Explanation:**  
  A DaemonSet (e.g. logging agent, CNI plugin) is missing from control-plane nodes because it lacks the `node-role.kubernetes.io/control-plane:NoSchedule` toleration.
* **🛠️ Step-by-Step Fix:**  
  Add master node tolerations to the DaemonSet pod template.

---

### 14.3 `GatewayAPI / Ingress 502 / 504 Bad Gateway`
* **📖 Plain English Explanation:**  
  The Ingress controller received the HTTP request, but the backend Pod timed out (`504`) or rejected the connection on the target port (`502`).
* **🛠️ Step-by-Step Fix:**  
  Verify that the Kubernetes Service `targetPort` matches the container's actual listening port.

---

### 14.4 `x509: CertificateExpired` / `Certificate Signed by Unknown Authority`
* **📖 Plain English Explanation:**  
  Kubeadm control-plane certificates or admission webhook TLS certificates expired after 1 year.
* **🛠️ Step-by-Step Fix:**  
  Renew Kubeadm certs: `sudo kubeadm certs renew all`.

---

### 14.5 `IPv6DualStackAllocationFailed`
* **📖 Plain English Explanation:**  
  In Dual-Stack IPv4/IPv6 clusters, the CNI failed to assign both IPv4 and IPv6 addresses.
* **🛠️ Step-by-Step Fix:**  
  Verify dual-stack pod CIDRs on worker nodes: `kubectl get node <name> -o yaml | grep -A 4 podCIDRs`.

---

### 14.6 `WindowsNodeContainerFailed` (HNS Network & Isolation Mismatch)
* **📖 Plain English Explanation:**  
  On Windows Server worker nodes, the container OS build version (e.g. `ltsc2022`) does not match the host Windows OS build (`ltsc2019`).
* **🛠️ Step-by-Step Fix:**  
  Match container image OS versions with host node OS builds or use Hyper-V isolation (`isolation: hyperv`).

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
