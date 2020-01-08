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

**Docker Server** - Docker Daemon. Tool responsible for creating images, running containers etc.

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

