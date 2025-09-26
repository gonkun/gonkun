# Kubernetes

## Common Commands

#### Run pod

```bash
kubectl run <pod_name> --image <image> -n <namespace>
```

#### Run pod and passing a command

```bash
kubectl run -i --tty <pod_name> --image=<image> -n <namespace> -#### <command>
```

#### Get info about cluster

```bash
kubectl cluster-info
```

#### Get all pods

```bash
kubectl get pods -A
```

#### Get resource yaml

```bash
kubectl get <resource> -n <namespace> <resource_name> -o yaml
```

#### Get list of CRD applied

```bash
kubectl get customresourcedefinitions
```

#### List pods Docker images in a specific namespace

```bash
kubectl get pods -n <namespace> -o jsonpath="{.items[*].spec.containers[*].image}"
```

#### Restart all deployments in a namespace

```bash
kubectl rollout restart deployment -n <namespace>
```

#### List all running pods in a specific namespace

```bash
kubectl get pods -n <namespace> --field-selector=status.phase=Running
```

#### List all non-running pods in a specific namespace

```bash
kubectl get pods -A --field-selector=status.phase!=Running | grep -v Complete
```

#### Create tunnel to a service using port-forward (like a SSH tunnel)

```bash
kubectl port-forward -n <namespace> svc/<service_name> <localhost_port>:<service_port>
```

#### List all PVC associated with their respective pod

```bash
kubectl get po -o json --all-namespaces | \
  jq -j '.items[] | "\(.metadata.namespace), \(.metadata.name), \(.spec.volumes[].persistentVolumeClaim.claimName)\n"' | \
  grep -v null
```

#### List all pods with events related to them

```bash
kubectl get pods --watch --output-watch-events -A
```

#### List all events with filter "type=Warning"

```bash
kubectl get events -w --field-selector=type=Warning -A
```

#### List all environment variables of specific pod

```bash
kubectl set env -n <namespace> pod/<podname> --list
```

## Advanced Commands

#### Run pod `multitool` for testing/debugging on k8s

```bash
kubectl run multitool --namespace <namespace> --image=wbitt/network-multitool:extra -it --tty --rm --restart=Never -#### sh
```

#### List of nodes and their memory size

```bash
kubectl get no -o json | jq -r '.items | sort_by(.status.capacity.memory)[]|[.metadata.name,.status.capacity.memory]| @tsv'
```

#### List of nodes and the number of pods running on them

```bash
kubectl get pod -o json --all-namespaces | jq '.items | group_by(.spec.nodeName) | map({"nodeName": .[0].spec.nodeName, "count": length}) | sort_by(.count)'
```

#### List all pods sort by memory usage

```bash
k top pods -A --sort-by='memory'
```

#### List all pods sorted by cpu usage

```bash
k top pods -A --sort-by='cpu'
```

#### Sorting list of pods (in this case, by the number of restarts)

```bash
kubectl get pods --sort-by=.status.containerStatuses[0].restartCount
```

#### Print **limits** and **requests** for each pod

```bash
kubectl get pods -n <namespace> -o=custom-columns='NAME:spec.containers[*].name,MEMREQ:spec.containers[*].resources.requests.memory,MEMLIM:spec.containers[*].resources.limits.memory,CPUREQ:spec.containers[*].resources.requests.cpu,CPULIM:spec.containers[*].resources.limits.cpu'
```

#### Print all services and their respective nodePorts

```bash
kubectl get --all-namespaces svc -o json | \
  jq -r '.items[] | [.metadata.name,([.spec.ports[].nodePort | tostring ] | join("|"))]| @tsv'
```

#### Copy secrets from a namespace to another

```bash
kubectl get secrets -o json --namespace <namespace_source> | \
  jq '.items[].metadata.namespace = "<namespace_destiny>"' | \
  kubectl create -f -
```

## Logging Commands

#### Print logs with human-readable timestamp

```bash
kubectl -n <namespace> logs -f <pod_name> --timestamps
```

#### Getting logs from all pods using a label to filter

