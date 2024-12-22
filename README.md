# Docker Fundamentals

## This amazing repository contains:-

- [Containerization](#containerization)
- [Docker Engine](#docker-engine)
- [Docker Images vs Docker Containers](#docker-images-vs-docker-containers)
- [Docker Commands](#docker-commands)
- [Dockerfile](#dockerfile)
- [Layers](#layers)
- [Optimizing Dockerfile](#optimizing-dockerfile)
- [Volumes](#volumes)
- [Networks](#networks)
- [Docker Compose](#docker-compose)

# Containerization

mental image: Suppose you work at a company, where all the company’s work related stuff were stored in your machine(like database, libraries, packages, etc, etc). If you were to get fired, then you will have to delete every stuff manually from your machine( Thing we don’t like 😞 ). Now lets suppose there was a way were all those company related stuff were to be stored somewhere, which is isolated from your machine. And you could write one command and all of it is gone after you get fired (i hope not! but still..). Thats where containers come in. Now read the below definition.

Containerization is a lightweight form of virtualization that packages an application and its dependencies (libraries, configuration files, etc.) into a single, portable unit called a **container**. Containers ensure that the application runs consistently across different environments, whether it's a developer's laptop, a testing server, or a production environment.

### Key Features of Containerization:

1. **Isolation**:
    - Each container runs in its own isolated environment, separate from other containers and the host system.
    - This ensures that one container's operations do not affect others.
2. **Lightweight**:
    - Containers share the host operating system's kernel, making them much lighter than traditional virtual machines (VMs), which include a full OS.
3. **Portability**:
    - A container can run anywhere—on a developer's machine, a cloud server, or a bare-metal server—provided the platform supports container runtimes (e.g., Docker).
4. **Consistency**:
    - Containers include everything the application needs to run (dependencies, libraries, configuration), ensuring the app behaves the same regardless of the environment.

---

### How Docker Helps in Containerization:

Docker is one of the most popular platforms for creating, managing, and running containers.

1. **Docker Image**:
    - A read-only template containing the application code, runtime, libraries, and environment settings. It’s like a blueprint for creating containers.
2. **Docker Container**:
    - A running instance of a Docker image. It's what executes the application in an isolated environment.
3. **Dockerfile**:
    - A script that defines how to build a Docker image. It contains instructions such as which base image to use, dependencies to install, and commands to run.

---

### Example Use Case:

Suppose you're building a web app that requires Python and Flask. You can create a Docker container with:

1. A Python runtime.
2. Flask installed as a dependency.
3. Your web app code.

This container can run on any system without worrying about whether Python or Flask is installed.

# Docker Engine

The **Docker Engine** is the core component of the Docker platform that enables containerization. It is the runtime responsible for building, running, and managing Docker containers on a host machine.

So basically, when you install docker, the actual thing that your machine stores is Docker Engine. What does it do? Everything! 

It is responsible for creating images, containers, pulling images from dockerHub( just like Github). And more. Also it contains something called Daemon( i call it Doraemon, actually pronounced demon). It is the server that connects host machine to docker cli. 

### Components of Docker Engine

Docker Engine is typically composed of three key components:

1. **Docker Daemon (`dockerd`)**:
    - A background process (server) that runs on the host machine.
    - It performs all the container-related tasks, such as managing images, containers, networks, and volumes.
    - It listens to commands sent via the Docker CLI or API.
2. **Docker CLI (`docker`)**:
    - A command-line tool that allows users to interact with the Docker Daemon.
    - Commands like `docker run`, `docker build`, and `docker ps` are sent to the Docker Daemon through the CLI.
3. **Docker REST API**:
    - Provides a programmatic way to communicate with the Docker Daemon.
    - It allows developers or tools to interact with Docker programmatically instead of through the CLI.

---

### How Docker Engine Works

1. The **Docker CLI** sends a command (e.g., `docker run`) to the **Docker Daemon** via the **Docker REST API**.
2. The **Docker Daemon** executes the command by managing containers, images, networks, or other resources.
3. The output is sent back to the Docker CLI to display to the user.

---
# Docker Images vs Docker Containers

# **Docker Image**

---

Down below is the complex jargon terms created by AI. But, in ultra super easy simple words:

- Image is the recipe. Container is the dish.
- You can create a recipe yourself or use recipees created by someone else.
- If you want to use a recipee created by someone else, you need to pull it from a place called dockerhub. You can also publish your recipee there.
- Now you are ready for the AI’d stuff!

### AI’d Stuff

A Docker Image is a **read-only template** used to create Docker Containers. It contains all the necessary instructions, configurations, and dependencies required to run an application.

### Key Characteristics:

1. **Immutable**:
    - Once created, a Docker Image cannot be changed. If you need modifications, you create a new image based on the existing one.
2. **Layers**:
    - Docker Images are made up of **layers**. Each layer corresponds to an instruction in the `Dockerfile` (e.g., installing software, copying files).
    - Layers are shared between images to save disk space and speed up builds (called **layer caching**).
3. **Stored in Repositories**:
    - Docker Images are stored in repositories like **Docker Hub**, **AWS Elastic Container Registry (ECR)**, or private registries.
    - Images can be pulled (`docker pull`) from these repositories to use locally.
4. **Static**:
    - An image is a snapshot of the application at a specific point in time. It does not change or run on its own.

### How It’s Created:

Images are built using a **Dockerfile**, which is a script defining:

- A base image (e.g., `python:3.9` or `ubuntu`).
- Application files, dependencies, and configuration.
- Commands to execute inside the container.

### Example Dockerfile:

```
# Base image
FROM python:3.9

# Set working directory
WORKDIR /app

# Copy application files
COPY . /app

# Install dependencies
RUN pip install -r requirements.txt

# Run the application
CMD ["python", "app.py"]

```

Building this Dockerfile creates a Docker Image.

---

### **Docker Container**

A Docker Container is a **runtime instance** of a Docker Image. When you "run" an image, it creates a container that executes the application in an isolated environment.

### Key Characteristics:

1. **Mutable**:
    - Containers can change during runtime (e.g., files can be created or modified inside a container).
    - However, these changes are not saved back to the image unless explicitly done.
2. **Isolated Environment**:
    - Containers run in isolated environments, separate from the host machine and other containers.
    - They have their own filesystem, network interfaces, and process space.
3. **Ephemeral**:
    - By default, containers are temporary and can be stopped, restarted, or deleted.
    - When a container is deleted, any changes made inside it are lost unless they are saved externally (e.g., using volumes).
4. **Interactive**:
    - Containers can be accessed and interacted with directly (e.g., using `docker exec` or `docker attach`).

### How It’s Created:

- A container is created from an image using `docker run`. For example:

This creates and runs a container from the `myapp` image.
    
    ```bash
    docker run -d -p 5000:5000 myapp
    
    ```
    

---

### **Key Differences Between Docker Image and Docker Container**

| Feature | Docker Image | Docker Container |
| --- | --- | --- |
| **Definition** | Read-only blueprint/template for creating containers. | A running instance of a Docker Image. |
| **State** | Static and immutable. | Dynamic and mutable during runtime. |
| **Purpose** | To provide a predefined environment for an application. | To execute the application in an isolated environment. |
| **Storage** | Stored in Docker registries like Docker Hub. | Exists in memory or on disk while running. |
| **Creation** | Built using `docker build` and a Dockerfile. | Created from an image using `docker run`. |
| **Persistence** | Changes are not possible (immutable). | Changes can be made but are not saved back to the image. |
| **Lifecycle** | Exists indefinitely (until deleted manually). | Ephemeral and tied to its execution state. |

---

### **Analogy**

Think of a **Docker Image** as a **blueprint** or a **recipe**, and a **Docker Container** as the **actual dish prepared from the recipe**. You can create multiple dishes (containers) from the same recipe (image), and each dish can be consumed, modified, or discarded without affecting the original recipe.

---

### Example Workflow:

1. You create a **Docker Image** for a Python application using a Dockerfile.
    
    ```bash
    docker build -t myapp .
    
    ```
    
2. You run a **Docker Container** from this image:
    
    ```bash
    docker run -d -p 5000:5000 myapp
    
    ```
    
3. The container runs the Python application. If you stop the container:

The container stops running, but the image remains unchanged.
    
    ```bash
    docker stop <container_id>
    
    ```
    

---

# Docker Commands

---

### **1. Docker Setup**

- **`docker version`**: Check installed Docker version.
    
    ```bash
    docker version
    
    ```
    
- **`docker info`**: Display system-wide Docker information.
    
    ```bash
    docker info
    
    ```
    

---

### **2. Working with Images**

- **`docker pull`**: Download an image from a Docker registry.
    
    ```bash
    docker pull nginx
    
    ```
    
- **`docker images`**: List all available images on your machine.
    
    ```bash
    docker images
    
    ```
    
- **`docker rmi <image_name>`**: Remove an image from your system.
    
    ```bash
    docker rmi nginx
    
    ```
    

---

### **3. Working with Containers**

- **`docker run`**: Create and start a container.
    
    ```bash
    docker run -d -p 8080:80 nginx
    
    ```
    
    - `d`: Run in detached mode (background).
    - `p`: Map ports (host:container).
- **`docker ps`**: List all running containers.
    
    ```bash
    docker ps
    
    ```
    
- **`docker ps -a`**: List all containers (running and stopped).
    
    ```bash
    docker ps -a
    
    ```
    
- **`docker stop <container_id>`**: Stop a running container.
    
    ```bash
    docker stop <container_id>
    
    ```
    
- **`docker rm <container_id>`**: Remove a container.
    
    ```bash
    docker rm <container_id>
    
    ```
    

---

### **4. Building Images**

- **`docker build`**: Build an image from a Dockerfile.
    
    ```bash
    docker build -t myapp .
    
    ```
    
    - `t`: Name/tag the image.
    - `.`: Use the current directory for the Dockerfile.

---

### **5. Inspecting and Debugging**

- **`docker logs <container_id>`**: View logs of a container.
    
    ```bash
    docker logs <container_id>
    
    ```
    
- **`docker exec`**: Execute a command inside a running container.
    
    ```bash
    docker exec -it <container_id> bash
    
    ```
    
    - `it`: Interactive mode (e.g., for a shell).
- **`docker inspect <container_id>`**: Get detailed information about a container.
    
    ```bash
    docker inspect <container_id>
    
    ```
    

---

### **6. Managing Volumes**

- **`docker volume create <volume_name>`**: Create a Docker volume.
    
    ```bash
    docker volume create my_volume
    
    ```
    
- **`docker volume ls`**: List all volumes.
    
    ```bash
    docker volume ls
    
    ```
    
- **`docker volume rm <volume_name>`**: Remove a volume.
    
    ```bash
    docker volume rm my_volume
    
    ```
    

---

### **7. Managing Networks**

- **`docker network ls`**: List all networks.
    
    ```bash
    docker network ls
    
    ```
    
- **`docker network create <network_name>`**: Create a custom network.
    
    ```bash
    docker network create my_network
    
    ```
    
- **`docker network inspect <network_name>`**: View details about a network.
    
    ```bash
    docker network inspect my_network
    
    ```
    
- **`docker network connect <network_name> <container_id>`**: Connect a container to a network.
    
    ```bash
    docker network connect my_network <container_id>
    
    ```
    

---

### **8. Clean Up**

- **`docker system prune`**: Remove unused containers, images, networks, and volumes.
    
    ```bash
    docker system prune
    
    ```
    
- **`docker rm $(docker ps -a -q)`**: Remove all stopped containers.
    
    ```bash
    docker rm $(docker ps -a -q)
    
    ```
    
- **`docker rmi $(docker images -q)`**: Remove all images.
    
    ```bash
    docker rmi $(docker images -q)
    
    ```
    

---

### Quick Example Workflow:

1. Pull an image:
    
    ```bash
    docker pull nginx
    
    ```
    
2. Run a container from the image:
    
    ```bash
    docker run -d -p 8080:80 nginx
    
    ```
    
3. View running containers:
    
    ```bash
    docker ps
    
    ```
    
4. Stop the container:
    
    ```bash
    docker stop <container_id>
    
    ```
    
5. Remove the container:
    
    ```bash
    docker rm <container_id>
    
    ```
    

# Dockerfile

If you want to create an image from your own code, that you can push to `dockerhub`, you need to create a `Dockerfile` for your application.

A Dockerfile is a text document that contains all the commands a user could call on the command line to create an image.

---

### **How to write a dockerfile**

A dockerfile has 2 parts

1. Base image
2. Bunch of commands that you run on the base image (to install dependencies like Node.js)

---

### **Dockerfile Syntax and Common Instructions**

Here’s a breakdown of common Dockerfile instructions:

1. **`FROM`**: Specifies the base image.
    
    ```
    FROM python:3.9
    
    ```
    
2. **`WORKDIR`**: Sets the working directory inside the container.
    
    ```
    WORKDIR /app
    
    ```
    
3. **`COPY`**: Copies files/directories from the host to the container.
    
    ```
    COPY . /app
    
    ```
    
4. **`RUN`**: Executes a command during the image build process (e.g., installing dependencies).
    
    ```
    RUN apt-get update && apt-get install -y curl
    
    ```
    
5. **`CMD`**: Specifies the default command to run when the container starts.
    
    ```
    CMD ["python", "app.py"]
    
    ```
    
6. **`EXPOSE`**: Informs Docker that the container listens on a specific network port.
    
    ```
    EXPOSE 8080
    
    ```
    
7. **`ENV`**: Sets environment variables inside the container.
    
    ```
    ENV APP_ENV=production
    
    ```
    
8. **`ENTRYPOINT`**: Similar to `CMD`, but used to configure the container to run as an executable.
    
    ```
    ENTRYPOINT ["nginx", "-g", "daemon off;"]
    
    ```
    

---

### **Example Dockerfile**

Here’s a sample Dockerfile for a Python Flask application:

```
# Step 1: Use Python base image
FROM python:3.9

# Step 2: Set the working directory
WORKDIR /app

# Step 3: Copy application files into the container
COPY . /app

# Step 4: Install required dependencies
RUN pip install -r requirements.txt

# Step 5: Expose the application port
EXPOSE 5000

# Step 6: Define the default command to run the app
CMD ["python", "app.py"]

```

---

### **Building and Running a Dockerfile**

1. **Build the Docker image**:
    
    ```bash
    docker build -t my-flask-app .
    
    ```
    
    - `t`: Tags the image with a name (`my-flask-app`).
    - `.`: Refers to the directory containing the Dockerfile.
2. **Run a container from the image**:
    
    ```bash
    docker run -d -p 5000:5000 my-flask-app
    
    ```
    
    - Maps port `5000` on the host to port `5000` in the container.
    

---

### Don’t define and ENV inside Dockerfile

You don’t want to expose your secret anywhere else. Also the thing that dockerfile is pushed to github, so defining environment variables inside dockerfile is not a very good choice.

Instead, you can hardcode your environment variables while running the images :

```solidity
docker run -p 3000:3000 -e DATABASE_URL="postgres://avnadmin:AVNS_EeDiMIdW-dNT4Ox9l1n@pg-35339ab4-harkirat-d1b9.a.aivencloud.com:25579/defaultdb?sslmode=require" image_name
```

# Layers

In Docker, layers are a fundamental part of the image architecture that allows Docker to be efficient, fast, and portable. A Docker image is essentially built up from a series of layers, each representing a set of differences from the previous layer.

**How layers are made -**

1. **Base Layer:** The starting point of an image, typically an operating system (OS) like Ubuntu, Alpine, or any other base image specified in a Dockerfile.
2. **Instruction Layers:** Each command in a Dockerfile creates a new layer in the image. These include instructions like **`RUN`**, **`COPY`**, which modify the filesystem by installing packages, copying files from the host to the container, or making other changes. Each of these modifications creates a new layer on top of the base layer.
3. **Reusable & Shareable:** Layers are cached and reusable across different images, which makes building and sharing images more efficient. If multiple images are built from the same base image or share common instructions, they can reuse the same layers, reducing storage space and speeding up image downloads and builds.
4. **Immutable:** Once a layer is created, it cannot be changed. If a change is made, Docker creates a new layer that captures the difference. This immutability is key to Docker's reliability and performance, as unchanged layers can be shared across images and containers.

## **Layers practically**

For a simple Node.js app - [https://github.com/100xdevs-cohort-2/week-15-live-2](https://github.com/100xdevs-cohort-2/week-15-live-2)

**Dockerfile**

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fa7018106-27d9-4833-9206-d20d05ab8a11%2FScreenshot_2024-03-10_at_1.29.42_PM.png?table=block&id=5adef147-fe82-4e9a-9e82-dbb3738b3104&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fa7018106-27d9-4833-9206-d20d05ab8a11%2FScreenshot_2024-03-10_at_1.29.42_PM.png?table=block&id=5adef147-fe82-4e9a-9e82-dbb3738b3104&cache=v2)

**Logs**

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F891e06cd-8ce7-402e-9e0d-15d7e9852e3d%2FScreenshot_2024-03-10_at_1.31.53_PM.png?table=block&id=d06687c2-32b3-4419-865c-367f7a0ffdd8&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F891e06cd-8ce7-402e-9e0d-15d7e9852e3d%2FScreenshot_2024-03-10_at_1.31.53_PM.png?table=block&id=d06687c2-32b3-4419-865c-367f7a0ffdd8&cache=v2)

### **Observations -**

1. Base image creates the first layer
2. Each `RUN`, `COPY` , `WORKDIR` command creates a new layer
3. Layers can get re-used across docker builds (notice `CACHED` in 1/6)

## **Why layers?**

If you change your Dockerfile, layers can get re-used based on where the change was made

💡

If a layer changes, all subsequent layers also change

### **Case 1 - You change your source code**

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F56478744-6865-470c-a271-1837d86bdf87%2FScreenshot_2024-03-10_at_1.38.04_PM.png?table=block&id=0ef9171c-dba1-4dd5-88bf-f7eade9b197d&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F56478744-6865-470c-a271-1837d86bdf87%2FScreenshot_2024-03-10_at_1.38.04_PM.png?table=block&id=0ef9171c-dba1-4dd5-88bf-f7eade9b197d&cache=v2)

### *Logs*

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fe394bcde-4a1e-4793-b990-ac300a217212%2FScreenshot_2024-03-10_at_1.41.27_PM.png?table=block&id=244d5b29-cb92-4ce2-81ad-e7ec0dbc2230&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fe394bcde-4a1e-4793-b990-ac300a217212%2FScreenshot_2024-03-10_at_1.41.27_PM.png?table=block&id=244d5b29-cb92-4ce2-81ad-e7ec0dbc2230&cache=v2)

### **Case 2 - You change the package.json file (added a dependency)**

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F56478744-6865-470c-a271-1837d86bdf87%2FScreenshot_2024-03-10_at_1.38.04_PM.png?table=block&id=70ac3431-e628-4238-b115-c724a55ffa5d&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F56478744-6865-470c-a271-1837d86bdf87%2FScreenshot_2024-03-10_at_1.38.04_PM.png?table=block&id=70ac3431-e628-4238-b115-c724a55ffa5d&cache=v2)

### *Logs*

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fe394bcde-4a1e-4793-b990-ac300a217212%2FScreenshot_2024-03-10_at_1.41.27_PM.png?table=block&id=3eae7431-5ea2-4f49-9c0a-46b9e88b2f78&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fe394bcde-4a1e-4793-b990-ac300a217212%2FScreenshot_2024-03-10_at_1.41.27_PM.png?table=block&id=3eae7431-5ea2-4f49-9c0a-46b9e88b2f78&cache=v2)

### **Thought experiment**

How often in a project do you think `dependencies change` ?

How often does the `npm install` layer need to change?

Wouldn’t it be nice if we could `cache` the `npm install` step considering dependencies don’t change often?

# **Optimising Dockerfile**

What if we change the Dockerfile a bit - Dockerfile

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F3dc112b6-bf1a-4d48-acd4-dcaac3ef594a%2FScreenshot_2024-03-10_at_2.24.00_PM.png?table=block&id=d3ab0f48-79e0-47c0-8370-5d9e18accb34&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F3dc112b6-bf1a-4d48-acd4-dcaac3ef594a%2FScreenshot_2024-03-10_at_2.24.00_PM.png?table=block&id=d3ab0f48-79e0-47c0-8370-5d9e18accb34&cache=v2)

1. We first copy over only the things that `npm install` and `npx prisma generate` need
2. Then we run these scripts
3. Then we copy over the rest of the source code

**Case 1 - You change your source code (but nothing in package.json/prisma)**

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F60fd50e5-576a-4039-a0e0-6617293d10ce%2FScreenshot_2024-03-10_at_2.29.47_PM.png?table=block&id=e69f3527-8cd6-42a7-9d94-b17ac42467f0&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F60fd50e5-576a-4039-a0e0-6617293d10ce%2FScreenshot_2024-03-10_at_2.29.47_PM.png?table=block&id=e69f3527-8cd6-42a7-9d94-b17ac42467f0&cache=v2)

**Case 2 - You change the package.json file (added a dependency)**

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fda3357b0-33e6-47ab-b552-4e52ecbfa808%2FScreenshot_2024-03-10_at_2.30.51_PM.png?table=block&id=c8cc1aaf-8c6a-48d4-b764-f1531207260b&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fda3357b0-33e6-47ab-b552-4e52ecbfa808%2FScreenshot_2024-03-10_at_2.30.51_PM.png?table=block&id=c8cc1aaf-8c6a-48d4-b764-f1531207260b&cache=v2)

# Volumes

Docker **volumes** provide a way to persist data generated by and used in Docker containers. They allow you to store data outside of a container's filesystem, making it independent of the container's lifecycle. This means the data will persist even if the container is stopped, removed, or recreated.

---

## Why do we Need Volumes ?

### **Without volumes**

1. Start a mongo container locally

```jsx
docker run -p 27017:27017 -d mongo
```

1. Open it in MongoDB Compass and add some data to it

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F8c867185-969d-4d72-b3be-d3cb386903ef%2FScreenshot_2024-03-10_at_3.44.35_PM.png?table=block&id=a4a9ef50-3b92-488d-a365-2223c3e92b07&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F8c867185-969d-4d72-b3be-d3cb386903ef%2FScreenshot_2024-03-10_at_3.44.35_PM.png?table=block&id=a4a9ef50-3b92-488d-a365-2223c3e92b07&cache=v2)

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fb8c8ef76-2b19-468f-8bed-2660f6c34496%2FScreenshot_2024-03-10_at_3.48.44_PM.png?table=block&id=b093a08e-119b-4fa0-b0db-9ad3c02bcaa2&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fb8c8ef76-2b19-468f-8bed-2660f6c34496%2FScreenshot_2024-03-10_at_3.48.44_PM.png?table=block&id=b093a08e-119b-4fa0-b0db-9ad3c02bcaa2&cache=v2)

