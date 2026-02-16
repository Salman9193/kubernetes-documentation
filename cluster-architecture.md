# Kubernetes Cluster Architecture

> This article provides an overview of Kubernetes cluster architecture, detailing the roles and components of master and worker nodes in managing containerized applications.

This article presents a high-level overview of how Kubernetes organizes and manages containerized applications. You will learn about the roles, responsibilities, and configurations for each component, as well as practical insights for examining an existing cluster.

Kubernetes simplifies deployment, scaling, and management of containers through automation. Imagine two kinds of ships: cargo ships (worker nodes) that carry containers, and control ships (master nodes) that monitor and manage the cargo ships. In Kubernetes, the cluster consists of nodes—either physical or virtual—hosting your applications.

---

## Kubernetes Cluster Architecture Diagram

![Kubernetes Cluster Architecture](https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg)

---

## Master Node Components

The master node controls the cluster, tracking all nodes, deciding where applications run, and monitoring the overall fleet. Think of it as the command center coordinating the ships.

### Key Master Components:

- **ETCD Cluster:** Stores cluster-wide configuration and state data using a key-value format with quorum mechanism for reliability.
- **Kube Scheduler:** Determines the best node for new container deployments, considering load, resources, and constraints (taints, tolerations, node affinity).
- **Controllers:** Manage node operations, container replication, and ensure the desired state is maintained.
- **Kube API Server:** Acts as the central hub for all cluster communication and management.

> 💡 **Controllers** work like dock staff, ensuring the desired number of containers are running and managing node operations.

---

## Worker Node Components

Worker nodes (the cargo ships) run the containerized applications. Each worker node is managed by:

- **Kubelet:** The node's "captain" that manages the container lifecycle. Receives commands from the API server to create, update, or delete containers, and reports node status.
- **Kube Proxy:** Configures networking rules for inter-container and inter-node communication (e.g., between web servers and databases across different nodes).

> 💡 **Container Runtime:** All control system components are containerized. Each node—master or worker—needs a compatible container runtime engine (Docker, Containerd, or CRI-O).

---

## Summary of Kubernetes Architecture

| Component Category | Key Components                                     | Description                                                                                                 |
| ------------------ | -------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Master Node**    | etcd, Kube Scheduler, Controllers, Kube API Server | Centralized control and management of the entire cluster.                                                   |
| **Worker Node**    | Kubelet, Kube Proxy                                | Manages container lifecycle and ensures network communication between services.                             |

This clear separation and coordination between **master** and **worker nodes** is fundamental to Kubernetes' automated container orchestration capabilities.

---


## Control Plane Components

The Control Plane handles cluster-wide decisions like scheduling and scaling.

### 1️⃣ kube-apiserver

**Central communication hub for the Kubernetes cluster.**

- All interactions (e.g., `kubectl`) go through it
- Validates and processes REST requests
- Default secure port: **6443**

> 💡 **Interview Tip:** "The kube-apiserver is the front door of the Kubernetes cluster."

### 2️⃣ etcd

**Distributed key-value store holding all cluster state.**

Stores:
- Pods, Nodes, ConfigMaps, Secrets, Services, Desired state

**Key Characteristics:**
- Strongly consistent (uses quorum/majority rule)
- Default port: **2379**
- **Must be backed up regularly**—losing etcd loses cluster state

### 3️⃣ kube-scheduler

**Assigns newly created Pods to nodes (does not start containers).**

Evaluates:
- Resource requests (CPU/memory)
- Taints/tolerations
- Node/Pod affinity
- Topology constraints

### 4️⃣ kube-controller-manager

**Runs controllers that reconcile desired vs. actual state.**

Key Controllers:
- Node Controller
- ReplicaSet Controller
- Deployment Controller
- Endpoint Controller
- Job Controller

> 💡 Powers Kubernetes' **reconciliation loop**: Desired ≠ Actual → Fix it

### 5️⃣ cloud-controller-manager (Cloud-only)

**For AWS/Azure/GCP environments:**

- Manages load balancers
- PV provisioning
- Node lifecycle

---

## Worker Node Components

Worker nodes execute the actual applications.

### 1️⃣ kubelet

**Node agent that:**

- Registers node with cluster
- Watches assigned Pods, pulls images, starts containers via runtime
- Reports node/Pod status
- If kubelet stops: Node goes **NotReady**; Pods rescheduled

### 2️⃣ Container Runtime

**Kubernetes uses CRI (Container Runtime Interface).**

Common runtimes:
- containerd
- CRI-O
- ⚠️ Dockershim removed in newer K8s versions

### 3️⃣ kube-proxy

**Handles Pod/Service networking.**

- Maintains iptables/ipvs rules for service discovery
- Enables cluster communication

---

## Pod Creation Flow (Step-by-Step)

When you run `kubectl apply -f pod.yaml`:

1. **kubectl** → **kube-apiserver** (validates request)
2. **API server** → stores in **etcd**
3. **kube-scheduler** detects unscheduled Pod → assigns node
4. **kubelet** on node detects assignment → instructs runtime
5. **Container starts** → status reported back

> 📌 **Common interview flow question!**

---

## Failure Scenarios

| Failure | Impact | Recovery |
|---------|--------|----------|
| **kube-apiserver down** | No new changes; existing workloads run | Restart/redeploy |
| **etcd loses quorum** | Cluster read-only; no state changes | Restore quorum/backup |
| **Worker node fails** | Node Controller detects → Pods rescheduled | Self-healing via controllers |
| **kubelet stops** | Node NotReady → Pods rescheduled | Restart kubelet |

---

## 🔹 Top Interview Questions

### Main Kubernetes components?

**Control Plane:** apiserver, etcd, scheduler, controller-manager, cloud-controller-manager (optional)

**Worker:** kubelet, kube-proxy, container runtime

### Pod deployment flow?

`kubectl` → API server → etcd → scheduler → kubelet → runtime → container

### Scheduler vs. controller-manager?

- **Scheduler:** Where Pod runs (placement decision)
- **Controller:** Ensures desired state is maintained

### Can nodes talk directly to etcd?

**No**—only kube-apiserver can access etcd.

### If etcd lost?

Cluster state gone; workloads may run but unmanageable.

### Kubernetes self-healing?

- Node failure → Pods rescheduled
- ReplicaSets recreate missing Pods

### What is CRI?

**Container Runtime Interface**—plugs different runtimes into Kubernetes.

### Reconciliation loop?

Controllers continuously compare desired vs. actual state and take corrective actions.

---

## 🔴 Advanced Scenarios

- ❓ What if kubelet stops?
- ❓ What if scheduler fails?
- ❓ How does K8s handle NotReady nodes?
- ❓ Taints/tolerations impact on scheduling?
- ❓ How to back up etcd?
- ❓ High-availability control plane?

---
