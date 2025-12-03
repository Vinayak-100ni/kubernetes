#### 1. install gate way api (ex. for googlecloud)
gcloud container clusters update CLUSTER_NAME \
  --region REGION \
  --gateway-api=standard
#### 2. check 
  kubectl get gatewayclass
#### 3. create loadbalancer using Gateway.yml file
#### 4. create HTTProute yml file
