# Pesristence Volumes

## Create pv with gcloud

```bash
gcloud compute disks create --size=1GiB --zone=us-central1-a mongodb
```

The YAML for a pod that will use persistent disk:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mongodb
spec:
  volumes:
  - name: mongodb-data
    gcePersistentDisk:
      pdName: mongodb
      fsType: ext4
  containers:
  - image: mongo
    name: mongodb
    volumeMounts:
    - name: mongodb-data
      mountPath: /data/db
    ports:
    - containerPort: 27017
      protocol: TCP
```

```yaml
kind: PersistentVolume
metadata:
  name: mongodb-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
    - ReadOnlyMany
  persistentVolumeReclaimPolicy: Retain
  gcePersistentDisk:
    pdName: mongodb
    fsType: ext4
```

## Access mode

(photo_16)

## PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongodb-pvc
spec:
  resources:
    requests:
      storage: 1Gi
  accessModes:
  - ReadWriteOnce
  storageClassName: ""
---
apiVersion: v1
kind: Pod
metadata:
  name: mongodb
spec:
  containers:
  - image: mongo
    name: mongodb
    volumeMounts:
    - name: mongodb-data
      mountPath: /data/db
    ports:
    - containerPort: 27017
      protocol: TCP
  volumes:
  - name: mongodb-data
    persistentVolumeClaim:
      claimName: mongodb-pvc
```

## Storage objets

Another type of disk provisioned by GKE or similar and with some extra parameter for example SSD

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd
```

and use in a pvc:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongodb-pvc
spec:
  storageClassName: fast
  resources:
    requests:
      storage: 100Mi
  accessModes:
    - ReadWriteOnce
```

## Aplication with Persistence Storage

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: kubeserve-pvc
spec:
  storageClassName: fast
  resources:
    requests:
      storage: 100Mi
  accessModes:
    - ReadWriteOnce
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: kubeserve-pvc
spec:
  storageClassName: fast
  resources:
    requests:
      storage: 100Mi
  accessModes:
    - ReadWriteOnce
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kubeserve
spec:
  replicas: 1
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
      - env:
        - name: app
          value: "1"
        image: linuxacademycontent/kubeserve:v1
        name: app
        volumeMounts:
        - mountPath: /data
          name: volume-data
      volumes:
      - name: volume-data
        persistentVolumeClaim:
          claimName: kubeserve-pvc
```

## Lab 7 :Creating Persistent Storage for Pods in Kubernetes

```yaml
 apiVersion: v1
 kind: PersistentVolume
 metadata:
   name: mongodb-pv
 spec:
   storageClassName: local-storage
   capacity:
     storage: 1Gi
   accessModes:
     - ReadWriteOnce
   hostPath:
     path: "/mnt/data"
---
 apiVersion: v1
 kind: PersistentVolumeClaim
 metadata:
   name: mongodb-pvc
 spec:
   storageClassName: local-storage
   accessModes:
     - ReadWriteOnce
   resources:
     requests:
       storage: 1Gi  
---
 apiVersion: v1
 kind: Pod
 metadata:
   name: mongodb
 spec:
   containers:
   - image: mongo
     name: mongodb
     volumeMounts:
     - name: mongodb-data
       mountPath: /data/db
     ports:
     - containerPort: 27017
       protocol: TCP
   volumes:
   - name: mongodb-data
     persistentVolumeClaim:
       claimName: mongodb-pvc
```

All right, don't mind if we delete the pod and recreate, the info still there!!!
