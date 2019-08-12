# Networking

## Networking within a node

Virtual ethernet interface pair is created for the container: one for the node's namespaces and one for the container network namespace:
(photo 7)

and... Networking outside of the node:
(photo 8)

See which node our pod is on:

```bash
kubectl get pods -o wide
```

Log in to the node:

```bash
ssh [node_name]
```

View the node's virtual network interfaces:

```bash
ifconfig
```

View the containers in the pod:

```bash
docker ps
```

Get the process ID for the container:

```bash
docker inspect --format '{{ .State.Pid }}' [container_id]
```

```bash
nsenter -t [container_pid] -n ip addr
```

## Container network interface

Install flannel, this function is not include in Kubernetes stack. This created a clusterrole, clusterrrolebinfing, sa, daemonsets.
Calico, Romana, weavenet...

NEtwork agent in each node, this notify the node
The bootstraping are in the official documenting.
(photo_9)

## Cluster IP & NodePort

(photo10)
When we create node port, the kube-proxy are been notified to create a new iptables rules with this route:

endpoint from pod:

```bash
sudo iptables-save | grep KUBE | grep nginx
```

rule 1: `-s <endpoint> -d <ClusterIP Nodeport Service >` >> goin to the ramdom pod.

## Load balancers

(photo_11)

## Ingress

(photo_12)

## Cluster DNS

(photo_13)

```bash
kubectl exec -t busybox --cat /etc/resolve.conf
```

```bash
nameserver 10.96.0.10 (cluster IP from kube-dns)
search default.svc.cluster.local svc.cluster.local cluster.local us-east-2.compute-interna
....
```

```bash
kubectl exec -t busybox --cat nslookup kubernetes

server: 10.96.0.10
address: 10.96.0.10 kube-dns.kube-systm.svc.cluster.local

name: kubernetes
address: 10.96.0.1 kubernetes.default.svc.cluster.local
```

YAML spec for a custom DNS pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  namespace: default
  name: dns-example
spec:
  containers:
    - name: test
      image: nginx
  dnsPolicy: "None"
  dnsConfig:
    nameservers:
      - 8.8.8.8
    searches:
      - ns1.svc.cluster.local
      - my.dns.search.suffix
    options:
      - name: ndots
        value: "2"
      - name: edns0
```

https://coredns.io/2018/01/29/deploying-kubernetes-with-coredns-using-kubeadm/

## Lab 4 - Learning Objectives

Create an nginx deployment, and verify it was successful.

```bash
kubectl run nginx --image=nginx
kubectl get deployments
```

Create a service, and verify the service was successful.

```bash
kubectl expose deployment nginx --port 80 --type NodePort
kubectl get services
```

Create a pod that will allow you to query DNS, and verify it’s been created.

```yaml
apiVersion: v1
kind: Pod
metadata:
name: busybox
spec:
containers:
- image: busybox:1.28.4
    command:
    - sleep
    - "3600"
    name: busybox
restartPolicy: Always
```

```bash
kubectl create -f busybox.yaml
kubectl get pods
```

Perform a DNS query to the service.

```bash
kubectl exec busybox -- nslookup nginx
```

Record the DNS name.

Record the name of: `<service-name>;.default.svc.cluster.local`
