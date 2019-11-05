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

