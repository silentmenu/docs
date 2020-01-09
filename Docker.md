# Docker

**Docker Ecosystem**

* Docker client
* Docker server
* Docker machine
* Docker images
* Docker hub
* Docker compose

**Image** - single file with all dependencies and config required to run a program. Eg: Redis

**Container** - Instance of an image. Runs a program. Has its own isolated set of hardware resources.

**Docker Client** - Docker CLI. Tool that we use to issue commands

**Docker Server** - Docker Daemon. Tool responsible for creating images, running containers etc..

```javascript
docker version

docker run hello-world

// image list in local cache
docker image ls
```

*Namespacing* - Isolating resources per process (or group of processes). Processes, Hard drive, network, users, hostnames, Inter process communication

*Control Groups (cgroups)* - Limit amount of resources per process. Memory, CPU usage, HD I/O, Network bandwidth

*Image* - File system snapshot (files) + Startup command

```javascript
// Create and run a container from an image
docker run <image name>
docker run hello-world

// overriding default command
docker run <image name> command
docker run busybox echo hi there
docker run busybox ls
docker run busybox ping google.com

// List all running containers
docker ps
docker ps --all
```

```javascript
// docker run = docker create + docker start
docker create <image name>
docker start <container id>
    
docker create hello-world
docker start -a 531...

// delete all stopped containers
docker system prune

// log of container
docker logs <container id>
    
// stop container (waits for ~10 seconds to shutdown gracefully)
docker stop <container id>
    
// kill a contaner
docker kill <container id>
```

```javascript
// additional commands inside container
docker exec -it <container id> <command>
// -i = allow us to input to container
// -t = show text pretty
    
// get shell access to the container
docker exec -it <container id> sh
docker run -it busybox sh
```

## Images

1. Create Dockerfile, a plain text file with a couple of configuration options in it. It will define how the container behaves, what files or programs does it contain and what process will start when it runs.
2. Use Docker Client and sent to Docker Server (```docker build .```)
3. Docker Server will create the container from the image.

### Creating Dockerfile

1. Specify a base image
2. Run some commands to install additional programs
3. Specify a command to run on container startup

```dockerfile
# Use an existing Docker image as base
FROM alpine

# Download and install a dependency
RUN apk add --update redis

# Tell the image what to do when the container starts
CMD ["redis-server"]
```

```
docker build .
docker run <container id>
```

Docker will try to use cache as much as possible when building image.

#### Tagging an Image

```javascript
docker build -t dockerid/repo:version
docker run dockerid/redis
```

### Simpleweb

```Dockerfile
FROM node:13.6-alpine3.11

WORKDIR /usr/app

COPY ./ ./

RUN npm install

CMD ["npm", "start"]
```

```
docker build -t silentmenu/simpleweb .
docker run -p 80:8080 silentmenu/simpleweb
```

* You cannot expose ports in Dockerfile. It's strictly a runtime thing.
* In dockertoolbox, use IP in browser instead of localhost

