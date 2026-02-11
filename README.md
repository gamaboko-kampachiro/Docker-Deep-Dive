# Docker Deep‑Dive – Complete Guide

===
===

## 1. The Problem Docker Solves

Before containerization became popular, software development and deployment were painful, inconsistent, and slow. Docker was created to solve deep infrastructure and environment problems that developers and operations teams faced for years.

---

### 1.1 The "It Works on My Machine" Problem

This is the most famous issue in software development.

A developer builds an application on their laptop:
    -> Specific OS (Windows/macOS/Linux)
    -> Specific Python/Node/Java version
    -> Specific library versions
    -> Specific environment variables
The app works perfectly.

But when deployed to:
    -> Testing server ❌
    -> Production server ❌
    -> Teammate’s system ❌
It breaks.

**Why?**: 
Because the environment is different.
Even small differences can break applications:
    -> Python 3.9 vs 3.11
    -> Different OpenSSL version
    -> Missing system package
    -> Different path variables

Docker solves this by packaging:
    -> Application code
    -> Runtime
    -> System tools
    -> Libraries
    -> Dependencies

into one standardized unit called a container image.
That image runs the same everywhere.

---

### 1.2 Dependency Hell

Modern applications depend on many external libraries.
Example: App "A" requires:
    -> Node 16
    -> Library v1.2

App "B" requires:
    -> Node 18
    -> Library v2.0

Installing both on the same machine can create conflicts.
This is called dependency hell.

Docker isolates applications from each other. Each container:
    -> Has its own filesystem
    -> Has its own dependencies
    -> Does not interfere with others

Now App "A" and App "B" can run on the same server without conflict.

---

### 1.3 Inconsistent Development, Testing, and Production Environments

Traditional setup:
    -> Developer uses local machine
    -> Testing runs on staging server
    -> Production runs on powerful cloud machine

All three environments differ.
This leads to:
    -> Unexpected bugs
    -> Deployment failures
    -> Time wasted debugging environment issues

With Docker:
    -> Developer builds image once
    -> Same image goes to testing
    -> Same image goes to production
Build once → Run anywhere.

---

### 1.4 Slow and Heavy Virtual Machines

Before Docker, virtualization meant Virtual Machines (VMs).

Problems with VMs:
    -> Each VM requires full OS
    -> Large size (GBs)
    -> High memory usage
    -> Slow boot time (minutes)
    -> Infrastructure cost is high

If you needed 10 applications:
    -> You might need 10 VMs
    -> Each with its own OS
    -> Massive overhead

Docker containers:
    -> Share host OS kernel
    -> Start in seconds
    -> Use far less memory
    -> Allow high density (many containers per server)

This dramatically reduces infrastructure cost.

---

### 1.6 Onboarding Problems in Teams

New developer joins team.

Old process:
    -> Install language runtime
    -> Install database
    -> Install dependencies
    -> Configure environment variables
    -> Fix version mismatches
    -> Spend 2–3 days just setting up

With Docker:

```bash
docker-compose up
```

Entire project runs.
Setup time reduces from days to minutes.

---

### 1.7 Microservices & Modern Cloud Applications

Modern applications are built as:
    -> Microservices
    -> APIs
    -> Background workers
    -> Databases
    -> Caches
Each component needs isolation.

Docker makes it easy to:
    -> Run multiple services independently
    -> Deploy them separately
    -> Scale them independently
    -> Maintain clean separation
This is why Docker became foundational in cloud-native architecture.

### Final Understanding

Docker solves:
    -> Environment inconsistency
    -> Dependency conflicts
    -> Heavy infrastructure overhead
    -> Slow deployments
    -> Difficult scaling
    -> Team onboarding complexity

In simple terms:
    Docker standardizes how software is built, shipped, and run.

===
===

## 2. Virtual Machines vs Docker

containers is fundamental in modern DevOps, cloud computing, and infrastructure design. While both provide isolation and allow multiple workloads to run on a single physical machine, they operate at completely different levels of virtualization.

---

### 2.1 Core Concept Difference

<b>Virtual Machines: Hardware-Level Virtualization</b>
A Virtual Machine virtualizes physical hardware.
It emulates:
    -> CPU
    -> Memory
    -> Disk
    -> Network interfaces

Each VM runs:
    -> Its own Guest Operating System
    -> Its own kernel
    -> Its own system libraries
    -> Its own applications

In simple terms:
    A VM is a full computer running inside another computer.

<b>Docker: OS-Level Virtualization</b>
Docker virtualizes at the operating system level.
Containers:
    -> Share the host OS kernel
        -> Do NOT include a full OS
    -> Package only application + runtime + dependencies

In simple terms:
    A container is an isolated process running on the host OS.

---

### 2.2 Architecture Comparison

<b>Virtual Machine Architecture – Explanation</b>
In a Virtual Machine-based setup, the physical server first runs a Host Operating System. On top of that, a hypervisor (such as VMware, VirtualBox, Hyper-V, or KVM) is installed. The hypervisor is responsible for creating and managing multiple Virtual Machines.

<b><u>Virtual Machine Architecture</u></b>

```bash
+---------------------------------------------------+
|                   Physical Server                 |
|         (CPU, RAM, Disk, Network Hardware)        |
+---------------------------------------------------+
                            |
                            v
+---------------------------------------------------+
|               Host Operating System               |
+---------------------------------------------------+
                            |
                            v
+---------------------------------------------------+
|                   Hypervisor                      |
|       (VMware / VirtualBox / Hyper-V / KVM)       |
+---------------------------------------------------+
        |                   |               |
        v                   v               v
+---------------+ +---------------+ +---------------+
|   Guest OS    | |     Guest OS  | |      Guest OS |
|   (Linux)     | |    (Windows)  | |     (MAC os)  |
+---------------+ +---------------+ +---------------+
|  Application  | |  Application  | |  Application  |
+---------------+ +---------------+ +---------------+
```

