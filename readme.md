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

* our first custom dockerfile will create an image that runs redis-server 
* we create a project dir for the image /redis-image
* we cd into it and open our text editor
* we create anew file named 'Dockerfile'
* comments in dockerfile are startign with #
* first we use an existinf docker image as a base `FROM alpine`
* then we download and install a dependency `RUN apk add --update redis`
* finally we tell the image what to do when it starts as a container `CMD ["redis-server"]`
* with the dockerfile ready we build it: we run `docker build .` in the project root folder where Dockerfile resides
* the image builds and we get a message in the end `Successfully built 08960804125d`
* we cp the id. this is the imagege id. we use it to run our customimage `docker run 08960804125d`
* redis is running OK

### Lecture 27 - Dockerfile teardown

* 'FROM' 'RUN' 'CMD' : instructions telling docker server what to do
* 'alpine' 'apk add --update redis' '["redis-server' : arguments to the instructions
* `FROM alpine` sets the image it will use as a base

### Lecture 28 - What's a Base Image?

* ANALOGY: writing a dockerfile == being given a computer with no OS and being told to install Chrome. what would we do??
	* install an OS => `FROM alpine`
	* start up default browser ------------------
	* navigate to chrome.google.com
	* download installer        => `RUN apk add --update chrome`
	* open file/folder explorer
	* execute installer-------------------------
	* execute chrome runnable `CMD ["chrome`]`
* why do we use alpine as base image??? we choose this image because it suits our needs.
* if we used ubuntu we would use `apt-get install chrome`

### Lecture 29 - The Build Process in Detail

* when we run build command the '.' is the build context (files we want to includ ein build relative to the PWD)
* each line in dockerfile produces a build step
* for every step in teh build an intermediate container is used (then discarded)
* fr step 2 docker server looks at previous step and runs a temp container to host the command
* after the installation container is stopped and docker takes a filesystem snapshot out of it and saves it as a temp image with redis to be used int he next step (3/3)
* again step 3 runs a temp container out of the previous image.. in the end the output of step 3 (fs snapshot + primary commad) are the final build image 

### Lecture 31 - Rebuilds with Cache

* the reason of dockers great perforance when building new image: 
	* every step outputs its own image
	* if we add an other step (e.g RUN apk add --update gcc) and rebuild it goes very fast
	* this is why the temp images of each step are cached. so no recreated
	* even  virgin base image is not downloaded its local
	* it does not even go to step one. uses cached image of step 2 and goes to 3
* reversing steps order invalidates cach
* put your changes down the line to save time

### Lecture 32 - Tagging an Image

* our image has a generated meaningless image name
* Tagging an image: `docker build -t <dockerID>/<project>:<version> .`
	* -t : tag the image flag
	*  <dockerID>/<project>:<version> : image tag following the docker  standard
	* . : specifies the directury of files/folders to  use for the build (. == current dir)

### Lecture 33 - Manual image Generation with Docker Commit

* the docker build process use images to build containers and we use these containers to generate images
* we can manualy do the same. start a container ->  run a  command in it (install sthing) and generate an image out of it
```
docker run -it alpine sh
>> apk add --update redis
```
* on another terminal we run `docker commit -c 'CMD["redis-server"]' <container id>` . this command takes a snapshot of the runnign container genrating an image passing in the primary command (-c flag). we cane even tag the generated image

## Section 4 - Making Real Projects with Docker

### Lecture 34 - Project Outline

* in this section we  will do a new project: create a tiny nodeJS web app => wrap it up in a docker container => access the app from a browser running on our machine
* steps in our implementation:
	* create nodeJS webapp
	* create a Dockerfile
	* build image from dockerfile
	* run image as container
	* connect to web app from browser

### Lecture 35 - Node Server Setup

* we make a project dir /simpleweb and cd into it
* we add a package.json file which is a bare minimum one. just express and a start script
```
{
	"dependencies": {
		"express": "*"
	},
	"scripts": {
		"start": "node index.js"
	}
}
```
* we add app root js file index.js in root dir as well with boilerplate express js code
```
const express = require 'express';

const app = express();

app.get('/', (req,res)=>{
	res.send("Hi from node");
});

app.listen(3000, ()=>{
	console.log("Server is running on port 3000...");
})
```

* so no `npm init` (no node_modules dir) no fancy code yet

### Lecture 37 - A few planned Errors

* to start node we need to install dependencies `npm install` and start the server `npm start`
* both assume npm package is installed apart from node
* we start thinking how to setup our Dockerfile
	* Specify base image: FROM alpine
	* Run some commands to install aditional programs: RUN npm install
	* Specify a command to run on container setup: CMD ["npm","start"]
* we expect a fail...  alpine is not expected to have node and npm installed
* we add the dockerfile with these contents and run docker build... it fails. npm is not found

### Lecture 38 - base Image Issues

* we use alpine as base image. this is a small image that does not suitalbe for owr needs.
* we need an image with node in and npm. maybe node. OR add commands to install node and npm on alpine
* in [Dockerhub](http://hub.docker.com) node image has  node preinstalled. we see all the available versions eg. node:6.14 or node:alpine (most compact version)
* in Docker world Alpine means Minimum
* we build the new Dockerfile
```
# specify base image
FROM node:alpine
## install some dependencies
RUN npm install
# default command
CMD ["npm","start"]
```
* we get errors when running the comman npm install... the files in our machine are missing from teh container
* Files on our local machine are NOT by default avialble in ontainer FS (HD segemtn)
* so container cannot find package.json
* we need to allow use of local files inthe build container in the Dockerfile

### Lecture 40 - Copying Build Files

* To copy files from local machine to container: `COPY ./ ./` COPY from to
	* COPY : dockerfile commande for copying files
	* ./ : (1) path to folder to copy from on our machine relative to build context.
	* ./ : (2) place to copy stuff to inside the container 
* build context? PWD altered by docker build <path> . in our case where Dockerfile resides
* we put this command before the `RUN npm install`
* image is created... we tag it `docker build -t achliopa/simpleweb .`
* we run it: `docker run achliopa/simpleweb`
* it runs.
* we visit with borwser localhost:3000 and get an error... we need to map ports to container

### Lecture 41 - Container Port Mapping

* our  browser makes a request to localhost:8080 (on our machine)
* container has its own se of ports that by default do not accept incoming traffic
* we need to set explicit port mapping  to enable port forwarding to the localhost
* this is for incoming comm. container can talk to the outside world (it does it to get dependencies)
* Port mapping is set in Docker Run command
* Docker Run with Port mapping: `docker run -p 3000:3000 <image id>`
	* -p : port forwarding flag
	* 3000 : (1) localhost port to route from
	* 3000 : (2) contatiner port to route to

* ports do not have to be identical
