# kiada

Sample App from Kubernetes In Action 2nd Edition

## Docker

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

## Google Kubernetes Engine

Download and install the Google Cloud SDK, which includes the gcloud tool.

$ gcloud config set compute/zone asia-southeast1-a
$ gcloud config list

### create a 3 node cluster

$ gcloud container clusters create kiada --num-nodes 3

$ gcloud compute instances list

### scale number of nodes

$ gcloud container clusters resize kiada --num-nodes 0

### log into a node

$ gcloud compute ssh gke-kiada-default-pool-c2e550a5-bdrj

### deploy docker image to k8s cluster

$ kubectl create deployment kiada --image=thedubshepherd/kiada:0.1

### create a service to expose the app externally

$ kubectl expose deployment kiada --type=LoadBalancer --port 8080

### scale deployment

$ kubectl scale deployment kiada --replicas=3
