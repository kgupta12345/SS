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


##### STEP#3: Create a namespace inside new context:
