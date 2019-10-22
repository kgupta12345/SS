### JFrog Artifactory is the most popular place to maintain our Artifacts. Artifacts may include Docker Images. 

##### STEP#1: Create an account. Soon, you will get an email with below description:

```
JFrog Customer Portal
This is where you can view and manage account information such as statistics and billing details for your Artifactory Cloud account.
Login to your JFrog Customer Portal with the following details:
      URL: https://my.jfrog.com/
      User: rahulvaishportal
      Password: As you provided on the registration form(JFrog@0522)

Your Artifactory Cloud instance is running on Amazon Web Services (AWS)
This is where you manage all of your binary artifacts.
Login to your Artifactory Cloud instance with the following details:
      URL: https://rahulvaishserver.jfrog.io/rahulvaishserver/webapp/
      User: admin
      Password: Id9Qn6Cg3Bj7Jq
You can change your password after the first login.
```
##### STEP#2: Open the portal
![image](https://user-images.githubusercontent.com/45539698/67255037-b0841600-f49d-11e9-859e-233bc96c9762.png)
##### Click on rahulvaishserver:
![image](https://user-images.githubusercontent.com/45539698/67255058-c691d680-f49d-11e9-9a65-c21f8dbdfbe2.png)
![image](https://user-images.githubusercontent.com/45539698/67255072-d5788900-f49d-11e9-8c9c-1ba650b71e60.png)
##### Choose Remote :
![image](https://user-images.githubusercontent.com/45539698/67259780-48d9c500-f4b5-11e9-9e41-e307060f3243.png)
![image](https://user-images.githubusercontent.com/45539698/67255096-05279100-f49e-11e9-921d-e30cf0725319.png)
##### Click New | Choose Docker | Assign Repository Name:
![image](https://user-images.githubusercontent.com/45539698/67259871-d9b0a080-f4b5-11e9-8e21-ed4c9ee8862c.png)
##### STEP#3: Check Artifactory Home:
![image](https://user-images.githubusercontent.com/45539698/67259990-81c66980-f4b6-11e9-985e-cd64a90f43f4.png)
##### STEP#4: Push Docker Image on Artifactory:
```
docker login rahulvaishserver-rahulvaishdockerremote.jfrog.io
USERNAME: admin
PASSWORD: Id9Qn6Cg3Bj7Jq
docker tag rahulvaish/springbootdocker rahulvaishserver-rahulvaishdockerremote.jfrog.io/springbootdocker
docker push rahulvaishserver-rahulvaishdockerremote.jfrog.io/springbootdocker
```
```
curl -uadmin:Id9Qn6Cg3Bj7Jq -T /Users/rvaish/KUBEExperiments/rc.yml "https://rahulvaishserver.jfrog.io/rahulvaishserver/rahulvaishdockerlocal/targetfolder"
```

##### STEP#5: Pull Docker Image from Artifactory:



 docker login rahulvaishserver-rahulvaishdocker.jfrog.io

https://www.jfrog.com/confluence/display/RTF/Getting+Started+with+Artifactory+as+a+Docker+Registry