1. Kill the container

```jsx
docker kill <container_id>
```

1. Restart the container

```jsx
docker run -p 27017:27017 -d mongo
```

1. Try to explore the database in Compass and check if the data has persisted (it wouldn’t)

## **With volumes**

1. Create a `volume`

```jsx
docker volume create volume_database
```

1. Mount the folder in `mongo` which actually stores the data to this volume

```jsx
docker run -v volume_database:/data/db -p 27017:27017 mongo
```

1. Open it in MongoDB Compass and add some data to it

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F8c867185-969d-4d72-b3be-d3cb386903ef%2FScreenshot_2024-03-10_at_3.44.35_PM.png?table=block&id=cdf21e0d-100b-4091-b832-3d4a3887d7ab&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F8c867185-969d-4d72-b3be-d3cb386903ef%2FScreenshot_2024-03-10_at_3.44.35_PM.png?table=block&id=cdf21e0d-100b-4091-b832-3d4a3887d7ab&cache=v2)

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fb8c8ef76-2b19-468f-8bed-2660f6c34496%2FScreenshot_2024-03-10_at_3.48.44_PM.png?table=block&id=62467d68-6603-418d-abcf-db442078e6ce&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2Fb8c8ef76-2b19-468f-8bed-2660f6c34496%2FScreenshot_2024-03-10_at_3.48.44_PM.png?table=block&id=62467d68-6603-418d-abcf-db442078e6ce&cache=v2)

