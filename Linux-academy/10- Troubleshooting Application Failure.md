# Troubleshooting Application Failter: 
1.  Error 
Check the Last State: Message 
kubectl describe pod  >> Check logs. 

If not have the yaml, export the yaml >> kubectl get pods <pod name> -o yaml --export > default-pod.yaml
kubectl delete + kubectl apply -f ... 

2. Check the liveness probe: 

2. PullBackOff: 
Check the image >> edit version in order to pull one that exist. 

3. Pending: 
Describe pod >> Insufficiente memory >> Export the yaml >> reduce the memory request >> apply!!


# Troubleshooting - Control Plange Failure: 
(photo_19)

kubectl get events -n kube-system 
kubectl logs -n kube-sytem kube-scheduler-* >> Check the services are enable

Check docker and kubelet: 
    sudo systemctl status [docker|kubelet]
    sudo systemctl enable [docker|kubelet] && sytemctl start [docker|kubelet]

swapoff -a && sed -i '/ swap / s/^/#/' /etc/fstab

sudo systemctl status firewalld
sudo systemctl disable firewalld && systemctl stop firewalld

## Roubleshooting worker node failure: 
kubectl getdescribe nodes [node-name] >> We found "kubelet stopped posting node status."
ssh to node (could not resolver)

kubectl get nodes -o wide >> try ip-address (KO!) >> try ping (KO) 
    >> Create a new node: install kubelet, docker, kubeadm
    >> sudo kubeadm token generat (answer token)
    >> sudo kubeadm token create [token_name] --ttl 2h --print-join-command
    >> (the result) kubeadm join... 
    >> in master... kubectl get nodes!! (OK!!)

## Toubleshooting network: 
(photo_20)
kubectl run hostnames --image=k8s.gcr.io/serve_hostname \
                        --labels=app=hostnames \
                        --port=9376 \
                        --replicas=3
kubectl expose deployment hostnames --port=80 --target-port=9376

create pod to check: kubectl run -it --rm --restart=Never busybox --image=busybox:1.28 sh
    >> nslookup hostnames
    >> cat /etc/resolv.conf
    >> nslookup kubernetes.default
    >> Check endpoints: kubectl get ep
            wget -q0- <ip pod>:<port container>
    >> exec kube-proxy         
            ps auxw | grep kube-proxy
            iptables-save | grep hostnames

Delete flannel and install weave





## Lab 