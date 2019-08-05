# Security: 

## Service Accounts and 
kubectl create serviceaccount jenkins
(photo_17)

apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  serviceAccountName: jenkins
  containers:
  - image: busybox:1.28.4
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: busybox
  restartPolicy: Always

Set new credentials for your cluster:
    kubectl config set-credentials chad --

Create a role binding for anonymous 
    kubectl create clusterrolebinding cluster-system-anonymous --clusterrole=cluster-admin --

scp ca.crt cloud_

kubectl config set-cluster kubernetes --server=https://172.31.41.61:6443 --certificate-authority=ca.crt --embed-certs=true
kubectl config set-credentials chad --
kubectl config set-context kubernetes --cluster=kubernetes --
kubectl config 


## Cluster Authentication & Authororization
ClusterRole VS Role 

```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: web
  name: service-reader
rules:
- apiGroups: [""]
  verbs: ["get", "list"]
  resources: ["services"]

  kubectl create rolebinding test --role=service-reader --serviceaccount=web:default -n web
```

```
  kubectl create clusterrole pv-reader --verb=get,list --resource=persistentvolumes
  kubectl create clusterrolebinding pv-test --clusterrole=pv-reader --serviceaccount=web:default

    apiVersion: v1
    kind: Pod
    metadata:
    name: curlpod
    namespace: web
    spec:
    containers:
    - image: tutum/curl
        command: ["sleep", "9999999"]
        name: main
    - image: linuxacademycontent/kubectl-proxy
        name: proxy
    restartPolicy: Always
```


## Network policies: 

(phot_18)

deny-all: 
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

all traffic in pods with app=web
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-netpolicy
spec:
  podSelector:
    matchLabels:
      app: db
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: web
    ports:
    - port: 5432
```    

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ns-netpolicy
spec:
  podSelector:
    matchLabels:
      app: db
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          tenant: web
    ports:
    - port: 5432
```    
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ipblock-netpolicy
spec:
  podSelector:
    matchLabels:
      app: db
  ingress:
  - from:
    - ipBlock:
        cidr: 192.168.1.0/24
```
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: egress-netpol
spec:
  podSelector:
    matchLabels:
      app: web
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: db
    ports:
    - port: 5432
```
https://kubernetes.io/docs/concepts/services-networking/network-policies/
https://kubernetes.io/docs/tasks/administer-cluster/declare-network-policy/

## TLS Certiciates
2 authenticated with the API Server: 

Install cfssl: 
```
wget -q --show-progress --https-only --timestamping \
  https://pkg.cfssl.org/R1.2/cfssl_linux-amd64 \
  https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
Make the binary files executable:

chmod +x cfssl_linux-amd64 cfssljson_linux-amd64
Move the files into your bin directory:

sudo mv cfssl_linux-amd64 /usr/local/bin/cfssl
sudo mv cfssljson_linux-amd64 /usr/local/bin/cfssljson
Check to see if you have cfssl installed correctly:

cfssl version
```

```
cat <<EOF | cfssl genkey - | cfssljson -bare server
{
  "hosts": [
    "my-svc.my-namespace.svc.cluster.local",
    "my-pod.my-namespace.pod.cluster.local",
    "172.168.0.24",
    "10.0.34.2"
  ],
  "CN": "my-pod.my-namespace.pod.cluster.local",
  "key": {
    "algo": "ecdsa",
    "size": 256
  }
}
EOF

cat <<EOF | kubectl create -f -
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: pod-csr.web
spec:
  groups:
  - system:authenticated
  request: $(cat server.csr | base64 | tr -d '\n')
  usages:
  - digital signature
  - key encipherment
  - server auth
EOF


kubectl certificate approve pod-csr.web
kubectl get csr pod-csr.web -o jsonpath='{.status.certificate}' \
    | base64 --decode > server.crt
```
https://kubernetes.io/docs/tasks/tls/managing-tls-in-a-cluster/
https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-tls-bootstrapping/
https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/

## Secure imageS: 
```
kubectl create secret docker-registry acr --docker-server=https://podofminerva.azurecr.io --docker-

kubectl patch sa default -p '{"imagePullSecrets": [{"name": "acr"}]}'

apiVersion: v1
kind: Pod
metadata:
  name: acr-pod
  labels:
    app: busybox
spec:
  containers:
    - name: busybox
      image: podofminerva.azurecr.io/busybox:latest
      command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
      imagePullPolicy: Always
```      

#Security context: 

    securityContext:
      runAs

    securityContext:
      runAsNonRoot: true

    securityContext:
      privileged: true      

 - Allow to change the time 
    securityContext:
      capabilities:
        add:
        - SYS_TIME      

 - Removes capabilities:
    securityContext:
      capabilities:
        drop:
        - CHOWN
 
 - can’t write to the local filesystem
    securityContext:
      readOnlyRootFilesystem: true

## Securing Persistent Key Value Store
Generate a key for your https server:
    openssl genrsa -out https.key 2048

openssl req -new -x509 -key https.key -out https.cert -days 3650 -subj /CN=www.example.com

kubectl create secret generic example-https --from-file=https.key --from-file=https.cert --from-file=file

kubectl create secret generic example-https --from-file=https.key --from-file=https.cert --from-file=file

kubectl get secrets example-https -o yaml

Create the configMap that will mount to your pod:

apiVersion: v1
kind: ConfigMap
metadata:
  name: config
data:
  my-nginx-config.conf: |
    server {
        listen              80;
        listen              443 ssl;
        server_name         www.example.com;
        ssl_certificate     certs/https.cert;
        ssl_certificate_key certs/https.key;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers         HIGH:!aNULL:!MD5;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

    }
  sleep-interval: |
    25

The YAML for a pod using the new secret:

apiVersion: v1
kind: Pod
metadata:
  name: example-https
spec:
  containers:
  - image: linuxacademycontent/fortune
    name: html-web
    env:
    - name: INTERVAL
      valueFrom:
        configMapKeyRef:
          name: config
          key: sleep-interval
    volumeMounts:
    - name: html
      mountPath: /var/htdocs
  - image: nginx:alpine
    name: web-server
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html
      readOnly: true
    - name: config
      mountPath: /etc/nginx/conf.d
      readOnly: true
    - name: certs
      mountPath: /etc/nginx/certs/
      readOnly: true
    ports:
    - containerPort: 80
    - containerPort: 443
  volumes:
  - name: html
    emptyDir: {}
  - name: config
    configMap:
      name: config
      items:
      - key: my-nginx-config.conf
        path: https.conf
  - name: certs
    secret:
      secretName: example-https    

https://kubernetes.io/docs/concepts/configuration/secret/

## Lab 8 - Create a Cluster Role to access a PV in K8s: 
View the Persistent Volume.
    kubectl get pv

Create a ClusterRole.
    kubectl create clusterrole pv-reader --verb=get,list --resource=persistentvolumes 

Create a ClusterRoleBinding.
    kubectl create clusterrolebinding pv-test --clusterrole=pv-reader --serviceaccount=web:default

Create a pod to access the PV.

 apiVersion: v1
 kind: Pod
 metadata:
   name: curlpod
   namespace: web
 spec:
   containers:
   - image: tutum/curl
     command: ["sleep", "9999999"]
     name: main
   - image: linuxacademycontent/kubectl-proxy
     name: proxy
   restartPolicy: Always


 kubectl apply -f curlpod.yaml

Request access to the PV from the pod.
    kubectl exec -it curlpod -n web -- sh
    curl localhost:8001/api/v1/persistentvolumes