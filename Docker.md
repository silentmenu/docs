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

### Simple Node

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

## Docker with Redis

### Docker Compose

* Separate CLI tool that gets installed with Docker
* Used to start up multiple containers at the same time
* Automates some of the long-winded arguments we were passing to docker run
* File name is docker-compose.yml
* Docker automatically sets up network between multiple containers defined in a docker compose file

```javascript
docker-compose up

// rebuild image of container defined in docker compose file
docker build . 
docker run myimage
=> docker-compose up --build

// start group of containers in background
docker-compose up -d

// stop all containers of the compose
docker-compose down

// list all running containers of the current compose file
docker-compose ps
```

### Handling Crashes

Exit codes 0 - We exited and everything is OK

Exit codes 1,2,3 etc. - We exited because something went wrong

####  Restart Policies

* "no" - Never attempt to restart this container if it stops or crashes. mind the quotes.
* always - If this container stops for any reason, always restart it
* on-failure - Only restart the container if it stops with an error code
* unless-stopped - Always restart it unless we the developers forcibly stop it

We normally use always when our container is a web server and we want to ensure it is always available to customers. on-failure is generally used for worker processes, like processing some file and exiting it.



```dockerfile
# dockerfile

FROM node:alpine

WORKDIR '/app'

COPY package.json package-lock.json* ./
RUN npm install
COPY . .

CMD ["npm", "start"]
```



```dockerfile
# docker-compose
version: "3"

services:
  redis-server:
    image: "redis"

  node-app:
    restart: always
    build: .
    ports:
      - "4001:8081"
```

```javascript
# index.js
const express = require("express");
const redis = require("redis");
const process = require("process");

const app = express();
const client = redis.createClient({
  host: "redis-server",
  port: 6379
});
client.set("visits", 0);

app.get("/", (req, res) => {
  client.get("visits", (err, visits) => {
    res.send("Number of visits: " + visits);
    client.set("visits", parseInt(visits) + 1);
  });
});

app.get("/crash", (req, res) => {
  process.exit(0);
});

app.listen(8081, () => {
  console.log("app listening on port 8081");
});

```

```json
#package.json

{
  "dependencies": {
    "express": "*",
    "redis": "2.8.0"
  },
  "scripts": {
    "start": "node index.js"
  }
}
```

## React

To run a custom docker file: ```docker build -f Dockerfile.dev .```

### Docker Volumes

```
docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app <image_id>
```

* -v $(pwd):/app => map /app in the container to the current folder
* -v /app/node_modules => dont map the node_modules folder in the container though

You can run test on an existing container ```docker exec -it <container_id> npm run test```

When we docker attach, we always attach is to the primary process.

```dockerfile
# Dockerfile.dev

FROM node:alpine

WORKDIR /app

COPY package.json .
RUN npm install

COPY . .
CMD ["npm", "run", "start"]	
```

```dockerfile
# docker-compose.yml

version: "3"

services:
  web:
    environment:
      - CHOKIDAR_USEPOLLING=true
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - /app/node_modules
      - .:/app
```

#### Multi-step builds

```dockerfile
# Dockerfile (production, multi-step build)
FROM node:alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
RUN npm run build

FROM nginx
COPY --from=builder /app/build /usr/share/nginx/html
```











