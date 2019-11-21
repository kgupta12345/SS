### Kubernetes from Openshift CLI

##### STEP#1: Get and SetUp Openshift Client:
 <pre>  
 [1] <b>Download:</b> wget https://github.com/openshift/origin/releases/download/v3.11.0/openshift-origin-server-v3.11.0-0cbc58b-linux-64bit.tar.gz
 [2] <b>Untar:</b> tar -zxvf openshift-origin-server-v3.11.0-0cbc58b-linux-64bit.tar.gz 
 [3] <b>Execute:</b> mv openshift-origin-server-v3.11.0-0cbc58b-linux-64bit /usr/local/OSE/
 [4] <b>Execute:</b> sudo cp  /usr/local/OSE/oc /usr/local/bin
 [5] <b>Execute:</b> sudo cp  /usr/local/OSE/kubectl /usr/local/bin
 </pre>
##### STEP#2: Get the Connection String form OSE:
![image](https://user-images.githubusercontent.com/45539698/68072250-a8837a80-fda9-11e9-9b41-4fb49297aa92.png)
###### Click *Copy Login Command* [Top-Right]:
![image](https://user-images.githubusercontent.com/45539698/68072277-1334b600-fdaa-11e9-9dfe-ef254d94dbe9.png)

##### STEP#3: Login OSE from Ubuntu Terminal:
```
oc login --token=TGE2PKYD7r6TiEdlPIK4wYQjLGB-a-dS4BwSBbpTmTc --server=https://api.us-east-1.starter.openshift-online.com:6443
```
![image](https://user-images.githubusercontent.com/45539698/68084310-21420f80-fe5a-11e9-8a97-7215ddd7629f.png)
###### We can use kubectl commands too to leverage Openshift Account from Ubuntu Terminal:
![image](https://user-images.githubusercontent.com/45539698/68084329-40d93800-fe5a-11e9-9851-8c3dec736735.png)


