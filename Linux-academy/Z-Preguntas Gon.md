Problemas con el kubelet: 
    - systemctl daemon reboot
    - enable kubelet (para hacer peristente la configuración)

/etc/systemd para comprobar la configuración del kubelet
08 kubelet bootstrap in the hardway

CSR RBAC TLS manage certificates

kubectl run (restart=[jobs, deploy, pod...])
$ kubectl run nginx --image=nginx   (deployment)
$ kubectl run nginx --image=nginx --restart=Never   (pod)
$ kubectl run busybox --image=busybox --restart=OnFailure   (job)
$ kubectl run busybox --image=busybox --schedule="* * * * *"  --restart=OnFailure (cronJob)

## generate secrets: 
$ kubectl create secret generic my-secret --from-literal=foo=bar -o yaml --dry-run > my-secret.yaml



hostpath - volumen, secrets 

nslookup - DNS

Ficheros a mirar

static pods

/etc/kubelet/.....service
etc/kubelet/bootstrap
/var/lib/kubernetes/manifest


~~ 
 systemctl daemon-reload
systemctl restart kubelet