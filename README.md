# kiada

Sample App from Kubernetes In Action 2nd Edition

## Ch2 - Docker

https://livebook.manning.com/book/kubernetes-in-action-second-edition/chapter-2/

#### build local Docker image

$ docker build -t kiada:latest .

$ docker images

#### view layers

docker history kiada:latest

#### run in local container

$ docker run --name kiada-container -p 1234:8080 -d kiada

$ docker ps

#### run the app

$ curl localhost:1234

#### get additional info about the container

$ docker inspect kiada-container

#### inspect app logs

$ docker logs kiada-container

#### tag the image for Dockerhub

$ docker tag kiada thedubshepherd/kiada:0.1

$ docker images

#### push the image

$ docker push thedubshepherd/kiada:0.1

#### to run on any host - pull from Dockerhub

$ docker run -p 1234:8080 -d thedubshepherd/kiada:0.1

#### stop container

$ docker stop kiada-container

#### delete container

$ docker rm kiada-container

#### remove image

$ docker rmi kiada:latest

#### run a shell inside a running container and list running processes in container

$ docker exec -it kiada-container bash
root@44d76963e8e1:/# ps aux

#### enter Docker desktop VM

$ docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine chroot /host

#### limit container to only use cores one and two

$ docker run --cpuset-cpus="1,2" ...

#### limit container to use only half of a CPU core

$ docker run --cpus="0.5" ...

#### set the maximum memory size available in the container

$ docker run --memory="100m" ...

## Ch3 - Kubernetes

https://livebook.manning.com/book/kubernetes-in-action-second-edition/chapter-3/

Download and install the Google Cloud SDK, which includes the gcloud tool.

$ gcloud config set compute/zone asia-southeast1-a

$ gcloud config list

#### create a 3 node cluster

$ gcloud container clusters create kiada --num-nodes 3

$ gcloud compute instances list

#### scale number of nodes

$ gcloud container clusters resize kiada --num-nodes 0

#### log into a node

$ gcloud compute ssh gke-kiada-default-pool-c2e550a5-bdrj

#### deploy docker image to k8s cluster

$ kubectl create deployment kiada --image=thedubshepherd/kiada:0.1

#### create a service to expose the app externally

$ kubectl expose deployment kiada --type=LoadBalancer --port 8080

#### list services

$ kubectl get svc

$ kubectl get svc kiada

NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE
kiada LoadBalancer 10.19.243.17 35.246.179.22 8080:30838/TCP 82s

Note the external IP - hit the app

$ curl 35.246.179.22:8080

Kiada version 0.1. Request processed by "kiada-9d785b578-p449x". Client IP: ::ffff:1.2.3.4

#### scale deployment

$ kubectl scale deployment kiada --replicas=3

#### see results of scale-out

$ kubectl get deploy

$ kubectl get pods

$ kubectl get pods -o wide

## Ch4 - Kubernetes API Objects

https://livebook.manning.com/book/kubernetes-in-action-second-edition/chapter-4/

$ kubectl get nodes

$ kubectl get node kind-control-plane -o yaml

$ kubectl get node kind-control-plane -o json

$ kubectl proxy

$ kubectl explain nodes

$ kubectl explain node.spec

$ kubectl describe node kind-worker-2

$ kubectl get ev

$ kubectl get ev -o wide

$ kubectl get ev --field-selector type=Warning

## Ch5 - Pods

https://livebook.manning.com/book/kubernetes-in-action-second-edition/chapter-5/

Tip: create yaml template from dry-run:

$ kubectl run kiada --image=luksa/kiada:0.1 --dry-run=client -o yaml > mypod.yaml

Creating objects by applying the manifest file to the cluster:

$ kubectl apply -f pod.kiada.yaml

Take a look at the yaml following object creation:

$ kubectl get po kiada -o yaml

Check the status of the pod:

$ kubectl get pod kiada

$ kubectl describe pod kiada

$ kubectl get events

Get the pod's IP address:

$ kubectl get pod kiada -o wide

The Kubernetes network model dictates that each pod is accessible from any other pod and that each node can reach any pod on any node in the cluster. To test the app, ssh to any node and make a request:

$ gcloud compute ssh gke-kiada-default-pool-c2e550a5-cn0j

> curl 10.112.5.4:8080

Alternatively, create a one-off pod and call the app from there.
Useful for testing pod-to-pod connectivity.

$ kubectl run --image=curlimages/curl -it --restart=Never --rm client-pod curl 10.112.5.4:8080

Connecting to pods via kubectl port forwarding:

$ kubectl port-forward kiada 8080

From another terminal:

$ curl localhost:8080

See logs:

$ kubectl logs kiada

$ kubectl logs kiada -f

$ kubectl logs kiada --timestamps=true

$ kubectl logs kiada --since=2m

$ kubectl logs kiada --since-time=2020-02-01T09:50:00Z

$ kubectl logs kiada --tail=10

Copy files between local computer and k8s containers:

$ kubectl cp kiada:html/index.html /tmp/index.html

$ kubectl cp /tmp/index.html kiada:html/

Invoke a single command in a container:

$ kubectl exec kiada -- ps aux

$ kubectl exec kiada -- curl -s localhost:8080

Run a shell in a container:

$ kubectl exec -it kiada -- bash

Attach to a container:

$ kubectl attach kiada

Attach and pass stdin to the container:

$ kubectl attach -i kiada-stdin

Multi-container pods:

$ kubectl port-forward kiada-ssl 8080 8443 9901

Must specify container name when requesting logs:

$ kubectl logs kiada-ssl -c kiada

$ kubectl logs kiada-ssl -c envoy

$ kubectl logs kiada-ssl --all-containers

Similarly for exec:

$ kubectl exec -it kiada-ssl -c envoy -- bash

Delete a single pod by name:

$ kubectl delete po kiada

Delete multiple pods:

$ kubectl delete po kiada-init kiada-init-slow

Deleting objects by specifying the manifest file:

$ kubectl delete -f pod.kiada-ssl.yaml

Deleting objects from multiple manifest files:

$ kubectl delete -f pod.kiada.yaml,pod.kiada-ssl.yaml

Delete all pods:

$ kubectl delete po --all

Pods created by a Deployment will be restarted by k8s.
Delete all objects:

$ kubectl delete all --all

## Ch6 - Managing the lifecycle of the Pod’s containers

https://livebook.manning.com/book/kubernetes-in-action-second-edition/chapter-6/

Deploy a pod:

$ kubectl apply -f pod.kiada.yaml

Display pod's phase:

$ kubectl get po kiada -o yaml | grep phase

$ kubectl describe po kiada

$ kubectl get po kiada -o json | jq .status.conditions

$ kubectl describe po kiada

$ kubectl get po kiada -o json | jq .status.containerStatuses
