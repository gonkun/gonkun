# Kubernetes


## Common Commands

* Run pod
`kubectl run <pod_name> --image <image> -n <namespace>`
* Run pod and passing a command
`kubectl run -i --tty <pod_name> --image=<image> -n <namespace> -- <command>`
* Get info about cluster<br>
`kubectl cluster-info`
* Get all pods<br>
`kubectl get pods -A`
* Get resource yaml<br>
`kubectl get <resource> -n <namespace> <resource_name> -o yaml`
* Get list of CRD applied<br>
`kubectl get customresourcedefinitions`
* List pods Docker images in a specific namespace<br>
`kubectl get pods -n <namespace> -o jsonpath="{.items[*].spec.containers[*].image}" `
* Restart all deployments in a namespace<br>
`kubectl rollout restart deployment -n <namespace>`
* List all running pods in a specific namespace<br>
`kubectl get pods -n <namespace> --field-selector=status.phase=Running`
* List all non-running pods in a specific namespace<br>
`kubectl get pods -A --field-selector=status.phase!=Running | grep -v Complete`
* Create tunnel to a service using port-forward (like a SSH tunnel)<br>
`kubectl port-forward -n <namespace> svc/<service_name> <localhost_port>:<service_port>`
* List all PVC associated with their respective pod<br>
`kubectl get po -o json --all-namespaces | jq -j '.items[] | "\(.metadata.namespace), \(.metadata.name), \(.spec.volumes[].persistentVolumeClaim.claimName)\n"' | grep -v null`
* List aal pods with events related to them<br>
`kubectl get pods --watch --output-watch-events -A`
* List all events with filter "type=Warning"<br>
`kubectl get events -w --field-selector=type=Warning -A`
* List all environment variables of specific pod<br>
`kubectl set env -n <namespace> pod/<podname> --list`


## Advanced Commands

* List of nodes and their memory size<br>
`kubectl get no -o json | jq -r '.items | sort_by(.status.capacity.memory)[]|[.metadata.name,.status.capacity.memory]| @tsv'`
* List of nodes and the number of pods running on them<br>
`kubectl get pod -o json --all-namespaces | jq '.items | group_by(.spec.nodeName) | map({"nodeName": .[0].spec.nodeName, "count": length}) | sort_by(.count)'`
* List all pods sort by memory usage<br>
`k top pods -A --sort-by='memory'` 
* List all pods sorted by cpu usage<br>
`k top pods -A --sort-by='cpu'`
* Sorting list of pods (in this case, by the number of restarts)
`kubectl get pods --sort-by=.status.containerStatuses[0].restartCount`
* Print **limits** and **requests** for each pod<br>
`kubectl get pods -n <namespace> -o=custom-columns='NAME:spec.containers[*].name,MEMREQ:spec.containers[*].resources.requests.memory,MEMLIM:spec.containers[*].resources.limits.memory,CPUREQ:spec.containers[*].resources.requests.cpu,CPULIM:spec.containers[*].resources.limits.cpu'`
* Print all services and their respective nodePorts<br>
`kubectl get --all-namespaces svc -o json | jq -r '.items[] | [.metadata.name,([.spec.ports[].nodePort | tostring ] | join("|"))]| @tsv'`
* Copy secrets from a namespace to another<br>
`kubectl get secrets -o json --namespace <namespace_source> | jq '.items[].metadata.namespace = "<namespace_destiny>"' | kubectl create-f  -`


## Logging Commands

### Kubectl
* Print logs with human-readable timestamp<br>
`kubectl -n <namespace> logs -f <pod_name> --timestamps`
* Getting logs from all pods using  alabel to filter<br>
`kubectl -n <namespace> logs -f -l app=nginx`

### Kubetail
* Tail logs specific container<br>
`kubetail <pod_name> -c <container_name>`
* Tail logs from all containers on specific namespace<br>
`kubetail -n <namespace>`


### Stern
* Tail logs all pods on specific namespace<br>
`stern -n <namespace> .`
* Tail logs all pods with excludeds<br>
`stern -n <namespace> --exclude-container <pattern_string> .`
* Tail logs specific pods<br>
`stern -n <namespace> --container <pattern_string>`


## Tutorials and Guides

### Ingresses
- [Native EKS Ingress with AWS Load Balancer Controller](https://blog.antoinechoula.ga/native-eks-ingress-with-aws-load-balancer-controller)
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