1. Kill the container

```jsx
docker kill <container_id>
```

1. Restart the container

```jsx
docker run -v volume_database:/data/db -p 27017:27017 mongo
```

1. Try to explore the database in Compass and check if the data has persisted (it will!)

## **How Docker Volumes Work**

1. **Volume Creation**:
    - Volumes are stored on the host machine, outside the container’s writable layer.
    - They are managed by Docker and can be created explicitly or automatically when you use the `v` or `-mount` flag.
2. **Mounting a Volume**:
    - When a volume is mounted into a container, the container can read/write data to it.
    - Volumes can be shared between multiple containers, allowing data sharing and persistence.
3. **Automatic Cleanup**:
    - Volumes are not deleted automatically when the container is removed unless explicitly specified, ensuring data safety.

---

---

## **Using Volumes**

1. **Create and Mount a Named Volume**:
    
    ```bash
    docker volume create my-volume
    docker run -v my-volume:/data/db mongo
    
    ```
    
2. **Inspect a Volume**:
    
    ```bash
    docker volume inspect my-volume
    
    ```
    
3. **Remove a Volume**:
    
    ```bash
    docker volume rm my-volume
    
    ```
    
    - You can only remove a volume if it’s not in use by any containers.

---

### **Example: Persisting MongoDB Data**

