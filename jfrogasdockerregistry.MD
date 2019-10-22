```
JFrog Customer Portal
This is where you can view and manage account information such as statistics and billing details for your Artifactory Cloud account.
Login to your JFrog Customer Portal with the following details:
      URL: https://my.jfrog.com/
      User: rahulvaishportal
      Password: As you provided on the registration form

Your Artifactory Cloud instance is running on Amazon Web Services (AWS)
This is where you manage all of your binary artifacts.
Login to your Artifactory Cloud instance with the following details:
      URL: https://rahulvaishserver.jfrog.io/rahulvaishserver/webapp/
      User: admin
      Password: Id9Qn6Cg3Bj7Jq
You can change your password after the first login.
```

![image](https://user-images.githubusercontent.com/45539698/67255037-b0841600-f49d-11e9-859e-233bc96c9762.png)
![image](https://user-images.githubusercontent.com/45539698/67255058-c691d680-f49d-11e9-9a65-c21f8dbdfbe2.png)
![image](https://user-images.githubusercontent.com/45539698/67255072-d5788900-f49d-11e9-8c9c-1ba650b71e60.png)
![image](https://user-images.githubusercontent.com/45539698/67255096-05279100-f49e-11e9-921d-e30cf0725319.png)





 docker login rahulvaishserver-rahulvaishdocker.jfrog.io
curl -uadmin:Id9Qn6Cg3Bj7Jq -T /ReleaseNotes.html "https://rahulvaishserver.jfrog.io/rahulvaishserver/example-repo-lo
cal/TARGET_FILE_PATH"
https://www.jfrog.com/confluence/display/RTF/Getting+Started+with+Artifactory+as+a+Docker+Registry
