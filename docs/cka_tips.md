# CKA Tips

## Alias

### Set a shorter alias for kubectl

```bash
alias k=kubectl
```

## Autocompletion

### Enable kubectl autocompletion for the current session

```bash
source <(kubectl completion bash)
```

### Enable kubectl autocompletion permanently

```bash
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

### Enable autocompletion for the 'k' alias

```bash
source <(kubectl completion bash | sed 's/kubectl/k/g')
echo "source <(kubectl completion bash | sed 's/kubectl/k/g')" >> ~/.bashrc
```

## Editor

### Set your preferred editor for `kubectl edit`

```bash
export KUBE_EDITOR="nano"
```

## Output Format

### Get pods with more details

```bash
k get pods -o wide
```

### Get the full resource definition in YAML

```bash
k get pod <pod-name> -o yaml
```

## --dry-run

### Generate the YAML for a resource without creating it

```bash
k run nginx --image=nginx --dry-run=client -o yaml
```

## Force Deletion

### Force the deletion of a pod that is stuck terminating

```bash
k delete pod <pod-name> --grace-period=0 --force
```
