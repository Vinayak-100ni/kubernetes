# Dynamic PVC & StorageClass – Complete Guide (Kubernetes)

This document gives you a complete revision guide on how **dynamic provisioning**, **StorageClass**, **PVC**, and **volume resizing** work in Kubernetes.

---

## 📌 1. What is Dynamic Provisioning?

Dynamic provisioning means **you do NOT create the PersistentVolume manually**. Kubernetes automatically creates the storage volume whenever a PVC is created.

### 🔥 Benefits:

* No need to manually create PVs.
* Auto-creates disks (EBS, GCE PD, Azure Disk, etc.).
* Works across all cloud providers.
* Supports expansion (if enabled).

---

## 📌 2. What is a StorageClass?

A **StorageClass (SC)** defines *how* storage is provisioned.

It contains:

* Provisioner (CSI driver)
* Disk type
* Reclaim policy
* Volume binding mode
* AllowVolumeExpansion

### Example (GKE – High Performance SSD):

```yaml
a  piVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: high-performance-sc
provisioner: pd.csi.storage.gke.io
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
parameters:
  type: pd-ssd
```

---

## 📌 3. CSI (Container Storage Interface)

CSI drivers allow Kubernetes to talk to storage systems.

Examples:

* **GKE:** `pd.csi.storage.gke.io`
* **AWS:** `ebs.csi.aws.com`
* **Azure:** `disk.csi.azure.com`
* **Local:** `kubernetes.io/no-provisioner` or `local-path`

CSI handles:

* Volume creation
* Attaching/detaching
* Mounting
* Resizing

---

## 📌 4. PersistentVolumeClaim (PVC)

A PVC requests storage from a StorageClass.

Example PVC:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mydata-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: high-performance-sc
  resources:
    requests:
      storage: 20Gi
```

When applied, Kubernetes automatically:

1. Reads StorageClass
2. Creates a disk (PD/EBS/Azure Disk)
3. Creates a PV
4. Binds PV ↔ PVC

---

## 📌 5. Access Modes

| Access Mode             | Meaning                      | Multi-Pod?                    |
| ----------------------- | ---------------------------- | ----------------------------- |
| **RWO (ReadWriteOnce)** | Mounted by **one node only** | ❌ No                          |
| **ROX (ReadOnlyMany)**  | Read-only by many nodes      | ⚠️ Rare                       |
| **RWX (ReadWriteMany)** | Read-write by many nodes     | ✅ Yes (needs NFS / Filestore) |

> NOTE: Cloud disks (EBS, GCE PD, Azure Disk) = **RWO only**

---

## 📌 6. Reclaim Policies

Controls what happens when PVC is deleted.

| ReclaimPolicy | Action                                |
| ------------- | ------------------------------------- |
| **Delete**    | Volume is deleted automatically       |
| **Retain**    | Volume stays; requires manual cleanup |
| **Recycle**   | Deprecated                            |

Most StorageClasses default to **Delete**.

---

## 📌 7. VolumeBindingMode

Defines *when* the disk is created.

| Mode                     | Effect                                                                   |
| ------------------------ | ------------------------------------------------------------------------ |
| **Immediate**            | Disk created instantly when PVC is created                               |
| **WaitForFirstConsumer** | Disk created **only when pod is scheduled** → prevents wrong zone issues |

Production clusters always use **WaitForFirstConsumer**.

---

## 📌 8. Using PVC in Pods

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - mountPath: "/data"
          name: my-volume
  volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: mydata-pvc
```

This mounts the dynamically-created disk into the container.

---

## 📌 9. PVC Expansion (Resizing)

You **can increase** volume size (NOT reduce).

### Requirements:

* StorageClass has `allowVolumeExpansion: true`
* CSI driver supports resizing

### Resize example:

```yaml
spec:
  resources:
    requests:
      storage: 50Gi
```

Apply:

```bash
kubectl apply -f pvc.yaml
```

Check status:

```bash
kubectl get pvc
```

---

## 📌 10. Can multiple pods use the same volume?

Depends on AccessMode:

### ❌ Not allowed:

* Cloud disks (EBS, GCE PD) → **RWO** → only 1 node can attach.

### ✅ Allowed (RWX):

Use:

* NFS
* Google Filestore
* Amazon EFS
* Azure Files

These support **ReadWriteMany (RWX)**.

---

## 📌 11. When to Use Dynamic Provisioning

| Scenario                             | Use Dynamic PVC?                 |
| ------------------------------------ | -------------------------------- |
| Databases (MySQL, MongoDB, Postgres) | ✅ Yes                            |
| Caches (Redis)                       | ⚠️ Yes but ephemeral recommended |
| Logs                                 | ❌ Use RWX storage                |
| Shared data                          | ❌ Use NFS/EFS/Filestore          |

---

## 📌 12. Best Practices

* Use **WaitForFirstConsumer** to avoid AZ/Zone issues.
* Enable **allowVolumeExpansion**.
* Use **Retain** policy for production DBs.
* Never store DB data on hostPath.
* For multi-pod shared storage → use Filestore/NFS/EFS.

---

## 📌 13. Quick Commands

Check all StorageClasses:

```bash
kubectl get sc
```

Check PVCs:

```bash
kubectl get pvc
```

Describe a PVC:

```bash
kubectl describe pvc mydata-pvc
```

---

