# Kubernetes Scheduling Basics

* **Node Selector** → Simplest way to schedule pods on nodes using exact label matching.
* **Node Affinity** → Advanced scheduling using flexible matching rules (In, NotIn, Exists, etc.).
* **Pod Affinity** → Schedule pod near other pods with specific labels.
* **Pod Anti-Affinity** → Avoid placing pods near specific pods (for high availability).
* **Node Anti-Affinity** → Prevent pods from running on certain nodes (via affinity rules).


## 1. Node Selector

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
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
          image: nginx:1.14
          ports:
            - containerPort: 80
      nodeSelector:
        disktype: ssd
```

---

## 2. Node Affinity

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
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
          image: nginx:1.14
          ports:
            - containerPort: 80
          resources:
            requests:
              memory: "2Gi"
              cpu: "1"
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: disktype
                    operator: In
                    values:
                      - ssd
```

---

# Important Points You MUST Know 🔥

## 🔹 Node Selector vs Node Affinity

* Node Selector is **hard requirement only** (no flexibility)
* Node Affinity supports:

  * `requiredDuringSchedulingIgnoredDuringExecution` → Hard rule
  * `preferredDuringSchedulingIgnoredDuringExecution` → Soft rule (best effort)

---

## 🔹 Affinity Rule Types

### 1. Required (Hard Constraint)

* Pod WILL NOT schedule if condition not met

```
requiredDuringSchedulingIgnoredDuringExecution
```

### 2. Preferred (Soft Constraint)

* Scheduler tries but may ignore

```
preferredDuringSchedulingIgnoredDuringExecution
```

---

## 🔹 Common Operators (VERY IMPORTANT)

* In
* NotIn
* Exists
* DoesNotExist
* Gt
* Lt

---

## 🔹 Pod Affinity Example (Concept)

* Use when low latency communication is required
* Example: Backend + Cache on same node

---

## 🔹 Pod Anti-Affinity Example (Concept)

* Use for high availability
* Example: Spread replicas across nodes

---

## 🔹 Topology Key (INTERVIEW FAVORITE)

* Defines where affinity applies

Common values:

* kubernetes.io/hostname → Same node
* topology.kubernetes.io/zone → Same zone

---

## 🔹 Label Dependency ⚠️

* Scheduling depends on node labels

```bash
kubectl label nodes <node-name> disktype=ssd
```

---

## 🔹 Important Behavior

* IgnoredDuringExecution means:

  * If node label changes later → pod WILL NOT be evicted

---

## 🔹 Taints vs Affinity (CONFUSION POINT)

| Feature     | Purpose                     |
| ----------- | --------------------------- |
| Taints      | Repel pods from nodes       |
| Tolerations | Allow pods on tainted nodes |
| Affinity    | Attract pods to nodes       |

---

## 🔹 When to Use What (REAL WORLD)

* Use Node Selector → Simple environments
* Use Node Affinity → Production-grade scheduling
* Use Pod Affinity → Microservices needing proximity
* Use Pod Anti-Affinity → High availability & fault tolerance

---

## 🔹 Common Mistakes 🚫

* Typo in requiredDuringSchedulingIgnoredDuringExecution
* Wrong YAML indentation
* Missing node labels
* Using only hard rules → pods stay pending

---

## 🔹 Quick Debug Commands

```bash
kubectl get nodes --show-labels
kubectl describe pod <pod-name>
kubectl get pods -o wide
```

---

## 🔹 Quick Revision (1-Min Recap)

* Node Selector = Simple filter
* Node Affinity = Advanced + flexible rules
* Pod Affinity = Keep pods together
* Pod Anti-Affinity = Spread pods apart
* Taints = Block nodes
* Tolerations = Allow exceptions

---
