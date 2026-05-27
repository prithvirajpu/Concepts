# 🐳 Docker Commands Reference

A comprehensive guide to Docker CLI commands for developers.

---

## What is Docker?

**Docker** is a containerization platform that allows you to package an application along with all its dependencies into a standardized unit called a **container**.

- Containers are lightweight, portable, and run consistently across any environment.
- Docker uses OS-level virtualization instead of full hardware virtualization.

---

## Why use Docker?

- Consistent development, testing, and production environments
- Solves "it works on my machine" issues
- Faster startup and lower resource usage than virtual machines
- Easy to scale and deploy
- Excellent CI/CD integration
- Simplified dependency and environment management
- Portability across cloud providers and servers

---

## Key Features

- Containerization of applications
- Image layering and caching for fast builds
- Docker Compose for multi-container applications
- Built-in networking and persistent storage (volumes)
- Strong community and official image support
- Integration with orchestration tools (Kubernetes, Docker Swarm)
- Security features like secrets management and user namespaces

---

## Core Concepts

| Concept | Description |
|---------|-------------|
| **Image** | Read-only template used to launch containers (e.g., `nginx:latest`, `python:3.11-slim`) |
| **Container** | A running instance of an image |
| **Dockerfile** | Text file with instructions to build a Docker image |
| **Registry** | Storage for Docker images (Docker Hub, GitHub Container Registry, etc.) |
| **Volume** | Persistent storage for data |
| **Network** | Allows containers to communicate with each other |

---

## Docker Architecture

1. **Docker Client** — CLI tool (`docker` command)
2. **Docker Daemon (dockerd)** — Background service that manages containers, images, volumes, and networks
3. **Container Runtime** — Runs the actual containers
4. **Docker Registry** — Stores and distributes images

The client sends commands to the daemon, which then creates and manages containers.

---

## Where is Docker Used?

- Microservices architecture
- Web application deployment
- CI/CD pipelines
- Development and testing environments
- Cloud-native applications
- Data science and machine learning projects
- Internal tools and company infrastructure

---

## Real-World Applications

- Running web apps (Django + Gunicorn + Nginx)
- Database services (PostgreSQL, Redis, MongoDB)
- Monitoring stacks (Prometheus + Grafana)
- ELK Stack for logging
- Development environments with multiple services
- SaaS product deployments

---

## 📋 Docker Commands Reference

### 🔧 Basic Commands

| Command | Description |
|---------|-------------|
| `docker --help` | Get help with Docker |
| `docker version` | Show Docker version |
| `docker info` | Show system-wide information |

---

### 📦 Image Commands

| Command | Description |
|---------|-------------|
| `docker images` | List all local images |
| `docker pull \<image_name\>` | Pull image from Docker Hub |
| `docker build -t \<image_name\> .` | Build image from Dockerfile |
| `docker build -t \<image_name\> . --no-cache` | Build without cache |
| `docker rmi \<image_name\>` | Remove an image |
| `docker image prune` | Remove all unused images |
| `docker tag \<image_id\> \<repo\>:\<tag\>` | Tag an image |
| `docker push \<repo\>:\<tag\>` | Push image to Docker Hub |
| `docker search \<image_name\>` | Search Docker Hub |
| `docker login -u \<username\>` | Login to Docker Hub |

---

### 🐳 Container Commands

| Command | Description |
|---------|-------------|
| `docker run \<image_name\>` | Create and run a container |
| `docker run -d \<image_name\>` | Run container in background (detached) |
| `docker run --name \<name\> \<image\>` | Run with custom name |
| `docker run -p \<host_port\>:\<container_port\> \<image\>` | Map ports |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers (including stopped) |
| `docker start \<container_name\>` | Start a stopped container |
| `docker stop \<container_name\>` | Stop a running container |
| `docker restart \<container_name\>` | Restart a container |
| `docker rm \<container_name\>` | Remove a stopped container |
| `docker container prune` | Remove all stopped containers |
| `docker pause \<container_name\>` | Pause a running container |
| `docker unpause \<container_name\>` | Resume a paused container |

---

### 🔍 Inspect & Debug Commands

| Command | Description |
|---------|-------------|
| `docker exec -it \<container_name\> sh` | Open shell inside container |
| `docker exec -it \<container_name\> bash` | Open bash inside container |
| `docker logs \<container_name\>` | View container logs |
| `docker logs -f \<container_name\>` | Follow logs in real-time |
| `docker inspect \<container_name\>` | View detailed information |
| `docker container stats` | View resource usage (CPU, memory) |

---

### 🌐 Network Commands

| Command | Description |
|---------|-------------|
| `docker network ls` | List all networks |
| `docker network create \<network_name\>` | Create a network |
| `docker network rm \<network_name\>` | Remove a network |
| `docker network connect \<network\> \<container\>` | Connect container to network |
| `docker network disconnect \<network\> \<container\>` | Disconnect container from network |

---

### 💾 Volume Commands

| Command | Description |
|---------|-------------|
| `docker volume ls` | List all volumes |
| `docker volume create \<volume_name\>` | Create a volume |
| `docker volume rm \<volume_name\>` | Remove a volume |
| `docker run -v \<volume_name\>:\<container_path\> \<image\>` | Mount volume to container |
| `docker volume prune` | Remove unused volumes |

---

### 🔄 Docker Compose Commands

| Command | Description |
|---------|-------------|
| `docker-compose up` | Start all services |
| `docker-compose up -d` | Start all services in background |
| `docker-compose down` | Stop all services |
| `docker-compose ps` | List containers in compose |
| `docker-compose logs -f` | Follow logs from all services |

---

### 🧹 Cleanup Commands

| Command | Description |
|---------|-------------|
| `docker system prune` | Remove unused containers, networks, images |
| `docker system prune -a` | Remove all unused data (not just dangling) |

---

### ⚛️ React Portfolio Commands

Common commands for your React portfolio project:

| Command | Description |
|---------|-------------|
| `docker build -t my-react-portfolio .` | Build your portfolio image |
| `docker run -p 5173:80 my-react-portfolio` | Run it on port 5173 |
| `docker run -d -p 5173:80 my-react-portfolio --name portfolio` | Run in background with custom name |
| `docker ps` | See running containers |
| `docker stop portfolio` | Stop the container |
| `docker logs -f portfolio` | See logs in real-time |
| `docker exec -it portfolio sh` | Open shell inside container |
| `docker rm portfolio` | Remove container (after stopping) |

---

## 🚀 Quick Start for React Portfolio

```bash
# 1. Build the Docker image
docker build -t my-react-portfolio .

# 2. Run the container
docker run -p 3000:80 my-react-portfolio

# 3. Access at http://localhost:3000
```

---

## 💡 Tips

- Type `docker <command> --help` for detailed help on any command
- Use `docker ps -a` to see stopped containers
- Use `-d` flag to run containers in background
- Always stop containers before removing them
- Use `docker system prune` regularly to free up space

---

## 🔗 Resources

- [Official Docker Documentation](https://docs.docker.com/)
- [Docker CLI Cheat Sheet](https://www.docker.com/resources/cli-cheat-sheet/)
- [Docker Hub](https://hub.docker.com/)

---
