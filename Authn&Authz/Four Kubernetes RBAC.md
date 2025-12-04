Four Kubernetes RBAC objects

* ✅ Role (namespace level)
* ✅ ClusterRole (cluster wide)
* ✅ RoleBinding (attach Role)
* ✅ ClusterRoleBinding (attach ClusterRole)

You can copy-paste and use them directly in your cluster.

---

# 1️⃣ Role – *Namespace permissions*

A **Role** defines WHAT actions are allowed **inside ONE namespace ONLY**.

Example:
Allow a user to **read pods** in the `dev` namespace.

### 📄 role.yml

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: dev
rules:
- apiGroups: [""]                # "" = core API group
  resources: ["pods"]            # resource type
  verbs: ["get", "list", "watch"]  # allowed actions
```

### What this means in simple form

User can:
✅ get pods
✅ list pods
✅ watch pods

In:
✅ dev namespace only

❌ Cannot delete or create pods
❌ Cannot access other namespaces

---

# 2️⃣ ClusterRole – *Whole cluster permissions*

A **ClusterRole** defines WHAT actions are allowed **ACROSS ALL namespaces**.

Example:
Allow a user to **read nodes** and **pods everywhere**.

### 📄 clusterrole.yml

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-pod-reader
rules:
- apiGroups: [""]
  resources: ["pods", "nodes"]
  verbs: ["get", "list", "watch"]
```

### What this means

User can:
✅ get/list/watch pods in ALL namespaces
✅ get/list/watch nodes in the whole cluster

Use ClusterRole for:

* Admins
* Monitoring tools
* Cluster-level permissions

---

# 3️⃣ RoleBinding – *Attach Role to user/service account*

A **RoleBinding** connects`User → Role → Namespace`

Example:
Bind **user: vinayak** to the **pod-reader Role** in `dev` namespace.

### 📄 rolebinding.yml

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: dev
subjects:
- kind: User
  name: vinayak        # must match kubectl user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader     # Role name
  apiGroup: rbac.authorization.k8s.io
```

### What this means

User `vinayak` can now:
✅ read pods **only in dev namespace**

Try:

```bash
kubectl get pods -n dev   ✅
kubectl get pods -n prod  ❌
```

---

# 4️⃣ ClusterRoleBinding – *Attach ClusterRole to user/service account*

A **ClusterRoleBinding** gives **cluster-wide access**.

Example:
Bind **user: vinayak** to **cluster-pod-reader**.

### 📄 clusterrolebinding.yml

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-pods-global
subjects:
- kind: User
  name: vinayak
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Now vinayak can:

✅ Get pods in ALL namespaces
✅ Get nodes in cluster

Test:

```bash
kubectl get pods -A
kubectl get nodes
```

---

# Very easy memory table

| Object             | Scope         | Purpose                  |
| ------------------ | ------------- | ------------------------ |
| Role               | 1 namespace   | What actions allowed     |
| ClusterRole        | Whole cluster | What actions allowed     |
| RoleBinding        | 1 namespace   | Who gets the Role        |
| ClusterRoleBinding | Whole cluster | Who gets the ClusterRole |

> **Role = City rules**
> **ClusterRole = Country rules**
> **Binding = Who follows those rules**

---

# How to apply all files

```bash
kubectl apply -f role.yml
kubectl apply -f rolebinding.yml
kubectl apply -f clusterrole.yml
kubectl apply -f clusterrolebinding.yml
```

---
