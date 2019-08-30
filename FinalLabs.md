# CKA Practice Exam: Part 1

Create deploy: 
    
    kubectl run webapp --image=linuxacademycontent/podofminerva:latest --port=80 --replicas=3 -n web

Create SVC >> 
    
    kubectl expose deployment/webapp --port=80 --target-port=80 --type=NodePort -n web --dry-run -o yaml > web-service.yaml
    
   + Add namespaces
   + Add nodePort: `nodePort: 30080`   

    kubectl edit deploy webapp -n web

Add liveness and readiness probe in this deployment:
 
 ```yaml
 livenessProbe:
   httpGet:
     path: /healthz
     port: 8081
 readinessProbe:
   httpGet:
     path: /
     port: 80    
```     

# CKA Practice Exam: Part 2
You have been given access to a two-node cluster. Within that cluster, a PersistentVolume has already been created. You must identify the size of the volume in order to make a PersistentVolumeClaim and mount the volume to your pod. Once you have created the PVC and mounted it to your running pod, you must copy the contents of /etc/passwd to the volume. Finally, you will delete the pod and create a new pod with the volume mounted in order to demonstrate the persistence of data. You must perform the following tasks in order to complete this hands-on lab:

All objects should be in the web namespace.
The PersistentVolumeClaim name should be data-pvc.
The PVC request should be 256 MiB.
The access mode for the PVC should be ReadWriteOnce.
The storage class name should be local-storage.
The pod name should be data-pod.
The pod image should be busybox and the tag should be 1.28.
The pod should request the PersistentVolumeClaim named data-pvc, and the volume name should be temp-data.
The pod should mount the volume named temp-data to the /tmp/data directory.
The name of the second pod should be data-pod2.

- Create a PVC from docs: 
    + namespace
    + storageclass
    + GB: 

- kubectl run data-pod --image=busybox --restart=Never -o yaml --dry-run -- /bin/sh -c 'sleep 3600' > data-pod.yaml
    + add namspace
    + change-name
    + add volume mounts

```yaml
namespace: web
  volumeMounts: 
  - name: temp-data
    mountPath: /tmp/data
volumes: 
- name: temp-data
persistentVolumeClaim:
claimName: data-pvc
```    

https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/


# CKA Practice Exam: Part 3
You have been given access to a three-node cluster. You will be responsible for creating a deployment and a service to serve as a front end for a web application. In addition to the web application, you must deploy a Redis database and make sure the web application can only access this database using the default port of 6379. You will first create a default-deny network policy, so all pods within your Kubernetes are not able to communicate with each other by default. Then you will create a second network policy that specifies the communication on port 6379 between the web application and the database using their label selectors. You must apply these specifications to your resources in order to complete this hands-on lab:

Create a deployment named webfront-deploy.
The deployment should 
The deployment should expose container port 80 on each pod and contain 2 replicas.
Create a service named webfront-service and expose port 80, target port 80.
The service should be exposed externally by listening on port 30080 on each node.
Create one pod named db-redis using the image redis and the tag latest.
Verify that you can communicate to pods by default.
Create a network policy named default-deny that will deny pod communication by default.
Verify that you can no longer communicate between pods.
Apply the label role=frontend to the web application pods and the label role=db to the database pod.
Create a network policy that will apply an ingress rule for the pods labeled with role=db to allow traffic on port 6379 from the pods labeled role=frontend.
Verify that you have applied the correct l



Create a deployment and a service to expose your web front end.

     kubectl create deployment webfront-deploy  --image=nginx:1.7.8  --dry-run -o yaml > webfront-deploy.yaml

Add container port 80, to have your final YAML look like this:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: webfront-deploy
  name: webfront-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webfront-deploy
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: webfront-deploy
    spec:
      containers:
      - image: nginx:1.7.8
        name: nginx
        ports:
        - containerPort: 80
        resources: {}
status: {}
```

    kubectl apply -f webfront-deploy.yaml

    kubectl scale deployment/webfront-deploy --replicas=2

    kubectl expose deployment/webfront-deploy --port=80 --target-port=80 --type=NodePort --dry-run -o yaml webfront-service.yaml

Add the name and the nodePort, the complete YAML will look like this:
```yaml        
  apiVersion: v1
  kind: Service
  metadata:
    creationTimestamp: null
    labels:
      app: webfront-deploy
    name: webfront-service
  spec:
    ports:
    - port: 80
      protocol: TCP
      targetPort: 80
      nodePort: 30080
    selector:
      app: webfront-deploy
    type: NodePort
  status:
    loadBalancer: {}
  ```

    kubectl apply -f webfront-service.yaml

Verify that you can communicate with your pod directly:
          
        # wget -O- [pod_ip_address]:80
        # wget --spider --timeout=1 webfront-service

Create a database server to serve as the backend database.
  kubectl run db-redis --image=redis --restart=Never

Create a network policy that will deny communication by default.
```yaml
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: default-deny
  spec:
    podSelector: {}
    policyTypes:
    - Ingress
```

    kubectl apply default-deny.yaml

Verify that communication has been disabled by default:
    
    kubectl run busybox --rm -it --image=busybox /bin/sh
    # wget -O- [pod_ip_address]:80

Apply the labels and create a communication over port 6379 to the database server.
    
```
  kubectl get po
  kubectl label po <pod_name> role=frontend
  kubectl label po db-redis role=db
  kubectl get po --show-labels
```
```yaml
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: redis-netpolicy
  spec:
    podSelector:
      matchLabels:
        role: db
    ingress:
    - from:
      - podSelector:
          matchLabels:
            role: frontend
      ports:
      - port: 6379
```

    kubectl apply -f redis-netpolicy.yaml
    kubectl get netpol
    kubectl describe netpol redis-netpolicy
    kubectl get po --show-labels