### Contexts & Namespces
###### [Here]() we saw how a brief introduction to create a namespace and switching. The below guide aims to setup and understand Contexts & Namespces in Kubernetes.

##### STEP#1: Create a Context:
```
kubectl config set-context kubesys --namespace=vaishrahulnamespace --user=kubernetes-admin --cluster=kubernetes
```
##### NOTE: Kubernetes default context is ```kubernetes-admin@kubernetes``` and its default namespace is ```default```. We have created a new context ```kubesys``` and its default namespace is ```vaishrahulnamespace```.
![image](https://user-images.githubusercontent.com/45539698/68532907-0f280b80-0349-11ea-8bd4-090fb57c853a.png)

##### STEP#2: List all Contexts | Determine Current Context | Switch Context:
```
kubectl config get-contexts
```
```
kubectl config current-context
```
```
kubectl config use-context kubesys
```
![image](https://user-images.githubusercontent.com/45539698/68532675-785a4f80-0346-11ea-9852-0a4ff7d87924.png)


| ANALYSIS : To Understand Pod-Namespace Relationship |
|---|
###### *STEP#1: Create Namespaces:*
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
