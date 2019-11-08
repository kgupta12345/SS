### Docker Volumes

###### Volumes are locations on host machine which persists the data for containers. The below guide aims basic operations to do with Docker Volumes.

##### STEP#1: Create a Docker Volume:
<pre>
bash-3.2$ <b>docker volume create mydockervolume</b>
mydockervolume
</pre>

##### STEP#2: List the Docker Volume:
<pre>
bash-3.2$ <b>docker volume ls</b>
local               mydockervolume
</pre>

##### STEP#3: Investigate the Docker Volume:
<pre>
bash-3.2$ <b>docker volume inspect mydockervolume</b>
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
</pre>

##### NOTE: Sharing Docker Volumes refers to sharing this location: /var/lib/docker/volumes/mydockervolume/_data  


##### STEP#4: Attach a Docker Volume with Container (MySQL):
<pre>
bash-3.2$ <b>docker run --name testmysqldocker -d -p 3306:3306 -e MYSQL_DATABASE=dev -e MYSQL_ROOT_PASSWORD=root -v runtimemysqldockervolume:/var/lib/mysql/  mysql</b>
2fcecff8bfa9e454e582f4536c39b09c46bdc82bf184f0b91e39feb65630b7e9
bash-3.2$
</pre>

##### STEP#5: Delete Docker Volume:
<pre>
bash-3.2$ <b>docker volume rm mydockervolume</b>
mydockervolume
</pre>
 - ###### Delete Docker Volume when attached with container(Here, MySQL):
    <pre>
    bash-3.2$ <b>docker stop 438f7e343b4b</b>  
    438f7e343b4b 
    </pre>
    <pre>
    bash-3.2$ <b>docker container rm 438f7e343b4b </b> 
    438f7e343b4b
    </pre>
    <pre>
    bash-3.2$ <b>docker volume rm runtimemysqldockervolume </b> 
    runtimemysqldockervolume
    </pre>
    <pre>
    bash-3.2$ <b>docker volume ls </b> 
    DRIVER              VOLUME NAME
    </pre>
