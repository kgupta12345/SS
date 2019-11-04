### Docker + MySQL

###### To run MySQL image as container on Docker, follow the below steps:

##### WAY#1: Use Data Directory on the Host System:
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v /Users/rvaish/mysqlbackup:/var/lib/mysql/  mysql

docker exec -it testmysqldocker mysql -uroot -proot
```

##### WAY#2: Use Runtime DockerVolume:
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v runtimemysqldockervolume:/var/lib/mysql/  mysql

docker exec -it testmysqldocker mysql -uroot -proot
```

##### WAY#3: Use a DockerVolume: 
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v manualcreateddockervolume:/var/lib/mysql/  mysql

docker exec -it testmysqldocker mysql -uroot -proot
```

![dockermysql](https://user-images.githubusercontent.com/689226/62350151-76028100-b51f-11e9-83ba-16c81dae6964.jpeg)

##### NOTE: Your MySQL container is bounded with a Volume. To experiment:
[1] Check MySQL is running using ```docker ps```
[2] Delete the volume using ```docker volume prune``` -> The volume does not gets deleted.
[3] **Stop** the conatiner ``` docker stop <container id>``` & then try to delete the volume using ```docker volume prune``` -> The volume does not gets deleted.
[4] Stop the conatiner ``` docker stop <container id>``` and execute ``` docker system prune ``` and then use ```docker volume prune``` -> The volume gets deleted.
[5] Now you can't **start** the conatiner -> The conatiner does not gets started, as we used  ``` docker system prune ```

![image](https://user-images.githubusercontent.com/45539698/68086080-cfa38000-fe6d-11e9-93b7-290f13c5c02c.png)

