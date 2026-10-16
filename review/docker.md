# Table of contents

- [Table of contents](#table-of-contents)
- [Docker Fundamentals](#docker-fundamentals)
  - [Images, Containers, Registries](#images-containers-registries)
  - [Real-World Workflow](#real-world-workflow)
  - [Containers vs Virtual Machines](#containers-vs-virtual-machines)
  - [Commands](#commands)
- [Namespaces and cgroups (high level)](#namespaces-and-cgroups-high-level)
  - [Namespaces](#namespaces)
  - [Control Groups (`cgroups`)](#control-groups-cgroups)
- [Volumes](#volumes)
  - [What it is](#what-it-is)
  - [Key Characteristics of Docker Volumes](#key-characteristics-of-docker-volumes)
  - [Common Docker Volume Commands](#common-docker-volume-commands)
- [Bind Mounts](#bind-mounts)
  - [What it is](#what-it-is-1)
  - [Bind Mount vs Volume](#bind-mount-vs-volume)
- [Networking](#networking)
  - [Bridge Networks](#bridge-networks)
  - [Host Networks](#host-networks)
  - [Overlay Networks](#overlay-networks)
  - [DNS Inside Docker](#dns-inside-docker)
- [Dockerfile](#dockerfile)
  - [What it is](#what-it-is-2)
  - [Core Dockerfile Instructions](#core-dockerfile-instructions)
  - [A Practical Example: `Node.js` App](#a-practical-example-nodejs-app)


# Docker Fundamentals

## Images, Containers, Registries

- A **container** is a lightweight, isolated environment that contains:

    - An application, its dependencies, required libraries, configuration files

    - A container is **a running instance** of an image (`docker run nginx`)

    - Docker finds the `nginx` image, creates a container, starts the container

- An **image** is a read-only template (blueprint) used to create containers

    - Examples: Ubuntu image, Nginx image, MySQL image, Python image

    - It's like `class → object` in OOP

    - Download the Ubuntu image `docker pull ubuntu`, and create a container from it `docker run ubuntu`

- A **registry** is a place where Docker images are stored and shared

    - Think of it as: **GitHub for Docker Images**

    - Common registries: Docker Hub, GitHub Container Registry, Amazon ECR, Azure Container Registry, Google Artifact Registry

## Real-World Workflow

- Step 1: Create an Image

    ```bash
    my-web-app image
    ├── Node.js
    ├── App code
    └── Dependencies
    ```

- Step 2: Push to Registry `developer PC → Docker Hub`

- Step 3: Pull on Server `docker pull my-web-app`

- Step 4: Run Container `docker run my-web-app`

## Containers vs Virtual Machines

- VM

    - A VM includes: app, libraries, OS, virtual hardware

    - Architecture

        ```bash
        Physical Server
        │
        ├── Hypervisor
        │   ├── VM 1 (Windows)
        │   ├── VM 2 (Ubuntu)
        │   └── VM 3 (CentOS)
        ```

    - Advantages: strong isolation, different OS can run on the same host

    - Disadvantages: more CPU and RAM, large disk consumption, slower startup time

- Containers

    - A container includes: app, libraries, dependencies

    - Containers share the host OS kernel

    - Architecture:

        ```bash
        Physical Server
        │
        ├── Docker Engine
        │   ├── Container 1
        │   ├── Container 2
        │   └── Container 3
        ```

    - Advantages: lightweight, starts in milliseconds, fewer resources

    - Disadvantages: all containers share the host kernel, isolation is weaker than a VM

## Commands

```bash
docker version
docker info
docker images
docker ps
docker ps -a
docker run
docker start
docker stop
docker restart
docker rm
docker rmi
```


# Namespaces and cgroups (high level)

- `namespaces` and `cgroups` are two important **Linux kernel features** that help Docker run containers safely and efficiently

- `namespaces` = **Isolation** — control what a container can see

- `cgroups` = **Resource control** — control how much CPU, memory, and other resources a container can use

## Namespaces

- A Linux namespace isolates a particular type of system resource so that processes in one namespace have a different view of that resource from processes in another

- Main `namespace` types

    - PID: a container can see its own process tree

    - Network: containers can have separate network interfaces and IP addresses

    - UTS (Hostname and domain name): a container can have its own hostname

    - Others: mount, IPC, user

- Example: PID namespaces

    - On a Linux host, you might run `ps aux` and you see processes belonging to the host and potentially many apps

    - Inside a container, you might run: `docker exec -it myweb ps aux`

        - Depending on the image, you will generally see only the processes visible within that container's PID namespace

        - The Nginx process might appear as PID 1 inside the container even though it has a different PID on the host

## Control Groups (`cgroups`)

- A Linux kernel feature for organizing processes and controlling or accounting for their resource usage

- `cgroups` answer, "How much of the host's resources can this container use?"

- Example: Limit container memory

    - Suppose your server has 16 GB of RAM, and you want a test web server to use no more than 512 MB of memory

    - Docker configures the container's resource controls through the host's `cgroup` infrastructure


# Volumes

## What it is

- A mechanism to keep data, which is generated and used by containers, when the container is stopped, restarted or deleted

- Decoupling the container's lifecycle from the data's lifecycle

## Key Characteristics of Docker Volumes

- Managed by Docker

    - Volumes are stored in a dedicated directory on the host machine managed entirely by Docker

    - On Linux, this is typically located under `/var/lib/docker/volumes/`

    - Regular host processes outside of Docker shouldn't modify these files

- Easy Sharing: Volumes can be easily shared and safely reused among multiple concurrent containers

- Driver Support: Docker supports volume drivers that allow you to store data on remote cloud providers, encrypted storage, or network file systems (NFS)

## Common Docker Volume Commands

```bash
# create a volume
$ docker volume create my_custom_volume

# list volumes
$ docker volume ls

# inspect a volume
$ docker volume inspect my_custom_volume

# remove an unused volume
$ docker volume rm my_custom_volume

# attach a volume to a container
$ docker run -d --name web_server -v my_custom_volume:/usr/share/nginx/html nginx
```


# Bind Mounts

## What it is

- A storage mechanism allowing you to mount a specific file or directory **from the host machine's filesystem** directly into a running container

## Bind Mount vs Volume

- The primary difference between Volumes and Bind Mounts comes down to **ownership and control** over the host file system

    - Volumes are fully managed by Docker and isolated from the host's direct structure

    - Bind Mounts map a specific, absolute path from the host machine directly into the container

- Comparison

    - Docker

        - Storage Location: managed area inside Docker's internal filesystem (e.g., `/var/lib/docker/volumes/ on Linux`)

        - Handled completely via the Docker CLI and API (e.g., `docker volume create`)

        - Portability: high - independent of the host machine's directory layout or OS structure

    - Bind Mount

        - Storage Location: anywhere on the host machine filesystem, defined by an absolute path

        - Handled manually through the host OS's file manager or terminal

        - Portability: low - dependent on the specific file paths and directory layout of the host machine

- Both mechanisms **bypass the container's temporary writable layer** to persist data


# Networking

## Bridge Networks

- A bridge network is a private, virtual network internal to a single Docker host

- It is the default network type a container gets when you run it without specifying anything else

- How it works

    - Docker creates a software bridge (like a virtual switch) on the host machine
    
    - Every container attached to this bridge gets its own virtual Ethernet interface (`veth`) and an internal IP address

- Isolation

    - Containers on the same bridge can communicate freely with each other
    
    - But they are isolated from containers on other networks and from external traffic (unless ports are explicitly published/forwarded to the host)

- Default vs. Custom Bridges

    - The default `bridge` network DOESN'T support automatic container name resolution (you have to use IP addresses)

    - User-defined custom bridge networks (which you create with `docker network create`) feature built-in automatic service discovery, allowing containers to resolve each other by their container names or aliases

## Host Networks

- With a host network, you remove all network isolation between the container and the Docker host by passing `--network host`

- How it works

    - The container shares the host's network stack directly

    - It does not get its own internal IP address, and any port the container listens on is opened directly on the host machine

## Overlay Networks

- An overlay network extends networking capabilities across multiple physical or virtual Docker hosts (typically managed via Docker Swarm or external orchestrators)

- How it works

    - It creates a distributed virtual network that spans multiple hosts

    - Containers running on Server A can talk directly to containers running on Server B as if they were sitting on the same local switch

## DNS Inside Docker

- Docker has a built-in, lightweight embedded DNS server that manages name resolution for containers running on user-defined networks

- Automatic Service Discovery

    - When you create a custom bridge network and spin up two containers (e.g., a web app and a database), the web app can simply ping or connect to the hostname `database`

    - Docker's internal DNS server intercepts this query and automatically resolves it to the correct internal container IP address

- External Name Resolution

    - If a container tries to reach an external domain (like `github.com`), Docker's DNS server acts as a resolver
    
    - It forwards the query to the upstream DNS servers configured on the host machine (typically read from `/etc/resolv.conf`)


# Dockerfile

## What it is

- It is a text document that contains all the commands a user could call on the command line to **assemble an image**

- Build an image: `docker build -t my-app-name .`

- Run a container from your new image: `docker run -p 3000:3000 my-app-name`

## Core Dockerfile Instructions

- `FROM`: Sets the base image to start from (e.g., `python:3.11`, `node:18`, `ubuntu:latest`)

    - **Every Dockerfile must start with this**

- `WORKDIR`: Sets the working directory for any instruction that follows it (`RUN`, `CMD`, `COPY`, etc.)

    - If it doesn't exist, Docker will create it

- `COPY` / `ADD`: Copies new files or directories from your local machine into the container's filesystem

    - COPY is preferred over ADD unless you specifically need auto-tar extraction or remote URLs

- `RUN`: Executes commands in a new layer on top of the current image and commits the results

- `ENV`: Sets environment variables (e.g., ENV PORT=8080)

- `EXPOSE`: Informs Docker that the container listens on specific network ports at runtime

- `CMD`: Provides the default command to execute when the container starts

## A Practical Example: `Node.js` App

```dockerfile
# 1. Choose a lightweight official Node base image
FROM node:18-alpine

# 2. Set the working directory inside the container
WORKDIR /app

# 3. Copy package files first (optimizes caching!)
COPY package*.json ./

# 4. Install dependencies
RUN npm install

# 5. Copy the rest of the application code
COPY . .

# 6. Expose the port the app runs on
EXPOSE 3000

# 7. Define the command to run the app
CMD ["npm", "start"]
```