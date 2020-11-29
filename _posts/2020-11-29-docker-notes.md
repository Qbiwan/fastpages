# Docker Notes 

Source: [Docker Tutorial for Beginners - Full Course in 3 Hours by  TechWorld with Nana
](https://www.youtube.com/watch?v=3c-iBn73dDE)

## 1. How to install docker on Ubuntu

- Update Software Repositories
- Install Docker on Ubuntu 18.04
- If old version of Docker needs to be removed first, refer to this [Guide]((https://phoenixnap.com/kb/how-to-install-docker-on-ubuntu-18-04))

```
sudo apt-get update
sudo apt install docker.io
```
Reference:
  - https://phoenixnap.com/kb/how-to-install-docker-on-ubuntu-18-04

### Permission denied while trying to connect to the Docker daemon socket at unix:

After you installed Docker, you could have the error message of `Permission Denied`. You might need to create a docker group if you dont want to use `sudo` everytime.
```
(base) qbiwan@workspace:~$ docker ps
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.40/containers/json: dial unix /var/run/docker.sock: connect: permission denied
```

Use the  following command to create a docker group since you are not the root user. Test whether it works by running `docker run hello-world`

```
sudo groupadd docker
sudo usermod -aG docker ${USER} 
# logout of ubuntu and login again
newgrp docker
docker run hello-world
```
Reference: 
- https://docs.docker.com/engine/install/linux-postinstall/  
- https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket




## 2. How to download a Docker image

- Go to [Docker Hub](https://hub.docker.com/search?q=&type=image)
- search for the image you want e.g. Postgres 
- Go to **Docker Pull Command** and  `Copy and paste to pull this image` 
- Copy the command and add your desired version number e.g. `docker pull postgres:latest` 
- run the command on your local terminal

```
(base) qbiwan@workspace:~$ docker pull postgres:latest
latest: Pulling from library/postgres
Digest: sha256:839d6212e
Status: Image is up to date for postgres:latest
docker.io/library/postgres:latest
```

## 3. Docker images

Run the command `docker images` on your terminal, and see all the images in your local computer.

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
postgres            9.6                 bd02fe5289fa        4 days ago          200MB
postgres            latest              f51c55ac75ed        4 days ago          314MB
redis               latest              74d107221092        4 days ago          104MB
hello-world         latest              bf756fb1ae65        10 months ago   
```

## 4. Build a container from one of these images

Let's say you want to build a redis container. You could do it in *normal* or *detached* mode
```
docker run redis  # docker run == docker pull plus docker start

docker run -d redis  # detached mode
```

Check if the container is running by using `docker ps`

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS               NAMES
7e1e07e7cd87        redis               "docker-entrypoint.s…"   About a minute ago   Up About a minute   6379/tcp            agitated_jennings

```

To stop the container, we can use its `CONTAINER ID` or `NAMES`
```
docker stop 7e1e07e7cd87
```
OR
```
docker stop agitated_jennings

```
To restart the container, use

```
docker start 7e1e07e7cd87
```
#### Normal or detached mode

`docker run redis` will run the container in normal mode. To kill the container just press `ctrl-C`. `\\

`docker run -d redis` will run the container in detached mode i.e. in the background. To kill it you have to use `docker stop 7e1e07e7cd87`

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
9b4920e8ea00        redis               "docker-entrypoint.s…"   4 seconds ago       Up 2 seconds        6379/tcp            angry_stonebraker

```

### Get all docker containers

To get all docker containers, whether they are still running or had been stopped, use the flag `-a`
```
docker ps -a 
```

This gives you the history of all the containers on your local computer that had been started or stopped

```
(base) qbiwan@workspace:~$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS               NAMES
9b4920e8ea00        redis               "docker-entrypoint.s…"   6 minutes ago       Up 2 minutes                   6379/tcp            angry_stonebraker
7e1e07e7cd87        redis               "docker-entrypoint.s…"   16 minutes ago      Exited (0) 13 minutes ago                          agitated_jennings
9754a778aae1        postgres            "docker-entrypoint.s…"   About an hour ago   Exited (1) About an hour ago                       stupefied_mendeleev
1ff1d23ef217        postgres            "docker-entrypoint.s…"   About an hour ago   Exited (1) About an hour ago                       stupefied_austin
71ebc08d4ca1        postgres            "docker-entrypoint.s…"   About an hour ago   Exited (1) About an hour ago                       serene_hertz
a33a2fd1ce1e        postgres            "docker-entrypoint.s…"   About an hour ago   Exited (1) About an hour ago                       festive_shirley
ab457b78f697        postgres            "docker-entrypoint.s…"   About an hour ago   Exited (1) About an hour ago                       thirsty_meninsky
82d5b676655b        postgres            "docker-entrypoint.s…"   About an hour ago   Exited (1) About an hour ago                       jolly_wiles
0fdf29868a6a        postgres            "docker-entrypoint.s…"   About an hour ago   Exited (1) About an hour ago                       wonderful_bassi
7feacb4236c0        postgres            "docker-entrypoint.s…"   2 hours ago         Exited (1) 2 hours ago                             cool_elgamal
ddcd39d2052b        postgres            "docker-entrypoint.s…"   2 hours ago         Exited (1) 2 hours ago                             zen_bell
4ff893b87a4f        postgres:9.6        "docker-entrypoint.s…"   2 hours ago         Exited (1) 2 hours ago                             musing_williamson
5f54933d5d3c        postgres:9.6        "docker-entrypoint.s…"   2 hours ago         Exited (1) 2 hours ago                             priceless_neumann
0a193ea79c45        hello-world         "/hello"                 2 hours ago         Exited (0) 2 hours ago                             vibrant_swanson

```

## 5. Specifying Ports

### Ports

You can map the port of the local computer to the port of the container using the flag -p. Foe example, this command `-p 8080:8080` map port 8080 of your local computer to port 8080 of your container

### Mapping one host port to two different container ports (NO)
### Mapping two host ports to one container port (YES)

<br>

If you tried to map one host port to 2 container ports, you will have a conflict.However, two container ports can be listening on the same port *6379* while your host computer has two different ports.

For example, `redis:latest` and `redis:4.0` can both be listening on port *6379*, but your local ports mapped to them must be different. So you must map port `6000` to `redis:latest` and `6001` to `redis:9.90`

```
(base) qbiwan@workspace:~$ docker run -p6000:6379 -d redis
660ff75d97d07237d90e7c51f4b7bd16e43d594fbf612379bc6ea1651912ad61

(base) qbiwan@workspace:~$ docker run -p6001:6379 -d redis:4.0
c34abecfaf261521a4c7ad5e0b39af63963cc3ad69f1fd300a6a234258b9a919
```
This will not cause a conflict as we can see below

```
(base) qbiwan@workspace:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                    NAMES
c34abecfaf26        redis:4.0           "docker-entrypoint.s…"   11 seconds ago       Up 10 seconds       0.0.0.0:6001->6379/tcp   practical_poitras
660ff75d97d0        redis               "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:6000->6379/tcp   naughty_archimedes
(base) qbiwan@workspace:~$ 


```

## 6. Get docker log file for debugging

To get a docker container's log file, specify the container by its `container id` or `container name`

```
docker logs c34abecfaf26
OR
docker logs practical_poitras

```
this will give you its log file.
```
(base) qbiwan@workspace:~$ docker logs practical_poitras
1:C 23 Nov 14:06:54.196 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 23 Nov 14:06:54.196 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 23 Nov 14:06:54.196 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
1:M 23 Nov 14:06:54.197 * Running mode=standalone, port=6379.
1:M 23 Nov 14:06:54.197 # Server initialized
1:M 23 Nov 14:06:54.197 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
1:M 23 Nov 14:06:54.197 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
1:M 23 Nov 14:06:54.197 * Ready to accept connections

```

## 7. Name the container

You can name your container using the flag `--name` when you use `docker run` to specify a easily-identifieable name, for example, `--name=ml-app`

```
(base) qbiwan@workspace:~$ docker run -d -p6002:6379 --name ml-app redis:4.0
e1cf0aa306db7e4bfec18b8f65fed1dbe8c3650fadea3d14f64c93b176894489
```
You will see this container named accordingly rather than been given a random name such as `practical_poitras` or `naughty_archimedes`
```
(base) qbiwan@workspace:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
e1cf0aa306db        redis:4.0           "docker-entrypoint.s…"   9 seconds ago       Up 7 seconds        0.0.0.0:6002->6379/tcp   ml-app
c34abecfaf26        redis:4.0           "docker-entrypoint.s…"   16 minutes ago      Up 16 minutes       0.0.0.0:6001->6379/tcp   practical_poitras
660ff75d97d0        redis               "docker-entrypoint.s…"   17 minutes ago      Up 17 minutes       0.0.0.0:6000->6379/tcp   naughty_archimedes
(base) qbiwan@workspace:~$ 
```

## 8. Access the terminal inside the container

To access the terminal inside the container we use the flag `-it`, where **it** means**interative terminal**. Below we access bash inside a container named `redis-third` using this command `exec -it` 

To confirm that we successfully accessed the interactive terminal, note that the command promt changed from `(base) qbiwan@workspace:~$` to `root@e1cf0aa306db:/data` in this case.


```
(base) qbiwan@workspace:~$ docker exec -it redis-third bash
root@e1cf0aa306db:/data# 
```
print the current directory we are in, and we can see that we are at `/data`

```
root@e1cf0aa306db:/data# pwd
/data
```
Change to parent directory and we find ourselves at root `/`s
```
root@e1cf0aa306db:/data# cd ..
root@e1cf0aa306db:/# pwd
/
```

Let's list all the directories here

```
root@e1cf0aa306db:/# ls
bin  boot  data  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

```

Type `exit` to get out of the interactive terminal and get back to our local computer
```
root@e1cf0aa306db:/# exit
exit
(base) qbiwan@workspace:~$ 
```


