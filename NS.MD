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
##### *```NOTE```: We have created 3 contexts ```mycontext, mycontext1, mycontext2```. Contexts ```mycontext, mycontext1``` have their own default Namespaces ```vaishrahulnamespace, vaishrahulnamespace1``` respectively. Context ```mycontext2``` has chose its default Namespace as ```kube-system``` which is one of Kubernetes System's Namespace.*
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
![image](https://user-images.githubusercontent.com/45539698/68539048-b5a0fa80-03a3-11ea-8951-ddf725ef9e70.png)
##### *```NOTE```: We can't see default Namespaces which were created as default for respective Contexts.*
###### *STEP#3: Create a resource(Deployment) from mycontext :*
  - ###### *STEP#A: Switch to mycontext | Check current Context:*
    ```
    kubectl config use-context mycontext
    ```
    ```
    kubectl config current-context
    ```
  - ###### *STEP#B: Choose a Namespace:*
    ```
    kubectl config set-context $(kubectl config current-context) --namespace=rahulnamespace
    ```
  - ###### *STEP#C: Create a Deployment:*
    ```
    vi deploy.yml
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
      kubectl apply -f deploy.yml
      ``` 
      ##### *OBSERVATIONS:*
      - *From mycontext -deploy on rahulnamespace ------> SUCCESS*
      - *From mycontext -deploy on vaishnamespace ------> SUCCESS*
      - *From mycontext -deploy on vaishrahulnamespace (It's own default NS) ------> FAILURE*
      - *From mycontext -deploy on vaishrahulnamespace1 (Tagged as default NS) ------> FAILURE*
      - *From mycontext -deploy on kube-system ------> SUCCESS*

###### *STEP#4: ```CLEAR ALL DEPLOYMENTS FROM ALL NAMESPACES```*

###### *STEP#5: Create a resource(Deployment) from mycontext1 :*
  - ###### *STEP#A: Switch to mycontext1 | Check current Context:*
    ```
    kubectl config use-context mycontext1
    ```
    ```
    kubectl config current-context
    ```
  - ###### *STEP#B: Choose a Namespace:*
    ```
    kubectl config set-context $(kubectl config current-context) --namespace=rahulnamespace
    ```
  - ###### *STEP#C: Create a Deployment:*
    ```
    vi deploy.yml
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
      kubectl apply -f deploy.yml
      ```
      ##### *OBSERVATIONS:*
      - *From mycontext1 -deploy on rahulnamespace ------> SUCCESS*
      - *From mycontext1 -deploy on vaishnamespace ------> SUCCESS*
      - *From mycontext1 -deploy on vaishrahulnamespace (Tagged as default NS) ------> FAILURE*
      - *From mycontext1 -deploy on vaishrahulnamespace1 (It's own default NS) ------> FAILURE*
      - *From mycontext1 -deploy on kube-system ------> SUCCESS*

###### *STEP#6: ```CLEAR ALL DEPLOYMENTS FROM ALL NAMESPACES```*  

###### *STEP#7: Create a resource(Deployment) from mycontext2 :*
  - ###### *STEP#A: Switch to mycontext2 | Check current Context:*
    ```
    kubectl config use-context mycontext2
    ```
    ```
    kubectl config current-context
    ```
  - ###### *STEP#B: Choose a Namespace:*
    ```
    kubectl config set-context $(kubectl config current-context) --namespace=rahulnamespace
    ```
  - ###### *STEP#C: Create a Deployment:*
    ```
    vi deploy.yml
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
      kubectl apply -f deploy.yml
      ```
      ##### *OBSERVATIONS:*
      - *From mycontext2 -deploy on rahulnamespace ------> SUCCESS*
      - *From mycontext2 -deploy on vaishnamespace ------> SUCCESS*
      - *From mycontext2 -deploy on vaishrahulnamespace (Tagged as default NS) ------> FAILURE*
      - *From mycontext2 -deploy on vaishrahulnamespace1 (Tagged as default NS) ------> FAILURE*
      - *From mycontext2 -deploy on kube-system (It's mycontext2's default NS. But, also K8 System's NS) ------> SUCCESS*

<hr>


