### Contexts & Namespces
###### [Here](https://github.com/rahulvaish/ReferenceDocuments/blob/master/UnderstandingKubernetes/%5B05%5D%20Commands-ApplicationDeployment%5BNodes%20and%20Pods%5D.MD) we had a brief introduction about creating a namespace and switching. But, we didn't learnt anything about Contexts. C&N are used to make logical seperation/partition within a cluster. The below guide aims to setup and understand Contexts & Namespces in Kubernetes.


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
![image](https://user-images.githubusercontent.com/45539698/68540420-e4c16700-03b7-11ea-8827-728de9791603.png)
##### *```NOTE```: We have created 3 Contexts ```mycontext, mycontext1, mycontext2```. Contexts ```mycontext, mycontext1``` have their own default namespaces ```vaishrahulnamespace, vaishrahulnamespace1``` respectively. Context ```mycontext2``` has chose its default namespace as ```kube-system``` which is one of Kubernetes System's Namespace.*
###### *STEP#2: Create Additional Namespaces:*
```
apiVersion: v1
kind: Namespace
metadata:
  name: rahulnamespace

```
```
apiVersion: v1
kind: Namespace
metadata:
  name: vaishnamespace
```
###### *STEP#3: Listing Namespaces:*
![image](https://user-images.githubusercontent.com/45539698/68539048-b5a0fa80-03a3-11ea-8951-ddf725ef9e70.png)
##### *```NOTE```: We can't see default Namespaces which were created as part of Context.*
###### *STEP#3: Make Deployment:* 
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
##### OBSERVATIONS:
  - Switch to mycontext -deploy------> rahulnamespace 
  - Switch to mycontext -deploy------> vaishnamespace 
  - Switch to mycontext -can't deploy on------> vaishrahulnamespace
  - Switch to mycontext -can't deploy on------> vaishrahulnamespace1
  - Switch to mycontext -deploy------> kube-system