```bash
kubectl -n <namespace> logs -f -l app=nginx
```

### Kubetail

#### Tail logs specific container

```bash
kubetail <pod_name> -c <container_name>
```

#### Tail logs from all containers on specific namespace

```bash
kubetail -n <namespace>
```

### Stern

#### Tail logs all pods on specific namespace

```bash
stern -n <namespace> .
```

#### Tail logs all pods with excluded

```bash
stern -n <namespace> --exclude-container <pattern_string> .
```

#### Tail logs specific pods

```bash
stern -n <namespace> --container <pattern_string>
```

## Definitions

### External DNS

Service which manages DNS records, in our case, on Route53. External DNS check annotations on `Service` and `Ingress` objects. On UserTesting we configured External DNS just for checking annotations on `Ingress` objects.
External DNS search domain names on  `annotations` or `host` on `rules`  and creates a register on Hosted Zones which we configure or matching with domain name which we configured.

### AWS Load Balancer Controller

Service which manages `Ingress` and `Services` k8s objects.
For `Ingress` it creates an ALB by default, but can be configured to create a NLB.
For `Service` it creates NLB only, no ALB.
We can configure the Load Balancer parameters using `annotations`. On the other hand, for configure Listener Rules we use the parameter `rules` of `Ingress` object.

### Emissary Ingress

IT DOESN'T MANAGE LOAD BALANCER. It's a kind of application firewall. It routes requests using different paths. It works like a AWS Listener Rule but with a lot of extra options.

## Tutorials and Guides

### Local Cluster

