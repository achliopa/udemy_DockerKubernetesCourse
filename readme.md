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
* restart machine
* sign in
* use terminal

### Lecture  8 - Using the Docker Client

* we run a command in docker cli `docker run -hello-world`
* we see a message 'Hello from Docker!'
* THe actions logged by docker are
	* docker client contacted docker daemon
	* docker daemon pulled hello-world image from docker hub
	* docker daemon created a new container from that image that runs the executable that produces the output we read
	* docker daemon streamed the ourput to docker client which sent it to terminal
* Docker went to docker hub because it could not find the image locally
* docker server (daemon)d oes oall the heavy lifting
* after the run we have a local copy of the image on our local machine
* if we rerun the container the image is not downloaded

### Lecture 9 - What is a COntainer?

* in out machine the stack of running programs is:
	* Top Level: Processes running on the computer
	* Middle Level: System calls (running programs issue requests to kernel to interact with a piece of HW)
	* Low Level: Kernel - executes system calls and interacts with HW
	* HW (CPU,Mem,Periph,HDD)
* Say we have 1 version of Python on or HDD (v2). a process that needs python v2 will run while a process that needs python v3 will crash
* we can solve the issue with namespacing segments of HDD dedicated to each process. when a process makes a system call to read from HDD kernelcheck which process is making the call and directs it to its segment
* *Namespacing*: isolating resources per process (or process group) WHo?Process,Users What?HDD<NW<Hostnames,IPC
* *Control Groups* (cgroups): control the amount of resources What?CPU,HD I/O,MEM,NW BW
* Both feats together are used to isolate a process. the whole stack of an isolated process is what we call a container
* Container: A process + its isolated and dedicated resources using the kernel
* How we go from an image to a running container???
	* Image contains: an FS snapshot + startup command
	* when we run the imag (turn it into container): docker allocates an HDD segment to the container -> startup command is executed -> process is created -> process uses only its allocated HDD segment + aloocated resources

### Lecture 10 - How Docker is Running on our computer?

* namespacing and control goups are not natively supported by all OSs. only by LINUX
* to run docker on MacOS or Window docker setts up a Linux VM.
* so if we run `docker version` server will always be linux

## Section 2 - Manipulating Containers with the Docker Client

### Lecture 11 - Docker Run in Detail

* Creating and running a container from an image: `docker run <image name>`
	* docker: reference to docker client
	* run: try to create and run a container
	* <image name>: name of image to use for this container

### Lecture 12 - Overriding Default Commands

* Creating and running a container from an image overriding the default run command: `docker run <image name> command`
	* command: default command override
* example: `docker run busybox echo hi there` 'echo hi there' is the override
* command can be unix call like 'ls'. 
* busybox is just a cycle burner does nothing
* if i run `docker run busybox ls` i get root dir of the default docker image FS snapshot
* every image comes withy its FS snapshot
* i cannot run `docker run hello-world ls` as these programs do not exist in the hello-world default FS snapshot. they do in busybox that why i can run them

### Lecture 14 - Listing Running Containers

* List all running containers: `docker ps`
	* docker: reference the docker client
	* ps: list all running containers
* ps gives  a lot of info on the running container. id, image, command, status, random name
* to see all contianers that were created and run on our machine we can use `docker ps --all`

### Lecture 15 - Container Lifecycle

* we saw with ls that containers that stoped are still identified in our system
* docker run = docker create + docker start
	* docker create: creates the container `docker create <image name>` and printouts a <container id>
	* docker start: starts the container (identified by its id) `docker start <container id>`
* create sets up the FS snapshot on the HDD
* start executes the startup command but by default does not attach to the process so we dont see any output on terminal. but it runs
* to see output on terminal from the process we need to attach to it when starting it using -a `docker start -a <container id>`
* we dont need to write all the container id just the staritng part of it that is uniquely identifying. auto complete with TAB applies

### Lecture 15 - Restarting Stopped Containers

* if i run `docker ps --all` after a container has stoped i see it in the list with STATUS exited
* a stopped contianer can start again. we run `docker start` using its id
* once we have created a container with its default or overriden command we cannot change it. its in its blueprint as shown in the running conatiners list

### Lecture 16 - Removing Stopped Containers

* clean up is critical to free space on host machine
* we can use `docker system prune` to clean up my system of:
	* stopped containers
	* unused nw resources
	* build cache (downloaded images)
	* unused images

### Lecture 17 - Retrieving Log Outputs

* Get logs from a container: `docker logs <container id>`
	* logs: get logs
* this is an alternative to staritng the container with the -a flag.
* we can use log even after the container has been started, even after it has exited
* docker logs gets the logs of the running container up to that point in time
* docker logs DOES NOT rerun the container

### Lecture 18 - Stopping Containers

* Stop a Container: `docker stop <contianer id>`
	* sends a SIGTERM signal to the reunning process
	* SIGTERM is equivalent to ctrl+c (stop as soon as possible)
* Kill a Container: `docker kill <contianer id>`
	* sends a SIGKILL signal to the running process
	* SIGKILL is equivalent to kill command (stop NOW)
* stop command is always preferable
* docker automatically sends a kill command if docker stop does not stop the process in 10 seconds
* some processes do not listen to SIGTERM (aka stop command)

### Lecture 19 - Multi-Command Containers

* we start `redis-server` locally (not from docker)
* to attach to the server and manipulate data or see logs we run `redis-cli`
* we run redis on docker `docker run redis`
* running `redis-cli` on the host machine does not see the redis-server in the docker container
* redis is running int he container so is insulated from the outside world
* there is no free access from outside the container to the inside
* we need to be able to run a second command in the container

### Lecture 20 - Executing Commands in Running Containers

* Execute an additional command in a container: `docker exec -it <container id> <command>`
	* exec: run another command
	* -it: allow us to provide input to the container
	* <container id>: id of the (running) container
	* <command>: command to execute
* if we skip -it flag the command is executed but we cannot input any  text. it is running in the background. in our case redis-cli without an input attached closes

### Lecture 21 - THe purpose of the IT flag

* processes in docker run in a linux environment.
* any process running in a linux environment. has 2 communication channels attached to it. STDIN, STDOUT, STDERR
* STDIN is standard input (what we type in the terminal)
* STDOUT is standard output (what shows up on terminal screen)
* STDERR is standard error (shown up in terminal string)
* typically STD comm channels are attached to the terminal that invoked the process unless programmatically directed elsewere
* -it is -i and -t combined
	* -i: attaches processes ind ocker stdin to terminal
	* -t: makes text look pretty in terminal

### Lecture 22 - Getting a Command Promt in a COntainer

* exec command is also used to give us terminal (shell) access to arunning container
* in that way we can write commands without the need to issue new docker exec commands
* we do this by executing the sh command in a running container `docker exec -it <container id> sh`
* what we get is a terminal in the running container linux running instance
* we can run the command multiple times for multiple shells
* sh shands for shell. other command processors are: bash, powershell, zsh
* almost all containers have sh installed. some more bulky container include bash as well

### Lecture 23 - Starting with a Shell

* we can use the -it flag with run command and start a shell immediatley after the container starts up
* the downside of running `docker run -it <container id> sh` is that the primary process does not start up automatically. just the command we spec (sh). so we have to start the primary process our selves

### Lecture 24 - Container Isolation

* to exit from a container shell we type ctrl+D or exit
* 2 separate containers by default do not share their FS
* to show case we start 2 busybox containers in shell mode `docker run -it busybox sh`
* in the first we touch the file in its fs. the file is not existent in the orthers fs

## Section3 - Building Custom Images Through Docker Server

### Lecture 25 - Creating Docker images

* to make our own image that will run in our container we have to:
	* Create our Dockerfile: a config file that defines how our container should behave
	* then we provide the dockerfile to the docker cli
	* cli sends it to the docker deamon to build an image
	* we have our usable image to run
* in a dockerfile:
	* we specify a base image (existing image)
	* run additional commands to install additional programs
	* specify a command to run on container startup

### Lecture 26 - Building a Dockerfile

* 


