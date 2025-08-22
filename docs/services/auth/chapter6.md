
## Core Concepts

#### 1. **Docker Hub**
Docker Hub is a cloud-based registry where you can store and share container images. It allows you to distribute your applications and collaborate with others. Docker Hub provides both public and private repositories for your images.

#### 2. **Container Images**
A container image is a lightweight, standalone, and executable package that includes everything needed to run a piece of software. This includes the code, runtime, libraries, and dependencies.

Now that we have our application up and running, next we need to containerise our application as this will be essential for them to communicate with other services that we shall be creating later on iin the course. 

## Prerequisites
Before proceeding, ensure you have:
- Administrative privileges on your machine.
- A stable internet connection.

## Installing Docker

### macOS
1. **Install Docker**:
    - Download Docker Desktop for Mac from [Docker's official website](https://www.docker.com/products/docker-desktop).
    - Run the installer and follow the on-screen instructions.
    - Verify installation:  
      ```bash
      docker --version
      ```
2. **Install Kubernetes**:
    - Kubernetes is included with Docker Desktop. Enable Kubernetes in Docker Desktop settings.
    - Verify installation:  
      ```bash
      kubectl version --client
      ```

### Windows
1. **Install Docker**:
    - Download Docker Desktop for Windows from [Docker's official website](https://www.docker.com/products/docker-desktop).
    - Ensure WSL 2 is enabled (required for Docker Desktop).
    - Run the installer and follow the on-screen instructions.
    - Verify installation:  
      ```powershell
      docker --version
      ```
2. **Install Kubernetes**:
    - Kubernetes is included with Docker Desktop. Enable Kubernetes in Docker Desktop settings.
    - Verify installation:  
      ```powershell
      kubectl version --client
      ```

### Linux
1. **Install Docker**:
    - Follow the official Docker Engine installation guide for your Linux distribution: [Docker Linux Install](https://docs.docker.com/engine/install/).
    - Start and enable the Docker service:
      ```bash
      sudo systemctl start docker
      sudo systemctl enable docker
      ```
    - Verify installation:  
      ```bash
      docker --version
      ```
2. **Install Kubernetes**:
    - Install `kubectl` using the official guide: [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
    - Optionally, install Minikube for a local Kubernetes cluster: [Install Minikube](https://minikube.sigs.k8s.io/docs/start/).
    - Verify installation:  
      ```bash
      kubectl version --client
      ```
## Testing docker installation 

We can test to see if docker has been installed using the commanf bellow

```bash
docker run hello-world
```
To also test Kubernetes, which we shall alter use in the course, we can do this  by deploying a sample application:

```bash
kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
kubectl expose deployment hello-node --type=LoadBalancer --port=8080
```

Later on in the course we shall learn and apply more about kubernettes. for now lets focus on docker.

Since we have our app up and running, we should be thingking of deploying this application. We shall use docker to make this happen. 


Now that you’ve built your Docker image, you’re ready to publish it to Docker Hub. This guide will walk you through the steps required to share your image with others.

# Dockerising auth service
Dockerise here
