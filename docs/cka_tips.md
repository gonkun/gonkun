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
 