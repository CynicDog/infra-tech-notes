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
```bash
$ ls /var/log/containers/ | grep etcd
etcd-kind-control-plane_kube-system_etcd-44daab302813923f188d864543c....log
```

In most Linux environments, the things we call containers are just processes created with a few isolated bells and whistles that enable them to play nicely with hundreds of other processes in a microservices cluster. 

### Building a Pod from scratch 

<details>
  <summary>
    <h4><ins>Installing Essential Tools in the Kind Control Plane Container</ins></h4>
  </summary>
  
First we need to get inside the container where the control plane is running: 

```
$ kind create cluster
$ docker exec -it  kind-control-plane /bin/bash
```

Because we will edit a text file in our kind cluster, let’s install the Vim editor first:  
```
root@kind-control-plane:/# apt-get update -y
root@kind-control-plane:/# apt-get install vim
```

Since the image of `kind-control-plane` container does not include `ip` command binary, we will install it now too:   
```
root@kind-control-plane:/# apt install iproute2
```

We'll create a minimal container—a folder with just enough to run a Bash shell—using the `chroot` command. 

<ins>**The purpose of chroot is to create an isolated root for a process**</ins>. There are three steps to this:

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
  mkdir -p /home/namespace/box/usr/sbin
  mkdir -p /home/namespace/box/usr/bin
  mkdir -p /home/namespace/box/usr/share/
  mkdir -p /home/namespace/box/etc/

  cp -v /usr/bin/kill /home/namespace/box/bin/
  cp -v /usr/bin/ps /home/namespace/box/bin
  cp -v /bin/bash /home/namespace/box/bin
  cp -v /bin/ls /home/namespace/box/bin
  cp -v /usr/sbin/ip /home/namespace/box/usr/sbin/
  cp -v /usr/bin/vim /home/namespace/box/usr/bin/
  cp -r /usr/share/vim /home/namespace/box/usr/share/
  cp -r /etc/vim /home/namespace/box/etc/

  cp -r /lib/* /home/namespace/box/lib/
  cp -r /lib64/* /home/namespace/box/lib64/
  
  mount -t proc proc /home/namespace/box/proc

  chroot /home/namespace/box /bin/bash
  ```
</details>

</details>


<details>
  <summary>
    <h4><ins>Mounting Directories for Persistent Storage in the Chroot Environment</ins></h4>
  </summary>

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

We can `mount` <ins>**a folder to create a consistent reference point for a disk**</ins>, enabling it to exist in a different location. Open a new terminal and access the control plane container again. Then, create a new directory for the box namespace and `mount /tmp/` to that directory:
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
```shell
bash-5.2# ls /data/ 
a  b  c
```

Running `ps -ax` will still show that our chrooted container has full access to the host, which could lead to permanent damage. Using the `unshare` command, however, we can use `chroot` to run Bash in an isolated terminal with a truly disengaged process space: 
```
root@kind-control-plane:/# unshare -p -n -f --mount-proc=/home/namespace/box/proc chroot /home/namespace/box /bin/bash
```

You will see the unawareness of host processes has been accomplished as below:  

```bash
bash-5.2# ps -ax 
  PID TTY      STAT   TIME COMMAND
    1 ?        S      0:00 /bin/bash
    4 ?        R+     0:00 ps -ax
```
</details>

<details>
  <summary>
    <h4><ins>Creating a network namespace</ins></h4>
  </summary>

Although the previous command isolated the process from our other processes, it still uses the same network. 

Run the following command to see the ip addresses in use in the network: 

<details>
  <summary>
    <code><br>bash-5.2# ip a<br></code>
  </summary>
  <br>
  
  ```shell
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
  2: vethbd91ad41@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
      link/ether d2:4a:47:e7:b5:b8 brd ff:ff:ff:ff:ff:ff link-netnsid 1
      inet 10.244.0.1/32 scope global vethbd91ad41
         valid_lft forever preferred_lft forever
      inet6 fe80::d04a:47ff:fee7:b5b8/64 scope link
         valid_lft forever preferred_lft forever
  3: veth13a4fbcb@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
      link/ether 26:b0:93:34:14:79 brd ff:ff:ff:ff:ff:ff link-netnsid 2
      inet 10.244.0.1/32 scope global veth13a4fbcb
         valid_lft forever preferred_lft forever
      inet6 fe80::24b0:93ff:fe34:1479/64 scope link
         valid_lft forever preferred_lft forever
  4: veth04e35c60@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
      link/ether d6:1f:db:70:41:63 brd ff:ff:ff:ff:ff:ff link-netnsid 3
      inet 10.244.0.1/32 scope global veth04e35c60
         valid_lft forever preferred_lft forever
      inet6 fe80::d41f:dbff:fe70:4163/64 scope link
         valid_lft forever preferred_lft forever
  5: vethf09bcc10@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
      link/ether e2:91:5c:60:c0:68 brd ff:ff:ff:ff:ff:ff link-netnsid 4
      inet 10.244.0.1/32 scope global vethf09bcc10
         valid_lft forever preferred_lft forever
      inet6 fe80::e091:5cff:fe60:c068/64 scope link
         valid_lft forever preferred_lft forever
  6: eth0@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
      link/ether 02:42:ac:12:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
      inet 172.18.0.2/16 brd 172.18.255.255 scope global eth0
         valid_lft forever preferred_lft forever
      inet6 fc00:f853:ccd:e793::2/64 scope global nodad
         valid_lft forever preferred_lft forever
      inet6 fe80::42:acff:fe12:2/64 scope link
         valid_lft forever preferred_lft forever
  ```
</details>

<ins>**If we want to run the same program within a new network, we can again use the**</ins> `unshare` <ins>**command**</ins>: 
```bash
root@kind-control-plane:/# unshare -p -n -f --mount-proc=/home/namespace/box/proc chroot /home/namespace/box /bin/bash
```

Here we see network status in the new network:

<details>
  <summary>
    <code><br>bash-5.2# ip a<br></code>
  </summary>
  <br>
  
  ```shell
  1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
  ```
</details>

A chrooted process differs from a real Kubernetes Pod primarily by the absence of a functional `eth0` network device. While chroot forms the basis of containerization in Docker and Kubernetes, it lacks the necessary network and other features required for running containerized applications.

</details>

<details>
  <summary>
    <h4><ins>Allocating resource usages</ins></h4>
  </summary>
  
We'll walk through how the kubelet defines cgroup limits, which is configurable via the `--cgroup-driver` flag (commonly using `systemd`). The steps are consistent across Kubernetes, even with different architectures like Windows. To set cgroup limits, get the process PID (`echo $$`) first. 
```
bash-5.2# echo $$
3821
```

Write its limits to the OS to restrict its memory usage:
```
root@kind-control-plane:/# mkdir /sys/fs/cgroup/memory/chroot
root@kind-control-plane:/# echo "10" > /sys/fs/cgroup/memory/chroot/memory.limit_in_bytes   [1] 
root@kind-control-plane:/# echo "0" > /sys/fs/cgroup/memory/chroot/memory.swappiness        [2] 
root@kind-control-plane:/# echo 3821 >  /sys/fs/cgroup/memory/chroot/tasks
```
> [1] Allocates our container only 10 bytes of memory, making it incapable of doing basic work. 
> 
> [2] Ensures the container doesn’t allocate swap space (Kubernetes almost always runs this way).  

</details>

### Understainding `kube-proxy` service implementations  

Kubernetes services route traffic to multiple endpoints via `kube-proxy`, typically using iptables for network routing.

A Pod requires:

- Capability to accept traffic as a service endpoint
- Ability to send outbound traffic
- Mechanism to track ongoing TCP connections, typically via the conntrack module in the Linux kernel

The `kube-dns` is a great example to study as it typifies a Pod you'd commonly deploy in a Kubernetes application. Key points about the `kube-dns` include:

- It operates in any Kubernetes cluster.
- It has no special privileges and utilizes the standard Pod network instead of the host network.
- It communicates over port 53, the standard DNS port.
- It's included by default in your kind cluster.

In Kubernetes, a CNI provider provides a unique IP address and routing rules to access a Pod. We can investigate these routes by running the following command: 
```bash
root@kind-control-plane:/# ip route
default via 172.18.0.1 dev eth0
10.244.0.2 dev veth4c4e1e72 scope host
10.244.0.3 dev veth228a67a5 scope host
10.244.0.4 dev veth79bea39b scope host
172.18.0.0/16 dev eth0 proto kernel scope link src 172.18.0.2
```
> In the code snippet, IP routes direct traffic to specific veth devices created by our networking plugin.

But how do Kubernetes Services route traffic to these devices? We can find the answer by examining the output of the iptables program:

<details>
  <summary>
    <code><br>root@kind-control-plane:/# iptables-save | grep 10.244.0.* <br></code>
  </summary>
  <br>
  
  ```shell
  -A KIND-MASQ-AGENT -d 10.244.0.0/16 -m comment --comment "kind-masq-agent: local traffic is not subject to MASQUERADE" -j RETURN       
  -A KUBE-SEP-IT2ZTR26TO4XFPTO -s 10.244.0.2/32 -m comment --comment "kube-system/kube-dns:dns-tcp" -j KUBE-MARK-MASQ   [1] 
  -A KUBE-SEP-IT2ZTR26TO4XFPTO -p tcp -m comment --comment "kube-system/kube-dns:dns-tcp" -m tcp -j DNAT --to-destination 10.244.0.2:53
  -A KUBE-SEP-N4G2XR5TDX7PQE7P -s 10.244.0.2/32 -m comment --comment "kube-system/kube-dns:metrics" -j KUBE-MARK-MASQ
  -A KUBE-SEP-N4G2XR5TDX7PQE7P -p tcp -m comment --comment "kube-system/kube-dns:metrics" -m tcp -j DNAT --to-destination 10.244.0.2:9153
  -A KUBE-SEP-PUHFDAMRBZWCPADU -s 10.244.0.4/32 -m comment --comment "kube-system/kube-dns:metrics" -j KUBE-MARK-MASQ
  -A KUBE-SEP-PUHFDAMRBZWCPADU -p tcp -m comment --comment "kube-system/kube-dns:metrics" -m tcp -j DNAT --to-destination 10.244.0.4:9153
  -A KUBE-SEP-SF3LG62VAE5ALYDV -s 10.244.0.4/32 -m comment --comment "kube-system/kube-dns:dns-tcp" -j KUBE-MARK-MASQ
  -A KUBE-SEP-SF3LG62VAE5ALYDV -p tcp -m comment --comment "kube-system/kube-dns:dns-tcp" -m tcp -j DNAT --to-destination 10.244.0.4:53
  -A KUBE-SEP-WXWGHGKZOCNYRYI7 -s 10.244.0.4/32 -m comment --comment "kube-system/kube-dns:dns" -j KUBE-MARK-MASQ
  -A KUBE-SEP-WXWGHGKZOCNYRYI7 -p udp -m comment --comment "kube-system/kube-dns:dns" -m udp -j DNAT --to-destination 10.244.0.4:53
  -A KUBE-SEP-YIL6JZP7A3QYXJU2 -s 10.244.0.2/32 -m comment --comment "kube-system/kube-dns:dns" -j KUBE-MARK-MASQ
  -A KUBE-SEP-YIL6JZP7A3QYXJU2 -p udp -m comment --comment "kube-system/kube-dns:dns" -m udp -j DNAT --to-destination 10.244.0.2:53
  -A KUBE-SVC-ERIFXISQEP7F7OF4 ! -s 10.244.0.0/16 -d 10.96.0.10/32 -p tcp -m comment --comment "kube-system/kube-dns:dns-tcp cluster IP" -m tcp --dport 53 -j KUBE-MARK-MASQ
  -A KUBE-SVC-ERIFXISQEP7F7OF4 -m comment --comment "kube-system/kube-dns:dns-tcp -> 10.244.0.2:53" -m statistic --mode random --probability 0.50000000000 -j KUBE-SEP-IT2ZTR26TO4XFPTO
  -A KUBE-SVC-ERIFXISQEP7F7OF4 -m comment --comment "kube-system/kube-dns:dns-tcp -> 10.244.0.4:53" -j KUBE-SEP-SF3LG62VAE5ALYDV
  -A KUBE-SVC-JD5MR3NA4I4DYORP ! -s 10.244.0.0/16 -d 10.96.0.10/32 -p tcp -m comment --comment "kube-system/kube-dns:metrics cluster IP" -m tcp --dport 9153 -j KUBE-MARK-MASQ
  -A KUBE-SVC-JD5MR3NA4I4DYORP -m comment --comment "kube-system/kube-dns:metrics -> 10.244.0.2:9153" -m statistic --mode random --probability 0.50000000000 -j KUBE-SEP-N4G2XR5TDX7PQE7P
  -A KUBE-SVC-JD5MR3NA4I4DYORP -m comment --comment "kube-system/kube-dns:metrics -> 10.244.0.4:9153" -j KUBE-SEP-PUHFDAMRBZWCPADU
  -A KUBE-SVC-NPX46M4PTMTKRN6Y ! -s 10.244.0.0/16 -d 10.96.0.1/32 -p tcp -m comment --comment "default/kubernetes:https cluster IP" -m tcp --dport 443 -j KUBE-MARK-MASQ
  -A KUBE-SVC-TCOU7JCQXEZGVUNU ! -s 10.244.0.0/16 -d 10.96.0.10/32 -p udp -m comment --comment "kube-system/kube-dns:dns cluster IP" -m udp --dport 53 -j KUBE-MARK-MASQ
  -A KUBE-SVC-TCOU7JCQXEZGVUNU -m comment --comment "kube-system/kube-dns:dns -> 10.244.0.2:53" -m statistic --mode random --probability 0.50000000000 -j KUBE-SEP-YIL6JZP7A3QYXJU2
  -A KUBE-SVC-TCOU7JCQXEZGVUNU -m comment --comment "kube-system/kube-dns:dns -> 10.244.0.4:53" -j KUBE-SEP-WXWGHGKZOCNYRYI7
  ```
  > [1] In the command, the `-j` option directs any access to the rule `KUBE-SEP-IT2ZTR26TO4XFPTO` to jump to the `KUBE-MARK-MASQ` rule.
  > 
  > When a request is made to a service, kube-proxy relies on these rules to determine where to forward the traffic. It uses the rules to:
  >
  > 1. Identify the service (via the KUBE-SVC rules).
  > 2. Determine the available endpoints (via the KUBE-SEP rules) for that service.
  > 3. Route the traffic to one of the pod endpoints, using the DNAT rules for destination address translation.

</details>

Kubernetes Services route traffic to pods by utilizing iptables, a Linux utility for configuring network packet filtering rules. By examining the output of the `iptables-save` command, we can understand how this routing process works.

- **Traffic Masquerading**: The rule `-A KIND-MASQ-AGENT -d 10.244.0.0/16 ... -j RETURN` allows local traffic within the cluster network (`10.244.0.0/16`) to bypass masquerading, ensuring it remains within the internal network.

- **Service Endpoints (SEP)**: Each service has associated endpoint rules. For example, the rule `-A KUBE-SEP-IT2ZTR26TO4XFPTO -s 10.244.0.2/32 ... -j KUBE-MARK-MASQ` marks traffic going to a specific pod (`10.244.0.2`) for masquerading.

- **Destination NAT (DNAT)**: The `-A KUBE-SEP-IT2ZTR26TO4XFPTO -p tcp ... -j DNAT --to-destination 10.244.0.2:53` rule redirects TCP traffic destined for the service to the specific pod IP (`10.244.0.2`) on port 53. This is the endpoint where the `kube-dns`'s Pod serves its traffic (the IP address of the CoreDNS Pod that is running). Note that `kube-dns` is the name of our service, and CoreDNS is the very Pod that implements our `kube-dns` service endpoint.   

- **Service Routing (SVC)**: The rules for service routing, such as `-A KUBE-SVC-ERIFXISQEP7F7OF4 ... -m comment --comment "kube-system/kube-dns:dns-tcp -> 10.244.0.2:53"`, indicate that traffic directed to the service's Cluster IP (`10.96.0.10`) will be routed to one of the pod endpoints (`10.244.0.2` or `10.244.0.4`) based on random probability. This helps in load balancing.

- **Multiple Protocols**: Different protocols (TCP and UDP) are handled with specific rules. For example, the DNS service can route UDP packets via `KUBE-SVC-TCOU7JCQXEZGVUNU` to the appropriate endpoints.

- **Service Types**: The rules apply to various service types. For instance, the output shows service rules for DNS and metrics services within the kube-system namespace, highlighting the versatility of routing configurations in Kubernetes.

- **Load Balancing**: The use of `-m statistic --mode random --probability` within rules demonstrates how traffic can be distributed randomly across multiple endpoints to ensure efficient load balancing.

</details>

<details>
<summary><h3>CH4. Using cgroups for process in our Pods</h3></summary>

Rather than allowing all processes full access to the system's limited resources, we could allocate specific portions of CPU, memory, and disk resources to each process. 

cgroups allow us to define hierarchically separated bins for memory, CPU, and other OS resources. All threads created by a program use the same pool of resources initially granted to the parent process. In other words, no one can play in someone else’s pool.

### Processes and threads in Linux

Each process in Linux can create multiple threads, which are abstractions that allow programs to share memory with other processes. For instance, we can examine independent scheduling threads in Kubernetes using the `ps -T` command. 

<details>
  <summary>
    <code><br>root@kind-control-plane:/# ps -T $(pgrep -f kube-scheduler) <br></code>
  </summary>
  <br>
  
  ```shell
  PID    SPID TTY      STAT   TIME COMMAND
  554     554 ?        Ssl    0:19 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     623 ?        Ssl    0:04 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     624 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     625 ?        Ssl    0:02 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     626 ?        Ssl    0:02 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     627 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     628 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     629 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     630 ?        Ssl    0:01 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     631 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     633 ?        Ssl    0:02 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     634 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     635 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     638 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     677 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     741 ?        Ssl    0:01 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554     742 ?        Ssl    0:01 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554    1733 ?        Ssl    0:00 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  554    1734 ?        Ssl    0:01 kube-scheduler --authentication-kubeconfig=/etc/kubernet..
  ```
  > This query displays parallel scheduler threads that share memory with each other.
</details>

### cgroups for our process 

We now have a clear understanding of what this scheduler Pod is doing: it has spawned several child processes, likely created by Kubernetes, as it is a child of containerd, the container runtime used by Kubernetes in `kind`. 

To understand how processes work, you can kill the containerd process and observe the scheduler and its subthreads come back to life. This is managed by the kubelet, which contains a `/manifests` directory. This directory informs the kubelet about essential processes that should run even before an API server can schedule containers. In fact, this is how Kubernetes installs itself through the kubelet.

The life cycle of a Kubernetes installation using kubeadm, the most common installation tool, proceeds as follows:

1. The kubelet has a manifests directory containing the API server, scheduler, and controller manager.
2. The kubelet is initiated by systemd.
3. The kubelet instructs containerd (or the configured container runtime) to start all processes listed in the manifests directory.
4. Once the API server is up and running, the kubelet connects to it and executes any containers requested by the API server.

<ins>Static Pods</ins> are managed directly by the kubelet daemon on a specific node, without the API server observing them. Unlike Pods managed by the control plane (e.g., Deployments), the kubelet monitors and restarts static Pods if they fail, ensuring high availability.

The `kube-scheduler-kind-control-plane` is an example of a static Pod, which the kubelet manages. The corresponding mirror Pod is created for it in the API.

Static Pods are always bound to a single kubelet on a specific node, making them ideal for critical workloads. The kubelet automatically creates a <ins>mirror Pod</ins> for each static Pod, allowing visibility in the API without control plane management.

Run the following command on your mahcine to see details: 

<details><summary><code>kubectl edit pod/kube-scheduler-kind-control-plane -n kube-system <br></code></summary>
<br> 
  
```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubernetes.io/config.hash: 9bdf7f956212aa6f2f85e496c389a56e
    kubernetes.io/config.mirror: 9bdf7f956212aa6f2f85e496c389a56e
    kubernetes.io/config.seen: "2024-10-11T05:56:44.346226347Z"
    kubernetes.io/config.source: file
...
```
> [1] The mirror Pod ID of the scheduler. 

</details>

To make this clearer, we can examine the mirror Pod ID of the scheduler to locate its control groups (cgroups). Cgroups are used by the operating system to manage and limit the resources (like CPU and memory) allocated to processes. By looking at the mirror Pod ID, we can determine which cgroups are assigned to the scheduler, allowing us to understand how it manages its resources.

Let’s check for any cgroups associated with our processor by executing the following command: 

<details>
  <summary>
    <code><br>root@kind-control-plane:/# cat /proc/$(pgrep -f kube-scheduler)/cgroup <br></code>
  </summary>
  <br>
  
  ```shell
  29:name=systemd:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  28:misc:/
  27:rdma:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  26:pids:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  25:hugetlb:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  24:net_prio:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  23:perf_event:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  22:net_cls:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  21:freezer:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  20:devices:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  19:memory:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  18:blkio:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  17:cpuacct:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  16:cpu:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  15:cpuset:/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  0::/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod9bdf7f956212aa6f2f85e496c389a56e.slice/cri-containerd-a3ea79e0b419762300820f80caa43ebf0c7622d8ee128442421dea6c2cba9cce.scope
  ```
  > Each line in the output follows the structure `<cgroup_id>:<subsystem>:<path>`, where `<cgroup_id>` serves as the identifier for the cgroup, `<subsystem>` represents the resource controller being managed (such as `cpu` or `memory`), and `<path>` indicates the hierarchical location of the cgroup within the system.

</details>

We have observed that every process in Kubernetes (on a Linux machine) is ultimately recorded in the bookkeeping tables located in the `/proc` directory, which includes the `/cgroup` folder that provides details about control groups managing resource allocation for these processes. 

### Implementing cgroups for a normal Pod

A more realistic scenario might be checking whether the cgroups for an application you're running (like NGINX) were set up correctly.

Let's create a deployment of NGINX with three pods by running: 
```bash
kubectl apply -f nginx.yml 
```

<details><summary>where the <code>nginx.yml</code> would look like: </summary>
<br>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        resources:
          requests: 
            cpu: 1
            memory: "3G" 
        ports:
        - containerPort: 80
```
</details>

The NGINX container now has dedicated access to one core and one GB of memory. After creating this pod, we can directly examine its cgroup hierarchy by accessing the memory field, which can be tracked down by running the `ps -ax` command: 

```bash
root@kind-control-plane:/# ps ax | grep nginx 
   7623 ?        Ss     0:00 nginx: master process nginx -g daemon off;
   7661 ?        S      0:00 nginx: worker process
   7662 ?        S      0:00 nginx: worker process
```

Now, let's investigate the cgroup hierarchy for the NGINX master process to understand how it is managed by Kubernetes: 
```bash
root@kind-control-plane:/# cat /proc/7623/cgroup 
0::/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod99939d4f_c8cc_4e3a_93ff_8b265731a3cf.slice/cri-containerd-40cca363ebb6feabb8f6e6c72d5359bd30725aeab1bd6e2f5adf5317c1673701.scope
```
> This command reveals the cgroup path for the NGINX master process, showing its allocation under Kubernetes' management structures.

Next, we can explore the contents of this cgroup to see the available resources and settings.
This will lead us to ...
```bash
root@kind-control-plane:/# ls /sys/fs/cgroup/kubelet.slice/kubelet-kubepods.slice/kubelet-kubepods-burstable.slice/kubelet-kubepods-burstable-pod99939d4f_c8cc_4e3a_93ff_8b265731a3cf.slice/cri-containerd-40cca363ebb6feabb8f6e6c72d5359bd30725aeab1bd6e2f5adf5317c1673701.scope/
cgroup.controllers	cgroup.threads	 cpuset.cpus		   hugetlb.1GB.numa_stat     hugetlb.2MB.rsvd.max	hugetlb.64KB.events	   memory.events	memory.stat	      pids.events
cgroup.events		cgroup.type	 cpuset.cpus.effective	   hugetlb.1GB.rsvd.current  hugetlb.32MB.current	hugetlb.64KB.events.local  memory.events.local	memory.swap.current   pids.max
cgroup.freeze		cpu.idle	 cpuset.cpus.partition	   hugetlb.1GB.rsvd.max      hugetlb.32MB.events	hugetlb.64KB.max	   memory.high		memory.swap.events    pids.peak
cgroup.kill		cpu.max		 cpuset.mems		   hugetlb.2MB.current	     hugetlb.32MB.events.local	hugetlb.64KB.numa_stat	   memory.low		memory.swap.high      rdma.current
cgroup.max.depth	cpu.max.burst	 cpuset.mems.effective	   hugetlb.2MB.events	     hugetlb.32MB.max		hugetlb.64KB.rsvd.current  memory.max		memory.swap.max       rdma.max
cgroup.max.descendants	cpu.stat	 hugetlb.1GB.current	   hugetlb.2MB.events.local  hugetlb.32MB.numa_stat	hugetlb.64KB.rsvd.max	   memory.min		memory.swap.peak
cgroup.procs		cpu.stat.local	 hugetlb.1GB.events	   hugetlb.2MB.max	     hugetlb.32MB.rsvd.current	io.max			   memory.oom.group	memory.zswap.current
cgroup.stat		cpu.weight	 hugetlb.1GB.events.local  hugetlb.2MB.numa_stat     hugetlb.32MB.rsvd.max	io.stat			   memory.peak		memory.zswap.max
cgroup.subtree_control	cpu.weight.nice  hugetlb.1GB.max	   hugetlb.2MB.rsvd.current  hugetlb.64KB.current	memory.current		   memory.reclaim	pids.current
```
> This command lists the files related to the NGINX container’s cgroup, providing insights into its resource limits, current usage statistics, and management settings.

In conclusion, the isolation provided by Kubernetes can be understood on a Linux machine as a regular hierarchical distribution of resources organized through a straightforward directory structure.

### How the kubelet manages cgroups 

While most focus on CPU and memory isolation, understanding others is useful. For example, the `freezer` cgroup manages groups of tasks for pausing and resuming, though Kubernetes doesn’t fully utilize this. The `blkio` cgroup handles I/O management, and `/sys/fs/cgroup` shows how resources are allocated in Linux. 

<details><summary><code>root@kind-control-plane:/# ls -d /sys/fs/cgroup/*<br> </code></summary>

<br>

```bash
/sys/fs/cgroup/cgroup.controllers	  /sys/fs/cgroup/hugetlb.32MB.rsvd.current
/sys/fs/cgroup/cgroup.events		  /sys/fs/cgroup/hugetlb.32MB.rsvd.max
/sys/fs/cgroup/cgroup.freeze		  /sys/fs/cgroup/hugetlb.64KB.current
/sys/fs/cgroup/cgroup.kill		  /sys/fs/cgroup/hugetlb.64KB.events
/sys/fs/cgroup/cgroup.max.depth		  /sys/fs/cgroup/hugetlb.64KB.events.local
/sys/fs/cgroup/cgroup.max.descendants	  /sys/fs/cgroup/hugetlb.64KB.max
/sys/fs/cgroup/cgroup.procs		  /sys/fs/cgroup/hugetlb.64KB.numa_stat
/sys/fs/cgroup/cgroup.stat		  /sys/fs/cgroup/hugetlb.64KB.rsvd.current
/sys/fs/cgroup/cgroup.subtree_control	  /sys/fs/cgroup/hugetlb.64KB.rsvd.max
/sys/fs/cgroup/cgroup.threads		  /sys/fs/cgroup/init.scope
/sys/fs/cgroup/cgroup.type		  /sys/fs/cgroup/io.max
/sys/fs/cgroup/cpu.idle			  /sys/fs/cgroup/io.stat
/sys/fs/cgroup/cpu.max			  /sys/fs/cgroup/kubelet
/sys/fs/cgroup/cpu.max.burst		  /sys/fs/cgroup/kubelet.slice
/sys/fs/cgroup/cpu.stat			  /sys/fs/cgroup/memory.current
/sys/fs/cgroup/cpu.stat.local		  /sys/fs/cgroup/memory.events
/sys/fs/cgroup/cpu.weight		  /sys/fs/cgroup/memory.events.local
/sys/fs/cgroup/cpu.weight.nice		  /sys/fs/cgroup/memory.high
/sys/fs/cgroup/cpuset.cpus		  /sys/fs/cgroup/memory.low
/sys/fs/cgroup/cpuset.cpus.effective	  /sys/fs/cgroup/memory.max
/sys/fs/cgroup/cpuset.cpus.partition	  /sys/fs/cgroup/memory.min
/sys/fs/cgroup/cpuset.mems		  /sys/fs/cgroup/memory.oom.group
/sys/fs/cgroup/cpuset.mems.effective	  /sys/fs/cgroup/memory.peak
/sys/fs/cgroup/dev-hugepages.mount	  /sys/fs/cgroup/memory.reclaim
/sys/fs/cgroup/hugetlb.1GB.current	  /sys/fs/cgroup/memory.stat
/sys/fs/cgroup/hugetlb.1GB.events	  /sys/fs/cgroup/memory.swap.current
/sys/fs/cgroup/hugetlb.1GB.events.local   /sys/fs/cgroup/memory.swap.events
/sys/fs/cgroup/hugetlb.1GB.max		  /sys/fs/cgroup/memory.swap.high
/sys/fs/cgroup/hugetlb.1GB.numa_stat	  /sys/fs/cgroup/memory.swap.max
/sys/fs/cgroup/hugetlb.1GB.rsvd.current   /sys/fs/cgroup/memory.swap.peak
/sys/fs/cgroup/hugetlb.1GB.rsvd.max	  /sys/fs/cgroup/memory.zswap.current
/sys/fs/cgroup/hugetlb.2MB.current	  /sys/fs/cgroup/memory.zswap.max
/sys/fs/cgroup/hugetlb.2MB.events	  /sys/fs/cgroup/pids.current
/sys/fs/cgroup/hugetlb.2MB.events.local   /sys/fs/cgroup/pids.events
/sys/fs/cgroup/hugetlb.2MB.max		  /sys/fs/cgroup/pids.max
/sys/fs/cgroup/hugetlb.2MB.numa_stat	  /sys/fs/cgroup/pids.peak
/sys/fs/cgroup/hugetlb.2MB.rsvd.current   /sys/fs/cgroup/rdma.current
/sys/fs/cgroup/hugetlb.2MB.rsvd.max	  /sys/fs/cgroup/rdma.max
/sys/fs/cgroup/hugetlb.32MB.current	  /sys/fs/cgroup/sys-fs-fuse-connections.mount
/sys/fs/cgroup/hugetlb.32MB.events	  /sys/fs/cgroup/sys-kernel-config.mount
/sys/fs/cgroup/hugetlb.32MB.events.local  /sys/fs/cgroup/sys-kernel-debug.mount
/sys/fs/cgroup/hugetlb.32MB.max		  /sys/fs/cgroup/sys-kernel-tracing.mount
/sys/fs/cgroup/hugetlb.32MB.numa_stat	  /sys/fs/cgroup/system.slice
```
> In cgroup v2, specific controllers like blkio have been consolidated under the unified io controller. 

</details>

With an understanding of cgroups, let's explore how they are used in the kubelet, specifically through the `allocatable` data structure. Examining a Kubernetes node (you can do this with your kind cluster), the output from `kubectl get nodes -o yaml` shows:

root@kind-control-plane:/# kubectl get nodes -o yaml 
<details><summary><code>root@kind-control-plane:/# kubectl get nodes -o yaml </code></summary>
<br> 
  
```yaml
apiVersion: v1
items:
- apiVersion: v1
  kind: Node
  metadata:
    annotations:
      kubeadm.alpha.kubernetes.io/cri-socket: unix:///run/containerd/containerd.sock
      node.alpha.kubernetes.io/ttl: "0"
      volumes.kubernetes.io/controller-managed-attach-detach: "true"
    creationTimestamp: "2024-10-21T10:23:30Z"
    labels:
      beta.kubernetes.io/arch: arm64
      beta.kubernetes.io/os: linux
      kubernetes.io/arch: arm64
      kubernetes.io/hostname: kind-control-plane
      kubernetes.io/os: linux
      node-role.kubernetes.io/control-plane: ""
    name: kind-control-plane
    resourceVersion: "836"
    uid: 9c74ecaf-24c3-4eda-ad43-a475fc90cdba
  spec:
    podCIDR: 10.244.0.0/24
    podCIDRs:
    - 10.244.0.0/24
    providerID: kind://docker/kind/kind-control-plane
  status:
    addresses:
    - address: 172.18.0.2
      type: InternalIP
    - address: kind-control-plane
      type: Hostname
    allocatable:                          [1] 
      cpu: "8"
      ephemeral-storage: 235280588Ki
      hugepages-1Gi: "0"
      hugepages-2Mi: "0"
      hugepages-32Mi: "0"
      hugepages-64Ki: "0"
      memory: 8131696Ki
      pods: "110"
    capacity:
      cpu: "8"
      ephemeral-storage: 235280588Ki
      hugepages-1Gi: "0"
      hugepages-2Mi: "0"
      hugepages-32Mi: "0"
      hugepages-64Ki: "0"
      memory: 8131696Ki
      pods: "110"

```
> [1] These values represent the cgroup resources available for Pods, indicating the limits for CPU, memory, and other resources. The kubelet determines these values by calculating the node's total capacity and then subtracting the CPU and memory it needs for itself and the node's system processes. The remaining resources form the allocatable budget available for containers. (<ins>**Allocatable resource budget**</ins> = node capacity - `kubeReserved` - `systemReserved`)

</details>

To understand how this applies to a running container:

- The kubelet creates cgroups when Pods are launched, limiting their resource usage based on the Pod specifications.
- `systemd` usually manages the kubelet, which periodically reports available node resources to the Kubernetes API.
- `systemd` also typically manages the container runtime, ensuring it runs alongside the kubelet.
- The container runtime (e.g., containerd, CRI-O, or Docker) starts the container process within these cgroups, ensuring it adheres to the specified resource limits.

<ins>Kubernetes disables swap to ensure predictable resource allocation</ins>. By doing so, it avoids slowing down memory access for Pods, which could violate their guaranteed memory limits and lead to unpredictable performance.

Cgroups manage memory usage with two types of limits:

- **Soft limits**: Allow processes to use varying amounts of RAM based on system load.
- **Hard limits**: Terminate processes that exceed their memory limit for too long.

<ins>Kubernetes enforces hard limits</ins>, reporting an exit code and `OOMKilled` status when a process is terminated for exceeding these limits.

### QoS classes: Why they matter and how they work 

<ins>**Quality of Service** (QoS) refers to the availability of resources when needed</ins>. It helps maintain the performance of critical services while allowing less important services to operate suboptimally during peak times. Kubernetes defines three QoS classes based on how you configure a Pod:

- <ins>**BestEffort Pods**</ins>: Pods with no CPU or memory requests. They are easily killed or displaced when resources are scarce and may be rescheduled on a new node.
- <ins>**Burstable Pods**</ins>: Pods with defined memory or CPU requests but without limits for all classes. They are less likely to be displaced than BestEffort Pods.
- <ins>**Guaranteed Pods**</ins>: Pods with both CPU and memory requests. They are the least likely to be displaced compared to Burstable Pods.

When you run `kubectl get pods -o yaml`, you'll see the Burstable class assigned to your Pod's status field. During peak times, use this technique to ensure that critical processes are assigned a Guaranteed or Burstable status.

<details><summary><code>root@kind-control-plane:/# kubectl get pods -o yaml </code></summary>

<br>

```yaml
- apiVersion: v1
  kind: Pod
  metadata:
    creationTimestamp: "2024-10-21T11:09:54Z"
    generateName: nginx-6d65c5c6cb-
    labels:
      app: nginx
      pod-template-hash: 6d65c5c6cb
    name: nginx-6d65c5c6cb-zncnw
    namespace: default
    ownerReferences:
    - apiVersion: apps/v1
      blockOwnerDeletion: true
      controller: true
      kind: ReplicaSet
      name: nginx-6d65c5c6cb
      uid: dc09217b-d518-4b97-acc1-7421e8623758
    resourceVersion: "4092"
    uid: 9b3f8262-c12d-407d-82ec-6222b9bb352d
  spec:
    containers:
    - image: nginx
      imagePullPolicy: Always
      name: nginx
      ports:
      - containerPort: 80
        protocol: TCP
      resources:
        requests:
          cpu: "1"
          memory: 3G
      terminationMessagePath: /dev/termination-log
      terminationMessagePolicy: File
      volumeMounts:
      - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: kube-api-access-v67hh
        readOnly: true
    dnsPolicy: ClusterFirst
    enableServiceLinks: true
    nodeName: kind-control-plane
    preemptionPolicy: PreemptLowerPriority
    priority: 0
    restartPolicy: Always
    schedulerName: default-scheduler
    securityContext: {}
    serviceAccount: default
    serviceAccountName: default
    terminationGracePeriodSeconds: 30
    tolerations:
    - effect: NoExecute
      key: node.kubernetes.io/not-ready
      operator: Exists
      tolerationSeconds: 300
    - effect: NoExecute
      key: node.kubernetes.io/unreachable
      operator: Exists
      tolerationSeconds: 300
    volumes:
    - name: kube-api-access-v67hh
      projected:
        defaultMode: 420
        sources:
        - serviceAccountToken:
            expirationSeconds: 3607
            path: token
        - configMap:
            items:
            - key: ca.crt
              path: ca.crt
            name: kube-root-ca.crt
        - downwardAPI:
            items:
            - fieldRef:
                apiVersion: v1
                fieldPath: metadata.namespace
              path: namespace
  status:
    conditions:
    - lastProbeTime: null
      lastTransitionTime: "2024-10-21T11:10:18Z"
      status: "True"
      type: PodReadyToStartContainers
    - lastProbeTime: null
      lastTransitionTime: "2024-10-21T11:09:54Z"
      status: "True"
      type: Initialized
    - lastProbeTime: null
      lastTransitionTime: "2024-10-21T11:10:18Z"
      status: "True"
      type: Ready
    - lastProbeTime: null
      lastTransitionTime: "2024-10-21T11:10:18Z"
      status: "True"
      type: ContainersReady
    - lastProbeTime: null
      lastTransitionTime: "2024-10-21T11:09:54Z"
      status: "True"
      type: PodScheduled
    containerStatuses:
    - containerID: containerd://21465a16f6f1e6566ceb03a4529018895c8fce656cd8d26b46f286e1011687e0
      image: docker.io/library/nginx:latest
      imageID: docker.io/library/nginx@sha256:28402db69fec7c17e179ea87882667f1e054391138f77ffaf0c3eb388efc3ffb
      lastState: {}
      name: nginx
      ready: true
      restartCount: 0
      started: true
      state:
        running:
          startedAt: "2024-10-21T11:10:18Z"
      volumeMounts:
      - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: kube-api-access-v67hh
        readOnly: true
        recursiveReadOnly: Disabled
    hostIP: 172.18.0.2
    hostIPs:
    - ip: 172.18.0.2
    phase: Running
    podIP: 10.244.0.6
    podIPs:
    - ip: 10.244.0.6
    qosClass: Burstable                 [1]
    startTime: "2024-10-21T11:09:54Z"
kind: List
metadata:
  resourceVersion: ""
```
> [1] The line indicates the Pod is **Burstable**, allowing it to use extra resources if available.

</details>

### Monitoring the Linux kernel

In theory, a metric is any measurable value. The three fundamental types of metrics we focus on are histograms, gauges, and counters.

- **Gauges**: Measure the rate of requests per second at a specific moment in time.  
- **Histograms**: Track the distribution of event durations, such as the number of requests completed within different time ranges (e.g., under 500 ms).  
- **Counters**: Record a continuously increasing count of events, like the total number of requests processed.  

Metrics are crucial for containerized and cloud-based applications, but they need to be managed in a lightweight, decoupled way. This is achieved by integrating a Prometheus handler into a REST API server, which exposes a single meaningful endpoint: `/metrics`.

You can check if requests for Pods are straining your Kubernetes API server by running the following commands in your terminal (assuming your kind cluster is up and running). In a separate terminal, execute the `kubectl proxy` command, then use `curl` to access the API server’s metrics endpoint:

```
PS C:\Users> kubectl proxy
Starting to serve on 127.0.0.1:8001
```

Then you can make a GET request to the endpoint `/metrics` as follows: 
```
PS C:\Users> http 127.0.0.1:8001/metrics
apiserver_request_sli_duration_seconds_bucket{component="apiserver",group="batch",resource="jobs",scope="cluster",subresource="",verb="LIST",version="v1",le="0.2"} 1
apiserver_request_sli_duration_seconds_bucket{component="apiserver",group="batch",resource="jobs",scope="cluster",subresource="",verb="LIST",version="v1",le="0.4"} 1
apiserver_request_sli_duration_seconds_bucket{component="apiserver",group="batch",resource="jobs",scope="cluster",subresource="",verb="LIST",version="v1",le="0.6"} 1
apiserver_request_sli_duration_seconds_bucket{component="apiserver",group="batch",resource="jobs",scope="cluster",subresource="",verb="LIST",version="v1",le="0.8"} 1
...
```

</details>

<details>
<summary><h3>CH5. CNIs and providing the Pod with a network</h3></summary>

<ins>**Software-Defined Networking** (SDN)</ins> traditionally manages load balancing, isolation, and security for VMs in the cloud and on-premises data centers. It simplifies network reconfiguration for system administrators, enabling changes as frequently as daily with the creation or deletion of VMs. In the era of containers, SDN gains new significance, as networks in large Kubernetes clusters change constantly, necessitating software automation. The Kubernetes network is fully software-defined and dynamic, reflecting the ephemeral nature of Pods and service endpoints.

We'll explore Pod-to-Pod networking, focusing on how hundreds or thousands of containers on a machine can have unique, cluster-routable IP addresses. Kubernetes achieves this through the <ins>**Container Network Interface** (CNI)</ins> standard, which allows various technologies to provide each Pod with a unique routable IP address in a modular and extensible manner.

Kube-proxy manages <ins>**iptables rules**</ins> for <ins>nftables, IPVS (IP Virtual Server), and other network proxy implementations</ins>. It creates various `KUBE-SEP` rules that <ins>instruct the Linux kernel to masquerade traffic</ins>, meaning <ins>traffic leaving a container is marked as originating from a node, or to NAT traffic via a service IP</ins>. This traffic is then forwarded to a running Pod, which may often reside on a different node within the cluster.

<ins>Kube-proxy routes services to Pods</ins>, like when exposing an app via a node port. <ins>Its job is to map service IPs to Pod IPs</ins>, but it depends on a strong Pod network. If Pod IPs aren’t routable between nodes, kube-proxy’s routing fails, making the application inaccessible. Essentially, a load balancer remains the only reliable option.

### Why we need software-defined networks in Kubernetes

The container networking challenge is routing traffic consistently to the correct Pods, even as they move. Kubernetes solves this with two key tools:

- <ins>**Service Proxy**</ins>: Ensures stable IPs and load balances traffic across Pods behind a service.
- <ins>**CNI**</ins>: Maintains a flat, accessible network for Pods to be continually recreated within the cluster.

At the core of the solution is the <ins>**ClusterIP**</ins> service, a Kubernetes Service that <ins>routes traffic within the cluster</ins> but is not accessible externally. It serves as a foundational component for building other services.

We can see how new IP addresses are easily assigned when creating Pods. Since our kind cluster has two running CoreDNS Pods, we can verify their IP addresses to confirm this:
```bash
root@kind-control-plane:/# kubectl get pods -A -o wide | grep coredns
kube-system   coredns-7db6d8ff4d-9jk7h  1/1   Running   8 (6h1m ago)   10d   10.244.0.3   kind-control-plane   <none>  <none>
kube-system   coredns-7db6d8ff4d-jrsm6  1/1   Running   8 (6h1m ago)   10d   10.244.0.2   kind-control-plane   <none>  <none>
```

Kubernetes offers three main types of Service objects: <ins>**ClusterIP**</ins>, <ins>**NodePort**</ins>, and <ins>**LoadBalancer**</ins>. These Services determine which backend Pods to connect to using labels.

In SDN framework, kube-proxy plays a crucial role by maintaining network rules on each node. It watches for changes in the Services and updates the underlying routing rules so that traffic can reach the correct Pods, even when Pods or IPs change. This ensures that communication between Services and Pods remains consistent, making kube-proxy a key component of Kubernetes' SDN architecture.

### The kube-proxy's data plane 

We find that **kube-proxy** is split into two control paths: `server_windows.go` and `server_others.go`. The `server_windows.go` file is compiled into `kube-proxy.exe`, which makes native calls to Windows system APIs. These include the `netsh` command for the userspace proxy and the `hcsshim` and HCN containerization APIs for the Windows kernel proxy.

In most cases, **kube-proxy** runs on Linux, where a different binary (also called `kube-proxy`) is used without the Windows functionality. On Linux, it typically uses the **iptables** proxy. In kind clusters, **kube-proxy** defaults to **iptables mode**, which can be confirmed by running:

<details><summary><code>root@kind-control-plane:/# kubectl edit cm kube-proxy -n kube-system<br></code></summary>
<br>

```yaml
apiVersion: v1
data:
  config.conf: |-
    apiVersion: kubeproxy.config.k8s.io/v1alpha1
    bindAddress: 0.0.0.0
    bindAddressHardFail: false
    clientConnection:
      acceptContentTypes: ""
      burst: 0
      contentType: ""
      kubeconfig: /var/lib/kube-proxy/kubeconfig.conf
      qps: 0
    clusterCIDR: 10.244.0.0/16
    configSyncPeriod: 0s
    conntrack:
      maxPerCore: 0
      min: null
      tcpBeLiberal: false
      tcpCloseWaitTimeout: null
      tcpEstablishedTimeout: null
      udpStreamTimeout: 0s
      udpTimeout: 0s
    detectLocal:
      bridgeInterface: ""
      interfaceNamePrefix: ""
    detectLocalMode: ""
    enableProfiling: false
    healthzBindAddress: ""
    hostnameOverride: ""
    iptables:
      localhostNodePorts: null
      masqueradeAll: false
      masqueradeBit: null
      minSyncPeriod: 1s
      syncPeriod: 0s
    ipvs:
      excludeCIDRs: null
      minSyncPeriod: 0s
      scheduler: ""
      strictARP: false
      syncPeriod: 0s
      tcpFinTimeout: 0s
      tcpTimeout: 0s
      udpTimeout: 0s
    kind: KubeProxyConfiguration
    logging:
      flushFrequency: 0
      options:
        json:
          infoBufferSize: "0"
        text:
          infoBufferSize: "0"
      verbosity: 0
    metricsBindAddress: ""
    mode: iptables               [1] 
    nftables:
      masqueradeAll: false
      masqueradeBit: null
      minSyncPeriod: 0s
      syncPeriod: 0s
    nodePortAddresses: null
    oomScoreAdj: null
    portRange: ""
    showHiddenMetricsForVersion: ""
    winkernel:
      enableDSR: false
      forwardHealthCheckVip: false
      networkName: ""
      rootHnsEndpointName: ""
      sourceVip: ""
  kubeconfig.conf: |-
    apiVersion: v1
    kind: Config
    clusters:
    - cluster:
        certificate-authority: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        server: https://kind-control-plane:6443
      name: default
    contexts:
    - context:
        cluster: default
        namespace: default
        user: default
      name: default
    current-context: default
    users:
    - name: default
      user:
        tokenFile: /var/run/secrets/kubernetes.io/serviceaccount/token
kind: ConfigMap
metadata:
  creationTimestamp: "2024-10-23T08:13:15Z"
  labels:
    app: kube-proxy
  name: kube-proxy
  namespace: kube-system
  resourceVersion: "228"
  uid: 59a420cd-4d02-40f2-891c-bdb9e44eb7c5
~                                                    
```
> The kube-proxy mode is set to `iptables`.

</details>

and checking the `mode` field.

There are three types of kube-proxy mode: 

1. `iptables`: Uses Linux iptables to route traffic to Pods via defined rules. Simple and effective but may struggle with scale.

2. `IPVS` (IP Virtual Server): Utilizes the IPVS module for load balancing TCP/UDP traffic. Offers better performance and scalability with various load-balancing algorithms.

3. `userspace` (deprecated): Runs a proxy in userspace to forward traffic to Pods. Simple but has performance issues; now deprecated.

External load balancers or ingress/gateway routers are responsible for forwarding traffic into a Kubernetes cluster. The kube-proxy, on the other hand, manages the routing of traffic between services and Pods. It's worth noting that the term "proxy" can be misleading; typically, kube-proxy maintains static routing rules implemented by kernel technologies or other data plane methods, such as iptables rules.

### What about NodePorts?

Let's create a new Kubernetes service to demonstrate adding and modifying load-balancing rules. We'll set up a NodePort service pointing to a CoreDNS container in a Pod. Let's start the configuration by running the command and change the service type from `ClusterIP` to `NodePort` as below: 

<details><summary><code>kubectl get svc kube-dns -o yaml -n kube-system</code><br></summary> 
<br>

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    prometheus.io/port: "9153"
    prometheus.io/scrape: "true"
  creationTimestamp: "2024-10-11T05:56:44Z"
  labels:
    k8s-app: kube-dns
    kubernetes.io/cluster-service: "true"
    kubernetes.io/name: CoreDNS
  name: kube-dns
  namespace: kube-system
  resourceVersion: "240"
  uid: 9f99d4dc-8618-4522-bc55-6e7d57f2bf09
spec:
  clusterIP: 10.96.0.10
  clusterIPs:
  - 10.96.0.10
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - name: dns
    port: 53
    protocol: UDP
    targetPort: 53
  - name: dns-tcp
    port: 53
    protocol: TCP
    targetPort: 53
  - name: metrics
    port: 9153
    protocol: TCP
    targetPort: 9153
  selector:
    k8s-app: kube-dns
  sessionAffinity: None
  type: NodePort       [1]
status:
  loadBalancer: {}
```
> Changed from `ClusterIP` to `NodePort`

</details>

Running the command below will see that random ports (`30247`, `32585`) assigned for the NodePort service, which now forwards traffic to CoreDNS.

```bash
root@kind-control-plane:/# kubectl get svc -A
NAMESPACE     NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                                         AGE
default       kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP                                         13d
kube-system   kube-dns     NodePort    10.96.0.10   <none>        53:30247/UDP,53:32585/TCP,9153:31915/TCP   [1]  13d    
```
> [1] With service type of `ClusterIP`, it used to look like `53/UDP,53/TCP,9153/TCP`, with no random ports assigned. 

The random ports are mapped to port 53 from our DNS service Pods and are open on all cluster nodes, as each node runs kube-proxy.

### CNI Providers 

CNI providers implement the CNI specification, which outlines a contract that enables container runtimes to request a valid IP address for a process when it starts up. A CNI provider implements three key CNI operations: ADD, DELETE, and CHECK, which are invoked when containerd starts or deletes a Pod. CNIs are said to be configured once a CNI container writes a /etc/cni/net.d file on a kubelet’s local filesystem.

When a Pod is created, the CNI provider assigns it an IP address, allowing the Pod to communicate with other Pods and services within the cluster.

- **Calico**: is a networking and security solution for containers that uses a Layer 3 approach and supports IPv4 and IPv6, offering network policy enforcement and IP address management.

- **Flannel**: is a simple overlay network for Kubernetes that creates a Layer 3 network between containers, supporting various backends like VXLAN and host-gw.

- **Weave Net**: provides an easy-to-use overlay network with features like automatic service discovery, traffic encryption, and basic network policy management.

- **Cilium**: leverages eBPF technology for advanced networking capabilities, including dynamic load balancing and strong security measures.

- **Kube-Router**: integrates routing, load balancing, and network policy enforcement using standard Linux networking and supports BGP for route propagation.

- **Romana**: focuses on Layer 3 networking with integrated security policies, supporting IP address management and network isolation.

- **Antrea**: offers advanced networking features based on Open vSwitch, including network policies and traffic visibility, suitable for enterprise environments.

- **Contiv**: emphasizes policy-driven networking for microservices and supports multiple network models.

- **OpenShift SDN**: is integrated into OpenShift, supporting various networking models and providing multi-tenancy and service discovery.

- **Amazon VPC CNI**: integrates Kubernetes Pods with AWS VPC networking, allowing Pods to use IP addresses from the VPC subnet for seamless communication with AWS resources.

For our part, we will install the Calico provider and complete the implementation of CNI in our Kubernetes cluster.

#### Create a deedicated cluster

The first is to create a `kind-Calico-conf.yaml` that looks as below: 

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  disableDefaultCNI: true
  podSubnet: 192.168.0.0/16
nodes:
- role: control-plane
- role: worker
```

Then, run the following command to create a cluster: 

```bash
PS C:\Users> kind create cluster --name=calico --config=./kind-Calico-conf.yaml
```

It's important to note what happens when a Pod's CNI isn't available, leading to unschedulable Pods that aren't listed in the kubelet/manifests directory. You can observe this by running the following kubectl commands:

```bash
PS C:\Users> kubectl get pods -A
NAMESPACE            NAME                                           READY   STATUS    RESTARTS   AGE
kube-system          coredns-7db6d8ff4d-jfhw9                       0/1     Pending   0          3m6s
kube-system          coredns-7db6d8ff4d-k6kjv                       0/1     Pending   0          3m6s
...
```

#### Install Calico CNI provider 

To download the Calico configuration file and create according resources, run: 
```bash
root@calico-control-plane:/# curl -O https://raw.githubusercontent.com/projectcalico/calico/refs/heads/release-v3.28/manifests/calico.yaml
root@calico-control-plane:/# kubectl create -f Calico.yaml
```

You will then see the resources are correctly in place by running:

<details><summary><code>root@calico-control-plane:/# kubectl get pods --all-namespaces -o wide</code><br></summary>
<br>

```bash
NAMESPACE            NAME                                           READY   STATUS    RESTARTS   AGE     IP              NODE                   NOMINATED NODE   READINESS GATES
kube-system          calico-kube-controllers-5b9b456c66-p5wb5       1/1     Running   0          7m53s   192.168.9.131   calico-worker          <none>           <none>
kube-system          calico-node-54gt9                              1/1     Running   0          7m54s   172.18.0.4      calico-control-plane   <none>           <none>
kube-system          calico-node-8f2r8                              1/1     Running   0          7m54s   172.18.0.3      calico-worker          <none>           <none>
kube-system          coredns-7db6d8ff4d-jfhw9                       1/1     Running   0          24m     192.168.9.129   calico-worker          <none>           <none>
kube-system          coredns-7db6d8ff4d-k6kjv                       1/1     Running   0          24m     192.168.9.132   calico-worker          <none>           <none>
kube-system          etcd-calico-control-plane                      1/1     Running   0          24m     172.18.0.4      calico-control-plane   <none>           <none>
kube-system          kube-apiserver-calico-control-plane            1/1     Running   0          24m     172.18.0.4      calico-control-plane   <none>           <none>
kube-system          kube-controller-manager-calico-control-plane   1/1     Running   0          24m     172.18.0.4      calico-control-plane   <none>           <none>
kube-system          kube-proxy-rc2pk                               1/1     Running   0          24m     172.18.0.3      calico-worker          <none>           <none>
kube-system          kube-proxy-scthx                               1/1     Running   0          24m     172.18.0.4      calico-control-plane   <none>           <none>
kube-system          kube-scheduler-calico-control-plane            1/1     Running   0          24m     172.18.0.4      calico-control-plane   <none>           <none>
local-path-storage   local-path-provisioner-988d74bc-phz5f          1/1     Running   0          24m     192.168.9.130   calico-worker          <none>           <none>
```
</details>

Calico sets up a <ins>**DaemonSet**</ins> in Kubernetes to run its components on every node in the cluster. This is essential for managing networking for the Pods. 

The coordination container in Calico is responsible for tasks like aggregating or proxying metadata from Kubernetes, for example, aggregating NetworkPolicy information in a single place so that it can easily be consumed and deduplicated by the DaemonSet Pods.

Within this DaemonSet, there’s often a <ins>**coordination container**</ins> (like **calico-node**) that handles key network operations. This container configures network interfaces, sets up routes, manages network policies, and ensures connectivity between Pods across different nodes. 

You can list such Calico DaemonSet by running:  
```bash
root@calico-control-plane:/# kubectl get daemonsets -n kube-system
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
calico-node   2         2         2       2            2           kubernetes.io/os=linux   12m
kube-proxy    2         2         2       2            2           kubernetes.io/os=linux   29m
```

Let's look into details of the `calico-node` container: 

<details><summary><code>root@calico-control-plane:/# kubectl describe daemonset calico-node -n kube-system</code><br></summary>
<br>

```bash
Name:           calico-node
Selector:       k8s-app=calico-node
Node-Selector:  kubernetes.io/os=linux
Labels:         k8s-app=calico-node
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 2
Current Number of Nodes Scheduled: 2
Number of Nodes Scheduled with Up-to-date Pods: 2
Number of Nodes Scheduled with Available Pods: 2
Number of Nodes Misscheduled: 0
Pods Status:  2 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           k8s-app=calico-node
  Service Account:  calico-node
  Init Containers:
   upgrade-ipam:
    Image:      docker.io/calico/cni:v3.25.0
    Port:       <none>
    Host Port:  <none>
    Command:
      /opt/cni/bin/calico-ipam
      -upgrade
    Environment Variables from:
      kubernetes-services-endpoint  ConfigMap  Optional: true
    Environment:
      KUBERNETES_NODE_NAME:        (v1:spec.nodeName)
      CALICO_NETWORKING_BACKEND:  <set to the key 'calico_backend' of config map 'calico-config'>  Optional: false
    Mounts:
      /host/opt/cni/bin from cni-bin-dir (rw)
      /var/lib/cni/networks from host-local-net-dir (rw)
   install-cni:
    Image:      docker.io/calico/cni:v3.25.0
    Port:       <none>
    Host Port:  <none>
    Command:
      /opt/cni/bin/install
    Environment Variables from:
      kubernetes-services-endpoint  ConfigMap  Optional: true
    Environment:
      CNI_CONF_NAME:         10-calico.conflist
      CNI_NETWORK_CONFIG:    <set to the key 'cni_network_config' of config map 'calico-config'>  Optional: false
      KUBERNETES_NODE_NAME:   (v1:spec.nodeName)
      CNI_MTU:               <set to the key 'veth_mtu' of config map 'calico-config'>  Optional: false
      SLEEP:                 false
    Mounts:
      /host/etc/cni/net.d from cni-net-dir (rw)
      /host/opt/cni/bin from cni-bin-dir (rw)
   mount-bpffs:
    Image:      docker.io/calico/node:v3.25.0
    Port:       <none>
    Host Port:  <none>
    Command:
      calico-node
      -init
      -best-effort
    Environment:  <none>
    Mounts:
      /nodeproc from nodeproc (ro)
      /sys/fs from sys-fs (rw)
      /var/run/calico from var-run-calico (rw)
  Containers:
   calico-node:
    Image:      docker.io/calico/node:v3.25.0
    Port:       <none>
    Host Port:  <none>
    Requests:
      cpu:      250m
    Liveness:   exec [/bin/calico-node -felix-live -bird-live] delay=10s timeout=10s period=10s #success=1 #failure=6
    Readiness:  exec [/bin/calico-node -felix-ready -bird-ready] delay=0s timeout=10s period=10s #success=1 #failure=3
    Environment Variables from:
      kubernetes-services-endpoint  ConfigMap  Optional: true
    Environment:
      DATASTORE_TYPE:                     kubernetes
      WAIT_FOR_DATASTORE:                 true
      NODENAME:                            (v1:spec.nodeName)
      CALICO_NETWORKING_BACKEND:          <set to the key 'calico_backend' of config map 'calico-config'>  Optional: false
      CLUSTER_TYPE:                       k8s,bgp
      IP:                                 autodetect
      CALICO_IPV4POOL_IPIP:               Always
      CALICO_IPV4POOL_VXLAN:              Never
      CALICO_IPV6POOL_VXLAN:              Never
      FELIX_IPINIPMTU:                    <set to the key 'veth_mtu' of config map 'calico-config'>  Optional: false
      FELIX_VXLANMTU:                     <set to the key 'veth_mtu' of config map 'calico-config'>  Optional: false
      FELIX_WIREGUARDMTU:                 <set to the key 'veth_mtu' of config map 'calico-config'>  Optional: false
      CALICO_DISABLE_FILE_LOGGING:        true
      FELIX_DEFAULTENDPOINTTOHOSTACTION:  ACCEPT
      FELIX_IPV6SUPPORT:                  false
      FELIX_HEALTHENABLED:                true
    Mounts:
      /host/etc/cni/net.d from cni-net-dir (rw)
      /lib/modules from lib-modules (ro)
      /run/xtables.lock from xtables-lock (rw)
      /sys/fs/bpf from bpffs (rw)
      /var/lib/calico from var-lib-calico (rw)
      /var/log/calico/cni from cni-log-dir (ro)
      /var/run/calico from var-run-calico (rw)
      /var/run/nodeagent from policysync (rw)
  Volumes:
   lib-modules:       
    Type:          HostPath (bare host directory volume)
    Path:          /lib/modules
    HostPathType:
   var-run-calico:
    Type:          HostPath (bare host directory volume)
    Path:          /var/run/calico
    HostPathType:
   var-lib-calico:
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/calico
    HostPathType:
   xtables-lock:
    Type:          HostPath (bare host directory volume)
    Path:          /run/xtables.lock
    HostPathType:  FileOrCreate
   sys-fs:
    Type:          HostPath (bare host directory volume)
    Path:          /sys/fs/
    HostPathType:  DirectoryOrCreate
   bpffs:
    Type:          HostPath (bare host directory volume)
    Path:          /sys/fs/bpf
    HostPathType:  Directory
   nodeproc:
    Type:          HostPath (bare host directory volume)
    Path:          /proc
    HostPathType:
   cni-bin-dir:
    Type:          HostPath (bare host directory volume)
    Path:          /opt/cni/bin
    HostPathType:
   cni-net-dir:                                                 [1] 
    Type:          HostPath (bare host directory volume)
    Path:          /etc/cni/net.d
    HostPathType:
   cni-log-dir:
    Type:          HostPath (bare host directory volume)
    Path:          /var/log/calico/cni
    HostPathType:
   host-local-net-dir:
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/cni/networks
    HostPathType:
   policysync:
    Type:               HostPath (bare host directory volume)
    Path:               /var/run/nodeagent
    HostPathType:       DirectoryOrCreate
  Priority Class Name:  system-node-critical
  Node-Selectors:       kubernetes.io/os=linux
  Tolerations:          :NoSchedule op=Exists
                        :NoExecute op=Exists
                        CriticalAddonsOnly op=Exists
Events:
  Type    Reason            Age   From                  Message
  ----    ------            ----  ----                  -------
  Normal  SuccessfulCreate  12m   daemonset-controller  Created pod: calico-node-54gt9
  Normal  SuccessfulCreate  12m   daemonset-controller  Created pod: calico-node-8f2r8
```
> [1] Calico mounts a hostPath volume type that connects to `/etc/cni/net.d/` on the kubelet. The CNI binary for the Calico-node process accesses this hostPath to call the CNI API when an IP address is needed for a new Pod. This setup serves as the installation mechanism for the host's CNI provider. 

</details>

<details><summary>We can verify Calico's Pod-to-Pod networking following the steps.</summary>

- Deploy an nginx pod:
  ```bash
  root@calico-control-plane:/# kubectl run test-nginx --image=nginx --port=80
  ```

- Expose the nginx pod as a service:
  ```bash
  root@calico-control-plane:/# kubectl expose pod test-nginx --port=80 --target-port=80
  ```

- Get the service’s cluster IP:
  ```bash
  root@calico-control-plane:/# kubectl get svc test-nginx
  NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
  test-nginx   ClusterIP   10.96.210.232   <none>        80/TCP    20m
  ```

- Run a test pod:
  ```bash
  root@calico-control-plane:/# kubectl run test-pod --image=busybox --restart=Never -- sleep 3600
  ```

- Exec into the test pod:
  ```bash
  root@calico-control-plane:/# kubectl exec -it test-pod -- sh
  ```

- Test connectivity:
  ```bash
  / # wget http://<CLUSTER-IP>:80
  / # cat index.html
  <!DOCTYPE html>
  <html>
  <head>
  <title>Welcome to nginx!</title>
  <style>
  html { color-scheme: light dark; }
  body { width: 35em; margin: 0 auto;
  font-family: Tahoma, Verdana, Arial, sans-serif; }
  </style>
  </head>
  <body>
  <h1>Welcome to nginx!</h1>
  <p>If you see this page, the nginx web server is successfully installed and
  working. Further configuration is required.</p>
  
  <p>For online documentation and support please refer to
  <a href="http://nginx.org/">nginx.org</a>.<br/>
  Commercial support is available at
  <a href="http://nginx.com/">nginx.com</a>.</p>
  
  <p><em>Thank you for using nginx.</em></p>
  </body>
  </html>
  ```

</details>

We can now examine the routes created by Calico.

Let's first install `calicoctl`, a command-line tool for managing Calico networking.  

```bash
root@calico-control-plane:~# curl -L https://github.com/projectcalico/calico/releases/download/v3.28.2/calicoctl-linux-amd64 -o calicoctl
root@calico-control-plane:~# mv calicoctl usr/local/bin/
root@calico-control-plane:~# chmod +x usr/local/bin/calicoctl
root@calico-control-plane:~# calicoctl version
Client Version:    v3.28.2
Git commit:        9a96ee39f
Cluster Version:   v3.28.2
Cluster Type:      k8s,bgp,kubeadm,kdd
```


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
