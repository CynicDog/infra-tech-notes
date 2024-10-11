<details>
<summary><h1>Kubernetes for Developers</h1></summary>
<details>
<summary><h3>CH3. Deploying to Kubernetes</h3></summary>

### Kubernetes Architecture 

Kubernetes is <ins>an abstraction layer that sits at the workload level on top of the raw compute primitives</ins> like VMs (or bare metal machines) and load balancers.

VMs are referred to as nodes and are arranged into a cluster. Containers (one or multiple) are grouped into a scheduling unit known as a Pod. Networking is configured via a Service. 

Worker nodes (herein referred to simply as nodes) are responsible for managing the lifecycle of containers that run, including tasks such as starting and stopping containers. The control plane will instruct the node to run a certain container, but the actual execution of the container is then the responsibility of the node.

The **Pod** is used as the primary scheduling unit in Kubernetes. Encompassing your application and its containers, it’s the unit of compute that Kubernetes schedules onto nodes according to the resources you require. 

A **Deployment** is a specification for the desired state of the system, which Kubernetes seeks to actuate. Kubernetes continuously reconciles the observed state to the desired state while attempting to deliver what you requested. 

**Services** are how you expose an application running on a set of Pods as a network service.

</details>
</details>

<details>
<summary><h1>Core Kubernetes</h1></summary>

<details>
<summary><h3>CH1. Why Kubernetes exists</h3></summary>

### Reviewing a few key terms before we get started 

- **CNI** and **CSI** — The container networking interface and container storage interface, respectively, that allow pluggable networking and storage for Pods that run in Kubernetes.
- **Container** — A standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.
- **Control plane** — The brains of a Kubernetes cluster, where scheduling of containers and managing all Kubernetes objects takes place (sometimes referred to as Masters).
- **DaemonSet** — Ensures that all (or some) Nodes run a copy of a Pod.
- **OCI** — The common image format for building executable, self-contained applications. Also referred to as Docker images.
- **Privileged containers** — A container that can run with elevated permissions, granting them access to all devices, capabilities, and the host's kernel features, effectively giving them the same level of access as processes running directly on the host system.

### Containers and images 

The OCI specification is a standard way to define an image that can be executed by a program such as Docker, and it ultimately is a tarball with various layers.

Containers provide a layer of isolation that eliminates the need to manage libraries on a server or preload infrastructure with unintended application dependencies.

### Core foundation of Kubernetes 

When the nodes in the cluster respond to ongoing events and update their Node objects through the kubelet's communication with the API server, things can go wrong at any time. So we refer to Kubernetes as an **<ins>eventually consistent system</ins>**, where reconciliation of the desired state over time is a key design philosophy.

Kubernetes automates the technology stack using the Kubernetes API, managed entirely as YAML and JSON resources. This includes traditional IT infrastructure rules that still apply to microservices, such as:

- Server configuration of ports or IP routes
- Persistent storage availability for applications
- Hosting software on specific or arbitrary servers
- Security provisioning, including RBAC and networking rules for application access
- DNS configuration for applications on a per-application and global basis

These components are defined in configuration files representing objects in the Kubernetes API. Kubernetes uses these building blocks to apply changes, monitor them, and address temporary failures or disruptions until the desired end state is achieved.

### Kubernetes features 

- Expose a cloud-neutral API for all functionality within the API server.
- Integrate with major cloud and hypervisor platforms through the Kubernetes controller manager (KCM).
- Provide a fault-tolerant framework to store and define the state of all services, applications, and data center configurations.
- Manage deployments to minimize user-facing downtime, whether for an individual host, service, or application.
- Automate scaling for hosts and applications, ensuring rolling updates are handled smoothly.
- Enable internal and external integrations (such as ClusterIP, NodePort, or LoadBalancer Service types) with load balancing.
- Schedule applications to run on specific virtualized hardware based on metadata, using node labeling and the Kubernetes scheduler.
- Deliver a highly available platform through DaemonSets and other technologies that prioritize container deployment on all nodes in the cluster.
- Support service discovery via a domain name service (DNS), implemented by KubeDNS and more recently by CoreDNS, which integrates with the API server.
- Run batch processes (known as Jobs) that utilize storage and containers similarly to persistent applications.
- Include API extensions and allow the creation of native API-driven programs using custom resource definitions without the need for port mappings.
- Enable inspection of failed cluster-wide processes, including remote execution into any container at any time using `kubectl exec` and `kubectl describe`.
- Allow the mounting of local or remote storage to containers and manage declarative storage volumes with the StorageClass API and PersistentVolumes.

