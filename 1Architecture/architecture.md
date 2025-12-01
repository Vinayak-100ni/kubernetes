# 🌐 Kubernetes Architecture – Simple Explanation

![Kubernetes Architecture Diagram](./Screenshot%202025-12-01%20150407.png)

A Kubernetes cluster is made of **two main parts**:

1. **Master Node (Control Plane)**
2. **Worker Nodes (where containers run)**

All of this is usually created and managed on a **Cloud Provider** (like AWS, Azure, or GCP).

---

## 1. Master Node (Control Plane)

The **Master Node** is the brain of the cluster.  
It **decides what should happen** and **controls the whole system**.

It contains these main parts:

### ✅ kube-apiserver
- The **main entry point** of Kubernetes.
- All commands go through it (`kubectl`, ArgoCD, CI/CD, etc).
- Communicates with all other components.

### ✅ etcd
- A **key-value database** storing the cluster’s data.
- Stores:
  - Pods
  - Services
  - ConfigMaps
  - Secrets
  - Cluster state

### ✅ kube-scheduler
- Decides **which worker node** a new pod should run on.
- Checks CPU, Memory, GPU, etc, before placing a pod.

### ✅ kube-controller-manager
- Makes sure the **actual state = desired state**
- Example: If a pod dies → creates a new one.

### ✅ cloud-controller-manager
- Talks to the **cloud provider** (AWS, Azure, GCP).
- Manages cloud services like:
  - Load Balancers
  - Disks (EBS, Persistent Volumes)
  - Network components

---

## 2. Worker Nodes (node-cpu / node-gpu)

Worker nodes are the machines that **actually run your applications** (inside Pods).

Each worker node contains:

### ✅ kubelet
- Agent running on each node.
- Listens to the master node.
- Makes sure containers are running correctly.

### ✅ kube-proxy
- Handles **networking and load balancing** inside the cluster.
- Forwards traffic to the correct pod.

### ✅ CRI (Container Runtime Interface)
- The component that runs containers
- Supports:
  - Docker
  - containerd
  - CRI-O

### ✅ Pods
- A **Pod = one or more containers**
- This is the smallest deployable unit in Kubernetes
- Your app runs inside pods

In the diagram:
- `node-cpu` → Normal workloads
- `node-gpu` → GPU workloads (AI/ML jobs)

---

## 3. How Everything Works Together (Simple Flow)

1. You run `kubectl apply` or ArgoCD syncs a change
2. The request goes to **kube-apiserver**
3. **kube-scheduler** selects the best worker node
4. **kubelet** on that node creates the pod
5. **CRI** starts the containers
6. **kube-proxy** enables networking and communication

---

## 4. One-Line Summary

> **Master Node controls everything, Worker Nodes run everything.**

---

## 5. Quick Cheat Sheet

| Component | Role |
|------|-----|
| kube-apiserver | Entry point to cluster |
| etcd | Database for Kubernetes |
| scheduler | Chooses node for pod |
| controller-manager | Maintains desired state |
| kubelet | Controls pods on node |
| kube-proxy | Networking |
| CRI | Runs containers |
| Pod | Runs your application |

---
