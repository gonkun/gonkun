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

## Managing etcd service
* Get list of members of a cluster **etcd**<br>
After login on `etcd-server`, get the list of endpoints for to know if etcd server is  acluster and how many nodes has:
```
ETCDCTL_API=3 etcdctl \
--endpoints=https://127.0.0.1:2379 \
--cacert=<CA_public_cert> \
--cert=<server_public_cert> \
--key=<server_private_key> \
  member list
```
**NOTE:** We have to find certificate's path on `etcd` process

* Backup database **etcd**
```
ETCDCTL_API=3 etcdctl \
--endpoints=https://127.0.0.1:2379 \
--cacert=<CA_public_cert> \
--cert=<server_public_cert> \
--key=<server_private_key> \
snapshot save <backup-file-location>
```

## Certificates API
* Example CertificateSigningRequest resource
```
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  groups:
  - system:authenticated
  request: "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQW5lMU5DamI4RFR1VWp2Ky9MT3d4Rnl4bTMvdHVuNVN3N0hoWVhhR3Q1N3dMClB5Q0dnZXB0WGFqV1U2V2JjbnhhK1Myb3pNU0hnMmxSaVB3T2MzN3dBOFZJMi9NaEpTRUtscHBsdWdmRzdJTS8KNytNVEdiSUE0RVQ4Ykt0RzcxUGt3NWNuTjdCWmFmME1lT2gyakErN09SREtnelh5QUQ4d0haanZwTFppMFI3cgowM1hqK3F1MUI2emZoeFRmZ2VmRExUV05XNTdKR2phenhISkZybkFCWkR2RG84OTkwcXdmbDY4MHZScW9lcURYCkhvWFRmWVA2aERwcTRicjBRaUd2aDZpYmZYU2V3L25tUzdvaGtIcGhxMXBTNk96K2JsMExVb3NQSDRBR2p2ZDIKZ0FlanNaMnJPN1RMcCsvalovdUwzazlua281R1B0TzhnTE8xTFVXcTB3SURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBQ09Yc0Jhe
nlSYkZ0LzVQTHdBTFBLNFpuWStLcFlJeG5ONzYxaGsxQU1Za2R2R1ZOdkIzCkx5dTBDZjJaeE5ZWVM3OWxwL0tWV2w4cjByTjk4RjdIZUM2RXkzM3gvYUtzYi9Ma2FLMFp1UVVPcW9IQ3JZb24KVVhHQ0dlOC9ud00xMFZFb05xNnBHZCtPZXB6MkdJTitpcUdPeVNMUWhYL0cwSnRnMHlrS0hvRE5OL2Nvc29kLwp0eUlTbXNDTzdhRlY0a2M1Q1ZPU2FSWC9kazRPSjkyVXB6N1F4alh2V0c1NEFFZXV6ZzNBdjdoc1JCTVlSc1I0CjQ1U1hKK3dCVlZXS1FtRDM3NXNMTDVTb2ZjTGdvdkcyUTB6ZmVlU0pkSGNadlNwSnYrSmFvNVZrT25HbldxOWcKblg0WVFDRVdtczZmVGo1SnMveHZvQ0k3UCtHeDQ2MlljNG89Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo="
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```

* Approve a CertficateSigningRrequest
```
kubectl certificate approve <certificate_name>
```

* Deny/Reject a CertficateSigningRrequest
```
kubectl certificate deny <certificate_name>
```
