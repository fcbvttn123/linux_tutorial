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