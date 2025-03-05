[Back to NodeJS Notes](index)

# Docker Integration Notes

Docker allows to pack software so it can be run on any hardware.

**Must know docker concepts**:
- DockerFile: Blueprint for building a docker image
- Image: Template for running a docker container
- Container: Running process

Some commands to memorize:

- `docker ps` lists the running containers on my system.
- `docker build -t username/appname:appversion /path/to/Dockerfile` builds the image file and sets as a tag username/appname:appversion
- `docker run -p 5000:8080 ID` maps the container port 8080 to the local machine port 5000.
- `docker exec` for debugging
- `docker image ls` lists downloaded images
- `docker rm $(docker ps -qa)` delete all active containers
- `docker search <package>` searches for available packages on the official images repositories.
- `docker pull <image>` pulls an image from a repository on Docker Hub
- `docker image history <image>` shows all the layers of an image



# Creating an example Dockerfile

`FROM node:12` sets the base image. In our case we choose node version 12 as our base image
`WORKDIR /app` sets the application working directory.
`COPY package*.json ./` copies the package.json file to the current working directory (/app).
`RUN npm install` installs dependencies specified on the package.json file copied before.
`COPY . .` copies all the files on the directory of the Dockerfile to the current working directory (/app)
`COPY src /app/src` copies the contents of the directory src to the directory /app/src on the target
`ENV PORT=8080` creates an environment variable that is exposed to the app.
`EXPOSE 8080` makes the container listen on port 8080.
`CMD [ "npm", "start" ]` tells the container how to run the application. There can only be one CMD command on the Dockerfile.


Every instruction on the Dockerfile is considered to be its own step or layer. Docker tries to cache layers if nothing is changed on the layer. For this reason, we install our dependencies at the beginning of the Dockerfile, so that they're not downloaded every time the cache misses.

There exists a .dockerignore file that works as a .gitignore.

1 process per container



# Some Docker (idk) modules

- [Vite](https://vite.dev)
- [Traefik.io](https://traefik.io)


# Docker Components In Depth

## Docker Containers

The containers are isolated environments for each of your app's components. Containers are:
- Self contained
- Isolated
- Independent
- Portable

To stop a container use the command `docker container stop ID`. Note that it is not necessary to provide the full ID. For example, if the ID is ff77e200fada, you can enter `docker container stop ff77` and it will delete the correct container.

One **best practice** for containers is that each container should do one thing and do it well. Avoid the tendency of having one container doing multiple things.

## Docker Images

A container image is a package that includes all of the files, binaries, librarie and configurations to run the container. The images are:
- Immutable
- Composed of layers

## Registry vs Repository

A registry stores and manages container images.

A repository is a collection of related container images within a registry.

## Docker Compose

With Docker compose you can define all your containers and their configurations in a single YAML file.

Quite often, a compose file references a Dockerfile to build an image to use for a particular service.

Useful commands:
- `docker compose watch`: automatically updates and previews your running Compose services.
- `docker compose up`: reconciles the changes in your file and apply them intellegently
- `docker compose down`: tears down all the containers created with compose.

## Docker Volumes

Volumes is the storage used by containers created and managed by docker.

A volume can be created by using the command `docker volume create`

When you create a volume its stored within a directory on the Docker Host. When you mount the volume to a container, this directory is what's mounted into the container.

The data on the volume is persistent regardless of the state of any container. If the container that the volume was mounted on is removed, the writable layer is destroyed with it.




# Analyzing the docker example project

## compose.yml

This file defines each service. For each service, docker compose creates a container.

The services defined are:

1. Proxy: This service remaps certain routes to the appropiate service. It uses the traefik image version 2.11 which listens at port 80.
2. Backend: This defines the Node.js server service. It builds the backend-dev stage from Dockerfile to use it as image. It also defines some environments variables to be used inside the server. It specifies which directories the docker compose watch command need to synchronize. Finally the labels are some specification for the proxy, it tells docker to listen on localhost/api and route it to port 3000.
3. Client: Defines the React app. It uses the client-dev defined on the Dockerfile as image. It tells docker compose watch which files to synchronize. It routes the localhost to the port 5173.


