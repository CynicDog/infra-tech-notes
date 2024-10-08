<details>
<summary><h1>Core-Kubernetes-Notes</h1></summary>
  
<details>
<summary><h2>Chapter 1. Why Kubernetes exists</h2></summary>

### Reviewing a few key terms before we get started 

- **CNI** and **CSI** — The container networking interface and container storage interface, respectively, that allow pluggable networking and storage for Pods that run in Kubernetes.
- **Container** — A standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.
- **Control plane** — The brains of a Kubernetes cluster, where scheduling of containers and managing all Kubernetes objects takes place (sometimes referred to as Masters).
- **DaemonSet** — Ensures that all (or some) Nodes run a copy of a Pod.
- **OCI** — The common image format for building executable, self-contained applications. Also referred to as Docker images.
- **Privileged containers** — A container that can run with elevated permissions, granting them access to all devices, capabilities, and the host's kernel features, effectively giving them the same level of access as processes running directly on the host system.

### Containers and images 

The OCI specification is a standard way to define an image that can be executed by a program such as Docker, and it ultimately is a tarball with various layers.

When the nodes in the cluster respond to ongoing events and update their Node objects through the kubelet's communication with the API server, things can go wrong at any time. So we refer to Kubernetes as an **eventually consistent system**, where reconciliation of the desired state over time is a key design philosophy.

</details>


<details>
<summary><h2>Chapter 2. Why the Pod?</h2></summary>
</details>

<details>
<summary><h2>Chapter 3. Let's build a Pod </h2></summary>
</details>

<details>
<summary><h2>Chapter 4. Using cgroups for process in our Pods</h2></summary>
</details>

<details>
<summary><h2>Chapter 5. CNIs and providing the Pod with a network</h2></summary>
</details>

<details>
<summary><h2>Chapter 6. Troubleshooting large-scale network errors</h2></summary>
</details>

<details>
<summary><h2>Chapter 7. Pod storage and the CSI</h2></summary>
</details>

<details>
<summary><h2>Chapter 8. Storage implementation and modelling</h2></summary>
</details>

<details>
<summary><h2>Chapter 9. Running Pods: How the kubelet works</h2></summary>
</details>

<details>
<summary><h2>Chapter 10. DNS in Kubernetes</h2></summary>
</details>

<details>
<summary><h2>Chapter 11. The core of the controlplane</h2></summary>
</details>

<details>
<summary><h2>Chapter 12. etcd and controlplane</h2></summary>
</details>

<details>
<summary><h2>Chapter 13. Container and Pod Security</h2></summary>
</details>

<details>
<summary><h2>Chapter 14. Nodes and Kubernetes Security</h2></summary>
</details>

<details>
<summary><h2>Chapter 15. Installing applications</h2></summary>
</details>

</details>
