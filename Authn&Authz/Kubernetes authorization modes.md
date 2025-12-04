First remember this:

> **Authentication = Who are you?**
> **Authorization = What are you allowed to do?**

The flag we are talking about is set on the **kube-apiserver**:

```
--authorization-mode=Node,RBAC,...
```

This tells the API server:

> “Which authorization systems should I use to allow or block a request?”

You can enable **one or more** modes together.

---

# 1. Node Authorization

This one is ONLY for **worker nodes (kubelet)**.

It protects the cluster from **malicious or compromised nodes**.

### What does it allow?

A node can only:
✅ Read pods scheduled on itself
✅ Read secrets/configmaps used by its own pods
✅ Update its own node status
❌ Cannot touch other nodes’ pods or secrets

### Example

If Pod is running on **Node-A**, then:

* Node-A → can access details ✅
* Node-B → access denied ❌

So:

> **Node Mode = Each node can only access its own resources**

This prevents one hacked node from destroying the whole cluster.

---

# 2. RBAC (Role Based Access Control) ✅ (Most important)

This is what **humans & applications** use.

RBAC decides:

* Who can access what
* In which namespace
* And which actions (get, list, create, delete…)

RBAC works using:

| Object             | Purpose                    |
| ------------------ | -------------------------- |
| Role               | Namespace permissions      |
| ClusterRole        | Whole cluster permissions  |
| RoleBinding        | Attach Role to user        |
| ClusterRoleBinding | Attach ClusterRole to user |

### Example

User: `vinayak`
Permission: Only view pods in `dev` namespace

RBAC allows:
✅ `kubectl get pods -n dev`
❌ `kubectl delete pod`
❌ `kubectl get secrets`

So:

> **RBAC = Controls user / service account permissions**

This is used in **almost every production cluster**.

---

# 3. ABAC (Attribute Based Access Control)

This is an **older / less-used** system.

Here permissions are stored in a **JSON file on the master** and defined like:

> “If user = X AND namespace = Y AND resource = Z then allow”

It is:
❌ Hard to manage
❌ Not dynamic
✅ But flexible

Example rule in simple form:

```
User: vinayak
Namespace: dev
Resource: pods
Action: get
```

But because it’s file-based, **RBAC is preferred over ABAC now**.

So:

> **ABAC = Old, file-based, not recommended now**

---

# 4. Webhook Authorization

Here Kubernetes asks an **external server** for permission.

The API server sends a webhook call like:

> “User vinayak wants to delete a pod. Allow or deny?”

External system replies:
✅ or ❌

Used when companies want:

* Custom logic
* Integration with external systems
* Advanced security

So:

> **Webhook = External system decides access**

Very popular in large enterprises.

---

# 5. AlwaysAllow

This means:

> “Allow EVERY request, no matter who it is”

⚠️ Very dangerous
Mostly used for testing only

No security at all.

---

# 6. AlwaysDeny

This means:

> “DENY everything, no one can do anything”

Nothing works if you enable this (even admin cannot access).

Used mainly for testing purposes.

---

# Summary Table (Easy to remember)

| Mode            | Used for             | Safe for production?   |
| --------------- | -------------------- | ---------------------- |
| **Node**        | Protect nodes        | ✅ Yes                  |
| **RBAC**        | Protect users & apps | ✅ Yes (MOST IMPORTANT) |
| **ABAC**        | Old method           | ❌ Not recommended      |
| **Webhook**     | External decision    | ✅ Yes (advanced)       |
| **AlwaysAllow** | Allow everything     | ❌ No                   |
| **AlwaysDeny**  | Block everything     | ❌ No                   |

In most production systems, you will see:

```
--authorization-mode=Node,RBAC
```

Because:
✅ Node protects nodes
✅ RBAC protects users and applications
Together = secure cluster

---

## Super Simple Memory Trick 🧠

Think of a company building:

| Kubernetes  | Real life                    |
| ----------- | ---------------------------- |
| Node        | Security at building gate    |
| RBAC        | Manager controls room access |
| ABAC        | Old paper system             |
| Webhook     | Head office calls            |
| AlwaysAllow | Doors always open            |
| AlwaysDeny  | Doors locked forever         |

---