Each Virtual Machine contains:
    -> A full Guest Operating System
    -> Its own kernel
    -> System libraries
    -> Installed applications

This means every VM behaves like a completely independent computer. It boots independently, consumes dedicated resources, and must be maintained separately (updates, patches, security fixes).

Because every VM includes a full OS, resource consumption is higher and startup time is longer.

+++

<b>Docker Architecture – Explanation</b>
In a Docker-based setup, the physical server runs a Host Operating System (typically Linux). On top of this OS, the Docker Engine is installed.

<b><u>Docker Architecture</u></b>
```bash
+---------------------------------------------------+
|               Physical Server                     |
|       (CPU, RAM, Disk, Network Hardware)          |
+---------------------------------------------------+
                            |
                            v
+---------------------------------------------------+
|           Host Operating System                   |
|               (Linux Kernel)                      |
+---------------------------------------------------+
                            |
                            v
+---------------------------------------------------+
|                   Docker Engine                   |
|           (dockerd + container runtime)           |
+---------------------------------------------------+
        |                   |               |
        v                   v               v
+---------------+ +---------------+ +---------------+
|   Container 1 | |  Container 2  | |  Container 3  |
|   App + Libs  | |  App + Libs   | |  App + Libs   |
+---------------+ +---------------+ +---------------+
```
Instead of creating full operating systems, Docker creates containers. Containers:
    -> Share the host OS kernel
    -> Include only the application and its required libraries
    -> Run as isolated processes

Docker uses Linux kernel features such as namespaces (for isolation) and cgroups (for resource management) to separate containers from each other.

Since containers do not include a full OS, they are lightweight, start quickly, and consume significantly fewer resources compared to Virtual Machines.

---

### 2.3 Isolation Level

<b>Virtual Machines</b>
    -> Strong isolation
    -> Separate kernel per VM
    -> If one VM crashes, others are unaffected
    -> Suitable for strict multi-tenant environments

<b>Docker Containers</b>
    -> Process-level isolation
    -> Shared kernel
    -> Uses:
        <-> Namespaces (process isolation)
        <-> cgroups (resource control)
        <-> Union file systems (layered storage)

Isolation is lighter than VMs but sufficient for most applications.

---
### 2.4 Resource Usage

<b>Virtual Machines</b>
    -> Each VM requires dedicated RAM
    -> Guest OS consumes memory
    -> Storage size often 10–20 GB minimum
    -> Lower workload density per server
Example: A 16 GB RAM server may run 4–5 VMs efficiently.

<b>Docker Containers</b>
    -> No separate OS per container
    -> Minimal memory overhead
    -> Images typically 50–500 MB
    -> High workload density
Example: The same 16 GB server might run 40–60 containers.

### 2.5 Performance

<b>Virtual Machines</b>
    -> Slight overhead due to hardware virtualization
    -> Performance depends on hypervisor efficiency

<b>Docker Containers</b>
    -> Near-native performance
    -> No hardware emulation
    -> Faster I/O and startup

### 2.6 Startup Time

<b>Virtual Machine</b>
    -> Boots like a real operating system
    -> Typically 1–3 minutes

<b>Docker Container</b>
    -> Starts like a normal process
    -> Usually 1–3 seconds
    -> Sometimes milliseconds

This difference is critical for autoscaling systems.

---

### 2.7 Image Size Comparison
```bash
+----------------------------------------------------------------------------------+
|   <b>Aspect</b>       |   <b>Virtual Machine</b>  |	<b>Docker Container</b>    |
+----------------------------------------------------------------------------------+
|   Image Size	        |           Several GBs	    |       MBs to few hundred MB  |
+----------------------------------------------------------------------------------+
|   Contains Full OS?   |	            Yes         |        	    No             |
+----------------------------------------------------------------------------------+
|   Kernel	            |           Separate        |	        Shared             |
+----------------------------------------------------------------------------------+
```
---

### 2.8 Portability

<b>Virtual Machines</b>
    -> Portable but heavy
    -> Slower migration
    -> Large disk transfers

<b>Docker Containers</b>
    -> Highly portable
    -> Same image runs anywhere Docker is installed
    -> Ideal for CI/CD pipelines

### 2.9 Security Considerations

<b>Virtual Machines</b>
    -> Strong security boundary
    -> Hypervisor-level isolation
    -> Preferred for:
        <-> Hosting different customers
        <-> Running different OS types

<b>Docker Containers</b>
    -> Shared kernel increases attack surface
    -> Requires additional hardening:
        <-> Seccomp
        <-> AppArmor
        <-> SELinux
        <-> Kubernetes security policies

In real-world cloud environments:
    -> Cloud providers use VMs as infrastructure
    -> Containers run inside those VMs
They complement each other rather than compete.

### 2.10 Real-World Usage Model
Modern cloud stack often looks like:

Physical Server
    → Virtual Machines (EC2, Azure VM, etc.)
    → Docker Containers inside VMs
    → Applications

VMs provide infrastructure isolation. Docker provides application-level isolation.

### 2.11 When to Use What?
Use <b>Virtual Machines</b> When:
    -> You need different operating systems
    -> Strong isolation is required
    -> Running legacy or OS-dependent software
    -> Hosting infrastructure services

Use <b>Docker</b> When:
    -> Building microservices
    -> Running CI/CD pipelines
    -> Deploying scalable cloud-native apps
    -> You want fast startup and efficient resource usage