If you want MongoDB data to persist, use a named volume like this:

1. **Run MongoDB with a Volume**:
    
    ```bash
    docker run -d --name my-mongo -v mongo-data:/data/db -p 27017:27017 mongo
    
    ```
    
2. **Stop and Remove the Container**:
    
    ```bash
    docker stop my-mongo
    docker rm my-mongo
    
    ```
    
3. **Run a New Container with the Same Volume**:
    
    ```bash
    docker run -d --name new-mongo -v mongo-data:/data/db -p 27017:27017 mongo
    
    ```
    
    - The `mongo-data` volume ensures your database and data persist.

---

### **Benefits of Using Volumes**

1. **Data Persistence**: Survives container restarts and removals.
2. **Data Sharing**: Allows sharing of data between multiple containers.
3. **Efficiency**: Stored outside the container's writable layer, reducing image size.
4. **Backups and Migration**: Volumes can be easily backed up or transferred between systems.

---

# Networks

In Docker, a network is a powerful feature that allows containers to communicate with each other and with the outside world.

Docker containers can’t talk to each other by default.

[`localhost`](http://localhost/) on a docker container means `it's own network` and not the network of the `host machine` 

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F8b69d8c4-0014-46a6-80ee-b6fc40e07765%2FScreenshot_2024-03-10_at_4.32.34_PM.png?table=block&id=ad64379b-d26c-43ad-987b-628383216586&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F8b69d8c4-0014-46a6-80ee-b6fc40e07765%2FScreenshot_2024-03-10_at_4.32.34_PM.png?table=block&id=ad64379b-d26c-43ad-987b-628383216586&cache=v2)

