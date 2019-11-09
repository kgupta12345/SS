### Contexts & Namespces
###### [Here]() we saw how a brief introduction to creata a namespace and switching. The below guide aims to setup and understand Contexts & Namespces in Kubernetes.

##### STEP#1: Create a Context:
```
kubectl config set-context kubesys --namespace=kube-system --user=kubernetes-admin --cluster=kubernetes
```
![image](https://user-images.githubusercontent.com/45539698/68532074-62955c00-033f-11ea-821b-46ef8ea62e9a.png)

##### STEP#2: List Contexts | Determine Current Context | Switch Context:
```
kubectl config get-contexts
```
```
kubectl config current-context
```
```
kubectl config use-context kubesys
```
![image](https://user-images.githubusercontent.com/45539698/68532303-adb06e80-0341-11ea-9307-e34244f14143.png)

##### STEP#3:
