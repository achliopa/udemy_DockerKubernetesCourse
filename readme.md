# Udemy Course - Docker and Kubernetes: The Complete Guide

* [Course](https://www.udemy.com/docker-and-kubernetes-the-complete-guide/)
* [Repository](https://github.com/StephenGrider/DockerCasts)

## Section 1 - Dive Into Docker

### Lecture 1 - Why use Docker?

* when we install SW we might get errors so we need to rerun the process. DOcker fixes that
* Docker makes easy to install and run sw in any platform or any machine
* installing sw on docker is as easy as `docker run -it redis`

### Lecture 2 - What is Docker?

* Docker is an ecosystem of systems and Sw:
	* Docker Client
	* Docker Server
	* Docker machine
	* Docker Image
	* Docker Hub
	* Docker Compose
* Docker is a platform and ecosystem around creating and running containers
* a docker image is a single file with all  the  deps and config required to run a program
* a docker container is an instace of an image. a container runs a program
* docker gets images from docker hub
* a container is a running program with its own isolated set of HW resources

### Lecture 3 - Docker for Mac/Windows

* we have already docker on our machine (Linux)
* in a Docker for Win/Mac package there are two tools.
	* Docker Client(Docker CLI) use to issue commands to
	* Docker Server(Docker Daemon) responsible for creating images, running containers etc

### Lecture 4 - Installing Docker on macOS
	
* steps: signup for docker hub account -> download/instal docker for mac -> login to docker ->  verify docker installation
* get community edition
* we test installation running `docker version` on terminal

### Lecture 5 - Docker Setup on Windows

* we choose to use Linux containers
* 