---

### Final Understanding

<b>Virtual Machines</b> virtualize hardware and run full operating systems.
<b>Docker containers</b> virtualize the operating system and run isolated processes.
<b>Both</b> are important in modern infrastructure — but they solve different layers of the problem.

===
===

## 3. Understanding Docker Architecture
<b>What Gets Installed When Docker Is Installed?</b>
Understanding Docker architecture requires knowing what actually runs behind the scenes when you install Docker. Many people think Docker is just a CLI tool, but it is actually a collection of multiple components working together.
When Docker is installed, several layers of software are installed and configured to enable containerization.

---

### 3.1 High-Level Architecture Overview
Docker follows a Client–Server Architecture.

Docker Client ---> Docker Daemon ---> Containers
                        |
                        v
                    containerd
                        |
                        v
                       runc

***

#### 3.1.1 Docker Client (docker CLI)

<b>What It Is</b>
The Docker Client is the command-line interface you use.

Examples:
```bash
docker build
docker run
docker ps
```

<b>What It Does</b>
    -> Takes user commands
    -> Converts them into API requests
    -> Sends them to the Docker Daemon

The client does NOT create containers directly. It communicates with the Docker Daemon using:
    -> REST API
    -> Unix socket (/var/run/docker.sock)

So when you type:
```bash
docker run nginx
```
The client sends a request to the daemon.

***

#### 3.1.2 Docker Daemon (dockerd)

<b>What It Is</b>
The Docker Daemon is the background service that manages everything.
It runs as:
    -> A system service (Linux: systemd)
    -> A Windows service (on Windows)

<b>Responsibilities</b>
The daemon:
    -> Builds images
    -> Pulls images from registries
    -> Creates containers
    -> Manages networks
    -> Manages volumes
    -> Monitors container lifecycle

Think of it as the brain of Docker.
If the daemon stops → containers stop.

***

#### 3.1.3 containerd

Docker does not directly create containers.
It uses an internal component called containerd.

<b>What containerd Does</b>
    -> Manages container lifecycle
    -> Handles image transfer and storage
    -> Talks to low-level runtime (runc)
    -> Manages namespaces and cgroups

containerd is an industry-standard container runtime. It is also used by Kubernetes.

Flow: Docker Daemon → containerd → runc

***

#### 3.1.4 runc (Low-Level Runtime)

runc is the actual low-level tool that creates containers.

<b>What runc Does</b>
    -> Uses Linux kernel features
    -> Creates namespaces
    -> Applies cgroups
    -> Sets up isolation
    -> Starts container process

runc follows the OCI (Open Container Initiative) standard.

In simple words: runc is what finally runs your application as an isolated process.

---

#### 3.2 Linux Kernel Features Used by Docker

Docker containers rely on Linux kernel capabilities.
When Docker is installed on Linux, it uses:

***

#### 3.2.1 Namespaces
Provide isolation for:
    -> Process IDs (PID namespace)
    -> Network interfaces (NET namespace)
    -> File system mounts (MNT namespace)
    -> Hostname (UTS namespace)
    -> Users (USER namespace)
This makes each container appear like an independent system.

***

#### 3.2.2 cgroups (Control Groups)
Used for resource limitation.
They control:
    -> CPU usage
    -> Memory usage
    -> Disk I/O
    -> Network bandwidth
Example: You can limit a container to 512MB RAM.

***

#### 3.2.3 Union File System (Layered Filesystem)
Docker images use layered storage.
Example image layers:
    -> Base OS layer
    -> Python layer
    -> App dependencies layer
    -> Application code layer

This allows:
    -> Faster builds
    -> Layer caching
    -> Efficient storage reuse

---

#### 3.3 Docker Images

When Docker is installed, it also installs:
    -> Image management system
    -> Local image storage

Images are:
    -> Read-only templates
    -> Built using Dockerfile
    -> Stored in /var/lib/docker (Linux)

Images can be pulled from:
    -> Docker Hub
    -> AWS ECR
    -> Private registries

---

#### 3.4 Docker Containers

Containers are:
    -> Running instances of images
    -> Isolated processes
    -> Managed by containerd

They include:
    -> Application
    -> Runtime
    -> Dependencies

They DO NOT include:
    -> A separate kernel

All containers share the host OS kernel.

---

#### 3.5 Docker Networking Components

When Docker is installed, it sets up networking drivers.

Default network driver:
    -> bridge

Other drivers:
    -> host
    -> none
    -> overlay
    -> macvlan

Docker also creates:
    -> docker0 virtual bridge interface

This allows containers to communicate.

---

#### 3.6 Docker Volume System

Docker installs volume drivers that allow:
    -> Persistent storage
    -> Data sharing between containers
    -> External storage integration

Volume data is stored in: /var/lib/docker/volumes

---

#### 3.7 What Gets Installed on Different Operating Systems?

On <b>Linux</b>
    -> docker CLI
    -> dockerd
    -> containerd
    -> runc
    -> Networking drivers
    -> Storage drivers

Docker runs natively using Linux kernel features.

***

On <b>Windows</b> / <b>macOS</b>
Docker Desktop installs:
    -> Docker CLI
    -> Docker Daemon
    -> containerd
    -> runc
    -> A lightweight Linux virtual machine

Because Windows and macOS do not use a Linux kernel natively, Docker runs inside a lightweight VM.

So architecture becomes:

Windows/macOS → Lightweight Linux VM → Docker Engine → Containers

---

### 3.8 Complete Execution Flow Example

When you run:
```bash
docker run nginx
```

