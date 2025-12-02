##### install ingress controller 
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml

##### we can check service with this command
kubectl get service -o wide -w -n ingress-nginx

#### facing any issue firsly check 
 kubectl logs -n ingress-nginx -l app.kubernetes.io/name=ingress-nginx

1. adding certificate
  a. kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.5/cert-manager.yaml

