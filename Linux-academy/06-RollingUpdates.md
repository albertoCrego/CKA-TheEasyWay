
## Deploying an Application, Rolling Updates, and Rollbacks

Expose the deployment and provide it a service:

```bash
kubectl expose deployment kubeserve --port 80 --target-port 80 --type NodePort
```

Set the `minReadySeconds` attribute to your deployment:

```bash
kubectl patch deployment kubeserve -p '{"spec": {"minReadySeconds": 10}}'
```

Use `kubectl apply` to update a deployment:

```bash
kubectl apply -f kubeserve-deployment.yaml
```

Use `kubectl replace` to replace an existing deployment:

```bash
kubectl replace -f kubeserve-deployment.yaml
```

Run this `curl` look while the update happens:

```bash
while true; do curl http://10.105.31.119; done
```

Perform the rolling update:

```bash
kubectl set image deployments/kubeserve app=linuxacademycontent/kubeserve:v2 --v 6
```

Describe a certain ReplicaSet:

```bash
kubectl describe replicasets kubeserve-[hash]
```

Apply the rolling update to version 3 (buggy):

```bash
kubectl set image deployment kubeserve app=linuxacademycontent/kubeserve:v3
```

Undo the rollout and roll back to the previous version:

```bash
kubectl rollout undo deployments kubeserve
```

Look at the rollout history:

```bash
kubectl rollout history deployment kubeserve
```

Roll back to a certain revision:

```bash
kubectl rollout undo deployment kubeserve --to-revision=2
```

Pause the rollout in the middle of a rolling update (canary release):

```bash
kubectl rollout pause deployment kubeserve
```

Resume the rollout after the rolling update looks good:

```bash
kubectl rollout resume deployment kubeserve
```

## Configuring an Application for High Availability and Scale

PHOTO15

```kubectl describe deployment```

```yaml
Create a ConfigMap with two keys:
kubectl create configmap appconfig --from-literal=key1=value1 --from-literal=key2=value2
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
  - name: app-container
    image: busybox:1.28
    command: ['sh', '-c', "echo $(MY_VAR) && sleep 3600"]
    env:
    - name: MY_VAR
      valueFrom:
        configMapKeyRef:
          name: appconfig
          key: key1
---
apiVersion: v1
kind: Pod
metadata:
  name: configmap-volume-pod
spec:
  containers:
  - name: app-container
    image: busybox
    command: ['sh', '-c', "echo $(MY_VAR) && sleep 3600"]
    volumeMounts:
      - name: configmapvolume
        mountPath: /etc/config
  volumes:
    - name: configmapvolume
      configMap:
        name: appconfig
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: appsecret
stringData:
  cert: value
  key: value
Create the secret:

kubectl apply -f appsecret.yaml
The YAML for a pod that will use the secret:

apiVersion: v1
kind: Pod
metadata:
  name: secret-pod
spec:
  containers:
  - name: app-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
    env:
    - name: MY_CERT
      valueFrom:
        secretKeyRef:
          name: appsecret
          key: cert

apiVersion: v1
kind: Pod
metadata:
  name: secret-volume-pod
spec:
  containers:
  - name: app-container
    image: busybox
    command: ['sh', '-c', "echo $(MY_VAR) && sleep 3600"]
    volumeMounts:
      - name: secretvolume
        mountPath: /etc/certs
  volumes:
    - name: secretvolume
      secret:
        secretName: appsecret
```

## Lab 6

Create and roll out version 1 of the application, and verify a successful deployment.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: kubeserve
spec:
replicas: 3
selector:
matchLabels:
    app: kubeserve
template:
    metadata:
    name: kubeserve
    labels:
        app: kubeserve
    spec:
    containers:
    - image: linuxacademycontent/kubeserve:v1
        name: app
```

Use the following command to create the deployment:

```bash
kubectl apply -f kubeserve-deployment.yaml --record
```

Use the following command to verify the deployment was successful:

```bash
kubectl rollout status deployments kubeserve
```

Use the following command to verify the app is at the correct version:

```bash
kubectl describe deployment kubeserve
```

Scale up the application to create high availability.

```bash
kubectl scale deployment kubeserve --replicas=5
```

Use the following command to verify the additional replicas have been created:

```bash
kubectl get pods
```

Create a service, so users can access the application.

```bash
kubectl expose deployment kubeserve --port 80 --target-port 80 --type NodePort
```

Use the following command to verify the service is present and collect the cluster IP:

```bash
kubectl get services
```

Use the following command to verify the service is responding:

```bash
curl http://<ip-address-of-the-service>
```

Use this curl loop command to see the version change as you perform the rolling update:

```bash
while true; do curl http://<ip-address-of-the-service>; done
```

Use this command to perform the update (while the curl loop is running):

```bash
kubectl set image deployments/kubeserve app=linuxacademycontent/kubeserve:v2 --v 6
```

Use this command to view the additional ReplicaSet created during the update

```bash
kubectl get replicasets
```

Use this command to verify all pods are up and running:

```bash
kubectl get pods
```

Use this command to view the rollout history:

```bash
kubectl rollout history deployment kubeserve
```
