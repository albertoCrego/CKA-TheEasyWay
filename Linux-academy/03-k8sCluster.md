# Managing the Kubernetes Cluster

## Upgrading the Kubernetes Cluster

Updagrading version using kubeadm

(photo 4)

```bash
export VERSION=v.1.14.1
export ARCH=amd64
curl -sSl ....

sudo install -o root -g root -m 0755 ./kubeadm /usr/bin/kubeadm
sudo kubeadm upgrade plan

sudo kubeadm upgrade apply v.1.14.1
```

View the differences between the old and new manifests:

```bash
diff kube-controller-manager.yaml /etc/kubernetes/manifests/kube-controller-manager.yaml
```

Curl the latest version of kubelet:

```bash
curl -sSL https://dl.k8s.io/release/${VERSION}/bin/linux/${ARCH}/kubelet > kubelet
```

Install the latest version of kubelet:

```bash
sudo install -o root -g root -m 0755 ./kubelet /usr/bin/kubelet
```

Restart the kubelet service:

```bash
sudo systemctl restart kubelet.service
```

Watch the nodes as they change version:

```bash
kubectl get nodes -w
```

(photo 5)

## Node maintenance

sudo kubeadm token generate >> get a new token to join

```bash
sudo kubeadm token create <token name > --tl 23h --print-join-command
        >> kubeadm join (copy)
```

kubectl get nodes works right!!!!
(photo 6)

## Backup ETCD

For a Kubernernetes cluster created with kubeadm, the `etcdctl` command line tools can backup your etcd datastore in a single command:

```bash
sudo ETCDCTL_API=3 etcdctl snashot save snapshot.db --cacert /etc/kubernetes/pki/etcd/servre.crt --cert /etc/kubernetes/pki/etcd/ca.key
#Successuful snapshot.db!
```

## Lab 3 - Upgrading the Kubernetes Cluster Using kubeadm

Get version 1.13.5 of kubeadm

```bash
export VERSION=v1.13.5
export ARCH=amd64
curl -sSL https://dl.k8s.io/release/${VERSION}/bin/linux/${ARCH}/kubeadm > kubeadm
```

Install kubeadm and verify it has been installed correctly.

```bash
sudo install -o root -g root -m 0755 ./kubeadm /usr/bin/kubeadm
sudo kubeadm version
```

Plan the upgrade in order to check for errors.

```bash
sudo kubeadm upgrade plan
```

Perform the upgrade of the kube-scheduler and kube-controller-manager.

```bash
sudo kubeadm upgrade apply v1.13.5
```

Get the latest version of kubelet.

```bash
export VERSION=v1.13.5
export ARCH=amd64
curl -sSL https://dl.k8s.io/release/${VERSION}/bin/linux/${ARCH}/kubelet > kubelet
```

Install kubelet on each node and restart the kubelet service.

```bash
sudo install -o root -g root -m 0755 ./kubelet /usr/bin/kubelet
sudo systemctl restart kubelet.service
```

Verify the kubelet was installed correctly.

```bash
    kubectl get nodes
```

Get version 1.13.5 of kubectl

```bash
curl -sSL https://dl.k8s.io/release/${VERSION}/bin/linux/${ARCH}/kubectl > kubectl
```

Upgrade kubectl

```bash
sudo install -o root -g root -m 0755 ./kubectl /usr/bin/kubectl
```
