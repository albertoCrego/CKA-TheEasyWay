# Core Concepts (19%)

Master: 
    - API Server: exposes the K8s API, all cluster componentes. 
    - Scheduler: Assigns the app to a worker node. Auto-detects which pod to assign to which node bases on resource requirementes, hardware contraints, etc.
    - Controller Manager: Maintains the cluster. Handless node failter, replicating componentes, mainting the correct amount of pods, etc. 
    - etcd: Data store the cluster configuration. 

Worker: 
    - kubelet: runs and manage the contianers on the node and talks to the API server. 
    - kube-proxy: load balances traffic between applications componentes. https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/
    - container runtime: the program that runs your containers. 

Services: 
- API Server communicates to kube-proxy that there are new service. 
- Create new ip tables routes are created. 
- When the pod do the curl to the service IP, the iptables change the destiny from service cluster ip address to the final pod IP (photo1)

Commands: 
  - kubetl get nodes
  - kubectl get componentstatus
  - 



- kubetl label pods <pod name> env=prod >> Apply a label to a pod 
- kubectl get pods -L env >> See specific labels
- kubectl get pods --field.selector status.phase=Running
- k get services --field-selector metadata.namespace=default
- k get pods --field.selector=status.phase=Running,metadata.namespace=default
- k get pods --field.selector=status.phase!=Running,metadata.namespace!=default
- curl localhost:30XXX
- k exec busybox -- curl 10.101.56.80 (photo 1)
- (cat << EOF | kubectl create -f -
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: busybox
    image: radial/busyboxplus:curl
    args:
    - sleep
    - "1000"
EOF)

## Lab1 - Exploring the Kubernetes Cluster via the Command Line

- kubectl get nodes
kubectl get pods --all-namespaces
