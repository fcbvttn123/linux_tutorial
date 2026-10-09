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