### Kubernetes components 

Kubernetes is a state-reconciliation machine with various control loops. 

- Hardware infrastructure - Includes computers, network infrastructure, storage infrastructure and a container registry
- Kubernetes worker nodes - The base unit of compoute in a Kubernetes cluster.
- Kubernetes control plane = The mothership of Kubernetes which covers the API server, schedulers controller manager and other controllers. 

Virtually everything in Kubernetes exists to support Pods. There are 70 different API types, you can view those by running: 
<details>
  <summary>
    <code><br>kubectl api-resources<br></code>
  </summary>
  <br>

  ```bash
  NAME                                SHORTNAMES   APIVERSION                        NAMESPACED   KIND
  bindings                                         v1                                true         Binding
  componentstatuses                   cs           v1                                false        ComponentStatus
  configmaps                          cm           v1                                true         ConfigMap
  endpoints                           ep           v1                                true         Endpoints
  events                              ev           v1                                true         Event
  limitranges                         limits       v1                                true         LimitRange
  namespaces                          ns           v1                                false        Namespace
  nodes                               no           v1                                false        Node
  persistentvolumeclaims              pvc          v1                                true         PersistentVolumeClaim
  persistentvolumes                   pv           v1                                false        PersistentVolume
  pods                                po           v1                                true         Pod
  podtemplates                                     v1                                true         PodTemplate
  replicationcontrollers              rc           v1                                true         ReplicationController
  resourcequotas                      quota        v1                                true         ResourceQuota
  secrets                                          v1                                true         Secret
  serviceaccounts                     sa           v1                                true         ServiceAccount
  services                            svc          v1                                true         Service
  mutatingwebhookconfigurations                    admissionregistration.k8s.io/v1   false        MutatingWebhookConfiguration
  validatingadmissionpolicies                      admissionregistration.k8s.io/v1   false        ValidatingAdmissionPolicy
  validatingadmissionpolicybindings                admissionregistration.k8s.io/v1   false        ValidatingAdmissionPolicyBinding
  validatingwebhookconfigurations                  admissionregistration.k8s.io/v1   false        ValidatingWebhookConfiguration
  customresourcedefinitions           crd,crds     apiextensions.k8s.io/v1           false        CustomResourceDefinition
  apiservices                                      apiregistration.k8s.io/v1         false        APIService
  controllerrevisions                              apps/v1                           true         ControllerRevision
  daemonsets                          ds           apps/v1                           true         DaemonSet
  deployments                         deploy       apps/v1                           true         Deployment
  replicasets                         rs           apps/v1                           true         ReplicaSet
  statefulsets                        sts          apps/v1                           true         StatefulSet
  selfsubjectreviews                               authentication.k8s.io/v1          false        SelfSubjectReview
  tokenreviews                                     authentication.k8s.io/v1          false        TokenReview
  localsubjectaccessreviews                        authorization.k8s.io/v1           true         LocalSubjectAccessReview
  selfsubjectaccessreviews                         authorization.k8s.io/v1           false        SelfSubjectAccessReview
  selfsubjectrulesreviews                          authorization.k8s.io/v1           false        SelfSubjectRulesReview
  subjectaccessreviews                             authorization.k8s.io/v1           false        SubjectAccessReview
  horizontalpodautoscalers            hpa          autoscaling/v2                    true         HorizontalPodAutoscaler
  cronjobs                            cj           batch/v1                          true         CronJob
  jobs                                             batch/v1                          true         Job
  certificatesigningrequests          csr          certificates.k8s.io/v1            false        CertificateSigningRequest
  leases                                           coordination.k8s.io/v1            true         Lease
  endpointslices                                   discovery.k8s.io/v1               true         EndpointSlice
  events                              ev           events.k8s.io/v1                  true         Event
  flowschemas                                      flowcontrol.apiserver.k8s.io/v1   false        FlowSchema
  prioritylevelconfigurations                      flowcontrol.apiserver.k8s.io/v1   false        PriorityLevelConfiguration
  ingressclasses                                   networking.k8s.io/v1              false        IngressClass
  ingresses                           ing          networking.k8s.io/v1              true         Ingress
  networkpolicies                     netpol       networking.k8s.io/v1              true         NetworkPolicy
  runtimeclasses                                   node.k8s.io/v1                    false        RuntimeClass
  poddisruptionbudgets                pdb          policy/v1                         true         PodDisruptionBudget
  clusterrolebindings                              rbac.authorization.k8s.io/v1      false        ClusterRoleBinding
  clusterroles                                     rbac.authorization.k8s.io/v1      false        ClusterRole
  rolebindings                                     rbac.authorization.k8s.io/v1      true         RoleBinding
  roles                                            rbac.authorization.k8s.io/v1      true         Role
  priorityclasses                     pc           scheduling.k8s.io/v1              false        PriorityClass
  csidrivers                                       storage.k8s.io/v1                 false        CSIDriver
  csinodes                                         storage.k8s.io/v1                 false        CSINode
  csistoragecapacities                             storage.k8s.io/v1                 true         CSIStorageCapacity
  storageclasses                      sc           storage.k8s.io/v1                 false        StorageClass
  volumeattachments                                storage.k8s.io/v1                 false        VolumeAttachment
  ```
