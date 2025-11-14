---
weight: 400
title: "Containers"
description: "How to use Podman and Docker containers"
icon: "article"
date: "2024-08-13T11:23:45+02:00"
lastmod: "2024-08-13T11:23:45+02:00"
draft: false
toc: true
---

# Podman

### Installation

The command below will install podman, buildah, crun, criu, conmon, fuse-overlayfs, slirp4netns

```bash
$ sudo apt install podman
```

If you want to use podman-compose, you must install it:

```bash
$ sudo apt install podman-compose
```

If you want to use skopeo you must install it:

```bash
$ sudo apt install skopeo
```

Skopeo handles image transfers securely.

### Post-Installation Configuration (optional but recommended)

- Enable Rootless Mode: Add your user to necessary groups if not already done:

```bash
sudo usermod --add-subuids 100000-165535 --add-subgids 100000-165535 $USER
newgrp subuid subgid  # Or log out and back in
```

### Equivalences with Docker

- All Podman commands are the same as Docker. Just use the prefix **podman** instead of **docker**:
    - podman build
    - podman run
    - podman-compose up -d
    - etc
- Podman can use both Podman files and Docker files:
    - `Containerfile` and `Dockerfile`
    - `podman-compose.yaml` and `docker-compose.yaml`


### Operations

##### Build an Image

- Create a `Containerfile` or `Dockerfile` and run:

```bash
$ podman build -t my-go-app .               // Creates an image tagged my-go-app 
```

##### Create a Container

Creates but do not run a container:

```bash
podman create my-go-app:latest              // Creates a container with random name using my-go-app image
podman create --name foo my-go-app:latest   // Creates a container named foo using my-go-app image
```

##### Create and Start Container

Creates and run a container:

```bash
podman run -d --rm -p 8080:8080 --name foo my-go-app:latest
podman run -d --rm -p 8080:8080 my-go-app
```

##### Flags for Create and Run commands

- **-d**: detatch
- **--rm**: when container is stopped, it is also removed
- **-p**: port
- **-t**: tag image (when using **--name** also, no need for **-t**)
- **--name**: name image


##### Start a Container

Start an already created container. Most likely the `Containerfile` or `Dockerfile` will need some initialization data, like port number, etc:

```bash
podman start container-name
```

##### Stop a Container

Stop a running container. If the container was started with the flag **--rm**, the container will be deleted as well:

```bash
podman stop container-name
```

##### Remove a Container

```bash
podman rm container-name
```

##### Remove an image

```bash
podman rmi my-go-app
```

##### Remove all dangling images

```
podman images prune -f
```



### Buildah

You can also build images and create containers using **Buildah**. Instead of a `Dockerfile` file, Buildah uses a `Containerfile` file, but script is the same.

##### Build image

```bash
$ buildah build --tag my-image
$ buildah images
```

##### Start a container

The `buildah from` command can be used to start a container that uses the image:

```bash
$ buildah from my-image
$ buildah containers
```

##### Remove a container

```bash
buildah rm my-image-working-container
```

##### Remove image

```bash
buildah rmi my-image
```

or

```bash
buildah rmi localhost/my-image:latest
```

### Further operations

For further operations, check the Docker operations below:

