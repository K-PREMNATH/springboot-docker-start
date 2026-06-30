# springboot-docker-start

We'll build one project throughout the course.
study-tracker
│
├── user-service (Spring Boot)
│
├── SQL Server
│
├── Redis
│
├── Docker
│
├── Docker Compose
│
└── Kubernetes (later)

By the end, you'll understand not just Docker commands, but how Docker is actually used in a Spring Boot microservices environment.

Module 1 - Docker Fundamentals
Lesson 1 - Why Docker Exists
Before Docker
Imagine you develop this application:
Spring Boot
↓
Java 21
↓
Maven
↓
SQL Server Driver
↓
Windows

It works perfectly on your laptop.
You send it to another developer.

They get:
Java version mismatch
Port already in use
Missing environment variables
Different Maven version
Different OS
Missing SQL Server

Everyone says:
"It works on my machine."
This was one of the biggest problems Docker was created to solve.

What is Docker?

Docker packages everything your application needs into a container.
Think of it as a shipping container.
A shipping container can carry:
TVs
Furniture
Food
Ships, trains, and trucks don't need to know what's inside—they just transport the container.
Docker works the same way.
Container

┌────────────────────┐
│ Spring Boot App    │
│ Java Runtime       │
│ Libraries          │
│ Configuration      │
│ Dependencies       │
└────────────────────┘
As long as Docker is installed, that container behaves the same on your laptop, a colleague's machine, a test server, or the cloud.

Important Terms
Image
An image is a blueprint or template.
An image is read-only and doesn't run by itself.

Container
A container is a running instance of an image.
A Docker image is like a Java class, and a Docker container is like an object created from that class.

Dockerfile
A Dockerfile is simply a text file that tells Docker how to build an image.

Docker Engine
Docker Engine is the software running on your computer that:

Builds images
Starts containers
Stops containers
Manages networks
Manages volumes

When you run:
docker run nginx
the Docker CLI sends that request to the Docker Engine, which downloads the image (if needed) and starts the container.

Image vs Container
| Image                      | Container                  |
| -------------------------- | -------------------------- |
| Blueprint                  | Running application        |
| Read-only                  | Read/write while running   |
| Can create many containers | Created from one image     |
| Stored on disk             | Runs in memory and on disk |

Example:
Image
springboot:v1

        │
        ├─────────────┐
        │             │
        ▼             ▼

Container A     Container B
One image can create multiple containers.



| Command | What it shows |
|---------|---------------|
| **`docker version`** | Client and server (daemon) version numbers, API versions, Go version, build dates, and OS/Arch details |
| **`docker info`** | System-wide Docker stats: total containers (running/paused/stopped), images count, storage driver, cgroup driver, kernel version, OS, memory/CPU resources, registry settings |
| **`docker images`** | All locally stored Docker images with their repository name, tag, image ID, creation date, and size |
| **`docker ps`** | **Only running** containers with container ID, image used, running command, creation time, status (Up), port mappings, and container name |
| **`docker ps -a`** | **All containers** (running, stopped, exited, created, paused) — same columns as `docker ps` but includes containers in any state |

---

### Summary of key distinctions

- **`docker ps`** = active containers only
- **`docker ps -a`** = all containers (active + inactive)
- **`docker images`** = image files (not containers)
- **`docker version`** = software version info
- **`docker info`** = system resources + configuration

docker run hello-world
Docker creates a new container.

For example:
docker run hello-world
docker run hello-world
docker run hello-world

docker ps -a
You might see something like:

CONTAINER ID   IMAGE         COMMAND    STATUS
a1b2c3d4e5f6   hello-world   ...        Exited
b2c3d4e5f6g7   hello-world   ...        Exited
c3d4e5f6g7h8   hello-world   ...        Exited

Notice:

All three containers use the same image (hello-world).
Each has a different Container ID and a different name.
Each is a separate container.

Image
   │
   ├── docker run
   ▼
Container 1

Image
   │
   ├── docker run
   ▼
Container 2

Image
   │
   ├── docker run
   ▼
Container 3

What actually happens when you run docker run?

Internally, Docker performs these steps:

docker run hello-world
Checks whether the hello-world image exists locally.
If it doesn't, downloads it from Docker Hub.
Creates a new container from that image.
Starts the container.
Runs the command inside it.
The command finishes.
The container stops (status becomes Exited).

The container is not deleted automatically—it remains on your machine unless you remove it.

How can I avoid creating lots of stopped containers?
If you don't need to keep them, use:
docker run --rm hello-world

An image is a template, and every docker run creates a new container from that template.
