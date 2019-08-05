# The final tips!!

## Official Webpage: 

  - https://www.cncf.io/certification/cka/ (There are many codes on Internet with a discount in the price :) )

## Useful links: 


## Breakdown of the Test:

  - Core Concepts (19%)
  - Installation, Configuration & Validation (12%)
  - Application Lifecycle Management (8%)
  - Networking (11%)
  - Scheduling (5%)
  - Security (12%)
  - Cluster Maintenance (11%)
  - Logging / Monitoring (5%)
  - Storage (7%)
  - Troubleshooting (10%)

Complete CV: https://github.com/cncf/curriculum/blob/master/CKA_Curriculum_V1.14.1.pdf

### Core concepts
  Master: 
      - API Server: exposes the K8s API, all cluster componentes. 
      - Scheduler: Assigns the app to a worker node. Auto-detects which pod to assign to which node bases on resource requirementes, hardware contraints, etc.
      - Controller Manager: Maintains the cluster. Handless node failter, replicating componentes, mainting the correct amount of pods, etc. 
      - etcd: Data store the cluster configuration. 
  https://kubernetes.io/docs/concepts/overview/components/#master-components 

  Worker: 
      - kubelet: runs and manage the contianers on the node and talks to the API server. 
      - kube-proxy: load balances traffic between applications componentes. https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/
      - container runtime: the program that runs your containers. 

  https://kubernetes.io/docs/concepts/overview/components/#node-components

  Services: 
  - API Server communicates to kube-proxy that there are new service. 
  - Create new ip tables routes are created. 
  - When the pod do the curl to the service IP, the iptables change the destiny from service cluster ip address to the final pod IP (photo1)

  Commands: 
  ```
  - kubetl label pods <pod name> env=prod >> Apply a label to a pod 
  - kubectl get pods -L env
  - k get pods --field.selector=status.phase=Running,metadata.namespace=default
  ```

### Instalation, Configuration and Validation

Run this commands in all nodes Kubernetes: 
  - In all nodes: 
    - Docker (Add Docker GPG key, repository, k8s, repository)
    - Update Packages 
    - Instalar Docker, kubelet, kubeadm and kubectl 
    - Hold Docker and k8s at their current version

  Docs (Container Runtime): https://kubernetes.io/docs/setup/production-environment/container-runtimes/

Run these commands only on the master: 
   -  sudo kubeadm init --pord-network-cidr=10.244.0.0./16 (Initialize the k8s cluster)
   -  Execute the commands from the init result: (make directorory, cp file /.kube/config, change ownership of the config)
   -  kubectl apply -f <flannel>

Run these commands only in the workers: 
   - kubeadm join

        All right!!!

Create deployment
Create svc
Labeled node

taint del nodo? ?????? cpaitulo Scheduling Pods with Taints and Tolerations in Kubernetes ????

- Rollout 






## Role and Rolebinding: 

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
```

  kubectl create rolebinding test --role=service-reader --serviceaccount=web:default -n web



https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/

