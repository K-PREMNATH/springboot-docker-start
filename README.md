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
