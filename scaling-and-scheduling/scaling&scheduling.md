A "ResourceQuota" in Kubernetes is like setting a spending limit for a namespace.
 A rule in Kubernetes that sets the maximum resources a namespace can use. It is useful When we have multiple teams or apps in the same cluster and you don’t want one team 
 to use everything. it will share resources fairly, avoid crashes, and control costs.
    example:- temp> spec> containers>
                           resources:  
                            requests:
                              cpu: 100m
                              memory: 128Mi
                            limits:
                              cpu: 200m
                              memory: 256Mi

A "probe" is a way for Kubernetes to test the health or status of a container by running a command, making an HTTP request, or opening a TCP connection.
| Probe Type      | Checks                                    | If Fails                                               | **When to Use**                                                      | **Example**                                        |
| --------------- | ----------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------- | -------------------------------------------------- |
| Liveness Probe  | Is the container still alive and working? | Kubernetes "restarts"   the container                  | App might **hang or crash** but still keep running in the background | `/health` endpoint that returns 200 if OK          |
| Readiness Probe | Is the container ready to accept traffic? | Pod is "removed from Service" until it’s ready again   | App is running but needs **warm-up time** before handling requests   | `/ready` endpoint after DB connection established  |
| Startup Probe   | Has the app finished starting?            | If it fails, Kubernetes "kills and restarts" the pod   | Slow-starting apps like large Java/Spring Boot apps                  | Check `/startup` before running liveness/readiness |

### probing mechanisms 

    |         |               exec              |         http                |       TCP
     probe    | exec:                           |  httpGet:                   |   tcpSocket:
                command:                            path: /health                 port: 8080
                 - mongo                             port: 8080
                 - --eval
                 - "db.adminCommand('ping')"
    success  |             0                   |   200-399                   |  if port accepts traffic

    failure  |             1                   |  other than 200-399         | if port can't accept traffic

### probing customization:

                          |           purpose                                         | default value
initialDelaySeconds       | Delay to run the probe initially                          | 0 sec
periodSeconds             | How frequently probe should execute after initial delay   | 10 sec
timeoutSeconds            | Timeout period to mark as failure                         | 1 sec
failure/successThreshold  | how many times to retry in case of failure                | 3 times


"A taint is like putting a ‘Do Not Enter’ sign on a Kubernetes node."
Pods can’t run on that node unless they have special permission called a toleration.
We use taints to keep certain workloads on specific nodes — for example, keeping GPU jobs only on GPU nodes, or keeping critical apps separate from others.
Taints keep things organized and prevent the wrong Pods from running in the wrong place.

    - [ kubectl taint nodes <node-name> key=value:effect]
        kubectl taint nodes node1 gpu=true:NoSchedule

>template > spec >   container > 
                       tolerations:
                       - key: "gpu"
                         operator: "Equal"
                         value: "true"
                         effect: "NoSchedule"



