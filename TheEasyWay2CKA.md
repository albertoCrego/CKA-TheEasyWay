# Tips to prepare CKA Exam!!

## Official Webpage: 

  - https://www.cncf.io/certification/cka/ (There are many codes on the Internet with a discount on the price :) Try this: `DCUBEOFFER`)

## Useful links: 

  - https://github.com/kelseyhightower/kubernetes-the-hard-way (TheHardWay!)
  - https://github.com/dgkanatsios/CKAD-exercises/ (Exercises to CKAD)
  - https://kubernetes.io/docs/reference/kubectl/cheatsheet/ (Very useful in the exam!)
  - https://github.com/walidshaari/Kubernetes-Certified-Administrator (Links to K8s documentation with all importat topics)

  - https://github.com/arush-sal/cka-practice-environment (one test similar to the exam, I recommend doing this one the last day!!)


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

  ## Info about the exam 

  - The exam duration is 3 hours, but you can connect 15 minutes before to show the documentation to examinator. 
  - To obtain the certificate you should get 74% points. 
  - There are 24 questions, 19 about developing objects in Kubernetes and 5 about the administration topics. 
  - During the exam, you only have 2 tabs to work: the exam webpage and the documentation (https://github.com/kubernetes/ or https://kubernetes.io/)
  - The prerequisites and other tips: https://training.linuxfoundation.org/wp-content/uploads/2019/08/Important-Tips-CKA-CKAD-Master-8.5.19.pdf

  # Proposal Learning Path

  + Create a place to work I recommend Minikube or IKS (free if you are IBMer) where you can deploy, create and play with the pods. 
  +  Start reviewing all the documentation from all chapters, the best links is this: https://github.com/walidshaari/Kubernetes-Certified-Administrator all the points connect directly with kubernetes.io
  + After, you can do these exercises to be familiar with the Kubectl and Search in Kubernetes.io: https://github.com/dgkanatsios/CKAD-exercises/ (I recommend do it in a real cluster point 1)
  + To finish do the TheHardWay: https://github.com/kelseyhightower/kubernetes-the-hard-way. 

    And something more... Do these labs: https://github.com/albertoCrego/CKA-TheEasyWay/blob/master/FinalLabs.md

  # And more to study:

  ## Administrate service system
   
   ```bash
   systemctl [enable|stop|restart|status] kubelet
   systemctl [enable|stop|restart|status] docker
   ```

   Folder with the services: /usr/lib/systemd/system/

  ## CSR RBAC TLS manage certificates

  - The Hard Way
  - https://kubernetes.io/docs/reference/access-authn-authz/rbac/


  ## Differents options between creation k8s objects
  
  ```bash
  kubectl run (restart=[jobs, deploy, pod...])
  kubectl run nginx --image=nginx   (deployment)
  kubectl run nginx --image=nginx --restart=Never   (pod)
  kubectl run busybox --image=busybox --restart=OnFailure   (job)
  kubectl run busybox --image=busybox --schedule="0 1 * * *"  --restart=OnFailure (cronJob)
  ```

  ## Static pods: 
  
  - https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/
   
  ## Switch Between Multiple Contexts

    kubectl config use-context
    https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/  #define-clusters-users-and-contexts  2

  ## Others important topics

  - Manage Hostpath and volumes
  - Manage Secrets 
  - Manage DNS resolution about componentes in inside K8s Cluster (with nslookup)
  - Files to review: /var/lib/kubelet/bootstrap-kubeconfig >> https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-tls-bootstrapping/#kubelet-configuration
  - Kubeconfig: `~/.kube/config`


