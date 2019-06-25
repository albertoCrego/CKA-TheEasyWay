# Exam Bootstrap

## 1. Installing tmux

```
sudo apt-get update
sudo apt-get install tmux
```

## 2. Setting up aliases

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
