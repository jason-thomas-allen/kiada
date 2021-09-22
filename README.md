# kiada

Sample App from Kubernetes In Action 2nd Edition

### build local Docker image

$ docker build -t kiada:latest .

$ docker images

### view layers

docker history kiada:latest

### run in local container

$ docker run --name kiada-container -p 1234:8080 -d kiada

$ docker ps

### run the app

$ curl localhost:1234

### get additional info about the container

$ docker inspect kiada-container

### inspect app logs

$ docker logs kiada-container

### tag the image for Dockerhub

$ docker tag kiada thedubshepherd/kiada:0.1

$ docker images

### push the image

$ docker push thedubshepherd/kiada:0.1

### to run on any host - pull from Dockerhub

$ docker run -p 1234:8080 -d thedubshepherd/kiada:0.1

### stop container

$ docker stop kiada-container

### delete container

$ docker rm kiada-container

### remove image

$ docker rmi kiada:latest

### run a shell inside a running container and list running processes in container

$ docker exec -it kiada-container bash
root@44d76963e8e1:/# ps aux

### enter Docker desktop VM

$ docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine chroot /host

### limit container to only use cores one and two

$ docker run --cpuset-cpus="1,2" ...

### limit container to use only half of a CPU core

$ docker run --cpus="0.5" ...

### set the maximum memory size available in the container

$ docker run --memory="100m" ...
