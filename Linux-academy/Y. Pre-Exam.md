Use kubectl completion
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-autocomplete

Use kubectl help
kubectl help
https://kubernetes.io/docs/reference/kubectl/overview/#syntax

Use kubectl explain
Get documentation of various resources. For instance pods, nodes, services, etc.

kubectl explain deployments
https://kubernetes.io/docs/reference/kubectl/overview/#operations

Switch Between Multiple Contexts
kubectl config use-context
https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/#define-clusters-users-and-contexts  2