<u>Step-by-step:</u>
    1. Docker CLI sends request to Docker Daemon
    2. Daemon checks if nginx image exists locally
    3. If not → pulls from Docker Hub
    4. Daemon tells containerd to create container
    5. containerd calls runc
    6. runc:
        6.1. Creates namespaces
        6.2. Applies cgroups
        6.3. Sets up filesystem
        6.4. Starts nginx process
    7. Container starts running
All this happens in seconds.

---

### 3.9 Final Layered View

Complete stack:

Physical Hardware → Host OS (Linux Kernel) → Docker Daemon (dockerd) → containerd → runc → Namespaces + cgroups → Container Process (Your App)

---

### Final Understanding

When Docker is installed, you are not just installing a tool.
You are installing:
    -> A Client (docker CLI)
    -> A Background Service (dockerd)
    -> A Container Runtime (containerd)
    -> A Low-Level Runtime (runc)
    -> Networking drivers
    -> Storage drivers
    -> Image management system
    -> Integration with Linux kernel features (namespaces & cgroups)

Docker is a complete container ecosystem — not just a command-line utility.

===
===

## 4. Dockerfile Deep Dive
A Dockerfile is a step-by-step instruction manual that tells Docker how to build an image.

```bash
docker build -t myapp .
```

Docker reads the Dockerfile line by line and creates image layers for most instructions.

### 4.1 FROM

Example:
```bash
FROM python:3.10-slim
```

<b>What It Does</b>
    -> Defines the base image.
    -> Every Dockerfile MUST start with FROM (except scratch-based builds).
    -> This is the first layer of your image.

<b>What Happens Internally</b>
    -> Docker checks locally for the image.
    -> If not present → pulls from registry (Docker Hub by default).
    -> The base image becomes the foundation layer.

---

### 4.2 LABEL

Example:
```bash
LABEL maintainer="manish@example.com"
```

<b>What It Does</b>
    -> Adds metadata to the image.
    -> Useful for versioning, ownership, documentation.

Example:
```bash
LABEL version="1.0"
LABEL description="Flask production app"
```
This does not affect container runtime behavior.

---

### 4.3 WORKDIR

Example:
```bash
WORKDIR /app
```

<b>What It Does</b>
    -> Sets working directory inside the container.
    -> Similar to cd /app.

<b>What Happens Internally</b>
    -> If directory does not exist → Docker creates it.
    -> All future instructions (COPY, RUN, CMD) execute relative to this path.

<b>Why Important</b>
    -> Avoid using absolute paths repeatedly.

### 4.4 COPY

Example:
```bash
COPY requirements.txt .
```

<b>What It Does</b>
    -> Copies files from host → image filesystem.

Syntax:
```bash
COPY <source> <destination>
```

Example:
```bash
COPY . .
```
Copies entire current directory.

<b>What Happens Internally</b>
    -> Docker calculates checksum of files.
    -> Creates a new image layer.
    -> Uses caching if files unchanged.

---

### 4.5 ADD

Example:
```bash
ADD app.tar.gz /app
```

<b>Difference from COPY</b>
    -> Can extract tar files automatically.
    -> Can fetch remote URLs.

---

### 4.6 RUN

Example:
```bash
RUN pip install -r requirements.txt
```

<b>What It Does</b>
    -> Executes command during image build.
    -> Creates a new image layer.

<b>What Happens Internally</b>
    -> Runs inside a temporary container.
    -> Commits result as new layer.

### 4.7 ENV

Example:
```bash
ENV PORT=5000
```

<b>What It Does</b>
    -> Sets environment variable inside container.
    -> Available at runtime.

Multiple Variables:
```bash
ENV APP_ENV=production \
DEBUG=false
```

---

### 4.8 ARG

Example:
```bash
ARG VERSION=1.0
```

<b>What It Does</b>
    -> Build-time variable.
    -> Only available during build.
    -> Not available in running container (unless copied to ENV).

Used as:
```bash
docker build --build-arg VERSION=2.0 .
```

---

### 4.9 EXPOSE

Example:
```bash
EXPOSE 5000
```

<b>What It Does</b>
    -> Documents which port container uses.
    -> Does NOT publish port.

To publish port:
```bash
docker run -p 5000:5000 image
```
Think of EXPOSE as documentation only.

---

### 4.10 USER

Example:
```bash
USER appuser
```

<b>What It Does</b>
    -> Runs container as non-root user.

<b>Why Important</b>
Running as root is a security risk.
Best Practice:
    -> Create user
    -> Switch to it

---

### 4.11 VOLUME

Example:
```bash
VOLUME ["/data"]
```

<b>What It Does</b>
    -> Creates mount point.
    -> Marks directory as externally managed storage.
Data persists outside container lifecycle.

---

### 4.12 ENTRYPOINT

Example:
```bash
ENTRYPOINT ["python"]
```

<b>What It Does</b>
    -> Defines main executable.
    -> Cannot be easily overridden.

Example:
```bash
ENTRYPOINT ["python", "app.py"]
```

---

### 4.13 CMD

Example:
```bash
CMD ["python", "app.py"]
```

<b>What It Does</b>
    -> Default command when container starts.
    -> Can be overridden.

<b>Difference Between CMD and ENTRYPOINT</b>
    -> ENTRYPOINT = fixed executable
    -> CMD = default arguments

Example Combination:
```bash
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Running:
```bash
docker run image other.py
```

Executes:
```bash
python other.py
```

---

### 4.14 Multi-Stage Build

Example:
```bash
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm install && npm run build

FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
```

<b>Why Use It</b>
    -> Reduce final image size
    -> Separate build environment from runtime

First stage → builds app
Second stage → copies only output

Result: Smaller, production-ready image.

---

### 4.15 How Dockerfile Is Processed

    1. Docker reads instructions top to bottom.
    2. Each instruction creates a layer (except some metadata).
    3. Layers are cached.
    4. If one layer changes → all next layers rebuild.

This is why instruction order matters.

---

### 4.16 Production-Ready Example
```bash
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PYTHONUNBUFFERED=1

EXPOSE 5000

USER nobody

CMD ["python", "app.py"]
```

---

### Final Understanding

A Dockerfile:
    -> Defines the base image
    -> Installs dependencies
    -> Copies application code
    -> Configures environment
    -> Defines how container starts

Each instruction contributes to:
    -> Image size
    -> Security
    -> Performance
    -> Maintainability

===
===

## 5. Key Docker Commands

This section provides a deep understanding of the most important Docker commands used in real-world development, debugging, deployment, and production environments.

Commands are grouped logically:
    -> Image Management
    -> Container Lifecycle
    -> Inspection & Debugging
    -> Networking
    -> Volumes
    -> System & Cleanup

---

### 5.1 Image Management Commands

<b>docker build</b>
Builds an image from a Dockerfile.
Syntax:
```bash
docker build -t <image_name>:<tag> <path>
```

Example:
```bash
docker build -t myapp:1.0 .
```

<b>What happens internally</b>:
    -> Docker reads Dockerfile
    -> Executes instructions sequentially
    -> Creates layered image
    -> Uses build cache if possible

<b>Important Flags:</b>
    -t → tag image
    --no-cache → ignore cache
    --build-arg → pass build-time variables

***

#### 5.1.1 docker images

Lists locally stored images.
```bash
docker images
```

Shows:
    -> Repository
    -> Tag
    -> Image ID
    -> Size

***

#### 5.1.2 docker pull

Downloads image from registry.
```bash
docker pull nginx:latest
```

Used when:
    -> Pre-downloading images
    -> Pulling from Docker Hub or private registry

***

#### 5.1.3 docker push

Uploads image to registry.
```bash
docker push username/myapp:1.0
```

Requires login:
```bash
docker login
```

***

#### 5.1.4 docker rmi

Removes image.
```bash
docker rmi image_id
```

If image is used by container → must remove container first.

---

### 5.2 Container Lifecycle Commands

#### 5.2.1 docker run

Creates and starts a container from image.
Basic:
```bash
docker run nginx
```

Common Production Usage:
```bash
docker run -d -p 5000:5000 --name mycontainer myapp
```

<b>Important Flags:</b>
    -d → detached mode (background)
    -p → port mapping (host:container)
    --name → assign name
    -e → set environment variable
    -v → mount volume
    --network → attach network
    --rm → auto remove when stopped

***

#### 5.2.2 docker ps

Lists running containers.
```bash
docker ps
```

To see all containers:
```bash
docker ps -a
```

***

#### 5.2.3 docker stop

Gracefully stops container.
```bash
docker stop container_id
```

Sends SIGTERM signal.

***

#### 5.2.4 docker start

Starts stopped container.
```bash
docker start container_id
```

***

#### 5.2.5 docker restart

Stops and starts container.
```bash
docker restart container_id
```

***

#### 5.2.6 docker rm

Removes container.
```bash
docker rm container_id
```

Force remove:
```bash
docker rm -f container_id
```

---

### 5.3 Inspection & Debugging Commands

#### 5.3.1 docker logs

View container logs.
```bash
docker logs container_id
```

Follow live logs:
```bash
docker logs -f container_id
```

***

#### 5.3.2 docker exec

Run command inside running container.
Interactive shell:
```bash
docker exec -it container_id bash
```

Used for:
    -> Debugging
    -> Checking files
    -> Running migrations

***

#### 5.3.3 docker inspect

Shows detailed JSON metadata.
```bash
docker inspect container_id
```

Used to check:
    -> IP address
    -> Mount points
    -> Network settings
    -> Environment variables

***

#### 5.3.4 docker stats

Real-time resource usage.
```bash
docker stats
```

Shows:
    -> CPU usage
    -> Memory usage
    -> Network I/O

---

### 5.4 Networking Commands

#### 5.4.1 docker network ls

List networks.
```bash
docker network ls
```

***

#### 5.4.2 docker network create

Create custom network.
```bash
docker network create mynetwork
```

Attach container to network:
```bash
docker run --network mynetwork nginx
```

***

#### 5.4.3 docker network inspect

Inspect network configuration.
```bash
docker network inspect mynetwork
```

***

### 5.5 Volume Commands

#### 5.5.1 docker volume ls
List volumes.
```bash
docker volume ls
```

***

#### 5.5.2 docker volume create

Create volume.
```bash
docker volume create myvolume
```

***

#### 5.5.3 docker volume inspect

Inspect volume details.
```bash
docker volume inspect myvolume
```

***

#### 5.5.4 docker volume rm

Remove volume.
```bash
docker volume rm myvolume
```

---

### 5.6 System & Cleanup Commands

#### 5.6.1 docker system df

Shows disk usage.
```bash
docker system df
```

***

#### 5.6.2 docker system prune

Removes:
    -> Stopped containers
    -> Unused networks
    -> Dangling images

```bash
docker system prune
```

Aggressive cleanup:
```bash
docker system prune -a
```

---

### 5.7 Docker Compose Commands (Modern Usage)

Modern syntax:
```bash
docker compose up
```

Detached mode:
```bash
docker compose up -d
```

Stop services:
```bash
docker compose down
```

View logs:
```bash
docker compose logs -f
```

---

### 5.8 Real-World Workflow Example

Typical Development Flow:

1. Build image
```bash
docker build -t myapp .
```

2. Run container
```bash
docker run -d -p 5000:5000 --name app myapp
```

3. Check logs
```bash
docker logs -f app
```

4. Debug inside container
```bash
docker exec -it app bash
```

5. Stop and remove
```bash
docker stop app
docker rm app
```

---

### Final Understanding

Mastering these commands allows you to:
    -> Build images efficiently
    -> Manage containers confidently
    -> Debug production issues
    -> Monitor performance
    -> Maintain clean Docker environments

These commands form the foundation of real-world Docker usage in DevOps, cloud engineering, and production systems.

===
===

## 6. Docker Networking

Docker networking enables communication:
    -> Between containers on the same host
    -> Between containers on different hosts
    -> Between containers and the outside world
    -> Between containers and the host machine

Understanding Docker networking is critical for building microservices, multi-container applications, and production-ready systems.


### 6.1 Why Docker Networking Is Needed

Containers are isolated environments.
Each container:
    -> Has its own filesystem
    -> Has its own process space
    -> Has its own network namespace

Without networking:
    -> Containers cannot talk to each other
    -> Services like backend → database would fail

Docker solves this by creating virtual networks.

***

### 6.2 How Docker Networking Works Internally

When Docker is installed, it creates:
    -> A virtual bridge network (docker0)
    -> Virtual Ethernet interfaces (veth pairs)
    -> Network namespaces for containers
    -> iptables rules for NAT and port forwarding

When a container starts:
    1. Docker creates a network namespace
    2. A veth pair is created
    3. One end connects to container
    4. Other end connects to bridge (docker0)
    5. Container gets an internal IP

This is how containers communicate.

***

### 6.3 Types of Docker Networks

Docker supports multiple network drivers.

#### 6.3.1 Bridge Network (Default)

Used for standalone containers on a single host.
When you run:
```bash
docker run nginx
```

It attaches to the default bridge network.
Features:
    -> Containers get private IP (e.g., 172.x.x.x)
    -> Communication via IP
    -> Port mapping required to access from host

Example:
```bash
docker run -d -p 8080:80 nginx
```
Host port 8080 → Container port 80

Custom Bridge (Recommended):
```bash
docker network create mynet
```

Attach container:
```bash
docker run -d --network mynet --name web nginx
```

Advantages of custom bridge:
    -> Automatic DNS resolution (container name as hostname)
    -> Better isolation

Example:
If you run two containers on same custom network:
    -> backend
    -> database

Backend can connect to database using:
```bash
host = "database"
```

***

#### 6.3.2 Host Network

Container shares host's network stack.
Run:
```bash
docker run --network host nginx
```

Features:
    -> No NAT
    -> No port mapping needed
    -> Higher performance

Downside:
    -> No isolation
    -> Port conflicts possible

Used in:
    -> High-performance workloads
    -> Monitoring agents

***

#### 6.3.3 None Network

Disables networking completely.
```bash
docker run --network none nginx
```

Container has:
    -> No external access
    -> Only loopback interface

Used for:
    -> Security-sensitive workloads
    -> Batch processing

***

#### 6.3.4 Overlay Network

Used in Docker Swarm or multi-host environments.
Allows containers on different machines to communicate.
Uses:
    -> VXLAN tunneling
    -> Distributed key-value store

Example use case:
    -> Microservices running across multiple EC2 instances

***

#### 6.3.5 Macvlan Network

Assigns real MAC address to container.
Container appears as a physical device on network.
Used when:
    -> Need direct access from LAN
    -> Legacy applications

---

### 6.4 Port Mapping Explained

By default, container ports are internal.

Example:
Flask runs on port 5000 inside container.

Without mapping:
    -> Cannot access from browser

With mapping:
```bash
docker run -p 5000:5000 myapp
```

Format:
```bash
-p host_port:container_port
```
Flow:
Browser → Host:5000 → Docker NAT → Container:5000

---

### 6.5 Container-to-Container Communication

Best practice:
    -> Use custom bridge network
    -> Use service names instead of IP

Example:
Create network:
```bash
docker network create appnet
```

Run database:
```bash
docker run -d --network appnet --name db postgres
```

Run backend:
```bash
docker run -d --network appnet --name backend myapp
```

Backend connects using:
```bash
host="db"
```
Docker provides built-in DNS.

---

### 6.6 Inspecting Networks

List networks:
```bash
docker network ls
```

Inspect details:
```bash
docker network inspect appnet
```

Shows:
    -> Subnet
    -> Gateway
    -> Connected containers
    -> IP addresses

---

### 6.7 Docker Compose Networking

Docker Compose automatically:
    -> Creates a default network
    -> Connects all services to it
    -> Enables DNS-based communication

Example:
```bash
services:
  web:
    build: .
  db:
    image: postgres
