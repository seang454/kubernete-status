# 🚀 The Ultimate Kubernetes Pod Statuses, Errors & Exit Codes Guide

> A beginner-friendly, real-world troubleshooting guide covering all Kubernetes Pod lifecycle phases, error states, exit codes, and step-by-step diagnostic fixes.

---

## 📌 Table of Contents

- [🧠 0. Understanding the Pod Lifecycle & States](#-0-understanding-the-pod-lifecycle--states)
- [🛠️ Diagnostic Toolbox: Essential Debugging Commands](#️-diagnostic-toolbox-essential-debugging-commands)
- [📑 1. Scheduling & Node Assignment Errors](#-1-scheduling--node-assignment-errors-before-pod-starts)
- [📦 2. Image & Registry Errors](#-2-image--registry-errors)
- [⚙️ 3. Configuration & Secret Errors](#️-3-configuration--secret-errors)
- [💾 4. Storage & Volume Mounting Errors](#-4-storage--volume-mounting-errors)
- [🔄 5. Init Container Errors](#-5-init-container-errors)
- [💥 6. Runtime, Crash & Resource Errors](#-6-runtime-crash--resource-errors)
- [🏥 7. Health Check (Probes) & Network Errors](#-7-health-check-probes--network-errors)
- [🚪 8. Eviction, Preemption & Teardown Errors](#-8-eviction-preemption--teardown-errors)
- [🔢 9. Container Exit Codes Reference Table](#-9-container-exit-codes-reference-table)
- [🗺️ 10. Step-by-Step Troubleshooting Flowchart](#️-10-step-by-step-troubleshooting-flowchart)

---

## 🧠 0. Understanding the Pod Lifecycle & States

Before diving into errors, it's important to understand the standard Pod lifecycle. A Kubernetes Pod generally goes through these high-level phases:

```
[ Pod Created ] 
       │
       ▼
 [ Pending ] ──► (Scheduling / Pulling Images / Initializing)
       │
       ▼
 [ Running ] ──► (Containers executing & Probes passing)
       │
   ┌───┴───────────────┐
   ▼                   ▼
[ Succeeded ]      [ Failed ]
(Jobs finished)    (App crashed / Error)
```

### High-Level Pod Phases:
| Phase | Meaning |
| :--- | :--- |
| **`Pending`** | Pod has been accepted by the cluster, but one or more containers are not yet created or running (waiting for scheduling, image download, init containers, or storage). |
| **`Running`** | Pod is bound to a node and all containers have been created. At least one container is currently running or in the process of starting/restarting. |
| **`Succeeded`** | All containers in the Pod completed successfully with exit code `0` and will not restart (e.g., completed Batch Jobs or DB migration scripts). |
| **`Failed`** | All containers in the Pod terminated, and at least one container failed (exited with a non-zero exit code). |
| **`Unknown`** | The state of the Pod cannot be obtained, usually due to a communication failure between the Master control plane and the Worker Node. |

---

## 🛠️ Diagnostic Toolbox: Essential Debugging Commands

When a Pod fails, run these commands in sequence to inspect the issue:

```bash
# 1. Check Pod status and restart counts across namespaces
kubectl get pods -A -o wide

# 2. Inspect events, state transitions, and root causes (The #1 most useful command!)
kubectl describe pod <pod-name> -n <namespace>

# 3. View current application logs
kubectl logs <pod-name> -n <namespace> -c <container-name>

# 4. View logs of a crashed container before its last restart
kubectl logs <pod-name> -n <namespace> -c <container-name> --previous

# 5. Check cluster-wide recent warning events
kubectl get events --sort-by='.metadata.creationTimestamp' -A

# 6. Check node health and available resources
kubectl describe node <node-name>
kubectl top nodes
kubectl top pods -n <namespace>
```

---

## 📑 1. Scheduling & Node Assignment Errors (Before Pod starts)

These errors occur when the Kubernetes Scheduler (`kube-scheduler`) is trying to place your Pod onto a suitable Worker Node.

---

### 1.1 `Pending`
* **What It Means:** The Pod definition has been accepted by Kubernetes, but it has not been scheduled onto a node or cannot start its containers yet.
* 🎭 **Real Scenario:** You deploy a new microservice that requests `4 CPU` cores and `8 GB RAM`, but all nodes in your development cluster only have `2 CPU` and `4 GB RAM` total. The scheduler cannot find any node capable of hosting it, so the Pod stays `Pending` forever.
* **Root Causes:**
  - Insufficient CPU, Memory, or GPU capacity on worker nodes.
  - Required PersistentVolumeClaim (PVC) is not bound or storage driver is unavailable.
  - All nodes have taints that the Pod does not tolerate.
  - NodeSelector or NodeAffinity requirements cannot be met.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  ```
  Look at the bottom section under **Events**. You will see: `0/3 nodes are available: 3 Insufficient cpu`.
* 💡 **How to Fix:**
  - Reduce the container's `resources.requests` in your YAML.
  - Add more worker nodes to the cluster or enable Cluster Autoscaler.
  - Check PVC status with `kubectl get pvc`.

---

### 1.2 `FailedScheduling`
* **What It Means:** The Kubernetes Scheduler actively attempted to find an eligible node from the list of all cluster nodes and failed all filtering steps.
* 🎭 **Real Scenario:** Your team created specialized GPU nodes and applied a taint `gpu=true:NoSchedule` to prevent standard apps from wasting GPU resources. You deploy your app without specifying a toleration for this taint. The scheduler rejects all GPU nodes and standard nodes lack resources.
* **Root Causes:**
  - Nodes have untolerated Taints (`NoSchedule` or `NoExecute`).
  - Pod Anti-Affinity rules prevent placing two instances on the same host.
  - Cluster node limit reached (e.g., maximum pods per node `maxPods: 110`).
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Example Event:
  # Warning  FailedScheduling  pod/backend  0/5 nodes available: 2 node(s) had untolerated taint, 3 Insufficient memory.
  ```
* 💡 **How to Fix:**
  - Add appropriate `tolerations` in the Pod spec if intended for dedicated nodes:
    ```yaml
    tolerations:
    - key: "gpu"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
    ```
  - Check Node taints: `kubectl describe node <node-name> | grep -i taints`.

---

### 1.3 `MatchNodeSelector` / `NodeAffinity`
* **What It Means:** The Pod explicitly requested to be placed on a node with specific labels (e.g. SSD disk, specific cloud region, ARM/x86 CPU architecture), but no matching node exists.
* 🎭 **Real Scenario:** You build a Docker container for Linux `arm64` (Apple Silicon M-series or AWS Graviton) and set `nodeSelector: { "kubernetes.io/arch": "arm64" }`. However, all your Kubernetes nodes are standard Intel `amd64` machines.
* **Root Causes:**
  - Typo in node label name or value in `nodeSelector` or `nodeAffinity`.
  - Required nodes are currently offline, drained, or deleted.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl get nodes --show-labels
  kubectl describe pod <pod-name>
  ```
* 💡 **How to Fix:**
  - Verify node labels match your YAML: `kubectl label nodes <node-name> disktype=ssd`.
  - Fix typos in `nodeSelector` or switch to soft affinity (`preferredDuringSchedulingIgnoredDuringExecution`).

---

## 📦 2. Image & Registry Errors

These errors occur when the container runtime (`containerd` or `CRI-O`) on the assigned worker node fails to download or verify your container image.

---

### 2.1 `ErrImagePull`
* **What It Means:** The worker node failed on its initial attempt to download (pull) the container image from the container registry (Docker Hub, GitHub Container Registry, AWS ECR, Harbor, etc.).
* 🎭 **Real Scenario:** You pushed your Docker image as `myorg/payment-service:v1.2.0`, but in the Kubernetes deployment YAML you wrote `myorg/payment-service:1.2.0` (missing the `v`). The registry returns HTTP 404 (Not Found).
* **Root Causes:**
  - Typo in image name, repository, or tag.
  - The repository is private and the Pod lacks `imagePullSecrets`.
  - Node DNS or outbound internet connection is down.
  - Image tag does not exist.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Example Event:
  # Warning  Failed  kubelet  Failed to pull image "myorg/api:v1": rpc error: code = NotFound desc = failed to pull and unpack image
  ```
* 💡 **How to Fix:**
  - Verify the image tag exists by running `docker pull <image>` locally.
  - For private registries, create a secret and attach it:
    ```bash
    kubectl create secret docker-registry regcred \
      --docker-server=https://index.docker.io/v1/ \
      --docker-username=<user> \
      --docker-password=<pass> \
      --docker-email=<email>
    ```
    ```yaml
    spec:
      imagePullSecrets:
      - name: regcred
    ```

---

### 2.2 `ImagePullBackOff`
* **What It Means:** Kubernetes tried to pull the image, failed (`ErrImagePull`), and is now waiting exponentially (5s, 10s, 20s, up to 5 minutes) before trying again to avoid flooding the registry.
* 🎭 **Real Scenario:** Your CI/CD pipeline deploys 50 Pods across a cluster using public Docker Hub images without authentication. Docker Hub enforces an anonymous pull rate limit (HTTP 429 Too Many Requests). All nodes get blocked, and pods transition into `ImagePullBackOff`.
* **Root Causes:**
  - Direct continuation of `ErrImagePull`.
  - Docker Hub rate limit reached (HTTP 429).
  - Registry authorization expired or invalid registry token.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Look for: "toomanyrequests: You have reached your unauthenticated pull rate limit"
  ```
* 💡 **How to Fix:**
  - Resolve the underlying `ErrImagePull` cause.
  - Configure authenticated credentials with `imagePullSecrets` or mirror public images to an internal registry.

---

### 2.3 `InvalidImageName`
* **What It Means:** The image name provided in the Pod specification contains invalid syntax or illegal characters according to Docker/OCI naming conventions.
* 🎭 **Real Scenario:** You accidentally configured an environment variable inside the image name field, resulting in `image: "my-app:$(BUILD_NUMBER)"` or uppercase letters like `MyDockerApp:V1`.
* **Root Causes:**
  - Uppercase letters in repository name (Docker image names must be lowercase).
  - Spaces, unescaped special characters, or unclosed quotes in the image path.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  ```
* 💡 **How to Fix:**
  - Fix the image name syntax to conform to OCI standards (all lowercase repository names, valid tag characters `[a-zA-Z0-9_.-]`).

---

### 2.4 `ErrImageNeverPull`
* **What It Means:** You set `imagePullPolicy: Never`, which tells Kubernetes the image must ALREADY exist in the local node's cache, but the image is missing on the assigned worker node.
* 🎭 **Real Scenario:** You are working on Minikube or Kind. You built an image on your host machine with `docker build -t test-app:local .` without loading it into the cluster node runtime or using `minikube image load test-app:local`.
* **Root Causes:**
  - `imagePullPolicy: Never` is configured, but the node has never pulled or cached that image.
  - Multi-node cluster where the image was built locally on Node 1, but the Pod was scheduled on Node 2.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Event: Container image "local-app:v1" is not present with pull policy of Never
  ```
* 💡 **How to Fix:**
  - Change `imagePullPolicy` to `IfNotPresent` or `Always`.
  - Load the image into local clusters:
    - Minikube: `minikube image load <image-name>`
    - Kind: `kind load docker-image <image-name>`
    - K3s: `docker save <image> | sudo k3s ctr images import -`

---

### 2.5 `ImageInspectError`
* **What It Means:** The container runtime downloaded the image, but failed when trying to unpack, read, or inspect the image filesystem manifest/metadata.
* 🎭 **Real Scenario:** A network interruption occurred halfway through an image download or the worker node's disk ran out of space during decompression, resulting in a corrupted image layer in `/var/lib/containerd`.
* **Root Causes:**
  - Corrupted layer in local container storage.
  - Incompatible image manifest format for the installed runtime version.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  ```
* 💡 **How to Fix:**
  - SSH into the node and clean the cache: `crictl rmi <image-id>` or `docker rmi <image-id>`.
  - Re-trigger Pod creation: `kubectl delete pod <pod-name>`.

---

## ⚙️ 3. Configuration & Secret Errors

These errors occur when Kubernetes is preparing the container's environment (environment variables, ConfigMaps, Secrets, volume mounts) right before launching the container process.

---

### 3.1 `CreateContainerConfigError`
* **What It Means:** Kubernetes cannot create the container configuration because a referenced `ConfigMap` or `Secret` is missing, misspelled, or not found in the same namespace.
* 🎭 **Real Scenario:** Your backend deployment references a secret for database passwords:
  ```yaml
  env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secrets-prod  # <-- You haven't created this secret yet!
          key: password
  ```
  Since `app-secrets-prod` does not exist, Kubernetes cannot start the container.
* **Root Causes:**
  - Referenced `ConfigMap` or `Secret` does not exist in the Pod's namespace.
  - Referenced key inside the ConfigMap/Secret does not match (case-sensitive).
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Look for: Error: configmap "app-config" not found OR secret "db-secrets" not found
  ```
* 💡 **How to Fix:**
  - List existing ConfigMaps & Secrets:
    ```bash
    kubectl get configmaps -n <namespace>
    kubectl get secrets -n <namespace>
    ```
  - Create the missing resource or fix the naming typo in your deployment YAML.
  - If the secret/config is optional, add `optional: true`.

---

### 3.2 `CreateContainerError`
* **What It Means:** The container runtime failed while attempting to construct the container container sandbox.
* 🎭 **Real Scenario:** You specified two different volume mounts pointing to the exact same `mountPath` (e.g. `/etc/config` mounted twice), or configured an invalid Linux security capability that the node kernel does not support.
* **Root Causes:**
  - Conflicting or duplicate `mountPath` declarations in the container spec.
  - Invalid `securityContext` settings (e.g., `readOnlyRootFilesystem: true` while the app tries to create temp files in `/tmp`).
  - Requesting privileged operations on a cluster with Pod Security Standards (PSS) / OPA Gatekeeper restrictions.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  ```
* 💡 **How to Fix:**
  - Verify volume mounts have unique destination paths.
  - Review and adjust `securityContext` rules.

---

### 3.3 `RunContainerError`
* **What It Means:** The container was created successfully, but failed immediately when the runtime attempted to execute the entrypoint process.
* 🎭 **Real Scenario:** You wrote an entrypoint script `entrypoint.sh` on Windows with `CRLF` line endings or built a Docker image where the binary `/app/start.sh` does not have executable permissions (`chmod +x`).
* **Root Causes:**
  - Permission Denied (`126`) on entrypoint script or executable.
  - Windows CRLF line endings in Linux shell scripts (`/bin/sh^M: bad interpreter`).
  - Attempting to allocate a TTY (`tty: true`) when not supported.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Example: failed to start container: exec: "/app/entrypoint.sh": permission denied
  ```
* 💡 **How to Fix:**
  - Ensure execution permissions in `Dockerfile`: `RUN chmod +x /app/entrypoint.sh`.
  - Fix line endings with `dos2unix` or in your IDE before building images.

---

### 3.4 `PostStartHookError` / `PreStopHookFailed`
* **What It Means:** A lifecycle hook (`postStart` or `preStop` handler) configured in the Pod spec failed or timed out.
* 🎭 **Real Scenario:** You added a `postStart` HTTP hook to notify an external discovery service when the container starts. The external service is down or returns a 500 error, causing Kubernetes to immediately kill the container.
* **Root Causes:**
  - PostStart hook command exited with non-zero exit code.
  - PreStop hook took longer than `terminationGracePeriodSeconds`.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  ```
* 💡 **How to Fix:**
  - Check the hook command / script logic. Ensure `postStart` handlers are asynchronous or complete quickly.

---

## 💾 4. Storage & Volume Mounting Errors

These errors occur when a Pod requires persistent disks (AWS EBS, GCP Persistent Disk, Azure Disk, Longhorn, Ceph, NFS) that fail to attach or mount.

---

### 4.1 `ContainerCreating` (Stuck)
* **What It Means:** The Pod is stuck in `ContainerCreating` state for several minutes, waiting for disk attachment or network interface setup.
* 🎭 **Real Scenario:** A node in your AWS cluster crashed abruptly. Its EBS volume is still marked as attached to the dead node in AWS EC2. When Kubernetes attempts to recreate the Pod on a new node, AWS refuses to attach the disk (`Multi-Attach error for volume`).
* **Root Causes:**
  - Cloud storage volume is still locked/attached to an old terminated node.
  - Calico / Flannel / Cilium CNI failed to allocate an IP address from the subnet.
  - Secret or ConfigMap mounted as volume is missing.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  ```
  Look for `AttachVolume.Attach failed` or `CNI failed to set up pod network`.
* 💡 **How to Fix:**
  - For volume locks: Detach the stuck volume in your cloud console or wait for the CSI driver timeout.
  - For CNI IP exhaustion: Check IP pool capacity (`kubectl get ippools` in Calico).

---

### 4.2 `FailedMount` & `FailedAttachVolume`
* **What It Means:** 
  - `FailedAttachVolume`: The storage controller failed to attach the physical disk from the cloud provider to the VM.
  - `FailedMount`: The disk is attached to the VM, but the Linux filesystem cannot mount it to the container directory.
* 🎭 **Real Scenario 1 (FailedAttachVolume):** The worker node is located in AWS availability zone `us-east-1a`, but the EBS volume (PV) was created in `us-east-1b`. Cross-AZ volume attachment is physically impossible.
* 🎭 **Real Scenario 2 (FailedMount):** A previous crash caused filesystem corruption on the disk, or the disk filesystem is read-only.
* **Root Causes:**
  - Availability Zone (AZ) mismatch between PV and Node.
  - Cloud IAM permissions missing on worker node (e.g. AWS `ec2:AttachVolume` denied).
  - Storage provider reached IOPS / volume count limit per EC2 instance.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  kubectl get pv,pvc -n <namespace>
  ```
* 💡 **How to Fix:**
  - Ensure storage class uses topology-aware volume binding:
    ```yaml
    volumeBindingMode: WaitForFirstConsumer
    ```
  - Verify Cloud IAM permissions for your CSI driver.

---

## 🔄 5. Init Container Errors

Init Containers run *before* app containers. If an Init Container fails, main application containers **will never start**.

---

### 5.1 `Init:0/1` / `Init:1/2` (Waiting or Stuck)
* **What It Means:** The Pod is currently waiting for one or more Init Containers to finish executing successfully.
* 🎭 **Real Scenario:** You configured an Init Container running `until nc -z postgres-service 5432; do sleep 2; done` to wait for PostgreSQL. If PostgreSQL is offline or the service name has a typo, the Init Container loops indefinitely and stays in `Init:0/1`.
* **Root Causes:**
  - Init container is waiting on a downstream dependency (database, microservice, API).
  - Init container is performing a very large download or heavy data migration.
* 🔍 **How to Diagnose:**
  ```bash
  # Check logs of the specific init container
  kubectl logs <pod-name> -c <init-container-name>
  ```
* 💡 **How to Fix:**
  - Check why the external dependency is not responding.
  - Add timeout logic or verify service discovery DNS.

---

### 5.2 `Init:CrashLoopBackOff` & `Init:Error`
* **What It Means:**
  - `Init:Error`: The Init container started and immediately crashed with a non-zero exit code.
  - `Init:CrashLoopBackOff`: Kubernetes is retrying to execute the crashed Init container with an exponential backoff delay.
* 🎭 **Real Scenario:** An Init Container runs database schema migrations (`flyway` or `prisma migrate deploy`). The migration script hits an invalid SQL statement or invalid database credentials, throws an error, and terminates with exit code `1`.
* **Root Causes:**
  - Database migration syntax error or bad credentials.
  - Permission denied when writing to a shared `emptyDir` volume (`chmod 777` needed).
* 🔍 **How to Diagnose:**
  ```bash
  kubectl logs <pod-name> -c <init-container-name>
  kubectl describe pod <pod-name>
  ```
* 💡 **How to Fix:**
  - Inspect the init container log output, fix the script/migration error, and update the deployment.

---

### 5.3 `Init:ImagePullBackOff`
* **What It Means:** The Init container image itself cannot be downloaded from the container registry.
* 🎭 **Real Scenario:** You used an alpine image `alpine:latestt` (typo) for your Init container.
* 💡 **How to Fix:**
  - Apply the same fixes as section [2.1 ErrImagePull](#21-errimagepull).

---

## 💥 6. Runtime, Crash & Resource Errors

These are the most common application-level errors encountered when containers execute code and interact with system resources.

---

### 6.1 `Error`
* **What It Means:** The container started, executed, and terminated with a non-zero exit code (e.g., exit code 1).
* 🎭 **Real Scenario:** A Python Flask app starts up, reads an environment variable `DATABASE_URL`, finds it empty, raises an unhandled `KeyError: 'DATABASE_URL'`, and exits immediately.
* **Root Causes:**
  - Unhandled application runtime exception.
  - Missing mandatory configuration or environment variable.
  - Port collision (attempting to bind to a port already in use).
* 🔍 **How to Diagnose:**
  ```bash
  kubectl logs <pod-name>
  # If already restarted:
  kubectl logs <pod-name> --previous
  ```
* 💡 **How to Fix:**
  - Fix the exception inside your application code or provide the missing configuration.

---

### 6.2 `CrashLoopBackOff`
* **What It Means:** The container crashes repeatedly after starting. Kubernetes tries to restart it, applying exponential backoff delay (10s → 20s → 40s → 80s → up to 5 minutes) to protect system CPU/disk from crash loops.
* 🎭 **Real Scenario:** Your Java Spring Boot app requires 1GB RAM to initialize its JVM heap. In Kubernetes, you set `resources.limits.memory: "512Mi"`. Every time the app boots, it exceeds the limit and gets killed immediately. Kubernetes restarts it forever in a `CrashLoopBackOff`.
* **Root Causes:**
  - Underlying repeated `Error` or `OOMKilled`.
  - Misconfigured command or entrypoint in the Docker image.
  - Missing dependent service (app fails fast if it cannot connect to Redis/RabbitMQ).
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Look under "Last State" -> Exit Code and Reason
  kubectl logs <pod-name> --previous
  ```
* 💡 **How to Fix:**
  - Check the previous logs with `kubectl logs <pod-name> --previous`.
  - Identify the exit code (e.g. Exit code 137 = Out of Memory, Exit code 1 = Application crash).

---

### 6.3 `OOMKilled` (Exit Code 137)
* **What It Means:** **Out Of Memory Killed**. The container consumed more memory than allowed by its `resources.limits.memory` setting, and the Linux kernel Out-Of-Memory Killer sent a `SIGKILL` (Signal 9) to protect the host machine.
* 🎭 **Real Scenario:** An image processing microservice receives a batch upload of 4K images. The Node.js buffer expands to 1.5 GB memory, exceeding the `limits.memory: 1Gi` limit. The Linux kernel immediately kills the container.
* **Root Causes:**
  - Memory limit (`resources.limits.memory`) configured too low for application peak loads.
  - Application memory leak (e.g., unclosed database connections, growing in-memory caches).
  - Java JVM unaware of container memory constraints (JVM heap larger than container limit).
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Look for:
  # State:          Terminated
  #   Reason:       OOMKilled
  #   Exit Code:    137
  ```
* 💡 **How to Fix:**
  - Increase `resources.limits.memory` in your deployment YAML:
    ```yaml
    resources:
      requests:
        memory: "512Mi"
      limits:
        memory: "2Gi"
    ```
  - For Java: configure JVM flags `-XX:MaxRAMPercentage=75.0` or `-Xmx`.
  - Profile the application to detect memory leaks.

---

### 6.4 `Completed` (Exit Code 0)
* **What It Means:** The container completed its task successfully and exited with exit code `0`.
* 🎭 **Real Scenario:** A Kubernetes `Job` runs a backup script at midnight. Once the backup is saved to S3, the script exits cleanly with `exit 0`.
* **Is this an error?**
  - **No** for Kubernetes `Jobs` or `CronJobs` — this is the expected healthy result.
  - **Yes** if this was supposed to be a continuous web server (like Nginx or Express). If a web server container shows `Completed`, it means your process ran in the background / detached mode instead of foreground mode (e.g., running `systemctl start nginx` instead of `nginx -g 'daemon off;'`).

---

## 🏥 7. Health Check (Probes) & Network Errors

Kubernetes uses three types of Probes to verify container health: **Startup**, **Liveness**, and **Readiness**.

---

### 7.1 `Unhealthy` (Liveness Probe Failed)
* **What It Means:** The container's Liveness Probe failed `failureThreshold` times in a row. Kubernetes assumes the container is deadlocked or frozen and **kills & restarts** it.
* 🎭 **Real Scenario:** A backend API experiences a thread deadlock or CPU spike (100%), rendering the `/healthz` endpoint unresponsive. When the kubelet queries `/healthz`, the HTTP request times out. After 3 failed attempts, Kubernetes restarts the pod.
* **Root Causes:**
  - Application deadlock or unresponsive event loop.
  - Probe `timeoutSeconds` is too low for heavy load.
  - Health check endpoint performs deep database queries and times out.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Look for: Warning  Unhealthy  Liveness probe failed: HTTP probe failed with statuscode: 500
  ```
* 💡 **How to Fix:**
  - Ensure liveness endpoints are lightweight (check in-memory status, do not query all external databases).
  - Increase `timeoutSeconds` or `initialDelaySeconds` in probe settings:
    ```yaml
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 15
      timeoutSeconds: 5
      failureThreshold: 3
    ```

---

### 7.2 `ReadinessProbeFailed`
* **What It Means:** The Readiness Probe failed. The container is **NOT restarted**, but Kubernetes removes the Pod from the Service load balancer so it **receives no incoming user traffic**.
* 🎭 **Real Scenario:** Your app relies on Redis for caching. When Redis goes down, your app's `/ready` endpoint returns HTTP 503. Kubernetes keeps the container alive but stops routing user requests to it until Redis recovers.
* **Root Causes:**
  - App is still warming up, compiling JIT caches, or loading big datasets.
  - Downstream dependency (database, external API) is unreachable.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Warning  Unhealthy  Readiness probe failed: Get "http://10.244.1.5:8080/ready": dial tcp: connection refused
  ```
* 💡 **How to Fix:**
  - Check why the readiness endpoint is failing in application logs (`kubectl logs <pod-name>`).
  - Tune `initialDelaySeconds` if the application naturally takes time to warm up.

---

### 7.3 `StartupProbeFailed`
* **What It Means:** The Startup Probe timed out before the container finished booting. Kubernetes kills and restarts the container.
* 🎭 **Real Scenario:** A legacy Enterprise Java / Spring application takes 90 seconds to boot up on cold start. The liveness probe has an initial delay of only 30 seconds. By adding a `startupProbe`, you grant the app up to 2 minutes to boot without being killed prematurely.
* 💡 **How to Fix:**
  - Add or increase `startupProbe` failure allowance:
    ```yaml
    startupProbe:
      httpGet:
        path: /healthz
        port: 8080
      failureThreshold: 30
      periodSeconds: 10   # 30 * 10s = 300 seconds (5 minutes max startup window)
    ```

---

## 🚪 8. Eviction, Preemption & Teardown Errors

These errors occur when Kubernetes forcefully evicts or terminates Pods due to node resource pressure, priority scheduling, or node failure.

---

### 8.1 `Evicted`
* **What It Means:** The Pod was forcefully expelled from the node by the `kubelet` because the node ran out of critical physical resources.
* 🎭 **Real Scenario:** A rogue logging script writes 100 GB of logs to `/var/log` on the host node, filling up the disk to 95%. The kubelet triggers `DiskPressure` and begins evicting pods according to their QoS class (`BestEffort` first, then `Burstable`) to save the node from freezing.
* **Root Causes:**
  - `DiskPressure`: Node disk utilization exceeded threshold (usually >85%).
  - `MemoryPressure`: Node total available RAM is critically low.
  - `PIDPressure`: Too many OS processes running on the node.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl describe pod <pod-name>
  # Look for: Reason: Evicted, Message: The node was low on resource: ephemeral-storage.
  kubectl describe node <node-name>
  ```
* 💡 **How to Fix:**
  - Clean up node disk space (prune unused Docker/containerd images: `crictl rmi --prune`).
  - Define resource requests and limits on all containers to ensure a `Guaranteed` or `Burstable` Quality of Service (QoS) tier.
  - Set ephemeral storage limits in YAML:
    ```yaml
    resources:
      limits:
        ephemeral-storage: "2Gi"
    ```

---

### 8.2 `Preempting` / `Preempted`
* **What It Means:** A higher-priority Pod (e.g., CoreDNS, Calico CNI, or a Pod with `PriorityClass: high-priority`) needed scheduling, but the cluster was full. Kubernetes evicted your lower-priority Pod to free up CPU/RAM.
* 🎭 **Real Scenario:** A production payment service configured with high priority must scale up during a flash sale. Kubernetes evicts background data-processing worker pods with default priority.
* 💡 **How to Fix:**
  - Define `PriorityClass` objects for mission-critical workloads.
  - Add more compute nodes to prevent resource starvation.

---

### 8.3 `Terminating` (Stuck)
* **What It Means:** You deleted a Pod (or a deployment updated), but the Pod remains stuck in `Terminating` state indefinitely.
* 🎭 **Real Scenario:** Your Pod has a `finalizer` attached (e.g. from an operator or backup system like Velero), or a mounted NFS/CSI storage volume became unresponsive. The Linux kernel is stuck in an uninterruptible I/O wait state trying to unmount the volume.
* **Root Causes:**
  - Pod or associated PVC has unresolved **Finalizers**.
  - Storage volume driver cannot safely unmount the disk.
* 🔍 **How to Diagnose:**
  ```bash
  kubectl get pod <pod-name> -o yaml | grep -A 5 finalizers
  ```
* 💡 **How to Fix:**
  - Remove blocking finalizers (use with caution):
    ```bash
    kubectl patch pod <pod-name> -p '{"metadata":{"finalizers":null}}'
    ```
  - Force delete only if the underlying process is confirmed dead:
    ```bash
    kubectl delete pod <pod-name> --grace-period=0 --force
    ```

---

### 8.4 `Unknown` / `NodeLost` / `NodeNotReady`
* **What It Means:** The Kubernetes Control Plane (`kube-controller-manager`) has stopped receiving heartbeats from the `kubelet` on that worker node.
* 🎭 **Real Scenario:** A physical server lost power, a cloud VM instance was terminated by AWS Spot instance interruption, or the VPN / WireGuard network tunnel between nodes disconnected.
* 💡 **How to Fix:**
  - Check node status: `kubectl get nodes`.
  - Check kubelet daemon on the host: `systemctl status kubelet`.
  - Restart kubelet: `sudo systemctl restart kubelet`.

---

## 🔢 9. Container Exit Codes Reference Table

When you run `kubectl describe pod <name>`, inspect the **Last State** section to find the **Exit Code**. 

> 💡 **Linux Signal Rule:** Any exit code greater than `128` represents termination caused by an OS signal:
> $$\text{Exit Code} = 128 + \text{Signal Number}$$
> *Example:* `137 = 128 + 9 (SIGKILL)` | `143 = 128 + 15 (SIGTERM)`

| Exit Code | Signal / Name | Real Meaning & What Happened | Real-World Scenario & Beginner Fix |
| :---: | :---: | :--- | :--- |
| **`0`** | `SUCCESS` | Container executed its task and completed successfully. | Normal for Jobs. If an ongoing web server exits with 0, ensure the app process runs in the foreground (`daemon off`). |
| **`1`** | `GENERAL ERROR` | Container crashed due to an unhandled application error or uncaught exception. | Python `ZeroDivisionError`, Java `NullPointerException`, Node.js unhandled promise rejection. Check `kubectl logs <pod>`. |
| **`2`** | `MISUSE OF SHELL` | Invalid argument or syntax error in Pod YAML `command:` or `args:`. | Typo in bash flags, missing closing quotes in command string (e.g. `sh -c "echo hello`). |
| **`126`** | `CANNOT EXECUTE` | Command/binary found, but cannot be executed (Permission Denied). | `/entrypoint.sh` lacks execute permissions. Fix with `RUN chmod +x /entrypoint.sh` in your Dockerfile. |
| **`127`** | `COMMAND NOT FOUND` | Binary specified in entrypoint/command does not exist inside the image. | You specified `command: ["bash"]` in a minimal `alpine` or `scratch` container image that only has `/bin/sh`. |
| **`128`** | `INVALID EXIT ARG` | Container code called `exit()` with an invalid argument (outside 0-255). | Application logic bug in custom shutdown hook. |
| **`130`** | `SIGINT (128+2)` | Process terminated by user Interrupt (equivalent to `Ctrl + C`). | Container received an interrupt signal from manual debug session. |
| **`137`** | `SIGKILL (128+9)` | Container was forcefully killed by the OS kernel or Kubernetes (`kill -9`). | **1.** `OOMKilled` (exceeded memory limit).<br>**2.** Pod failed to shut down within `terminationGracePeriodSeconds` (default 30s) and was hard-killed. |
| **`139`** | `SIGSEGV (128+11)` | Segmentation Fault (Attempted to access unauthorized memory space). | Low-level memory bug in C/C++/Go/Rust code or corrupted native C-bindings (e.g. `node-sass`, `grpc`). |
| **`143`** | `SIGTERM (128+15)` | Container received graceful termination signal from Kubernetes. | Expected during Pod rollout/scaling down. Kubernetes asked the app to close connections and shut down cleanly. |
| **`255`** | `EXIT OUT OF RANGE` | Container exited with an error code outside standard range or runtime error. | Entrypoint script failed with unknown status code. |

---

## 🗺️ 10. Step-by-Step Troubleshooting Flowchart

Follow this systematic decision tree whenever you encounter an unhealthy Pod:

```
                      [ Pod is not Ready / Running ]
                                    │
                                    ▼
                     Run: `kubectl get pod <name>`
                                    │
    ┌───────────────────────────────┼───────────────────────────────┐
    ▼                               ▼                               ▼
[ Pending ]              [ ImagePullBackOff ]            [ CrashLoopBackOff ]
    │                               │                               │
    ├─► Check `kubectl describe`    ├─► Check image name/tag        ├─► Run `kubectl logs --previous`
    ├─► Node capacity (CPU/RAM)?    ├─► Private repo? (Secret)      ├─► Exit Code 137? -> More RAM
    └─► PVC Bound? Taints?          └─► Docker Hub Rate Limit?      └─► Exit Code 1? -> Fix code bug
                                                                    
    ┌───────────────────────────────┼───────────────────────────────┐
    ▼                               ▼                               ▼
[ Init:Error / Crash ]   [ ContainerCreating Stuck ]      [ Terminating Stuck ]
    │                               │                               │
    ├─► Check `kubectl logs -c init`├─► Volume attached to old node?├─► Check finalizers in YAML
    └─► DB / migration ready?       └─► CNI IP pool exhausted?      └─► Check CSI / NFS storage driver
```

---

## 📚 Summary Cheat Sheet: Quick Fix Reference

| When you see... | First thing to check... | Immediate Fix Command |
| :--- | :--- | :--- |
| **`Pending`** | CPU / RAM / Node Taints | `kubectl describe pod <name>` |
| **`ImagePullBackOff`** | Image tag typo or registry secrets | `kubectl get secrets` & check registry |
| **`CreateContainerConfigError`** | Missing ConfigMap or Secret | `kubectl get configmap,secret` |
| **`CrashLoopBackOff`** | Application logs before crash | `kubectl logs <name> --previous` |
| **`OOMKilled` (137)** | Container memory limit | Increase `resources.limits.memory` |
| **`Unhealthy` (Probes)** | Application deadlock or slow response | Tune `initialDelaySeconds` / probe timeout |
| **`Evicted`** | Worker node disk or memory usage | `kubectl describe node <node>` |
| **`FailedMount`** | Cloud PV/PVC attachment & AZ | `kubectl get pvc,pv` |

---
*Created with ❤️ for Kubernetes developers and DevOps engineers.*
