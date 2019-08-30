# Utils Cheatsheet

    https://kubernetes.io/docs/reference/kubectl/cheatsheet/

## Exam Bootstrap

### 1. Installing tmux

```
sudo apt-get update
sudo apt-get install tmux
```

### 2. Setting up aliases

```
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.
```

```
alias k=kubectl
complete -F __start_kubectl k
```

```
alias kg=kubectl get
alias kgp=kubectl get pod
```

*IMPORTANT*: the best way to do this is to copy/paste from the cheatsheet kubernetes page


## Another one

```bash
kubectl run nginx image=nginx  --port=80  --record
kubectl set image deployment nginx nginx=nginx:1.2
kubectl rollout history deployment nginx
kubectl rollout status deployment nginx
kubectl rollout undo deployment nginx  --to-revision=2 
kubectl autoscale deployment nginx  --cpu-percent=50  -- min=1  -- max 2
kubectl run nginx3  --image=nginx  --requests=cpu=200m  --limits=cpu=300m  --requests=memory=1Gi  --limits=memory=2Gi
kubectl run hello  --schedule="*/1 * * * *"  --restart=OnFailure  -- image=busybox  -- /bin/sh -c "date; echo Hello from the kubernetes cluster"
kubectl port-forward redis-master-765d459796–258hz 6379:6379 
kubectl get pods redis-master-765d459796–258hz -o yaml
kubectl create secret docker-registry  --dry-run=true registryhttps  --docker-server=https://example.com:5000  --docker-username=username  --docker-password=password --docker-email=docker@docker.com -o yaml
kubectl create secret generic db-user-pass  --from-file=./username.txt  --from-file=./password.txt (echo -n 'username' > ./username.txt, echo -n 'password' > ./pass)
kubectl get secrets -o yaml
kubectl create secret generic db-pass  --from-literal=username=<username>  --from-lieral=password=<somebase64password>
kubectl top node NODE_NAME
kubectl top pod  --namespace=<namespace>
kubectl top pod POD_NAME  --containers 
kubectl top pod -l name=myLabel 
Kubectl rollout resume deploy/nginx
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep username | awk '{print $1}')
```
_source: https://github.com/walidshaari/Kubernetes-Certified-Administrator_