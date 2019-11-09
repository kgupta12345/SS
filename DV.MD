### Docker Volumes

###### Volumes are locations on host machine which persists the data for containers. The below guide aims basic operations to do with Docker Volumes.

##### STEP#1: Create a Docker Volume:
<pre>
docker volume create mydockervolume<
</pre>

##### STEP#2: List the Docker Volume:
<pre>
docker volume ls
</pre>

##### STEP#3: Investigate the Docker Volume:
<pre>
bash-3.2$ <b>docker volume inspect mydockervolume</b>
[
    {
        "CreatedAt": "2019-11-04T12:07:53Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "<b>/var/lib/docker/volumes/mydockervolume/_data</b>",
        "Name": "mydockervolume",
        "Options": {},
        "Scope": "local"
    }
]
bash-3.2$ 
</pre>

##### STEP#4: Attach a Docker Volume with Container (MySQL):
<pre>
docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v runtimemysqldockervolume:/var/lib/mysql/  mysql
</pre>

##### STEP#5: Delete Docker Volume:
<pre>
docker volume rm mydockervolume
</pre>
 - ###### Delete Docker Volume when attached with container(Here, MySQL):
    <pre>
    docker stop <<container_id>>
    </pre>
    <pre>
    docker container rm <container_id> 
    </pre>
    <pre>
    docker volume rm runtimemysqldockervolume
    </pre>

<hr>
