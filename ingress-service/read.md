##### install ingress controller 
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml

##### we can check service with this command
kubectl get service -o wide -w -n ingress-nginx

