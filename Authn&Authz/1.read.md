## 1. What is Authentication in Kubernetes?

**Authentication = “Who are you?”**

When someone (or something) tries to access a Kubernetes cluster — for example:

```bash
kubectl get pods
```

The **kube-apiserver** first checks:

> 👉 *Are you really the person / system you claim to be?*

This verification is called **authentication**.

### Common authentication methods in Kubernetes

Kubernetes can verify identity in several ways:

| Method                  | What it means                           | Example                  |
| ----------------------- | --------------------------------------- | ------------------------ |
| **Client certificates** | You use a certificate to prove identity | Used by cluster admins   |
| **Bearer tokens**       | You use a token to login                | Used by service accounts |
| **Username/Password**   | Not very common                         | Mostly in testing        |
| **OIDC (SSO)**          | Login using Google, Azure AD, etc.      | In enterprises           |
| **Webhook**             | External auth server                    | Enterprise setups        |

### Example

When you run:

```bash
kubectl get pods
```

kubectl sends:

* Your username / certificate / token
  to the **kube-apiserver**

Then API server confirms:
✅ *Yes, this user is real*

If not real:
❌ *Access denied (Unauthorized)*

---

## 2. What is Authorization in Kubernetes?

**Authorization = “What are you allowed to do?”**

Once Kubernetes knows **who you are**, it then checks:

> 👉 *Are you allowed to do this action?*

Like:

* Can you create pods?
* Can you delete services?
* Can you access secrets?

This is **authorization**.

### Common authorization modes

| Mode                              | Use                       |
| --------------------------------- | ------------------------- |
| **RBAC** ✅ (default, most common) | Role-based access control |
| ABAC                              | Attribute based           |
| Webhook                           | External authorization    |
| Node                              | Node-level restrictions   |

**RBAC is what you’ll use 99% of the time.**

---

## 3. How RBAC actually works

RBAC uses **4 main objects**:

| Object                 | Purpose                                       |
| ---------------------- | --------------------------------------------- |
| **Role**               | What actions are allowed in a namespace       |
| **ClusterRole**        | What actions are allowed in the whole cluster |
| **RoleBinding**        | Connects a Role to a user (namespace only)    |
| **ClusterRoleBinding** | Connects ClusterRole to a user (cluster-wide) |

### Example Scenario:

You want user `vinayak` to only **view pods** in the `dev` namespace.

### Role:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: dev
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

This says:
✅ Can see pods
❌ Cannot create/modify/delete pods

### RoleBinding:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: User
  name: vinayak
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

This connects:

> **User "vinayak" ➜ Role "pod-reader" in namespace dev**

✅ Now vinayak can view pods in dev, nothing else.

---

## 4. Real-world flow (Super simple)

When you run a command:

```bash
kubectl delete pod nginx
```

Behind the scenes:

1. **Authentication**

   * Kubernetes checks: Who is making this request?
   * maybe: vinayak ✅

2. **Authorization**

   * Kubernetes checks RBAC rules:

     * Is vinayak allowed to DELETE pods?
     * ❌ No role found → **Forbidden**

So you see error:

```
Error from server (Forbidden)
```

---

## 5. Easy Memory Shortcut

> **Authentication = WHO you are**
>
> **Authorization = WHAT you can do**

Or small example:

| Step | Question         | Example               |
| ---- | ---------------- | --------------------- |
| 1    | Who are you?     | Vinayak               |
| 2    | What can you do? | Only view pods in dev |

---
