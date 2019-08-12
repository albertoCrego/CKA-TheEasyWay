# Installation, Configuration, and Validation (12%)

- k cluster-info
- k config view
- k describe nodes
- k describe pods
- k api-resources -o wide

## High Availability Cluster

New the pods in the default namespace with a custom view:

```bash
kubectl get pods -o custom-columns=POD:metadata.name,NODE:spec.nodeName --sort-by spec.nodeName -n kube-system
```

View the kube-scheduler YAML:

```bash
kubectl get endpoints kube-scheduler -n kube-system -o yaml
```

Create a stacked etcd topology using kubeadm:

```bash
kubeadm init --config=kubeadm-config.yaml
```

Watch as pods are created in the default namespace:

```bash
kubectl get pods -n kube-system -w
```

## Securing K8s API Access

kubectl | pod >> API Server >> Authentication >> Authorization >> Admission >> Resource Validation >> etcd

View kube config

```bash
cat .kube/config
```

RBAC is used to prevent unauthorized users from modfyinj the cluster state: (photo 2)

View the token file from within a pod:

```bash
cat /var/run/secrets/kubernetes.io/serviceaccount/token
```

## End to end test

https://v1-12.docs.kubernetes.io/docs/getting-started-guides/ubuntu/validation/

(photo 3) - port forwarding

## Lab 2

Get the Docker gpg, and add it to your repository.
keyboard_arrow_down:

```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
    deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

sudo apt-get update
```

Install docker:

```bash
sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu kubelet=1.13.5-00 kubeadm=1.13.5-00 kubectl=1.13.5-00 --allow-change-held-packages
```

Initialize cluster and setup config:

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Apply flannel | calico:

Join the worker nodes to the cluster, and verify they have joined successfully.

```bash
sudo kubeadm create token --print-join-command <your unique string from the output of kubeadm init>
```

Create deployments:

```bash
kubectl create deployment nginx --image=nginx
```

Test porf forward:

```bash
kubectl port-forward [pod_name] 8081:80
```

Expose service and test nodeport:

```bash
kubectl expose deployment nginx --port 80 --type NodePort
kubectl get services
curl -I localhost:$node_port
```
