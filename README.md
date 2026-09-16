# 🚀 The Master Kubernetes Pod Statuses, Errors & Exit Codes Encyclopedia

> **The Definitive, 360° Beginner-to-Advanced Troubleshooting Guide**  
> Covering every single Kubernetes Pod Phase, Container State, Scheduling Error, Storage Glitch, Network/DNS Failure, Admission Webhook Rejection, Lifecycle Probe Breakdown, and Exit Code with **Real-World Scenarios**, **Exact Root Causes**, **Inspection Commands**, and **Step-by-Step Fixes**.

---

## 📌 Table of Contents

- [🧠 0. Pod Lifecycle Architecture, Phases & Conditions](#-0-pod-lifecycle-architecture-phases--conditions)
- [🛠️ Diagnostic Master-Kit: 10 Essential Commands](#️-diagnostic-master-kit-10-essential-commands)
- [📑 1. Scheduling & Node Placement Errors](#-1-scheduling--node-placement-errors-before-pod-starts)
  - [1.1 `Pending` (Generic Unscheduled)](#11-pending-generic-unscheduled)
  - [1.2 `FailedScheduling` (Insufficient CPU / RAM / GPU)](#12-failedscheduling-insufficient-cpu--ram--gpu)
  - [1.3 `MatchNodeSelector` / `NodeAffinity` Failure](#13-matchnodeselector--nodeaffinity-failure)
  - [1.4 `UntoleratedTaint` (`NoSchedule` / `NoExecute`)](#14-untoleratedtaint-noschedule--noexecute)
  - [1.5 `PodTopologySpreadFilter` / `AntiAffinity` Conflict](#15-podtopologyspreadfilter--antiaffinity-conflict)
  - [1.6 `VolumeZoneConflict` (Cross-AZ Disk Attachment)](#16-volumezoneconflict-cross-az-disk-attachment)
  - [1.7 `MaxPodsExceeded` / `NodeUnschedulable` (`Cordoned`)](#17-maxpodsexceeded--nodeunschedulable-cordoned)
- [📦 2. Image, OCI Registry & Runtime CRI Errors](#-2-image-oci-registry--runtime-cri-errors)
  - [2.1 `ErrImagePull`](#21-errimagepull)
  - [2.2 `ImagePullBackOff`](#22-imagepullbackoff)
  - [2.3 `InvalidImageName`](#23-invalidimagename)
  - [2.4 `ErrImageNeverPull`](#24-errimageneverpull)
  - [2.5 `ImageInspectError`](#25-imageinspecterror)
  - [2.6 `ImageArchitectureMismatch` (`exec format error`)](#26-imagearchitecturemismatch-exec-format-error)
  - [2.7 `RegistryUnauthorized` / `401 Unauthorized` / `403 Forbidden`](#27-registryunauthorized--401-unauthorized--403-forbidden)
  - [2.8 `DockerHubRateLimit` (`HTTP 429 Too Many Requests`)](#28-dockerhubratelimit-http-429-too-many-requests)
- [⚙️ 3. Container Configuration, Runtime & Security Context Errors](#️-3-container-configuration-runtime--security-context-errors)
  - [3.1 `CreateContainerConfigError`](#31-createcontainerconfigerror)
  - [3.2 `CreateContainerError`](#32-createcontainererror)
  - [3.3 `RunContainerError`](#33-runcontainererror)
  - [3.4 `RunAsNonRootError` / `MustRunAsNonRoot`](#34-runasnonrooterror--mustrunasnonroot)
  - [3.5 `ReadOnlyFilesystemError`](#35-readonlyfilesystemerror)
  - [3.6 `PostStartHookError` & `PreStopHookFailed`](#36-poststarthookerror--prestophookfailed)
- [💾 4. Storage, CSI Driver & Volume Mounting Errors](#-4-storage-csi-driver--volume-mounting-errors)
  - [4.1 `ContainerCreating` (Stuck on Volume Attachment)](#41-containercreating-stuck-on-volume-attachment)
  - [4.2 `FailedMount` & `FailedAttachVolume`](#42-failedmount--failedattachvolume)
  - [4.3 `PersistentVolumeClaimNotBound` / `ProvisioningFailed`](#43-persistentvolumeclaimnotbound--provisioningfailed)
  - [4.4 `VolumeSubpathInitializationFailed`](#44-volumesubpathinitializationfailed)
  - [4.5 `Multi-Attach error for volume` (ReadWriteOnce Conflict)](#45-multi-attach-error-for-volume-readwriteonce-conflict)
- [🔄 5. Init Container & Modern Sidecar Errors](#-5-init-container--modern-sidecar-errors)
  - [5.1 `Init:0/N` / `Init:1/N` (Stuck Waiting)](#51-init0n--init1n-stuck-waiting)
  - [5.2 `Init:CrashLoopBackOff` & `Init:Error`](#52-initcrashloopbackoff--initerror)
  - [5.3 `Init:ImagePullBackOff`](#53-initimagepullbackoff)
  - [5.4 `ServiceMeshSidecarNotReady` (Istio/Envoy / Linkerd Proxy Delay)](#54-servicemeshsidecarnotready-istioenvoy--linkerd-proxy-delay)
- [💥 6. Runtime Crashes, Memory & Resource Starvation](#-6-runtime-crashes-memory--resource-starvation)
  - [6.1 `Error` (Non-Zero Exit Code)](#61-error-non-zero-exit-code)
  - [6.2 `CrashLoopBackOff`](#62-crashloopbackoff)
  - [6.3 `OOMKilled` (Exit Code 137 - Out of Memory)](#63-oomkilled-exit-code-137---out-of-memory)
  - [6.4 `Completed` (Exit Code 0 on Web Servers)](#64-completed-exit-code-0-on-web-servers)
  - [6.5 `ContainerStatusUnknown`](#65-containerstatusunknown)
- [🏥 7. Health Checks (Probes) & Networking/DNS Errors](#-7-health-checks-probes--networkingdns-errors)
  - [7.1 `Unhealthy` (Liveness Probe Failed)](#71-unhealthy-liveness-probe-failed)
  - [7.2 `ReadinessProbeFailed` (Traffic Cut Off)](#72-readinessprobefailed-traffic-cut-off)
  - [7.3 `StartupProbeFailed` (Slow Boot Timeout)](#73-startupprobefailed-slow-boot-timeout)
  - [7.4 `NetworkNotReady` / `CNINetworkError`](#74-networknotready--cninetworkerror)
  - [7.5 `CoreDNS CrashLoopBackOff` & DNS Lookup Timeouts](#75-coredns-crashloopbackoff--dns-lookup-timeouts)
  - [7.6 `AdmissionWebhookDenied` / `WebhookTimeout`](#76-admissionwebhookdenied--webhooktimeout)
- [🚪 8. Node Pressure, Eviction, Preemption & Teardown Errors](#-8-node-pressure-eviction-preemption--teardown-errors)
  - [8.1 `Evicted` (`DiskPressure`, `MemoryPressure`, `PIDPressure`)](#81-evicted-diskpressure-memorypressure-pidpressure)
  - [8.2 `Preempted` / `Preempting` (`PriorityClass`)](#82-preempted--preempting-priorityclass)
  - [8.3 `Terminating` (Stuck on Finalizers or Unmount)](#83-terminating-stuck-on-finalizers-or-unmount)
  - [8.4 `Unknown` / `NodeLost` / `NodeNotReady`](#84-unknown--nodelost--nodenotready)
- [🔢 9. Master Container Exit Codes & OS Signals Table](#-9-master-container-exit-codes--os-signals-table)
- [🗺️ 10. The Ultimate 60-Second Troubleshooting Decision Tree](#️-10-the-ultimate-60-second-troubleshooting-decision-tree)

---

## 🧠 0. Pod Lifecycle Architecture, Phases & Conditions

### The Pod Lifecycle Pipeline

```
[ Pod Object Created via API ]
              │
              ▼
   [ Phase: Pending ] ──► (kube-scheduler assigns a Node)
              │
              ▼
  [ Node Kubelet Takes Over ]
   ├─► Attach & Mount Storage Volumes (CSI)
   ├─► Allocate Network & IP Address (CNI)
   ├─► Pull Container Images (CRI)
   ├─► Execute Init Containers (Sequence 1..N)
   └─► Start Main App Containers & Native Sidecars
              │
              ▼
   [ Phase: Running ] ──► (Startup & Readiness Probes Pass)
              │
      ┌───────┴───────┐
      ▼               ▼
[ Phase: Succeeded ] [ Phase: Failed ]
(Exit 0 / Jobs)      (Non-zero Exit / Crash)
```

### Pod Conditions (Boolean Health Flags)
Run `kubectl get pod <name> -o yaml` and inspect `status.conditions`:
* **`PodScheduled` (`True`/`False`):** Has the Scheduler successfully assigned the Pod to a node?
* **`Initialized` (`True`/`False`):** Have all Init Containers finished successfully?
* **`ContainersReady` (`True`/`False`):** Are all containers in the Pod ready?
* **`Ready` (`True`/`False`):** Is the Pod ready to serve incoming traffic through Kubernetes Services?
* **`DisruptionTarget` (`True`):** The Pod is being terminated due to node drain, eviction, or preemption.

---

## 🛠️ Diagnostic Master-Kit: 10 Essential Commands

```bash
# 1. Quick overview of all pods, nodes, IP addresses, and restarts
kubectl get pods -o wide -A

# 2. The #1 command: Detailed events, state reasons, and probe failures
kubectl describe pod <pod-name> -n <namespace>

# 3. View live application logs
kubectl logs <pod-name> -n <namespace> -c <container-name>

# 4. View logs of the container BEFORE it crashed
kubectl logs <pod-name> -n <namespace> -c <container-name> --previous

# 5. Follow live stream logs with timestamps
kubectl logs -f <pod-name> -n <namespace> --timestamps

# 6. Stream logs of an Init Container
kubectl logs <pod-name> -n <namespace> -c <init-container-name>

# 7. List recent cluster warnings and scheduling failures sorted by time
kubectl get events -n <namespace> --sort-by='.metadata.creationTimestamp'

# 8. Check node resource consumption (CPU / Memory pressure)
kubectl top nodes
kubectl top pods -n <namespace>

# 9. Dump full JSON/YAML status of a stuck Pod
kubectl get pod <pod-name> -n <namespace> -o yaml

# 10. Launch an interactive debug container directly into the Pod's network/namespace
kubectl debug -it <pod-name> --image=nicolaka/netshoot --target=<container-name>
```

---

## 📑 1. Scheduling & Node Placement Errors (Before Pod starts)

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

### 1.2 `FailedScheduling` (Insufficient CPU / RAM / GPU)
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

### 1.3 `MatchNodeSelector` / `NodeAffinity` Failure
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

### 1.4 `UntoleratedTaint` (`NoSchedule` / `NoExecute`)
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

### 1.5 `PodTopologySpreadFilter` / `AntiAffinity` Conflict
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

### 1.6 `VolumeZoneConflict` (Cross-AZ Disk Attachment)
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

### 1.7 `MaxPodsExceeded` / `NodeUnschedulable` (`Cordoned`)
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

## 📦 2. Image, OCI Registry & Runtime CRI Errors

---

### 2.1 `ErrImagePull`
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

### 2.2 `ImagePullBackOff`
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

### 2.3 `InvalidImageName`
* **📖 What It Means:** The image path violates Docker/OCI syntax specifications.
* 🎭 **Real-World Scenario:** An unrendered CI/CD template variable resulted in `image: "docker.io/repo/app:${BUILD_ID}"` or contained uppercase characters like `MyCompany/API:v1`.
* 🔬 **Root Causes:** Uppercase letters in repository names, spaces, or illegal punctuation.
* 🛠️ **Step-by-Step Fix:** Convert repository names to lowercase and verify variable substitution in CI pipelines.

---

### 2.4 `ErrImageNeverPull`
* **📖 What It Means:** The Pod specifies `imagePullPolicy: Never`, requiring the image to be pre-cached on the node, but the image is absent.
* 🎭 **Real-World Scenario:** In local development (Minikube / Kind), you built `my-app:dev` on your host laptop Docker engine, but did not load it into the Minikube/Kind VM node.
* 🛠️ **Step-by-Step Fix:**
  - Load into Kind: `kind load docker-image my-app:dev --name <cluster-name>`.
  - Load into Minikube: `minikube image load my-app:dev`.
  - Or change `imagePullPolicy` to `IfNotPresent`.

---

### 2.5 `ImageInspectError`
* **📖 What It Means:** The container runtime pulled the image layers, but could not parse the image config manifest or tar metadata.
* 🎭 **Real-World Scenario:** The worker node experienced disk corruption or ran out of disk space halfway through decompressing a 5 GB layer in `/var/lib/containerd`.
* 🛠️ **Step-by-Step Fix:**
  - SSH into the node and remove cached corrupted image:
    ```bash
    sudo crictl rmi <image-id>
    ```
  - Re-trigger pod: `kubectl delete pod <pod-name>`.

---

### 2.6 `ImageArchitectureMismatch` (`exec format error`)
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

### 2.7 `RegistryUnauthorized` / `401 Unauthorized` / `403 Forbidden`
* **📖 What It Means:** The registry rejected the pull request due to missing, invalid, or expired credentials.
* 🎭 **Real-World Scenario:** AWS ECR authorization tokens expire every 12 hours. If your cluster uses a cron job to refresh ECR tokens and the job fails, all new pod image pulls fail with `401 Unauthorized`.
* 🛠️ **Step-by-Step Fix:**
  - Refresh ECR / GCP Artifact Registry credentials or configure AWS IRSA (IAM Roles for Service Accounts) with Amazon EKS.

---

### 2.8 `DockerHubRateLimit` (`HTTP 429 Too Many Requests`)
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

## ⚙️ 3. Container Configuration, Runtime & Security Context Errors

---

### 3.1 `CreateContainerConfigError`
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

### 3.2 `CreateContainerError`
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

### 3.3 `RunContainerError`
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

### 3.4 `RunAsNonRootError` / `MustRunAsNonRoot`
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

### 3.5 `ReadOnlyFilesystemError`
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

### 3.6 `PostStartHookError` & `PreStopHookFailed`
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

## 💾 4. Storage, CSI Driver & Volume Mounting Errors

---

### 4.1 `ContainerCreating` (Stuck on Volume Attachment)
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

### 4.2 `FailedMount` & `FailedAttachVolume`
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

### 4.3 `PersistentVolumeClaimNotBound` / `ProvisioningFailed`
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

### 4.4 `VolumeSubpathInitializationFailed`
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

### 4.5 `Multi-Attach error for volume` (ReadWriteOnce Conflict)
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

## 🔄 5. Init Container & Modern Sidecar Errors

---

### 5.1 `Init:0/N` / `Init:1/N` (Stuck Waiting)
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

### 5.2 `Init:CrashLoopBackOff` & `Init:Error`
* **📖 What It Means:** The Init container crashed with a non-zero exit code (e.g. exit code 1). Main app containers will **never** start.
* 🎭 **Real-World Scenario:** An Init Container runs database schema migrations (`flyway migrate` or `prisma migrate deploy`). The database credentials in the secret are wrong, causing the migration to abort with an authentication error.
* 🔍 **How to Inspect:**
  ```bash
  kubectl logs <pod-name> -c <init-container-name> --previous
  ```
* 🛠️ **Step-by-Step Fix:**
  - Fix the database migration SQL script or credentials.

---

### 5.3 `Init:ImagePullBackOff`
* **📖 What It Means:** The Init Container's image cannot be downloaded from the container registry.
* 🛠️ **Step-by-Step Fix:** Refer to Section 2.1 to resolve registry access or image tag typos.

---

### 5.4 `ServiceMeshSidecarNotReady` (Istio/Envoy / Linkerd Proxy Delay)
* **📖 What It Means:** In clusters with Service Mesh auto-injection (Istio / Linkerd / Consul), the main app container starts and attempts outbound network requests before the Envoy sidecar proxy is fully initialized, causing immediate network failures.
* 🎭 **Real-World Scenario:** A Spring Boot app boots up, tries to connect to PostgreSQL immediately on startup, and fails with `Connection refused` because Istio Envoy proxy (`istio-proxy`) is still booting.
* 🛠️ **Step-by-Step Fix:**
  - In Kubernetes 1.28+, use native sidecar containers (`restartPolicy: Always` in `initContainers`).
  - In Istio: Enable `holdApplicationUntilProxyStarts: true` in Istio mesh config.

---

## 💥 6. Runtime Crashes, Memory & Resource Starvation

---

### 6.1 `Error` (Non-Zero Exit Code)
* **📖 What It Means:** The container started, executed application code, and exited with an error status (e.g. exit code 1).
* 🎭 **Real-World Scenario:** A Node.js backend starts up, attempts to read `process.env.JWT_SECRET`, finds it undefined, and throws an unhandled exception: `TypeError: Cannot read property 'length' of undefined`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl logs <pod-name> --previous
  ```
* 🛠️ **Step-by-Step Fix:**
  - Check the stack trace in logs and supply the missing environment variable or configuration.

---

### 6.2 `CrashLoopBackOff`
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

### 6.3 `OOMKilled` (Exit Code 137 - Out of Memory)
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

### 6.4 `Completed` (Exit Code 0 on Web Servers)
* **📖 What It Means:** The container process finished and exited cleanly with status code `0`.
* 🎭 **Real-World Scenario:** You containerized Nginx or an Express app, but the entrypoint command was `systemctl start nginx` or `node index.js &` (background daemon). Because the backgrounded process detaches, the main foreground PID 1 process terminates immediately with code 0.
* 🛠️ **Step-by-Step Fix:**
  - Ensure the main container process runs in the **foreground** as PID 1:
    - Nginx: `CMD ["nginx", "-g", "daemon off;"]`
    - Node.js: `CMD ["node", "index.js"]`

---

### 6.5 `ContainerStatusUnknown`
* **📖 What It Means:** The control plane cannot determine the container state because the worker node's container runtime or kubelet stopped reporting status.
* 🛠️ **Step-by-Step Fix:** Check node health and kubelet status: `sudo systemctl status kubelet`.

---

## 🏥 7. Health Checks (Probes) & Networking/DNS Errors

---

### 7.1 `Unhealthy` (Liveness Probe Failed)
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

### 7.2 `ReadinessProbeFailed` (Traffic Cut Off)
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

### 7.3 `StartupProbeFailed` (Slow Boot Timeout)
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

### 7.4 `NetworkNotReady` / `CNINetworkError`
* **📖 What It Means:** The node cannot start pods because the CNI plugin (Calico, Flannel, AWS VPC CNI, Cilium) is failing or cannot assign IP addresses.
* 🎭 **Real-World Scenario:** In AWS EKS, worker nodes run out of available secondary ENI private IP addresses in your subnet CIDR. New pods fail with `failed to assign an IP address to container`.
* 🔍 **How to Inspect:**
  ```bash
  kubectl describe node <node-name> | grep -i network
  kubectl get pods -n kube-system
  ```
* 🛠️ **Step-by-Step Fix:**
  - Check CNI daemonset pods in `kube-system`: `kubectl logs -n kube-system -l k8s-app=aws-node`.
  - Expand subnet CIDR or add additional subnets.

---

### 7.5 `CoreDNS CrashLoopBackOff` & DNS Lookup Timeouts
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

### 7.6 `AdmissionWebhookDenied` / `WebhookTimeout`
* **📖 What It Means:** A Validating or Mutating Admission Webhook (e.g. Kyverno, OPA Gatekeeper, Istio Injector, Cert-Manager) rejected the Pod creation request or timed out.
* 🎭 **Real-World Scenario:** OPA Gatekeeper policy requires all pods to have a `cost-center` label. You deploy without this label, and API server rejects the deployment. Or the Webhook pod is down, causing all Pod creations to time out.
* 🔍 **How to Inspect:**
  ```bash
  kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations
  ```
* 🛠️ **Step-by-Step Fix:**
  - Fix the policy violation in your YAML.
  - If a dead webhook is blocking the entire cluster, temporarily remove it:
    ```bash
    kubectl delete validatingwebhookconfiguration <broken-webhook-name>
    ```

---

## 🚪 8. Node Pressure, Eviction, Preemption & Teardown Errors

---

### 8.1 `Evicted` (`DiskPressure`, `MemoryPressure`, `PIDPressure`)
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

### 8.2 `Preempted` / `Preempting` (`PriorityClass`)
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

### 8.3 `Terminating` (Stuck on Finalizers or Unmount)
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

### 8.4 `Unknown` / `NodeLost` / `NodeNotReady`
* **📖 What It Means:** The Kubernetes control plane has lost contact with the worker node's `kubelet` for more than `node-monitor-grace-period` (default 40s).
* 🎭 **Real-World Scenario:** A physical server lost power, a network switch failed, or the cloud provider abruptly stopped a spot instance.
* 🛠️ **Step-by-Step Fix:**
  - Check physical node / VM status in cloud console.
  - Restart kubelet on the node: `sudo systemctl restart kubelet`.

---

## 🔢 9. Master Container Exit Codes & OS Signals Table

When inspecting `kubectl describe pod <name>`, check **Last State** -> **Exit Code**.

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

## 🗺️ 10. The Ultimate 60-Second Troubleshooting Decision Tree

```
                                [ Pod Is Not Healthy ]
                                          │
                                          ▼
                         Run: `kubectl describe pod <name>`
                                          │
        ┌─────────────────────────────────┼─────────────────────────────────┐
        ▼                                 ▼                                 ▼
   [ SCHEDULE ERROR ]               [ IMAGE ERROR ]                  [ RUNTIME ERROR ]
   • Pending                        • ErrImagePull                   • CrashLoopBackOff
   • FailedScheduling               • ImagePullBackOff               • OOMKilled (137)
   • UntoleratedTaint               • InvalidImageName               • Error (1)
        │                                 │                                 │
   ├─► Check CPU/RAM requests       ├─► Check tag spelling           ├─► Run `kubectl logs --previous`
   ├─► Check Node Taints & Labels   ├─► Check imagePullSecrets       ├─► Exit 137? -> Increase RAM
   └─► Check PVC status             └─► Docker Hub Rate Limit?       └─► Exit 1? -> Fix code bug
                                                                     
        ┌─────────────────────────────────┼─────────────────────────────────┐
        ▼                                 ▼                                 ▼
   [ STORAGE ERROR ]                [ PROBE ERROR ]                  [ TEARDOWN ERROR ]
   • ContainerCreating (Stuck)      • Unhealthy (Liveness)           • Terminating (Stuck)
   • FailedMount                    • ReadinessProbeFailed           • Evicted (DiskPressure)
   • Multi-Attach error             • StartupProbeFailed             • NodeLost / Unknown
        │                                 │                                 │
   ├─► Check PVC/PV binding         ├─► Increase timeout/delays      ├─► Remove finalizers
   ├─► Check AZ match (us-east-1a)  ├─► Check app CPU deadlock       ├─► Prune node images
   └─► Check CSI driver logs        └─► Separate startupProbe        └─► Restart kubelet
```

---

## 📚 Summary Cheat Sheet: Top 10 Immediate Actions

| If Pod Status Shows... | Look Here First... | Immediate Fix Command |
| :--- | :--- | :--- |
| **`Pending`** | Node capacity / Taints / PVC | `kubectl describe pod <pod>` |
| **`ImagePullBackOff`** | Tag spelling / Registry Secret | `kubectl get secrets` & check repo |
| **`CreateContainerConfigError`** | Missing ConfigMap or Secret | `kubectl get configmap,secret` |
| **`CrashLoopBackOff`** | Previous crash logs | `kubectl logs <pod> --previous` |
| **`OOMKilled` (137)** | Memory Limit vs Actual Usage | Increase `resources.limits.memory` |
| **`Unhealthy` (Liveness)** | Probe timeout & app responsiveness | Increase `initialDelaySeconds` |
| **`ReadinessProbeFailed`** | Backend dependencies (DB/Redis) | Check downstream service health |
| **`ContainerCreating (Stuck)`** | Storage volume locks / CNI IP pool | `kubectl describe pod <pod>` |
| **`Terminating (Stuck)`** | Blocking Finalizers | `kubectl patch pod <pod> -p '{"metadata":{"finalizers":null}}'` |
| **`Evicted`** | Worker node disk/memory pressure | `kubectl describe node <node>` |

---
*Created with ❤️ for Kubernetes developers, SREs, and DevOps engineers.*
