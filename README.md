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

Lesson 2 – Docker Architecture
Before learning more commands, you should understand how Docker works internally.
docker run nginx
But what actually happens behind the scenes?

Let's break it down.
                You
                 │
                 │ docker run nginx
                 ▼
        +------------------+
        | Docker CLI       |
        +------------------+
                 │
                 │ Request
                 ▼
        +------------------+
        | Docker Engine    |
        +------------------+
           │          │
           │          │
     Images Store   Containers

There are three important components:

Docker CLI
Docker Engine
Docker Hub
1. Docker CLI
        CLI means Command Line Interface.
        Whenever you type:
        docker images or docker ps
        you're talking to the Docker CLI.
        Think of it as the remote control.
        The remote control doesn't play the movie.
        It only sends commands.

2. Docker Engine
        This is the brain.
        Docker Engine actually does the work.
        It can
        Build images
        Create containers
        Stop containers
        Remove containers
        Create networks
        Create volumes
        
        When you execute
        docker run hello-world
        the CLI simply says
        "Docker Engine, please start a container."
        The Engine does everything.
3. Docker Hub
        Docker Hub is like GitHub...
        But instead of storing source code...
        It stores Docker Images.
        
        Example:
        Docker Hub
        
        ├── nginx
        ├── mysql
        ├── redis
        ├── postgres
        ├── mongo
        ├── eclipse-temurin
        └── hello-world
        
        When you ran
        docker run hello-world
        Did you ever download hello-world manually?
        No.
        Docker automatically did this:
        
        Docker Hub
        ↓
        Download Image
        ↓
        Local Machine
        ↓
        Create Container
        
        Let's verify this
        Run
        docker images
        You should see
        REPOSITORY     TAG      IMAGE ID
        hello-world    latest   xxxxxxxxx
        Question:
        Did you build this image?
        No.
        Docker downloaded it from Docker Hub.

        What happens the second time?
        docker run hello-world
        again.

        Does Docker download it again?
        No.
        Why?
        Because the image already exists locally.
        Docker simply creates another container.
        Flow:
   First Time

        docker run hello-world
        ↓
        Image not found
        ↓
        Download
        ↓
        Create Container
        ↓
        Run

Second time
        docker run hello-world
        ↓
        Image already exists
        ↓
        Create Container
        ↓
        Run

Docker Image Cache
Docker stores images locally.
Run:
docker images
You are looking at your local image cache.

Very Important Rule
A Docker container runs as long as its main process (PID 1) is running.
This is one of the most important concepts in Docker.

| Application   | Main Process        | Container Status  |
| ------------- | ------------------- | ----------------- |
| `hello-world` | Print message       | Exits immediately |
| Spring Boot   | `java -jar app.jar` | Running           |
| Nginx         | `nginx`             | Running           |
| SQL Server    | `sqlservr`          | Running           |
| Redis         | `redis-server`      | Running           |

Suppose you execute:
docker run my-springboot-app
and immediately afterward:
docker ps
returns no running containers.
What are the first three Docker commands you would run to investigate why the application didn't stay running?

This is the real workflow used by developers

docker run my-app
        │
        ▼
Container exited
        │
        ▼
docker ps -a
        │
        ▼
Find Container ID
        │
        ▼
docker logs <container-id>
        │
        ▼
Read Error
        │
        ▼
Fix Application
        │
        ▼
Build Image Again
        │
        ▼
Run Again

important
Many beginners think:
"Docker crashed."
Most of the time, Docker is perfectly fine.
It's actually the application inside the container that exited.

Docker Engine ✅ Healthy

Container ✅ Started
↓
Spring Boot ❌ Failed to connect to SQL Server
↓
Java Process Exited
↓
Container Exited


Container Lifecycle (States)
A container has a lifecycle.

The simplest view is:
Created
   │
   ▼
Running
   │
   ▼
Exited

Created

Docker has created the container.
The filesystem exists.
Networking is prepared.
But the application hasn't started yet.
Think of it as:
House built
Nobody living inside yet

Running

Docker starts the main process.
For Spring Boot:
PID 1
↓
java -jar app.jar

Now the container is alive.
docker ps
shows:
STATUS
Up 5 minutes

Exited

The main process finishes.
For example:

java -jar app.jar
↓
Database connection failed
↓
JVM exits
↓
Container exits

or

hello-world
↓
Print message
↓

Exit

Now:
docker ps
shows nothing.

But:
docker ps -a
shows:
Exited (1)

Lifecycle with Commands
               Docker Image
                     │
                     ▼
             docker create (docker create nginx)
                     │
                     ▼
                Created
                     │
          docker start / docker run (docker start <container>)
                     │
                     ▼
                 Running
                     │
     PID 1 exits OR docker stop (docker stop <container>)
                     │
                     ▼
                  Exited
                     │
              docker start
                     │
                     ▼
                 Running
                     │
               docker rm
                     │
                     ▼
                 Deleted


Container Port vs Host Port
This is one of the biggest sources of confusion.

Suppose your Spring Boot app listens on:
application.properties
server.port=8080

Inside the container:
Spring Boot
↓
Listening on 8080
Nobody outside the container can access it yet.

Port Mapping
Now run:
docker run -p 8080:8080 my-app

Now run:
docker run -p 8080:8080 my-app
I want you to understand it.
The syntax is:
HOST_PORT : CONTAINER_PORT
So -p 8080:8080

Laptop

localhost:8080
      │
      ▼
Container
8080

Your browser connects to your host, and Docker forwards the traffic into the container.

Another example
docker run -p 9090:8080 my-app

Browser
localhost:9090
      │
      ▼
Container
8080

