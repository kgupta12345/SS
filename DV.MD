### Docker Volumes

###### Volumes are _______. The below guide aims to setup and understand Volumes in Docker.

##### Creating a Docker Volume:
```
bash-3.2$ docker volume create mydockervolume
mydockervolume
```

##### List a Docker Volume:
```
bash-3.2$ docker volume ls
local               mydockervolume
```

##### Delete a Docker Volume:
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

##### Attach a Docker Volume:
```
```

##### Share a Docker Volume:
```
```

##### Bind Mounts
```
```
