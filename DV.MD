### Docker Volumes

###### Volumes are _______. The below guide aims to setup and understand Volumes in Docker.

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

##### STEP#4: Delete the Docker Volume:
```
bash-3.2$ docker system prune
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - all dangling build cache

Are you sure you want to continue? [y/N] y
Total reclaimed space: 0B



bash-3.2$ docker volume prune
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
Deleted Volumes:
mydockervolume

Total reclaimed space: 0B
bash-3.2$ 

```

##### STEP#5: Attach a Docker Volume:
```
```

##### Share a Docker Volume:
```
```

##### Bind Mounts
```
```
