## The default scheduler: 
Label your node as being located in availability zone 1:

kubectl label node chadcrowell1c.mylabserver.com availability-zone=zone1
Label your node as dedicated infrastructure:

kubectl label node chadcrowell1c.mylabserver.com share-type=dedicated
Here is the YAML for the deployment to include the node affinity rules:
```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
    name: pref
    spec:
    replicas: 5
    template:
        metadata:
        labels:
            app: pref
        spec:
        affinity:
            nodeAffinity:
            preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 80
                preference:
                matchExpressions:
                - key: availability-zone
                    operator: In
                    values:
                    - zone1
            - weight: 20
                preference:
                matchExpressions:
                - key: share-type
                    operator: In
                    values:
                    - dedicated
        containers:
        - args:
            - sleep
            - "99999"
            image: busybox
            name: main
```    
## Multiple Scheduler
        ```
        spec: 
            schedulerName: my-scheduler
        ```

## Scheduling Pods with Resource Limits and Label Selectors
Pods request/limits. 

## Scheduler events: 
kubectl describe kube-scheduler
kubectl get events (events for default namespace)

/var/log/kube-scheduler.log


## Lab 5: 

Taint one of the worker nodes to repel work.
    kubectl taint node <node_name> node-type=prod:NoSchedule