```

Inside web container:
```bash
host = "db"
```
No manual network creation needed.

---

### 6.8 Real-World Architecture Example

Example: 3-tier application
Frontend → Backend → Database
Flow:
User → Frontend (port 80)
Frontend → Backend (internal network)
Backend → Database (internal network)

Best practice:
    -> Frontend exposed to host
    -> Backend not exposed
    -> Database not exposed
    -> All connected via custom bridge network

---

### 6.9 Security Best Practices
    -> Do not expose database ports publicly
    -> Use custom networks
    -> Avoid --network host unless required
    -> Use firewalls and security groups in cloud
    -> Separate internal and external traffic

---

### 6.10 Summary

Docker networking provides:
    -> Isolation
    -> Service discovery
    -> Secure communication
    -> Port forwarding
    -> Multi-host networking

Mastering Docker networking allows you to design:
    -> Microservices architecture
    -> Secure production systems
    -> Scalable cloud deployments

Docker networking is the backbone of containerized application communication.

===
===

## 7. Volumes & Persistence

Docker containers are **ephemeral by design**.
This means:
    -> When a container is deleted, its internal data is deleted.
    -> Any files written inside the container filesystem are lost.

This is a major problem for:
    -> Databases
    -> User uploads
    -> Logs
    -> Application state

To solve this, Docker provides **Volumes & other persistence mechanisms**.


### 7.1 Why Persistence Is Necessary

Imagine running a PostgreSQL container:
```bash
docker run -d postgres
```

If you:
```bash
docker rm -f <container_id>
```
All database data is gone.
In real production systems:
    -> Data must survive container restarts
    -> Data must survive container recreation
    -> Data must survive host reboots

That is where Docker Volumes come in.

***

### 7.2 Understanding Container Storage Layers

Docker uses a layered filesystem:
    1. Image layers (read-only)
    2. Container writable layer (temporary)

When a container runs:
    -> It gets a thin writable layer
    -> All changes go into that layer
    -> When container is removed → writable layer is deleted

Important: Container storage ≠ persistent storage

***

### 7.3 What Is a Docker Volume?

A **Volume** is a special directory managed by Docker.
It is:
    -> Stored outside the container filesystem
    -> Managed by Docker
    -> Independent of container lifecycle

Location (Linux):
```bash
/var/lib/docker/volumes/
```
Even if the container is deleted, the volume remains.

***

### 7.4 Types of Docker Storage

Docker provides 3 main storage mechanisms:
    1. Volumes (Recommended)
    2. Bind Mounts
    3. tmpfs mounts

Let’s understand each.

***

### 7.5 Docker Volumes (Recommended for Production)

#### 7.5.1 Creating a Volume

```bash
docker volume create myvolume
```

List volumes:
```bash
docker volume ls
```

Inspect volume:
```bash
docker volume inspect myvolume
```

#### 7.5.2 Using a Volume

```bash
docker run -d \
  -v myvolume:/data \
  nginx
