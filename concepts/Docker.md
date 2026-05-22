# Docker Notes

> Docker is a platform for developing, shipping, and running applications inside lightweight, portable **containers**. It solves the "it works on my machine" problem by packaging applications with all their dependencies.

## Table of Contents
- [Docker Notes](#docker-notes)
  - [Table of Contents](#table-of-contents)
  - [What is Docker?](#what-is-docker)
  - [Why use Docker?](#why-use-docker)
  - [Key Features](#key-features)
  - [Core Concepts](#core-concepts)
  - [Docker Architecture](#docker-architecture)
  - [Where is Docker Used?](#where-is-docker-used)
  - [Real-World Applications](#real-world-applications)
  - [How Docker Works](#how-docker-works)
  - [Common Docker Commands](#common-docker-commands)

## What is Docker?

**Docker** is a containerization platform that allows you to package an application along with all its dependencies into a standardized unit called a **container**.

- Containers are lightweight, portable, and run consistently across any environment.
- Docker uses OS-level virtualization instead of full hardware virtualization.

## Why use Docker?

- Consistent development, testing, and production environments
- Solves "it works on my machine" issues
- Faster startup and lower resource usage than virtual machines
- Easy to scale and deploy
- Excellent CI/CD integration
- Simplified dependency and environment management
- Portability across cloud providers and servers

## Key Features

- Containerization of applications
- Image layering and caching for fast builds
- Docker Compose for multi-container applications
- Built-in networking and persistent storage (volumes)
- Strong community and official image support
- Integration with orchestration tools (Kubernetes, Docker Swarm)
- Security features like secrets management and user namespaces

## Core Concepts

- **Image**: Read-only template used to launch containers (e.g., `nginx:latest`, `python:3.11-slim`)
- **Container**: A running instance of an image
- **Dockerfile**: Text file with instructions to build a Docker image
- **Registry**: Storage for Docker images (Docker Hub, GitHub Container Registry, etc.)
- **Volume**: Persistent storage for data
- **Network**: Allows containers to communicate with each other

## Docker Architecture

1. **Docker Client** — CLI tool (`docker` command)
2. **Docker Daemon (dockerd)** — Background service that manages containers, images, volumes, and networks
3. **Container Runtime** — Runs the actual containers
4. **Docker Registry** — Stores and distributes images

The client sends commands to the daemon, which then creates and manages containers.

## Where is Docker Used?

- Microservices architecture
- Web application deployment
- CI/CD pipelines
- Development and testing environments
- Cloud-native applications
- Data science and machine learning projects
- Internal tools and company infrastructure

## Real-World Applications

- Running web apps (Django + Gunicorn + Nginx)
- Database services (PostgreSQL, Redis, MongoDB)
- Monitoring stacks (Prometheus + Grafana)
- ELK Stack for logging
- Development environments with multiple services
- SaaS product deployments

## How Docker Works

1. You create a `Dockerfile`
2. Build an image using `docker build`
3. Run a container from that image using `docker run`
4. Docker daemon manages the container lifecycle
5. Containers share the host OS kernel but are isolated from each other

## Common Docker Commands

```bash
docker build -t myapp .
docker run -d -p 8000:8000 myapp
docker ps
docker logs container_name
docker exec -it container_name bash
docker stop container_name
docker rm container_name
docker images
docker compose up -d