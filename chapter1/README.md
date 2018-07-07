# kubernetes

### Install Docker

```
$ sudo apt-get remove docker docker-engine docker.io

$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
 
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

$ sudo apt-key fingerprint 0EBFCD88

$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
   
$ sudo apt-get update

$ sudo apt-get install docker-ce
```

### Run a Hello World Container

```
$ sudo docker run busybox echo "Hello world"
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
07a152489297: Pull complete 
Digest: sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
Status: Downloaded newer image for busybox:latest
Hello world
```

### Node.js app

```
$ sudo mkdir ~/kubernetes

$ vi ~/kubernetes/app.js

$ sudo su
```

A simple node.js app

```
# cat app.js 
const http = require('http');
const os = require('os');
console.log("Kubia server starting...");
var handler = function(request, response) {
  console.log("Received request from " + request.connection.remoteAddress);
  response.writeHead(200);
  response.end("You've hit " + os.hostname() + "\n");
};
var www = http.createServer(handler);
www.listen(8080);
```

Build a container image using Dockerfile

```
# cat Dockerfile
FROM node:7
        ADD app.js /app.js
        ENTRYPOINT ["node", "app.js"]
```

```
# docker build -t kubia .
Sending build context to Docker daemon  3.072kB
Step 1/3 : FROM node:7
7: Pulling from library/node
ad74af05f5a2: Pull complete 
2b032b8bbe8b: Pull complete 
a9a5b35f6ead: Pull complete 
3245b5a1c52c: Pull complete 
afa075743392: Pull complete 
9fb9f21641cd: Pull complete 
3f40ad2666bc: Pull complete 
49c0ed396b49: Pull complete 
Digest: sha256:af5c2c6ac8bc3fa372ac031ef60c45a285eeba7bce9ee9ed66dad3a01e29ab8d
Status: Downloaded newer image for node:7
 ---> d9aed20b68a4
Step 2/3 : ADD app.js /app.js
 ---> 78c566ec0d78
Step 3/3 : ENTRYPOINT ["node", "app.js"]
 ---> Running in 8c512c52082a
Removing intermediate container 8c512c52082a
 ---> 033b6159e4a1
Successfully built 033b6159e4a1
Successfully tagged kubia:latest
```

List locally stored images

```
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
kubia               latest              033b6159e4a1        About a minute ago   660MB
busybox             latest              8c811b4aec35        6 weeks ago          1.15MB
node                7                   d9aed20b68a4        10 months ago        660MB
```

Run a container

```
# docker run --name=kubia-container -p 8080:8080 -d kubia
34256bef872ad8e0c1952c5c3f7ed75acc70b311305ffc3b9b6381631400da4c
```

Listing containers

```
# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
34256bef872a        kubia               "node app.js"       54 seconds ago      Up 53 seconds       0.0.0.0:8080->8080/tcp   kubia-container
```

Additional information about container

```
# docker inspect 34256bef872a
```

Getting inside the container

```
$ docker exec -it kubia-container bash
```

Stop the container

```
# docker stop 34256bef872a
```

Deleting the container

```
# docker rm 34256bef872a
```

Push the image to Docker Hub

```
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
kubia               latest              033b6159e4a1        11 minutes ago      660MB
busybox             latest              8c811b4aec35        6 weeks ago         1.15MB
node                7                   d9aed20b68a4        10 months ago       660MB

# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: m4nd4r
Password: 
Login Succeeded

# docker tag kubia m4nd4r/kubia

# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
kubia               latest              033b6159e4a1        12 minutes ago      660MB
m4nd4r/kubia        latest              033b6159e4a1        12 minutes ago      660MB

# docker push m4nd4r/kubia
The push refers to repository [docker.io/m4nd4r/kubia]
e58cfa5e286d: Pushed 
ab90d83fa34a: Mounted from library/node 
8ee318e54723: Mounted from library/node 
e6695624484e: Mounted from library/node 
da59b99bbd3b: Mounted from library/node 
5616a6292c16: Mounted from library/node 
f3ed6cb59ab0: Mounted from library/node 
654f45ecb7e3: Mounted from library/node 
2c40c66f7667: Mounted from library/node 
latest: digest: sha256:95bfb737a928a4aa369402668950a571c26fb10ac2df3c7988eab017dcefc1d6 size: 2213
```

Install Kubernetes client - kubectl

```
# Create environment variable for correct distribution
export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)"

# Add the Cloud SDK distribution URI as a package source
echo "deb http://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

# Import the Google Cloud Platform public key
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

# Update the package list and install the Cloud SDK
sudo apt-get update && sudo apt-get install google-cloud-sdk
```

```
# sudo apt-get install kubectl
```

Create a project

```
$ gcloud init
```