```

Format:
```bash
-v volume_name:container_path
```

Now:
    -> Data written to /data
    -> Stored in myvolume
    -> Survives container deletion

---

#### Example: Database with Volume

```bash
docker run -d \
  --name postgres-db \
  -v pgdata:/var/lib/postgresql/data \
  postgres
```

Here:
    -> PostgreSQL stores data in /var/lib/postgresql/data
    -> Docker maps it to volume pgdata
    -> If container is deleted → database still exists

---

### 7.6. Bind Mounts

Bind mounts map a host directory directly to container.
Example:
```bash
docker run -v /home/user/app:/app nginx
```

Format:
```bash
-v host_path:container_path
```

Characteristics:
    -> Direct access to host filesystem
    -> Good for development
    -> Not portable across machines

Use case:
    -> Live code editing
    -> Development environments

Risk:
    -> Container can modify host files
    -> Less isolated than volumes

---

### 7.7 tmpfs Mounts

Stored in RAM only.
Example:
```bash
docker run --tmpfs /app nginx
```

Characteristics:
    -> Data stored in memory
    -> Faster access
    -> Lost when container stops

Used for:
    -> Sensitive temporary data
    -> Cache storage

---

### 7.8 Named Volumes vs Anonymous Volumes

#### 7.8.1 Named Volume

```bash
-v myvolume:/data
```
    -> Easy to manage
    -> Reusable
    -> Visible in docker volume ls

#### 7.8.2 Anonymous Volume

```bash
-v /data
```
    -> Docker auto-generates name
    -> Harder to manage
    -> Often used unintentionally
Best practice:
Use named volumes in production.

---

### 7.9 Sharing Data Between Containers

Multiple containers can use the same volume.

Example:

Container 1:
```bash
docker run -d -v sharedvol:/data container1
```

Container 2:
```bash
docker run -d -v sharedvol:/data container2
```

Now both:
    -> Read/write same data
    -> Useful for microservices

Example scenario:
    -> Backend writes uploads
    -> Nginx serves those uploads

---

### 7.10 Volume Lifecycle

Important behavior:
    -> Removing container does NOT remove volume
    -> Removing volume deletes data permanently

Remove volume:
```bash
docker volume rm myvolume
```

Remove unused volumes:
```bash
docker volume prune
```
Be careful — this deletes all unused volumes.

---

### 7.11 Docker Compose and Volumes

In docker-compose.yml:
```bash
services:
  db:
    image: postgres
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

Compose automatically:
    -> Creates volume
    -> Manages lifecycle
    -> Connects it to service

---

### 7.12 Real-World Production Design

Example: 3-tier application
    Frontend (stateless)
    Backend (stateless)
    Database (stateful)
Only database needs volume.

Best practice:
    -> Stateless services → no volume
    -> Stateful services → use named volumes
    -> In cloud → use external storage (EBS, EFS, etc.)

---

### 7.13 Volumes in Cloud Environments

In AWS:
    -> EC2 → EBS attached volume
    -> Kubernetes → Persistent Volume (PV)
    -> EFS → Shared network storage

Docker volumes can map to:
    -> Local disk
    -> Network storage
    -> Cloud block storage

---

### 7.14 Security Considerations

    -> Do not mount sensitive host directories
    -> Use read-only volumes when possible:

```bash
-v myvolume:/data:ro
```

    -> Backup volume data regularly
    -> Restrict container permissions

---

### 7.15 Summary

Containers are temporary.Data must not be.
Docker Volumes provide:
    -> Data persistence
    -> Isolation from container lifecycle
    -> Safe storage for databases
    -> Shared storage for services

If you remember one rule:

👉 Any production database must use a named volume.

===
===

## 8. Docker compose

