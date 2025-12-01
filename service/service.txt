connect port container to out side world [Forwards a port from your local machine (host) to a port on a Kubernetes service running inside a cluster, allowing external access.]
  ## sudo -E kubectl port-forward service/nginx-service -n nginx 81:80 --address=0.0.0.0 
                                                |                |      
                            [service file metadata name]    [this port is for instance and we have to unable it in SG.]

## expose ingress
    kubectl get svc -n ingress-nginx [recheck this command]
    kubectl port-forward service/ingress-nginx-controller -n ingress-nginx 80:80 --address=0.0.0.0  [if not working]
then use --->> sudo -E kubectl port-forward service/ingress-nginx-controller -n ingress-nginx 80:80 --address=0.0.0.0

# 🌐 Kubernetes Services – Simple Explanation

A **Service in Kubernetes** is a **stable network endpoint** used to access one or more Pods.

**Why is it needed?**

Pods:

* Get destroyed and recreated
* Their IP addresses change

A Service gives:
✅ A **fixed IP address**
✅ A **DNS name**
✅ **Load balancing** between multiple Pods

> Think of a Service as a **permanent address for temporary Pods**.

---

## How a Service works (very simple)

1. Pods have labels:

   ```yaml
   labels:
     app: nginx
   ```

2. The Service finds Pods using:

   ```yaml
   selector:
     app: nginx
   ```

3. It sends traffic to **all matching pods**.

---

## Types of Services in Kubernetes

### 1. 🟢 ClusterIP (default)

* Only accessible **inside the cluster**
* Used for internal communication

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

Access inside cluster:

```
http://nginx-clusterip
```

---

### 2. 🟡 NodePort

* Accessible using:
  `NodeIP : NodePort`
* Range: **30000 – 32767**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

Access:

```
http://<NodeIP>:30080
```

---

### 3. 🔵 LoadBalancer

* Uses **cloud provider**
* Gives **external IP automatically**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-lb
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

Access:

```
http://<EXTERNAL-IP>
```

---

### 4. 🟣 ExternalName

* Redirects to an **external service**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: google-service
spec:
  type: ExternalName
  externalName: google.com
```

Access:

```
http://google-service
```

---

## Using Service with a Deployment (Recommended)

Deployment:

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
          image: nginx:latest
          ports:
            - containerPort: 80
```

Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

Apply:

```bash
kubectl apply -f deploy.yaml
kubectl apply -f service.yaml
```

---

## How to test your Service

Get service:

```bash
kubectl get svc
```

Describe:

```bash
kubectl describe svc nginx
```

Test inside a pod:

```bash
kubectl run test --image=busybox -it --rm -- sh
wget -qO- nginx
```

---

## One Line Summary

```text
Service = Stable + Load balanced access to Pods
``
