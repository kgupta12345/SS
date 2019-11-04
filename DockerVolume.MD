### Docker + MySQL

###### To run MySQL image as container on Docker, follow the below steps:

  - ##### WAY#1: Use Data Directory on the Host System:
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v /Users/rvaish/mysqlbackup:/var/lib/mysql/  mysql

docker exec -it testmysqldocker mysql -uroot -proot
```

  - ##### WAY#2: Use a DockerVolume: 
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v manualcreateddockervolume:/var/lib/mysql/  mysql

docker exec -it testmysqldocker mysql -uroot -proot
```

  - ##### WAY#3: Use Runtime DockerVolume:
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v runtimemysqldockervolume:/var/lib/mysql/  mysql

docker exec -it testmysqldocker mysql -uroot -proot
```

<hr>

##### NOTE: Your MySQL container is bounded with a Volume. To experiment:
###### [1] Check MySQL is running using ```docker ps``` </br>
###### [2] Delete the volume using ```docker volume prune``` &rarr; The volume does not gets deleted. </br>
###### [3] **Stop** the conatiner ``` docker stop <container id>``` & then try to delete the volume using ```docker volume prune``` &rarr; The volume does not gets deleted. </br>
###### [4] Stop the conatiner ``` docker stop <container id>``` and execute ``` docker system prune ``` and then use ```docker volume prune``` &rarr; The volume gets deleted. </br>
###### [5] Now you can't **start** the conatiner. The conatiner does not gets started, as we used  ``` docker system prune ``` </br>

![image](https://user-images.githubusercontent.com/45539698/68086080-cfa38000-fe6d-11e9-93b7-290f13c5c02c.png)
###### [6] On re-initiation of MySQL container on Docker it will be a *Fresh setup*. As your volume has been deleted - so executing the *```docker run....```* query (using manual created volume or runtime volume) will be a volume from scratch.</br>