</details>


Several API elements we will look in details are:

- Runtime Pods and deployments
- API implementation details
- Ingress Services and load balancing
- PersistentVolumes and PersistentVolumeClaims storage
- NetworkPolicies and network security

When you experience the benefits of moving to a standardized API-driven methodology, you begin to appreciate the declarative nature of Kubernetes and its cloud-native approach to the container orchestration.

</details>

<details>
<summary><h3>CH2. Why the Pod?</h3></summary>

### What is a Pod? 

The Pod is the smallest atomic unint that can be deployed to a Kubernetes cluster. 

Many other Kubernetes API objects either use pods directly or support Pods. A Deployment object, for example, uses Pods, as well as StatefulSets and DaemonSets. Several different high-level Kubernetes controllers create and manage the life cycles of Pods. 

Containerized applications running at large scale require a high level of awareness when it comes to scheduling services and managing load balancers:
- Storage-aware scheduling - To schedule a process in concert with making its data available.  
- Service-aware network load balancing - To send traffic to different IP addresses as containers move from one machine to another.

Roughly, a Pod is one or more OCI images that run as containers on a Kubernetes cluster node. The Kubernetes node is a single piece of computing power (a server) that runs a kubelet.

Pods aren’t deployed directly in most cases. Instead, they are automatically created for us by other API objects such as:

- Deployments — The most commonly used API object in a Kubernetes cluster. They are the typical API object that, say, deploys a microservice.
- Jobs — Run a Pod as a batch process.
- StatefulSets — Host applications that require specific needs and that are often stateful applications like databases.
- DaemonSets — Used when we want to run a single Pod as an “agent” on every node of a cluster (commonly used for system services involving networking, storage, or logging).

### Linux namespaces and the Pod

Linux namespaces are a Linux kernel feature that allows for process separation inside the kernel, providing the base functionality to take an image and create a running container. 

The Pod, along with its foundation in Linux namespaces, enables a variety of features in Kubernetes. Within the networking namespace, there is a virtual networking stack that integrates with a software-defined networking (SDN) system covering the entire Kubernetes cluster. To meet scaling needs, load balancing across multiple Pods of the application is commonly employed. The SDN framework within a Kubernetes cluster supports this load balancing.

### Kubernetes, infrastructure and the Pod 

As a unit of compute, a unit of CPU power is represented by a Kubernetes API object: Node. Node requires the following infrastructure: 

