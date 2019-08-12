Problemas con el kubelet y systemctl: 
https://github.com/kelseyhightower/kubernetes-the-hard-way/blob/master/docs/08-bootstrapping-kubernetes-controllers.md

```bash
systemctl daemon-reload
systemctl restart kubelet
systemctl enable kubelet (para arrancar el servicio en el reinicio)
```

Ficheros:

```bash
/etc/systemd/system/kubelet.service.d/ >> Ficheros de config del servicio kubelet
```

Static pods:

```bash
/etc/kubernetes/manifest
```

Kubectl:

```bash
kubectl run (restart=[jobs, deploy, pod...])
kubectl run nginx --image=nginx   (deployment)
kubectl run nginx --image=nginx --restart=Never   (pod)
kubectl run busybox --image=busybox --restart=OnFailure   (job)
kubectl run busybox --image=busybox --schedule="* * * * *"  --restart=OnFailure (cronJob)
```

Create secrets: 

```bash
kubectl create secret generic my-secret --from-literal=foo=bar -o yaml --dry-run > my-secret.yaml
```

CSR RBAC TLS manage certificates ¿?

PV hostpath - volumen, secrets 

nslookup - DNS