---

### **How to make containers talk to each other?**

Attach them to the same network

1. Clone the repo - [https://github.com/100xdevs-cohort-2/week-15-live-2.2](https://github.com/100xdevs-cohort-2/week-15-live-2.2)
2. Build the image

```jsx
docker build -t image_tag .
```

1. Create a network

```jsx
docker network create my_custom_network
```

1. Start the `backend process` with the `network` attached to it

```jsx
docker run -d -p 3000:3000 --name backend --network my_custom_network image_tag
```

1. Start mongo on the same network

```jsx
docker run -d -v volume_database:/data/db --name mongo --network my_custom_network -p 27017:27017 mongo
```

1. Check the logs to ensure the db connection is successful

```jsx
docker logs <container_id>
```

1. Try to visit an endpoint and ensure you are able to talk to the database
2. If you want, you can remove the port mapping for mongo since you don’t necessarily need it exposed on your machine

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F2d10fe34-c5af-4030-bf82-09f6ecf2c545%2FScreenshot_2024-03-10_at_5.16.46_PM.png?table=block&id=0b77d3ea-2dbc-41ca-ba4e-d49172524991&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F2d10fe34-c5af-4030-bf82-09f6ecf2c545%2FScreenshot_2024-03-10_at_5.16.46_PM.png?table=block&id=0b77d3ea-2dbc-41ca-ba4e-d49172524991&cache=v2)

