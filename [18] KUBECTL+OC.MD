### Kubernetes from Openshift CLI

##### STEP#1: Get Openshift Client:
 ```   
 [1] Download OSE Client from https://www.okd.io/download.html
 [1] tar -zxvf openshift-origin-server-v3.11.0-0cbc58b-linux-64bit.tar.gz 
 [2] mv openshift-origin-server-v3.11.0-0cbc58b-linux-64bit /usr/local/OSE/
 [3] sudo cp  /usr/local/OSE/oc /usr/local/bin
 [4] sudo cp  /usr/local/OSE/kubectl /usr/local/bin
 ```
##### STEP#2: Get the Token form OSE:
![image](https://user-images.githubusercontent.com/45539698/68072250-a8837a80-fda9-11e9-9b41-4fb49297aa92.png)
###### On clicking *Copy Login Command*:
![image](https://user-images.githubusercontent.com/45539698/68072277-1334b600-fdaa-11e9-9dfe-ef254d94dbe9.png)

##### STEP#3: Login OSE from Ubuntu Terminal:
```
oc login --token=TGE2PKYD7r6TiEdlPIK4wYQjLGB-a-dS4BwSBbpTmTc --server=https://api.us-east-1.starter.openshift-online.com:6443
```
![image](https://user-images.githubusercontent.com/45539698/68084310-21420f80-fe5a-11e9-8a97-7215ddd7629f.png)
###### We can use kubectl commands too to leverage Openshift Account from Ubuntu Terminal:
![image](https://user-images.githubusercontent.com/45539698/68084329-40d93800-fe5a-11e9-9851-8c3dec736735.png)


