<details>
<summary><h1>Kubernetes for Developers</h1></summary>
<details>
<summary><h3>CH3. Deploying to Kubernetes</h3></summary>

### Kubernetes Architecture 

Kubernetes is an abstraction layer that sits at the workload level on top of the raw compute primitives like VMs (or bare metal machines) and load balancers.

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

When the nodes in the cluster respond to ongoing events and update their Node objects through the kubelet's communication with the API server, things can go wrong at any time. So we refer to Kubernetes as an **eventually consistent system**, where reconciliation of the desired state over time is a key design philosophy.

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

</details>


<details>
<summary><h3>CH2. Why the Pod?</h3></summary>
</details>

<details>
<summary><h3>CH3. Let's build a Pod </h3></summary>
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