Docker Compose is a tool used to define and manage **multi-container Docker applications** using a single YAML configuration file.

Instead of running multiple long `docker run` commands manually, Docker Compose allows you to:
    -> Define services
    -> Configure networking
    -> Configure volumes
    -> Set environment variables
    -> Control startup order
    -> Scale services

All using one file: 
```bash
docker-compose.yml
```

### 8.1 Why Docker Compose Is Needed

Imagine a real-world application:
    -> Frontend (React / Nginx)
    -> Backend (Flask / Node.js)
    -> Database (PostgreSQL / MySQL)
    -> Cache (Redis)

Running each manually:
    -> Create network
    -> Create volume
    -> Start database
    -> Start backend
    -> Link them
    -> Expose ports

This becomes complex quickly.
Docker Compose solves this by:
    -> Managing everything declaratively
    -> Creating default networks automatically
    -> Starting services in correct order
    -> Allowing entire stack control with one command

***

### 8.2 Docker Compose Architecture

When you run:
```bash
docker compose up
```

Compose does the following:
    1. Reads docker-compose.yml
    2. Creates network (if not defined)
    3. Creates volumes (if defined)
    4. Builds images (if build section exists)
    5. Starts containers
    6. Connects them together

All services run in an isolated project network.

***

### 8.3 Structure of docker-compose.yml

Basic structure:
```bash
version: "3.8"

services:
  service_name:
    image: image_name
    ports:
      - "host:container"
    volumes:
      - volume_name:/path
    environment:
      - KEY=value
    depends_on:
      - another_service

volumes:
  volume_name:

networks:
  custom_network:
```

Main Sections:
    -> version (optional in newer versions)
    -> services (mandatory)
    -> volumes (optional)
    -> networks (optional)

***

### 8.4 Services Section (Most Important)

Each service represents a container.
Example:
```bash
services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db

  db:
    image: postgres
    volumes:
      - pgdata:/var/lib/postgresql/data
```

Explanation:
web:
    -> build: builds image using Dockerfile in current directory
    -> ports: maps host port to container port
    -> depends_on: ensures db starts first

db:
    -> image: pulls postgres image
    -> volumes: persists database data

---

### 8.5 Build vs Image

Use build when:
    -> You have custom Dockerfile
    -> You want to build locally

```bash
build:
  context: .
  dockerfile: Dockerfile
```

Use image when:
    -> Pulling from Docker Hub or registry

```bash
image: nginx:latest
```

---

### 8.6 Networking in Docker Compose

By default:
    -> Compose creates a bridge network
    -> All services can communicate using service name as hostname

Example:
If service name is db, Backend connects using:
```bash
host = "db"
```
No need for container IP addresses.

Custom Network Example:
```bash
networks:
  mynet:

services:
  web:
    networks:
      - mynet
```

---

### 8.7 Volumes in Compose

Defined at bottom:
```bash
volumes:
  pgdata:
```

Attached inside service:
```bash
services:
  db:
    volumes:
      - pgdata:/var/lib/postgresql/data
```

Compose automatically:
    -> Creates volume
    -> Manages lifecycle

---

### 8.8 Environment Variables

Two ways:
Inline:
```bash
environment:
  - DB_USER=admin
  - DB_PASS=secret
```

Using .env file:
Create .env:
```bash
DB_USER=admin
DB_PASS=secret
```

Use in compose:
```bash
environment:
  - DB_USER=${DB_USER}
```
This improves security and flexibility.

---

### 8.9 depends_on (Startup Order)

```bash
depends_on:
  - db
```
This ensures:
    -> db container starts before web

Important: It does NOT wait for database to be fully ready.
For production: Use healthchecks.

---

### 8.10 Healthchecks

Example:
```bash
healthcheck:
  test: ["CMD", "pg_isready"]
  interval: 10s
  retries: 5
```

This allows:
    -> Monitoring container health
    -> Controlled startup behavior

---

### 8.11 Scaling Services

You can scale services:
```bash
docker compose up --scale web=3
```

This creates:
    -> web_1
    -> web_2
    -> web_3

Useful for:
    -> Load balancing
    -> Microservices testing

---

### 8.12 Important Docker Compose Commands

Start services:
```bash
docker compose up
```

Start in background:
```bash
docker compose up -d
```

Stop services:
```bash
docker compose down
```

View running services:
```bash
docker compose ps
```

View logs:
```bash
docker compose logs
```

Rebuild images:
```bash
docker compose up --build
```

---

### 8.13 Real-World Example (3-Tier App)

```bash
version: "3.8"

services:
  frontend:
    image: nginx
    ports:
      - "80:80"

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    depends_on:
      - db

  db:
    image: postgres
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

Architecture:
    -> Frontend → Backend → Database
    -> Database data persisted
    -> All connected through default network

---

### 14. Docker Compose vs Kubernetes

Docker Compose:
    -> Best for development
    -> Single-host
    -> Simple YAML

Kubernetes:
    -> Production-grade
    -> Multi-node cluster
    -> Advanced orchestration

Compose is often first step before Kubernetes.

---

### 8.15 Best Practices
    -> Use named volumes for databases
    -> Use .env files for secrets
    -> Keep services stateless when possible
    -> Avoid hardcoding container IPs
    -> Use healthchecks
    -> Separate dev and prod compose files

Example:
    -> docker-compose.yml
    -> docker-compose.prod.yml

---

### 8.16. Summary

Docker Compose allows you to:
    -> Define multi-container applications declaratively
    -> Automatically configure networking
    -> Manage persistent storage
    -> Control environment variables
    -> Start entire application stack with one command

If Docker runs one container,
Docker Compose runs the entire application ecosystem.

===
