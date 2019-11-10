### Contexts & Namespces
###### [Here]() we saw how a brief introduction to create a namespace and switching.But, did not mentioned anything about Conexts. C&N are used to make logical seperation/partition within a cluster. The below guide aims to setup and understand Contexts & Namespces in Kubernetes.

##### STEP#1: Create a Context:
```
kubectl config set-context mycontext --namespace=vaishrahulnamespace --user=kubernetes-admin --cluster=kubernetes
```
##### NOTE: Kubernetes default context is ```kubernetes-admin@kubernetes``` and the default namespace is ```default```. We have created a new context ```mycontext``` and its default namespace is ```vaishrahulnamespace```. Default namespaces are not meant for holding resources(deployments/pods etc) *unless* it is any of Kubernetes System's Namespace. We shall see this in the below Experiment section.


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

| EXPERIMENT : To Understand Pod-Namespace Relationship |
|---|
###### *STEP#1: Create Context:*

```

```

```
vi n1.yml
```
```
apiVersion: v1
kind: Namespace
metadata:
  name: rahulnamespace

```
```
kubectl apply -f n1.yml
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
```
kubectl apply -f n2.yml
```
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
