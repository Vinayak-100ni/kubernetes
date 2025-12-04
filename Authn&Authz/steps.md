1. create service account
   ``` kubectl create serviceaccount twp-day9 ```
2. create token ``` TOKEN=$(kubectl create token twp-day9) ```
3. we can check current context with command  ``` kubectl config current-context ``` [ ex- kubernetes-admin@kubernetes]
4. we can set credentials with ``` kubectl config set-credentials twp-day9--token-$TOKEN ```
5. now we set context ```kubectl config set-context twp-day9-user-context --cluster=day-9-and-10 --user=twp-day9 ```

   Context name:  twp-day9-user-context
    Connects:
     Cluster:   day-9-and-10  
     User:     twp-day9  
     Namespace: (not set → default)
6. now we can switch context with the command ``` kubectl config use-context twp-day9-user-context ```
7. verify using command ```kubectl config current-context``` [twp-day9-user-context]
8. now we have to provide permissions to context ```AuthN done```
9. we will get all details in ```/etc/kubernetes/manifests$ ls
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
controlplane:/etc/kubernetes/manifests$ cat kube-apiserver.yaml ```
