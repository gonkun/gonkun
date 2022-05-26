# Kubernetes


## Common Commands

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

## Advanced Commands

* List of nodes and their memory size<br>
`kubectl get no -o json | jq -r '.items | sort_by(.status.capacity.memory)[]|[.metadata.name,.status.capacity.memory]| @tsv'`
* List of nodes and the number of pods running on them<br>
`kubectl get pod -o json --all-namespaces | jq '.items | group_by(.spec.nodeName) | map({"nodeName": .[0].spec.nodeName, "count": length}) | sort_by(.count)'`
* List of pods that eat up CPU<br>
`kubectl top pods -A | sort --reverse --key 3 --numeric`
* List of pods that eat up memoryCPU<br>
`kubectl top pods -A | sort --reverse --key 4 --numeric`
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
### Stern
* Tail logs all pods on specific namespace<br>
`stern -n <namespace> .`
* Tail logs all pods with excludeds<br>
`stern -n <namespace> --exclude-container <pattern_string> .`
* Tail logs specific pods<br>
`stern -n <namespace> --container <pattern_string>`