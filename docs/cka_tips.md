# CKA Tips
Some tips for prepare/use on CKA exam

## Generate YAML template/files for most common k8s resources objects 

* Generate a POD YAML file
```
    kubectl run nginx --image=nginx --dry-run=client -o yaml
```
<br>

* Generate Deployment YAML file
```
    kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
```
<br>

* Generate Service YAML file
```
    kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
```
<br>

* Create Service named 'redis-service' exposing pod 'redis'
```
    kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
```
 
 ## Upgrade Kubernetes Process
 Example how to upgrade a Kubernetes cluster. In this case is using tool `kubeadm` and upgrading from version `1.28` to `1.29`.

On the `controlplane` node:<br>
* Drain node
```
kubectl drain controlplane --ignore-daemonsets
```

* Open the file that defines the Kubernetes apt repository:
```
vim /etc/apt/sources.list.d/kubernetes.list
```

* Update the version in the URL to the next available minor release, i.e v1.29.
```
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /
```

* After making changes, save the file and exit from your text editor. Proceed with the next instruction.
```
root@controlplane:~# apt update
root@controlplane:~# apt-cache madison kubeadm
```

* Based on the version information displayed by apt-cache madison, it indicates that for Kubernetes version 1.29.0, the available package version is 1.29.0-1.1. Therefore, to install kubeadm for Kubernetes v1.29.0, use the following command:
```
root@controlplane:~# apt-get install kubeadm=1.29.0-1.1
```

* Run the following command to upgrade the Kubernetes cluster.
```
root@controlplane:~# kubeadm upgrade plan v1.29.0
root@controlplane:~# kubeadm upgrade apply v1.29.0
```

* Now, upgrade the version and restart Kubelet. Also, mark the node (in this case, the "controlplane" node) as schedulable.
```
root@controlplane:~# apt-get install kubelet=1.29.0-1.1
root@controlplane:~# systemctl daemon-reload
root@controlplane:~# systemctl restart kubelet
root@controlplane:~# kubectl uncordon controlplane
```

* Uncordon node
```
kubectl uncordon controlplane
```

On the `node01` node:<br>
* Drain node
```
kubectl drain node01 --ignore-daemonsets
```

* Use any text editor you prefer to open the file that defines the Kubernetes apt repository.
```
vim /etc/apt/sources.list.d/kubernetes.list
```

* Update the version in the URL to the next available minor release, i.e v1.29.
```
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /
```

* After making changes, save the file and exit from your text editor. Proceed with the next instruction.
```
root@node01:~# apt update
root@node01:~# apt-cache madison kubeadm
```

* Based on the version information displayed by apt-cache madison, it indicates that for Kubernetes version 1.29.0, the available package version is 1.29.0-1.1. Therefore, to install kubeadm for Kubernetes v1.29.0, use the following command:
```
root@node01:~# apt-get install kubeadm=1.29.0-1.1
# Upgrade the node 
root@node01:~# kubeadm upgrade node
```

* Now, upgrade the version and restart Kubelet.
```
root@node01:~# apt-get install kubelet=1.29.0-1.1
root@node01:~# systemctl daemon-reload
root@node01:~# systemctl restart kubelet
```