# Kubernetes


## Common Commands

* Run pod
```
  kubectl run <pod_name> --image <image> -n <namespace>
```
<br>

* Run pod and passing a command
```
  kubectl run -i --tty <pod_name> --image=<image> -n <namespace> -- <command>
```
<br>

* Get info about cluster
```
  kubectl cluster-info
```
<br>

* Get all pods
```
  kubectl get pods -A
```
<br>

* Get resource yaml
```
  kubectl get <resource> -n <namespace> <resource_name> -o yaml
```
<br>

* Get list of CRD applied
```
  kubectl get customresourcedefinitions
```
<br>

* List pods Docker images in a specific namespace
```
  kubectl get pods -n <namespace> -o jsonpath="{.items[*].spec.containers[*].image}"
```
<br>

* Restart all deployments in a namespace
```
  kubectl rollout restart deployment -n <namespace>
```
<br>

* List all running pods in a specific namespace
```
  kubectl get pods -n <namespace> --field-selector=status.phase=Running
```
<br>

* List all non-running pods in a specific namespace
```
  kubectl get pods -A --field-selector=status.phase!=Running | grep -v Complete
```
<br>

* Create tunnel to a service using port-forward (like a SSH tunnel)
```
  kubectl port-forward -n <namespace> svc/<service_name> <localhost_port>:<service_port>
```
<br>

* List all PVC associated with their respective pod
```
  kubectl get po -o json --all-namespaces | jq -j '.items[] | "\(.metadata.namespace), \(.metadata.name), \(.spec.volumes[].persistentVolumeClaim.claimName)\n"' | grep -v null
```
<br>

* List aal pods with events related to them
```
  kubectl get pods --watch --output-watch-events -A
```
<br>

* List all events with filter "type=Warning"
```
  kubectl get events -w --field-selector=type=Warning -A
```
<br>

* List all environment variables of specific pod<br>
```
  kubectl set env -n <namespace> pod/<podname> --list
```
<br>


## Advanced Commands

* Run pod `multitool` for testing/debugging on k8s
```
  kubectl run multitool --namespace <namespace> --image=wbitt/network-multitool:extra -it --tty --rm --restart=Never -- sh
```
<br>

* List of nodes and their memory size
```
  kubectl get no -o json | jq -r '.items | sort_by(.status.capacity.memory)[]|[.metadata.name,.status.capacity.memory]| @tsv'
```
<br>

* List of nodes and the number of pods running on them
```
kubectl get pod -o json --all-namespaces | jq '.items | group_by(.spec.nodeName) | map({"nodeName": .[0].spec.nodeName, "count": length}) | sort_by(.count)'
```
<br>

* List all pods sort by memory usage
```
  k top pods -A --sort-by='memory'
``` 
<br>

* List all pods sorted by cpu usage
```
  k top pods -A --sort-by='cpu'
```
<br>

* Sorting list of pods (in this case, by the number of restarts)
```
  kubectl get pods --sort-by=.status.containerStatuses[0].restartCount
```
<br>

* Print **limits** and **requests** for each pod
```
  kubectl get pods -n <namespace> -o=custom-columns='NAME:spec.containers[*].name,MEMREQ:spec.containers[*].resources.requests.memory,MEMLIM:spec.containers[*].resources.limits.memory,CPUREQ:spec.containers[*].resources.requests.cpu,CPULIM:spec.containers[*].resources.limits.cpu'
```
<br>

* Print all services and their respective nodePorts
```
  kubectl get --all-namespaces svc -o json | jq -r '.items[] | [.metadata.name,([.spec.ports[].nodePort | tostring ] | join("|"))]| @tsv'
```
<br>

* Copy secrets from a namespace to another
```
  kubectl get secrets -o json --namespace <namespace_source> | jq '.items[].metadata.namespace = "<namespace_destiny>"' | kubectl create-f  -
```
<br>


## Logging Commands

### Kubectl
* Print logs with human-readable timestamp
```
  kubectl -n <namespace> logs -f <pod_name> --timestamps
```
<br>

* Getting logs from all pods using  alabel to filter
```
  kubectl -n <namespace> logs -f -l app=nginx
```
<br>

### Kubetail
* Tail logs specific container
```
  kubetail <pod_name> -c <container_name>
```
<br>

* Tail logs from all containers on specific namespace
```
  kubetail -n <namespace>
```
<br>


### Stern
* Tail logs all pods on specific namespace
```
  stern -n <namespace> .
```
<br>

* Tail logs all pods with excludeds
```
  stern -n <namespace> --exclude-container <pattern_string> .
```
<br>

* Tail logs specific pods
```
  stern -n <namespace> --container <pattern_string>
```
<br>


## Definitions
### External DNS
Service which manages DNS records, in our case, on Route53. External DNS check annotations on `Service` and `Ingress` objects. On UserTesting we configured External DNS just for checking annotations on `Ingress` objects


## Tutorials and Guides

### Ingresses
- [Native EKS Ingress with AWS Load Balancer Controller](https://blog.antoinechoula.ga/native-eks-ingress-with-aws-load-balancer-controller)

### Certificates
- [Ingress + Cert-Manager](https://www.youtube.com/watch?v=ZKrC261Rxqo)

### DNS
- [The life of a DNS query in Kubernetes](https://www.nslookup.io/learning/the-life-of-a-dns-query-in-kubernetes/)

### Networking
- [The Kubernetes Networking Guide](https://www.tkng.io/)
- [An Illustrated Guide to Kubernetes Networking [Part 1]](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-1-d1ede3322727)
- [An Illustrated Guide to Kubernetes Networking [Part 2]](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-2-13fdc6c4e24c)
- [An Illustrated Guide to Kubernetes Networking [Part 3]](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-3-f35957784c8e)
- [ALB Ingress Controller on AWS EKS](https://medium.com/tensult/alb-ingress-controller-on-aws-eks-45bf8e36020d)
- [What Actually Happens When You Publish a Container Port](https://iximiuz.com/en/posts/docker-publish-container-ports/?z=101&utm_source=newsletter&utm_medium=email&utm_campaign=devopsbulletin&utm_content=devopsbulletin)

### Secrets
- [State of Kubernetes Secrets Management in 2022](https://medium.com/4th-coffee/state-of-kubernetes-secrets-management-in-2022-6148af91e7b5)
- [How to Set External-Secrets with AWS](https://blog.container-solutions.com/tutorial-how-to-set-external-secrets-with-aws)
- [How to Set External-Secrets with Hashicorp Vault](https://blog.container-solutions.com/tutorialexternal-secrets-with-hashicorp-vault)

### Virtual Kubernetes
- [Testing GitOps on Virtual Kubernetes Clusters with ArgoCD (Ephimeral k8s clusters)](https://piotrminkowski.com/2023/06/29/testing-gitops-on-virtual-kubernetes-clusters-with-argocd/)

### Troubleshooting
- [How to identify and troubleshoot common Kubernetes errors](https://newrelic.com/blog/how-to-relic/monitoring-kubernetes-part-three?utm_source=devopsbulletin&utm_id=newsletter)