### **Types of networks**

- **Bridge**: The default network driver for containers. When you run a container without specifying a network, it's attached to a bridge network. It provides a private internal network on the host machine, and containers on the same bridge network can communicate with each other.
- **Host**: Removes network isolation between the container and the Docker host, and uses the host's networking directly. This is useful for services that need to handle lots of traffic or need to expose many ports.

---

### Common Docker Network Commands

1. **List Networks**:
    
    ```bash
    docker network ls
    
    ```
    
    Displays all available Docker networks.
    
2. **Inspect a Network**:
    
    ```bash
    docker network inspect <network-name>
    
    ```
    
    Provides detailed information about the specified network, such as connected containers.
    
3. **Create a Network**:
    
    ```bash
    docker network create my-network
    
    ```
    
    Creates a new network named `my-network`.
    
4. **Connect a Container to a Network**:
    
    ```bash
    docker network connect my-network my-container
    
    ```
    
5. **Disconnect a Container from a Network**:
    
    ```bash
    docker network disconnect my-network my-container
    
    ```
    
6. **Remove a Network**:
    
    ```bash
    docker network rm my-network
    
    ```
    

---

# Docker Compose

Docker Compose is a tool designed to help you define and run multi-container Docker applications. With Compose, you use a YAML file to configure your application's services, networks, and volumes. Then, with a single command, you can create and start all the services from your configuration.

