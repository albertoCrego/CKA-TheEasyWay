## Add metrics server

the `kubectl top` command works.

Clone the metrics server repository:

```bash
git clone https://github.com/linuxacademy/metrics-server
```

Install the metrics server in your cluster:

```bash
kubectl apply -f ~/metrics-server/deploy/1.8+/
```

Get a response from the metrics server API:

```bash
kubectl get --raw /apis/metrics.k8s.io/
```

Get the CPU and memory utilization of the nodes in your cluster:

```
kubectl top node
```

Get the CPU and memory utilization of the pods in your cluster:

```bash
kubectl top pods
```

Get the CPU and memory of pods in all namespaces:

```bash
kubectl top pods --all-namespaces
```

Get the CPU and memory of pods in only one namespace:

```bash
kubectl top pods -n kube-system
```

Get the CPU and memory of pods with a label selector:

```bash
kubectl top pod -l run=pod-with-defaults
```

Get the CPU and memory of a specific pod:

```bash
kubectl top pod pod-with-defaults
```

Get the CPU and memory of the containers inside the pod:

```bash
kubectl top pods group-context --containers
```

## Liveness and readiness

## Logs in containers

```bash
/var/log/containers
```

## Logs: 

```bash
kubectl logs nginx
```

Get the logs from a specific container on a pod:

```bash
kubectl logs counter -c count-log-1
```

Get the logs from all containers on the pod:

```bash
kubectl logs counter --all-containers=true
```

Get the logs from containers with a certain label:

```bash
kubectl logs -lapp=nginx
```

Get the logs from a previously terminated container within a pod:

```bash
kubectl logs -p -c nginx nginx
```

Stream the logs from a container in a pod:

```bash
kubectl logs -f -c count-log-1 counter
```

Tail the logs to only view a certain number of lines:

```bash
kubectl logs --tail=20 nginx
```

View the logs from a previous time duration:

```bash
kubectl logs --since=1h nginx
```

View the logs from a container within a pod within a deployment:

```bash
kubectl logs deployment/nginx -c nginx
```

Redirect the output of the logs to a file:

```bash
kubectl logs counter -c count-log-1 > count.log
```
