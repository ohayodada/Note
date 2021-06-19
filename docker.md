# Docker
## run docker as non-root user
1. Create the docker group if it doese not exist
```shell
$ sudo groupadd docker
```
2. Add you user to the docker group
```shell
$ sudo usermod -aG docker $USER
```
3. Run the follow commend or Logout and login again and run
```shell
$ newgrp docker
```
4. check if docker can be run without root
```shell
$  docker run hello-world
```