In very very simple words - You have to use a set of commands to set multiple images up and running. for example, if the task is to connect to containers: 1. mongodb  2. backend,
First, you’ll have to create the container for mongo db using that long commands including volumes, networks, the name of container and what not.
Then you’ll have to run the backend image where you need to use port mapping, connect to the same network as mongodb container and so on.
Let’s your noob friend tries to do the task and he dont know any docker commands, how will he?
Docker-Compose!

So, You define all those long yet required commands in a simple yml file called 

docker-compose.yml 
and then, the only command your noob friend needs to run is:

```solidity
docker-compose up
```

yml would look something like:

![https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F161f82ec-cbf1-4654-ab9b-a052fd1da6be%2FScreenshot_2024-03-10_at_5.36.58_PM.png?table=block&id=8e4f86ba-c720-4f78-8c97-1926391deb73&cache=v2](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F085e8ad8-528e-47d7-8922-a23dc4016453%2F161f82ec-cbf1-4654-ab9b-a052fd1da6be%2FScreenshot_2024-03-10_at_5.36.58_PM.png?table=block&id=8e4f86ba-c720-4f78-8c97-1926391deb73&cache=v2)

### **Before docker-compose**

- Create a network

```jsx
docker network create my_custom_network
```

- Create a volume

```jsx
docker volume create volume_database
```

- Start mongo container

```jsx
docker run -d -v volume_database:/data/db --name mongo --network my_custom_network  mongo
```

- Start backend container

```jsx
docker run -d -p 3000:3000 --name backend --network my_custom_network backend
```

### **After docker-compose**

1. Install docker-compose - [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)
2. Create a `yaml` file describing all your containers and volumes (by default all containers in a docker-compose run on the same network)
3. Start the compose

```jsx
docker-compose up
```

1. Stop everything (including volumes)

```jsx
 docker-compose down --volumes
```
