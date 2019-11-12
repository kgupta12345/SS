### MySQL on Docker

###### To run MySQL image as container on Docker, follow the below steps:

  - ##### WAY#1: Use Data Directory on the Host System:
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v /Users/rvaish/mysqlbackup:/var/lib/mysql/  mysql
```
```
docker exec -it testmysqldocker mysql -uroot -proot
```

  - ##### WAY#2: Use exiting DockerVolume: 
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v manualcreateddockervolume:/var/lib/mysql/  mysql
```
```
docker exec -it testmysqldocker mysql -uroot -proot
```

  - ##### WAY#3: Use Runtime DockerVolume:
```
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v runtimemysqldockervolume:/var/lib/mysql/  mysql
```
```
docker exec -it testmysqldocker mysql -uroot -proot
```

<hr>


| **EXPERIMENT**: To Understand MySQL container is bounded with a Volume.  |
| --- |

###### *STEP#1: Launch MySQL Container:*
<pre>
bash-3.2$ <b>docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v runtimemysqldockervolume:/var/lib/mysql/  mysql </b>
438f7e343b4b46d9ffbc7a517d1ec151b89efa1b2a90bf4d1a165c5f95bc4ee8
</pre>
###### *STEP#2: Check the status of MySQL Container:*
<pre>
bash-3.2$ <b>docker ps </b> 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
438f7e343b4b        mysql               "docker-entrypoint.s…"   5 seconds ago       Up 3 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp   testmysqldocker
</pre>
###### *STEP#3: Check for bounded Volume:*
<pre>
bash-3.2$ <b>docker volume ls </b> 
DRIVER              VOLUME NAME
local               runtimemysqldockervolume
</pre>
##### *```NOTE```: The MySQL container, which was bounded with runtime docker volume- runtimemysqldockervolume (/var/lib/docker/volumes/) also exists.*
###### *STEP#4: Create a table inside MySQL Container:*
<pre>
bash-3.2$ <b>docker exec -it testmysqldocker mysql -uroot -proot </b> 
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.18 MySQL Community Server - GPL

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use dev;
Database changed
mysql> create table Employee (id varchar(20), firstname varchar(20));
Query OK, 0 rows affected (0.01 sec)

mysql> show tables;
+---------------+
| Tables_in_dev |
+---------------+
| Employee      |
+---------------+
1 row in set (0.00 sec)

mysql> exit;
Bye
bash-3.2$ 
</pre>
###### *STEP#5: Delete Docker Volume[1/5]:*
<pre>
bash-3.2$ <b>docker volume rm runtimemysqldockervolume </b> 
Error response from daemon: remove runtimemysqldockervolume: volume is in use - [438f7e343b4b46d9ffbc7a517d1ec151b89efa1b2a90bf4d1a165c5f95bc4ee8]
</pre>
###### *STEP#6: Delete Docker Volume[2/5]:*
<pre>
bash-3.2$ <b>docker stop 438f7e343b4b </b> 
438f7e343b4b
</pre>
###### *STEP#7: Delete Docker Volume[3/5]:*
<pre>
bash-3.2$ <b>docker volume rm runtimemysqldockervolume </b> 
Error response from daemon: remove runtimemysqldockervolume: volume is in use - [438f7e343b4b46d9ffbc7a517d1ec151b89efa1b2a90bf4d1a165c5f95bc4ee8]
</pre>
###### *STEP#8: Delete Docker Volume[4/5]:*
<pre>
bash-3.2$ <b>docker container rm 438f7e343b4b </b> 
438f7e343b4b
</pre>
###### *STEP#9: Delete Docker Volume[5/5]:*
<pre>
bash-3.2$ <b>docker volume rm runtimemysqldockervolume </b> 
runtimemysqldockervolume
</pre>
###### *STEP#10: Check the status of Docker Volume:*
<pre>
bash-3.2$ <b>docker volume ls </b> 
DRIVER              VOLUME NAME
</pre>
##### *```NOTE```: Volume runtimemysqldockervolume (/var/lib/docker/volumes/) can only be deleted if we stop and delete the MySQL container.*
###### *STEP#11: Start MySQL Container:*
<pre>
bash-3.2$ <b>docker start 438f7e343b4b </b> 
Error response from daemon: No such container: 438f7e343b4b
Error: failed to start containers: 438f7e343b4b
bash-3.2$ 
</pre>
##### *```NOTE```: Once the container is deleted, it cant be re-started.*
###### *STEP#12: Re-Launch MySQL Container:*
<pre>
bash-3.2$ <b>docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v runtimemysqldockervolume:/var/lib/mysql/  mysql </b> 
8388e3db25a2ffa577b0b36bf3b32422829112f34a37955f6b9241a253a32af3
</pre>
###### *STEP#13: Check tinside the MySQL Container:*
<pre>
bash-3.2$ <b>docker exec -it testmysqldocker mysql -uroot -proot </b> 
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.18 MySQL Community Server - GPL

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use dev;
Database changed
mysql> show tables;
<b>Empty set (0.00 sec) </b> 
mysql> exit;
Bye
bash-3.2$ 
</pre>
##### *```NOTE```: As we have deleted the runtimemysqldockervolume and now we are re-instantiating  MySQL Container with a new volume (same name), but as obvious, it won't carry/have anything of past volume, as its new!*
</pre>

<hr>