* [Create local cluster for gomelab with k3d](https://iamunnip.medium.com/building-a-local-kubernetes-cluster-using-k3d-3ec96a802e48)
* [Spin up a local cluster with k3d](https://akyriako.medium.com/provision-a-high-availability-k3s-cluster-with-k3d-a7519f476c9c)
* [Exposing services in k3d with traefik](https://www.ivankrizsan.se/2024/07/12/exposing-services-in-a-k3s-k3d-cluster-with-traefik/)

### Load Balancing Strategies

* [Powerful Load Balancing Strategies: Kubernetes Gateway API](https://cloudnativeengineer.substack.com/p/powerful-load-balancing-strategies-kubernetes)
* [Getting Started with Kubernetes Gateway API and Traefik](https://community.traefik.io/t/getting-started-with-kubernetes-gateway-api-and-traefik/23601)

### Debugging Kubernetes Issues

* [Debugging Kubernetes Issues in Production: A technical guide](https://hervekhg.medium.com/debugging-kubernetes-issues-in-production-a-technical-guide-9e3d26e27180)

### Ingresses

* [Native EKS Ingress with AWS Load Balancer Controller](https://blog.antoinechoula.ga/native-eks-ingress-with-aws-load-balancer-controller)
* [Why I'm moving from Kubernetes Ingress to Gateway API](https://blog.devops.dev/why-im-moving-from-kubernetes-ingress-to-gateway-api-9aa303127002)

### Certificates

* [Ingress + Cert-Manager](https://www.youtube.com/watch?v=ZKrC261Rxqo)

### DNS

* [The life of a DNS query in Kubernetes](https://www.nslookup.io/learning/the-life-of-a-dns-query-in-kubernetes/)

### Networking

* [The Kubernetes Networking Guide](https://www.tkng.io/)
* [An Illustrated Guide to Kubernetes Networking [Part 1]](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-1-d1ede3322727)
* [An Illustrated Guide to Kubernetes Networking [Part 2]](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-2-13fdc6c4e24c)
* [An Illustrated Guide to Kubernetes Networking [Part 3]](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-3-f35957784c8e)
* [ALB Ingress Controller on AWS EKS](https://medium.com/tensult/alb-ingress-controller-on-aws-eks-45bf8e36020d)
* [What Actually Happens When You Publish a Container Port](https://iximiuz.com/en/posts/docker-publish-container-ports/?z=101&utm_source=newsletter&utm_medium=email&utm_campaign=devopsbulletin&utm_content=devopsbulletin)

### Secrets

* [State of Kubernetes Secrets Management in 2022](https://medium.com/4th-coffee/state-of-kubernetes-secrets-management-in-2022-6148af91e7b5)
* [How to Set External-Secrets with AWS](https://blog.container-solutions.com/tutorial-how-to-set-external-secrets-with-aws)
* [How to Set External-Secrets with Hashicorp Vault](https://blog.container-solutions.com/tutorialexternal-secrets-with-hashicorp-vault)

### Virtual Kubernetes

* [Testing GitOps on Virtual Kubernetes Clusters with ArgoCD (Ephimeral k8s clusters)](https://piotrminkowski.com/2023/06/29/testing-gitops-on-virtual-kubernetes-clusters-with-argocd/)

### Troubleshooting

* [How to identify and troubleshoot common Kubernetes errors](https://newrelic.com/blog/how-to-relic/monitoring-kubernetes-part-three?utm_source=devopsbulletin&utm_id=newsletter)

## Advanced Commands

#### Run pod `multitool` for testing/debugging on k8s

```bash
  kubectl run multitool --namespace <namespace> --image=wbitt/network-multitool:extra -it --tty --rm --restart=Never -- sh
```

#### List of nodes and their memory size

```bash
  kubectl get no -o json | jq -r '.items | sort_by(.status.capacity.memory)[]|[.metadata.name,.status.capacity.memory]| @tsv'
```

#### List of nodes and the number of pods running on them

```bash
kubectl get pod -o json --all-namespaces | jq '.items | group_by(.spec.nodeName) | map({"nodeName": .[0].spec.nodeName, "count": length}) | sort_by(.count)
```

#### List all pods sort by memory usage

```bash
  k top pods -A --sort-by='memory'
```

#### List all pods sorted by cpu usage

```bash
  k top pods -A --sort-by='cpu'
```

#### Sorting list of pods (in this case, by the number of restarts)

```bash
  kubectl get pods --sort-by=.status.containerStatuses[0].restartCount
```

#### Print **limits** and **requests** for each pod

```bash
  kubectl get pods -n <namespace> -o=custom-columns='NAME:spec.containers[*].name,MEMREQ:spec.containers[*].resources.requests.memory,MEMLIM:spec.containers[*].resources.limits.memory,CPUREQ:spec.containers[*].resources.requests.cpu,CPULIM:spec.containers[*].resources.limits.cpu'
```

#### Print all services and their respective nodePorts

```bash
  kubectl get --all-namespaces svc -o json | \
  jq -r '.items[] | [.metadata.name,([.spec.ports[].nodePort | tostring ] | join("|"))]| @tsv'
```

#### Copy secrets from a namespace to another

```bash
  kubectl get secrets -o json --namespace <namespace_source> | \
  jq '.items[].metadata.namespace = "<namespace_destiny>"' | \
  kubectl create -f -
```

### Kubectl

#### Print logs with human-readable timestamp

```bash
  kubectl -n <namespace> logs -f <pod_name> --timestamps
```

#### Getting logs from all pods using a label to filter

```bash
  kubectl -n <namespace> logs -f -l app=nginx
```

### Kubetail

#### Tail logs specific container

```bash
  kubetail <pod_name> -c <container_name>
```

#### Tail logs from all containers on specific namespace

```bash
  kubetail -n <namespace>
```

### Stern

#### Tail logs all pods on specific namespace

```bash
  stern -n <namespace> .
```

#### Tail logs all pods with excluded

```bash
  stern -n <namespace> --exclude-container <pattern_string> .
```

#### Tail logs specific pods

```bash
  stern -n <namespace> --container <pattern_string>
```

## Definitions

### External DNS

Service which manages DNS records, in our case, on Route53. External DNS check annotations on `Service` and `Ingress` objects. On UserTesting we configured External DNS just for checking annotations on `Ingress` objects.
External DNS search domain names on  `annotations` or `host` on `rules`  and creates a register on Hosted Zones which we configure or matching with domain name which we configured.

### AWS Load Balancer Controller

Service which manages `Ingress` and `Services` k8s objects.
For `Ingress` it creates an ALB by default, but can be configured to create a NLB.
For `Service` it creates NLB only, no ALB.
We can configure the Load Balancer parameters using `annotations`. On the other hand, for configure Listener Rules we use the parameter `rules` of `Ingress` object.

### Emissary Ingress

IT DOESN'T MANAGE LOAD BALANCER. It's a kind of application firewall. It routes requests using different paths. It works like a AWS Listener Rule but with a lot of extra options.

## Tutorials and Guides

### Local Cluster

* [Create local cluster for gomelab with k3d](https://iamunnip.medium.com/building-a-local-kubernetes-cluster-using-k3d-3ec96a802e48)
* [Spin up a local cluster with k3d](https://akyriako.medium.com/provision-a-high-availability-k3s-cluster-with-k3d-a7519f476c9c)
* [Exposing services in k3d with traefik](https://www.ivankrizsan.se/2024/07/12/exposing-services-in-a-k3s-k3d-cluster-with-traefik/)

### Load Balancing Strategies

* [Powerful Load Balancing Strategies: Kubernetes Gateway API](https://cloudnativeengineer.substack.com/p/powerful-load-balancing-strategies-kubernetes)
* [Getting Started with Kubernetes Gateway API and Traefik](https://community.traefik.io/t/getting-started-with-kubernetes-gateway-api-and-traefik/23601)

### Debugging Kubernetes Issues

* [Debugging Kubernetes Issues in Production: A technical guide](https://hervekhg.medium.com/debugging-kubernetes-issues-in-production-a-technical-guide-9e3d26e27180)

### Ingresses

* [Native EKS Ingress with AWS Load Balancer Controller](https://blog.antoinechoula.ga/native-eks-ingress-with-aws-load-balancer-controller)
* [Why I'm moving from Kubernetes Ingress to Gateway API](https://blog.devops.dev/why-im-moving-from-kubernetes-ingress-to-gateway-api-9aa303127002)

### Certificates

* [Ingress + Cert-Manager](https://www.youtube.com/watch?v=ZKrC261Rxqo)

### DNS

* [The life of a DNS query in Kubernetes](https://www.nslookup.io/learning/the-life-of-a-dns-query-in-kubernetes/)

### Networking

* [The Kubernetes Networking Guide](https://www.tkng.io/)
* [An Illustrated Guide to Kubernetes Networking [Part 1]](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-1-d1ede3322727)
* [An Illustrated Guide to Kubernetes Networking [Part 2]](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-2-13fdc6c4e24c)
* [An Illustrated Guide to Kubernetes Networking [Part 3]](https://itnext.io/an-illustrated-guide-to-kubernetes-networking-part-3-f35957784c8e)
* [ALB Ingress Controller on AWS EKS](https://medium.com/tensult/alb-ingress-controller-on-aws-eks-45bf8e36020d)
* [What Actually Happens When You Publish a Container Port](https://iximiuz.com/en/posts/docker-publish-container-ports/?z=101&utm_source=newsletter&utm_medium=email&utm_campaign=devopsbulletin&utm_content=devopsbulletin)

### Secrets

* [State of Kubernetes Secrets Management in 2022](https://medium.com/4th-coffee/state-of-kubernetes-secrets-management-in-2022-6148af91e7b5)
* [How to Set External-Secrets with AWS](https://blog.container-solutions.com/tutorial-how-to-set-external-secrets-with-aws)
* [How to Set External-Secrets with Hashicorp Vault](https://blog.container-solutions.com/tutorialexternal-secrets-with-hashicorp-vault)

### Virtual Kubernetes

* [Testing GitOps on Virtual Kubernetes Clusters with ArgoCD (Ephimeral k8s clusters)](https://piotrminkowski.com/2023/06/29/testing-gitops-on-virtual-kubernetes-clusters-with-argocd/)

### Troubleshooting

* [How to identify and troubleshoot common Kubernetes errors](https://newrelic.com/blog/how-to-relic/monitoring-kubernetes-part-three?utm_source=devopsbulletin&utm_id=newsletter)
