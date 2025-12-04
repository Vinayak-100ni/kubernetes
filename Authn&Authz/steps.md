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