- Server
- Operating System
- systemd
- kubelet
- network proxy (kube-proxy)
- <ins>CNI provider</ins>
- <ins>container runtime accessble via a CRI</ins> 

Kubelet is a binary program that runs as an agent. Without it, a Kubernetes node is not schedulable or considered to be a part of a cluster. It ensures:
- Pods on a kubelet's host operate through a control loop that monitors their assignments to nodes.
- Since Kubernetes 1.17, the API server is updated about kubelet health via a heartbeat mechanism checked through the `kube-node-lease` namespace.
- Garbage collection manages ephemeral storage and network devices for Pods as needed.

<ins>Kubelet utilizes CRI and CNI to reconcile the state of a node with the state of the control plane</ins>. For example, when the control plane determines that NGINX will run on nodes two, three, and four of a five-node cluster, the kubelet ensures that the CRI provider pulls the container from an image registry and assigns it an IP address within the `podCIDR` range.

Service is an API object defined by Kubernetes. The Kubernetes network proxy binary(kube-proxy) handles the creation of the ClusterIP and NodePort Services on every node. The type of Services are: 
- ClusterIP - An internal Service that load balances Kubernetes Pods
- NodePort - An open port on a Kubernetes node that load balances multiple Pods
- LoadBalancer - An external Service that creates a load balancer external to the cluster

A DNS system like CoreDNS provides application lookup, allowing microservices in one Pod to look up and communicate with another Pod. 

### The Node API Object

We can view a Kind cluster's node details by running:

<details>
  <summary>
    <code><br>kubectl get node/kind-control-plane -o yaml<br></code>
  </summary>
  <br>

  ```yaml
  apiVersion: v1
  kind: Node
  metadata:
    annotations:
      kubeadm.alpha.kubernetes.io/cri-socket: unix:///run/containerd/containerd.sock    [1]
      node.alpha.kubernetes.io/ttl: "0"
      volumes.kubernetes.io/controller-managed-attach-detach: "true"
    creationTimestamp: "2024-08-12T05:13:53Z"
    labels:
      beta.kubernetes.io/arch: amd64
      beta.kubernetes.io/os: linux
      kubernetes.io/arch: amd64
      kubernetes.io/hostname: kind-control-plane
      kubernetes.io/os: linux
      node-role.kubernetes.io/control-plane: ""
    name: kind-control-plane
    resourceVersion: "588604"
    uid: 30eab556-6338-4784-9a51-03ae64603876
  spec:
    podCIDR: 10.244.0.0/24    [2] 
    podCIDRs:
      - 10.244.0.0/24
    providerID: kind://docker/kind/kind-control-plane
  status:
    addresses:
      - address: 172.18.0.2
        type: InternalIP
      - address: kind-control-plane
        type: Hostname
    allocatable:
      cpu: "16"
      ephemeral-storage: 1055762868Ki
      hugepages-1Gi: "0"
      hugepages-2Mi: "0"
      memory: 8059232Ki
      pods: "110"
    capacity:
      cpu: "16"
      ephemeral-storage: 1055762868Ki
      hugepages-1Gi: "0"
      hugepages-2Mi: "0"
      memory: 8059232Ki
      pods: "110"

  #...
  ```
  > [1] The CRI socket used. With kind (and most clusters), containerd socket is used.
  > 
  > [2] CNI IP address, which is CIDR for the Pod network. 

</details>

### The Kubernetes API server

The Kubernetes API server, `kube-apiserver`, is an HTTP-based REST server that exposes the various API objects for a Kubernetes cluster. 

The API server is the only component on the control plane that communicates with etcd, the database for Kubernetes. In essence, the API server provides a stateful interface for all operations modifying a Kubernetes cluster. 

Admission controllers that run as part of the API server provide both authentication and authorization when a client communicates with the API server.

### The Kubernetes scheduler 

The Kubernetes scheduler, `kube-scheduler`, provides a clean, simple implementation of scheduling.

The scheduler considers multiple factors in Pod scheduling. These include hardware components on a node, available CPU and memory resources, policy scheduling constraints, and other weighting factors.

The scheduler also follows Pod affinity and anti-affinity rules that specify Pod scheduling and placement behavior. 

