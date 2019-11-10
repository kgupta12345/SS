### Contexts & Namespces
###### [Here]() we saw how a brief introduction to create a namespace and switching.But, did not mentioned anything about Contexts. C&N are used to make logical seperation/partition within a cluster. The below guide aims to setup and understand Contexts & Namespces in Kubernetes.

##### STEP#1: Create a Context:
```
kubectl config set-context mycontext --namespace=vaishrahulnamespace --user=kubernetes-admin --cluster=kubernetes
```

##### STEP#2: List all Contexts | Determine Current Context | Switch Context:
```
kubectl config get-contexts
```
```
kubectl config current-context
```
```
kubectl config use-context mycontext
```
![image](https://user-images.githubusercontent.com/45539698/68538806-4ece1200-03a0-11ea-9d1e-1231aae7733e.png)

##### NOTE: Kubernetes default context is ```kubernetes-admin@kubernetes``` and the default namespace is ```default```. We have created a new context ```mycontext``` and its default namespace is ```vaishrahulnamespace```. Default namespaces are not meant for holding resources(deployments/pods etc) *unless* it is Kubernetes System's Namespace (```kube-system```).

<hr>

| EXPERIMENT : To Understand Pod-Context-Namespace Relationships |
|---|
###### *STEP#1: Create Contexts:*
```
kubectl config set-context mycontext --namespace=vaishrahulnamespace --user=kubernetes-admin --cluster=kubernetes
```
```
kubectl config set-context mycontext1 --namespace=vaishrahulnamespace1 --user=kubernetes-admin --cluster=kubernetes
```
```
kubectl config set-context mycontext2 --namespace=kube-system --user=kubernetes-admin --cluster=kubernetes
```
##### *```NOTE```: We have created 3 conexts.2/3 have their own N/Ss. 1/3 has its default NS as kube-system (Kubernetes System's Namespace):*
![image](https://user-images.githubusercontent.com/45539698/68538903-a28d2b00-03a1-11ea-8a64-637781d37634.png)
###### *STEP#2: Create Namespaces:*
```
apiVersion: v1
kind: Namespace
metadata:
  name: rahulnamespace

```
```
vi n2.yml
```
```
apiVersion: v1
kind: Namespace
metadata:
  name: vaishnamespace

```
###### *STEP#3: Listing Namespaces:*
![image](https://user-images.githubusercontent.com/45539698/68539048-b5a0fa80-03a3-11ea-8951-ddf725ef9e70.png)

###### *STEP#2: Make Deployment:*
```
vi deployment.yml
```
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: appdeploymentfromyml
  labels:
    app: appdeploymentfromyml
spec:
  replicas: 1
  selector:
    matchLabels:
      app: appdeploymentfromyml
  template:
    metadata:
      labels:
        app: appdeploymentfromyml
    spec:
      containers:
      - name: appdeploymentfromyml
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080
```
```
kubectl apply -f deployment.yml
```
