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
```
bash-3.2$ docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v runtimemysqldockervolume:/var/lib/mysql/  mysql
438f7e343b4b46d9ffbc7a517d1ec151b89efa1b2a90bf4d1a165c5f95bc4ee8



bash-3.2$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
438f7e343b4b        mysql               "docker-entrypoint.s…"   5 seconds ago       Up 3 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp   testmysqldocker



bash-3.2$ docker volume ls
DRIVER              VOLUME NAME
local               runtimemysqldockervolume



bash-3.2$ docker volume rm runtimemysqldockervolume
Error response from daemon: remove runtimemysqldockervolume: volume is in use - [438f7e343b4b46d9ffbc7a517d1ec151b89efa1b2a90bf4d1a165c5f95bc4ee8]



bash-3.2$ docker stop 438f7e343b4b
438f7e343b4b



bash-3.2$ docker volume rm runtimemysqldockervolume
Error response from daemon: remove runtimemysqldockervolume: volume is in use - [438f7e343b4b46d9ffbc7a517d1ec151b89efa1b2a90bf4d1a165c5f95bc4ee8]



bash-3.2$ docker container rm 438f7e343b4b
438f7e343b4b



bash-3.2$ docker volume rm runtimemysqldockervolume
runtimemysqldockervolume



bash-3.2$ docker volume ls
DRIVER              VOLUME NAME



bash-3.2$ docker start 438f7e343b4b
Error response from daemon: No such container: 438f7e343b4b
Error: failed to start containers: 438f7e343b4b
bash-3.2$ 

```