### Infrastructure controllers 

The API objects PersistentVolume (PV) and PersistentVolumeClaim (PVC) create the storage definitions and are brought to life by the Kubernetes controller manager (KCM) or the `kube-controller-manager` component, or cloud controller manager (CCM).

When running Kubernetes on a cloud platform, Kubernetes interacts with cloud APIs, and the **Cloud Controller Manager (CCM)** handles most of these API calls. For example, if you define a service like this:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  selector:
    app: example
  ports:
    - port: 8765
      targetPort: 9376
  type: LoadBalancer
```

- The **Kubernetes Controller Manager (KCM)** detects that a load balancer is needed based on the service configuration and makes the necessary API calls to the cloud provider to create one.
- Once the load balancer is set up, the **Container Network Interface (CNI) provider** manages the network routing, ensuring that traffic from the load balancer is directed to the correct Pod.

</details>

<details>
<summary><h3>CH3. Let's build a Pod </h3></summary>

### Pod Startup Latency

When you start a Pod, you might notice some latency. This is due to several low-level Linux processes needed to create the container. Here's what happens:

- The **kubelet** first identifies that it needs to run a container.
- The kubelet communicates with the **container runtime** to launch a **pause container**, which sets up the network environment for the actual application container. This pause container serves as a placeholder, allowing the Linux system to set up the container's network and assign its **Process ID (PID)**.
- During this setup, various components (e.g., **CNI provider**) go through different states. For example, the CNI provider remains idle until it needs to attach the pause container to the network namespace.

When a Pod starts, subpaths and storage directories are mounted using Linux bind mounts, allowing containers to access specific directories. These mounts facilitate critical Kubernetes functions, such as providing storage access to Pods. Tools like `nsenter` can inspect these directories directly through the OS, independent of container runtimes like Docker.

### Linux Primitives 

The <ins>network proxy `kube-proxy` creates iptables rules</ins>, and these rules are often inspected to debug container networking issues in large clusters. Running `iptables -L` in a Kubernetes node is an example usage. <ins>Container Network Interface providers also use this network proxy as well for tasks related to NetworkPolicies implementation</ins>. 

The <ins>Container Storage Interface defines a socket for communication between kubelet and storage stacks</ins> such as Network File System (NFS). For example, running `mount` in a cluster will show you the container and volume mounts managed by Kubernetes solely relying on Linux capabilities.   

Container runtime commands like `unshare` and `mount` are used when creating isolated processes. These commands typically need to be executed by the technologies that manage the containers. 

Linux primitives are fundamentally focused on manipulating, moving, or abstracting files. The entire design of Linux relies on <ins>the file abstraction as a control primitive</ins>.

- A directory is considered a file but contains the names of other files.
- Devices are represented as files to the Linux kernel, allowing you to use commands like ls to check if an Ethernet device is attached inside a container.
- Sockets and pipes are also treated as files, enabling local communication between processes. Later, we'll explore how the Container Storage Interface (CSI) leverages this abstraction to define how the kubelet communicates with volume providers, facilitating storage for our Pods.

We can use a pipe (`|`) to take the output from one command and pass it as input to another command: 

<details>
  <summary>
    <code><br>ls /var/log/containers/ | grep etcd <br></code>
  </summary>
  <br>

  ```bash
  etcd-kind-control-plane_kube-system_etcd-44daab302813923f188d864543c....log
  ```
</details>

In most Linux environments, the things we call containers are just processes created with a few isolated bells and whistles that enable them to play nicely with hundreds of other processes in a microservices cluster. 

### Building a Pod from scratch 

First we need to get inside the container where the control plane is running: 

```
kubectl exec -it  kind-control-plane /bin/bash
```

Because we will edit a text file in our kind cluster, let’s install the Vim editor first:  
```
root@kind-control-plane:/# apt-get update -y
root@kind-control-plane:/# apt-get install vim 
```

We'll create a minimal container—a folder with just enough to run a Bash shell—using the `chroot` command. 

The purpose of chroot is to create an isolated root for a process. There are three steps to this:

- Decide what program you want to run and where on your filesystem it should run.
- Create an environment for the process to run. There are many Linux programs that live in the lib64 directory, which are required to run even something like Bash. These need to be loaded into the new root.
- Copy the program you want to run to the chrooted location.

<details>
  <summary>
    Here's the script that includes `chroot` operation: 
  </summary>
  <br>
  
  ```shell
  #!/bin/bash
  mkdir -p /home/namespace/box
  mkdir -p /home/namespace/box/bin
  mkdir -p /home/namespace/box/lib
  mkdir -p /home/namespace/box/lib64
  mkdir -p /home/namespace/box/proc
  
  cp -v /usr/bin/kill /home/namespace/box/bin/
  cp -v /usr/bin/ps /home/namespace/box/bin
  cp -v /bin/bash /home/namespace/box/bin
  cp -v /bin/ls /home/namespace/box/bin
  
  cp -r /lib/* /home/namespace/box/lib/
  cp -r /lib64/* /home/namespace/box/lib64/
  
  mount -t proc proc /home/namespace/box/proc

  chroot /home/namespace/box /bin/bash
  ```
</details>

Give an execute permission to the script file and run the script: 
```
root@kind-control-plane:/# chmod +x chroot.sh
root@kind-control-plane:/# ./chroot.sh
```

You will then realize that we have isolated a separate `chroot` environment by seeing the result of `ls` on the new root directory: 
```
bash-5.2# ls
bin  lib  lib64 proc
```

We can mount a folder to create a consistent reference point for a disk, enabling it to exist in a different location. Open a new terminal and access the control plane container again. Then, create a new directory for the box namespace and mount /tmp/ to that directory:
```
root@kind-control-plane:/# mkdir -p /home/namespace/box/data
root@kind-control-plane:/# mount --bind /tmp/ /home/namespace/box/data
```

You've now created something similar to a container with access to storage. Let's check that access:

```bash
root@kind-control-plane:/# touch /tmp/a
root@kind-control-plane:/# touch /tmp/b
root@kind-control-plane:/# touch /tmp/c
```

Now, let's list the contents in the mounted directory:
<details>
  <summary>
    <code><br>bash-5.2# ls /data/ <br></code>
  </summary>
  <br>
  
  ```shell
  a  b  c
  ```
</details>


Running `ps -ax` will still show that our chrooted container has full access to the host, which could lead to permanent damage. Using the `unshare` command, however, we can use `chroot` to run Bash in an isolated terminal with a truly disengaged process space: 
```
root@kind-control-plane:/# unshare -p -f --mount-proc=/home/namespace/box/proc chroot /home/namespace/box /bin/bash
```

You will see the unawareness of host processes has been accomplished as below:  
<details>
  <summary>
    <code><br>bash-5.2# ps -ax <br></code>
  </summary>
  <br>
  
  ```shell
    PID TTY      STAT   TIME COMMAND
      1 ?        S      0:00 /bin/bash
      4 ?        R+     0:00 ps -ax
  ```
</details>


</details>

<details>
<summary><h3>CH4. Using cgroups for process in our Pods</h3></summary>
</details>

<details>
<summary><h3>CH5. CNIs and providing the Pod with a network</h3></summary>
</details>

<details>
<summary><h3>CH6. Troubleshooting large-scale network errors</h3></summary>
</details>

<details>
<summary><h3>CH7. Pod storage and the CSI</h3></summary>
</details>

<details>
<summary><h3>CH8. Storage implementation and modelling</h3></summary>
</details>

<details>
<summary><h3>CH9. Running Pods: How the kubelet works</h3></summary>
</details>

<details>
<summary><h3>CH10. DNS in Kubernetes</h3></summary>
</details>

<details>
<summary><h3>CH11. The core of the controlplane</h3></summary>
</details>

<details>
<summary><h3>CH12. etcd and controlplane</h3></summary>
</details>

<details>
<summary><h3>CH13. Container and Pod Security</h3></summary>
</details>

<details>
<summary><h3>CH14. Nodes and Kubernetes Security</h3></summary>
</details>

<details>
<summary><h3>CH15. Installing applications</h3></summary>
</details>

</details>
