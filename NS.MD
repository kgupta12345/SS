### Contexts & Namespces
###### [Here]() we saw how a brief introduction to create a namespace and switching. The below guide aims to setup and understand Contexts & Namespces in Kubernetes.

##### STEP#1: Create a Context:
```
kubectl config set-context kubesys --namespace=vaishrahulnamespace --user=kubernetes-admin --cluster=kubernetes
```

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