- [Creating a nginx container](#creating-a-nginx-container)
- [Dockerfile: custom image](#dockerfile--custom-image)
- [Volumes](#volumes-1)
- [Docker Compose](#docker-compose--multi-container-applications)



# Docker

### Installation

You can follow the Docker installation instructions for the following Linux distributions:

- **Debian**: [https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/)
- **Ubuntu**: [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
- **Centos**: [https://docs.docker.com/engine/install/centos/](https://docs.docker.com/engine/install/centos/)
- **Fedora**: [https://docs.docker.com/engine/install/fedora/](https://docs.docker.com/engine/install/fedora/)


### Post installation

After installation, you have to make the user member of group `docker` :

``` 
sudo usermod -aG docker $USER
newgrp docker
```

### Commands

#### General

* **docker version**: Check docker version installed
* **docker info**: Info about the docker environment
* **docker login -u "username"**: Access your Docker Hub repository

#### Images

* **docker images**: List all available images in our host
* **docker pull [image]**: Pull the image from docker hub to our host
* **docker run -d [image]**: Pull and start a new container based on the image (Pull only if image not present in local)
* **docker rmi [image]**: Remove the image from our host
* **docker rmi -f $(docker images -a -q)**: Remove all images

#### Containers

* **docker ps**: List all running containers
* **docker ps -a**: List all containers, running or not
* **docker stop [container]**: Stop a running container
* **docker start [container]**: Start a stopped container
* **docker rm [container]**: Remove a non running container 
* **docker rm [container] -f**: Remove a container, running or not 
* **docker kill $(docker ps -q)** Stop all running containers
* **docker rm $(docker ps -aq)**: Remove all non running containers
* **docker rm $(docker ps -aq) -f**: Remove all containers, running or not
* **docker exec -it [container] [BASH]**: Access container file system command prompt. [BASH] can be `/bin/bash` or `/bin/sh`
* **docker create nginx top**: Create a writeable container layer over the specified image and prepares it for running the specified command.
* **docker logs [container]**: Fetch the logs of the container.

#### Volumes
- **docker volume ls**: List all volumes
- **docker volume rm [volume]**: Remove volume [volume]
- **docker volume prune**: Remove all volumes


#### Cleaning the system

- **docker system df**: Show information on images, containers, volumes and build cache size
- **docker buildx prune -f**: Remove all cache
- **docker builder prune**: Remove all dangling build cache
- **docker system prune -a**: Delete all images, containers and cache


#### Operations

##### Build Image

- Create a Dockerfile and run:

```bash
$ docker build -t my-go-app .                               // Creates an image tagged my-go-app
```

##### Run Container

```bash
docker run -d --rm -p 8080:8080  my-go-app                  // Creates and run a container with random name using my-go-app image
docker run -d --rm -p 8080:8080  --name foo my-go-app       // Creates and run a container named foo using my-go-app image
```


## Creating a nginx container

`docker run -d -p 8080:80 --name mynginx nginx` 

The above command does the following:

1. Create a **nginx** container in the background called **mynginx** by using the image **nginx**. If the image **nginx** is not already in our system, it will be downloaded automatically from Docker Hub.
2. Use port **8080** to access the website

To browse, visit **localhost:8080** on a web browser

To access the container's shell, issue:

`docker exec -it mynginx bash` or `sh`

Pages are served from `/usr/share/nginx/html` 

### Finding the IP of a container
`docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id`


## Dockerfile : custom image

It is possible to build a custom image by using an image, copying the project to it and adding some configuration.

For that you need a **Dockerfile** with some commands:

### Static Site

#### Normal build

Providing we have these files in the directory where we are going to issue the build command:

* about.html
* contact.html
* index.html
* services.html

We create a file called `Dockerfile` and edit like this: 

```dockerfile 
FROM nginx:stable-alpine-slim
WORKDIR /usr/share/nginx/html
COPY . .
```
This dockerfile above will download a very tiny linux OS with nginx already installed and configured. We then set the default working directory of the image to `usr/share/nginx/html`. Lastly, we copy all files in our local directory to the image working directory, namely, all html files to the default nginx serving directory.




#### Multi-stage build

Normal builds generate very big image sizes. To reduce the image size to a minimun, Multi-stage builds come to the rescue. Using the previous example:

```dockerfile
# Stage 1: Build stage to collect static files
FROM alpine:latest AS build-stage
WORKDIR /app
COPY . ./static/

# Stage 2: Serve static files with nginx:alpine-slim
FROM nginx:alpine-slim
COPY --from=build-stage /app/static /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Dynamic Site

The examples below shows how to create a Dockerfile to build a Golang app in a Multi-stage fashion. The first Dockerfile is a normal build and the second Dockerfile is a Multi-stage build.

#### Normal build

```dockerfile
FROM golang:1.22.5

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . ./

RUN GO111MODULE=on CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -mod=readonly -v -o main . 

COPY /static ./static/

CMD ["/main"]
```

#### Multi-stage build

```dockerfile
FROM golang:1.22.5 AS build-stage

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . ./

RUN GO111MODULE=on CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -mod=readonly -v -o main . 

# Deploy the application binary into a lean image
FROM gcr.io/distroless/static
COPY --from=build-stage /app /

COPY /static ./static/

CMD ["/main"]
```

Or a slightly different approach:

```dockerfile
FROM golang:1.25.3-alpine AS build     
WORKDIR /src
COPY go.mod go.sum* ./
RUN go mod download
COPY . .               
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags="-w -s" -o /bin/hello ./main.go

FROM scratch
COPY --from=build /bin/hello /bin/hello
COPY --from=build /src/static /static

EXPOSE 8080
CMD ["/bin/hello"]
```

{{< alert context="info" text="For the smallest built container, use one of the following in the last stage build: `FROM scratch` or `FROM gcr.io/distroless/static`" />}}



### Building the image

In the same directory of the Dockerfile, run:

```bash
$ docker build -t nginx-mywebsite . 
```


### Running the container

To build the image from the Dockerfile, issue: 

```bash
docker run -d --rm -p 8080:8080 -t my-go-app                // tagged my-go-app
docker run -d --rm -p 8080:8080  my-go-app --name pepita    // tagged my-go-app and named pepita
```

- **-d**: detatch
- **--rm**: when container is stopped, it is also removed
- **-p**: port
- **-t**: tag image (when using **--name** also, no need for **-t**)
- **--name**: name image


### Pushing our new created image to Docker Hub

``` 
docker tag nginx-mywebsite nginx-mywebsite:v1
docker tag nginx-mywebsite <dockerhubusername>/<nginx-mywebsite>|<fooimage>
docker login
docker push <dockerhubusername>/nginx-mywebsite
docker logout
```

**Note**: It takes 24-48 hours for Docker Hub to index the image


## Volumes

### Volumes for development use

* Dockerfile:
```dockerfile
FROM php:7.2-apache
COPY src/ /var/www/html/
EXPOSE 80
```
**home/user/mywebsite:~$**: `docker build -t myimage .`

The above command does the following:
* Build an image of an apache server with php
* Copy the content of `/home/user/mywebsite/src/` into the the apache `/var/www/html/` serving directory
* Expose port 80 so it can be reached

Next we run:

`docker run -d -p 8080:80 -v /home/user/mywebsite/src/:/var/www/html --name mywebsite myimage` 

The above command does the following:

* Create a **nginx** container in the background called **mywebsite**
* Use port 8080 to access the website
* Mount the container directory `/var/www/html/` on to local directory `/home/user/mywebsite/src/` where the source code of the site is held. 
* We can now create and edit **index.php** and other files and directories directly in `/home/user/mywebsite/src/`, and it will update automatically.
* We can visit the website on **localhost:8080**

### Delete unused or lost volumes
```
$ docker volume rm $(docker volume ls -qf dangling=true)
$ docker volume ls -qf dangling=true | xargs -r docker volume rm
```

## Docker Compose : multi-container applications

[Docker Compose](https://docs.docker.com/compose/) is a tool for defining and running multi-container applications.

Compose simplifies the control of your entire application stack, making it easy to manage services, networks, and volumes in a single, comprehensible YAML configuration file. Then, with a single command, you create and start all the services from your configuration file.

### Creating the docker compose file

{{< alert context="info" text="Indent properly the code on **yaml** files. Otherwise it won't work." />}}

- Create a file called `docker-compose.yml` and add this content:

```yaml

services:
	golang-app:
		image: go-app
		ports:
			- 8080:8080
```

- Run the service:

```
$ docker-compose up
```

{{< alert text="You can use [composerize.com](https://www.composerize.com/) to automatically generate a docker compose file" />}}

### Commands
```
docker-compose up -d
docker-compose down
docker-compose down --volumes
```

### Scaling

Scaling services with *docker-compose* is as simple as:

```
docker-compose up -d --scale myservice=3
```

# Tips and Hints

* If there's a need to run Docker containers in production without Kubernetes, use the `--init` flag upon `docker run` . This injects a `PID 1` process, which handles its terminated children correctly, into the container to run.

* There are four important files that acts as contracts between deverlopers and sys admins:
  + **Dockerfile**: To build or application image
  + **docker-compose.yml**: To execute our application
  + **Dockerfile.test**: To build our application test image
  + **docker-test.yml**: Test our application

* You can copy a docker image to a pendrive:
	```bash
	docker save -o /path/to/pendrive/image_name.tar image_name:tag
	```
* You can import a saved docker image from a pendrive:
	```bash
	docker load -i /path/to/pendrive/image_name.tar
	```



# Create development environment in container

In this example will create a container ready to develop in Go and Hugo. The container will install Git, NeoVim, Tmux, Curl, Go, Hugo and some other applications.

1. Prepare the local computer
2. Create Dockerfile
3. Create docker-compose.yaml
4. Spin the container


### Prepare the local computer

Check that `~/.gitconfig` is not a directory but a file properly configured

```bash
[user]
	email = myemail@gmail.com
```

### Create a directory structure for the volumes
```bash
mkdir -p ~/Documents/code/docker/hugo-go-dev
cd ~/Documents/code/docker/hugo-go-dev
mkdir dev-workspace
```

From now on all commands below are issued from `~/Documents/code/docker/hugo-go-dev/`

### Create Dockerfile

```
$ nvim Dockerfile
```

```dockerfile
# ============ Builder Stage ============
FROM ubuntu:22.04 AS builder
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y wget curl && rm -rf /var/lib/apt/lists/*

# Hugo v0.152.1 Extended
RUN wget -O hugo.tar.gz \
    https://github.com/gohugoio/hugo/releases/download/v0.152.1/hugo_extended_0.152.1_Linux-64bit.tar.gz \
 && tar -xzf hugo.tar.gz -C /tmp && \
    find /tmp -name hugo -exec mv {} /usr/local/bin/hugo \; && \
    rm -rf /tmp/*

# ============ Runtime Stage (Alpine) ============
FROM alpine:3.20

# Enable community repo
RUN echo "http://dl-cdn.alpinelinux.org/alpine/v3.20/main"     >> /etc/apk/repositories && \
    echo "http://dl-cdn.alpinelinux.org/alpine/v3.20/community" >> /etc/apk/repositories && \
    apk update

# Install REAL Alpine packages only
RUN apk add --no-cache \
    go \
    git \
    neovim \
    bash \
    tmux \
    gcompat \
    && rm -rf /var/cache/apk/*

# Copy Hugo binary
COPY --from=builder /usr/local/bin/hugo /usr/local/bin/hugo

# User
ARG USERNAME=cheocon
ARG USER_UID=1000
ARG USER_GID=1000
RUN addgroup -g ${USER_GID} ${USERNAME} && \
    adduser -D -u ${USER_UID} -G ${USERNAME} -h /home/${USERNAME} ${USERNAME} && \
    mkdir -p /home/${USERNAME}/workspace && \
    chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}

WORKDIR /home/${USERNAME}/workspace
USER ${USERNAME}

EXPOSE 1313
CMD ["/bin/bash"]
```

### Create docker-compose.yaml

```yml
version: '3.9'

services:
  dev:
    build:
      context: .
      dockerfile: Dockerfile
    image: hugo-go-dev:latest
    container_name: cheocon-dev
    user: "1000:1000"
    working_dir: /home/cheocon/workspace
    volumes:
      # Persistent workspace
      - ./workspace:/home/cheocon/workspace
      # X11 GUI
      - /tmp/.X11-unix:/tmp/.X11-unix:rw

      # Git config — only if file exists
      - ${HOME:-~}/.gitconfig:/home/cheocon/.gitconfig:ro
      # SSH keys — only if directory exists
      - ${HOME:-~}/.ssh:/home/cheocon/.ssh:ro

    environment:
      - DISPLAY=${DISPLAY:-host.docker.internal:0}
      - XDG_RUNTIME_DIR=/tmp
    ports:
      - "1313:1313"
    devices:
      - /dev/dri:/dev/dri
    tty: true
    stdin_open: true
    restart: unless-stopped
    network_mode: bridge
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

### Build the container, run it and enter

```bash
docker build -t hugo-go-dev .
docker-compose up -d
docker-compose exec dev bash
```

- If docker build fails, run: `docker build --no-cache -t hugo-go-dev .`
- In order to serve pages with Hugo, you must run:

```bash
$ hugo server --bind 0.0.0.0 --port 1313 --watch
```

# Podman

### Installation

The command below will install podman, buildah, crun, criu, conmon, fuse-overlayfs, slirp4netns

```bash
$ sudo apt install podman
```

If you want to use podman-compose, you must install it:

```bash
$ sudo apt install podman-compose
```

If you want to use skopeo you must install it:

```bash
$ sudo apt install skopeo
```

Skopeo handles image transfers securely.

### Post-Installation Configuration (optional but recommended)

- Enable Rootless Mode: Add your user to necessary groups if not already done:

```bash
sudo usermod --add-subuids 100000-165535 --add-subgids 100000-165535 $USER
newgrp subuid subgid  # Or log out and back in
```
