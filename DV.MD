### Docker Volumes

###### Volumes are locations on host machine which persists the data for containers. The below guide aims to setup and understand Volumes in Docker.

##### STEP#1: Create a Docker Volume:
```
bash-3.2$ docker volume create mydockervolume
mydockervolume
```

##### STEP#2: List the Docker Volume:
```
bash-3.2$ docker volume ls
local               mydockervolume
```

##### STEP#3: Investigate the Docker Volume:
```
bash-3.2$ docker volume inspect mydockervolume
[
    {
        "CreatedAt": "2019-11-04T12:07:53Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/mydockervolume/_data",
        "Name": "mydockervolume",
        "Options": {},
        "Scope": "local"
    }
]
bash-3.2$ 
```
###### *Sharing Docker Volumes refers to sharing this location: /var/lib/docker/volumes/mydockervolume/_data*  

##### STEP#4: Delete a Docker Volume:
```
bash-3.2$ docker volume rm mydockervolume
mydockervolume
```
###### To Delete all unused-local docker volumes

```
bash-3.2$ docker volume prune
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
Deleted Volumes:
mydockervolume

Total reclaimed space: 0B
bash-3.2$ 

```

##### STEP#5: Attach a Docker Volume with Container (MySQL):
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -mydockervolume:/var/lib/mysql/  mysql

docker exec -it testmysqldocker mysql -uroot -proot
```

##### *NOTE: Your MySQL container is bounded with a Volume. To experiment:*
###### [1] Check MySQL is running using ```docker ps``` </br>
###### [2] Delete the volume using ```docker volume rm mydockervolume``` &rarr; The volume does not gets deleted. </br>
###### [3] Stop the container ``` docker stop <container id>``` & then, try to delete the volume using ```docker volume rm mydockervolume``` &rarr; The volume does not gets deleted. </br>
###### [4] Stop the container ``` docker stop <container id>``` and remove it using ``` docker container rm <container id>``` and then try to delete the volume using ```docker volume rm mydockervolume``` &rarr; The volume gets deleted. </br>
###### [5] Now you can't start the container. The stopped container has been removed. </br>
###### [6] On re-instantiating MySQL container on Docker it will be a *Fresh setup*. As your volume has been deleted - so executing the *```docker run....```* command (using manually created volume or runtime volume) will be a volume from scratch.</br>


