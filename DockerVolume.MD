### Docker + MySQL

###### To run MySQL image as container on Docker, follow the below steps:

##### WAY#1: Using Mounting Storage:
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v /Users/rvaish/mysqlbackup:/var/lib/mysql/  mysql

docker exec -it testmysqldocker mysql -uroot -proot
```
![dockermysql](https://user-images.githubusercontent.com/689226/62350151-76028100-b51f-11e9-83ba-16c81dae6964.jpeg)

##### WAY#2: Using DockerVolume:

##### MySQL & DockerVolumes
###### WAY#1: 
[1] Create a Docker Volume.
[2] Refer Docker Volume with MySQL execute command:

###### WAY#2: 
[1] Create a Docker Volume during run time:

![image](https://user-images.githubusercontent.com/45539698/68086080-cfa38000-fe6d-11e9-93b7-290f13c5c02c.png)

