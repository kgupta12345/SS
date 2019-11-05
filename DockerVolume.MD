### MySQL on Docker

###### To run MySQL image as container on Docker, follow the below steps:

  - ##### WAY#1: Use Data Directory on the Host System:
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v /Users/rvaish/mysqlbackup:/var/lib/mysql/  mysql

docker exec -it testmysqldocker mysql -uroot -proot
```

  - ##### WAY#2: Use exiting DockerVolume: 
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

##### *NOTE: Your MySQL container is bounded with a Volume. To experiment:*
###### [1] Check MySQL is running using ```docker ps``` </br>
###### [2] Delete the volume using ```docker volume rm mydockervolume``` &rarr; The volume does not gets deleted. </br>
###### [3] Stop the container ``` docker stop <container id>``` & then, try to delete the volume using ```docker volume rm mydockervolume``` &rarr; The volume does not gets deleted. </br>
###### [4] Stop the container ``` docker stop <container id>``` and remove it using ``` docker container rm <container id>``` and then try to delete the volume using ```docker volume rm mydockervolume``` &rarr; The volume gets deleted. </br>
###### [5] Now you can't start the container. The stopped container has been removed. </br>
###### [6] On re-instantiating MySQL container on Docker it will be a *Fresh setup*. As your volume has been deleted - so executing the *```docker run....```* command (using manually created volume or runtime volume) will be a volume from scratch.</br>
