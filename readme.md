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
* we can visit our running app now 

### Lecture 42 - Specifying a Working Directory

* we will run our built image starting a shell inside it to do dbugging `docker run -it achliopa/simpleweb sh` we dont do port mapping as we dont need to visit app for debugging
* we run ls and we see that our peoject files are added to the alpine linux root folder /
* this is not good practice.. we might overwritte linux kernel folders
* we need to define a working dir in the Dockerfile. w use the command `WORKDIR /usr/app`
* what this command does? it enforces that any following command will be executed relative to this path in the container of the image to be built. so `COPY ./ ./` is equivalent to `COPY ./ ./usr/app` if we didnt use the command
* if folder does not exist will create it
* usr/app is a safe place for our app we could use /home instead or /var
* we rebuild it (we changed the order of commands so no caching)
* if we open shel in the container it takes us directly to /usr/app
* we can run our app with port forwarding and visit browser
* in that running container we get its id and run `docker exec -it <cont id> sh` to open a terminal inside

### Lecture 43 - Unnecessary Rebuilds

* with our app running we visit our webpage and see content
* we change the index.js in the project folder in our local machine  modifying the console.log
* as we expect when we refreh page we see no change... we have to rebuild and rerun image
* step 3 'COPY ./ ./' copies all contents of project folder again even if the change is on 1 file 
* we also dont need to run 'CMD npm install' as  no change to package.json was made
* also we should try excluding node_modules from the COPY (faster)

### Lecture 44 - Minimizing Cache Busting and Rebuilds

* npm install command cares about package.json
* we can solve the unecessary reinstall by grouping COPY and CMD of dependent files
```
COPY ./package.json ./
RUN npm install
COPY ./ ./
```
* this approach is node specific
* the result is that any change on code will not trigger npm install

## Section 5 - Docker Compose with Multiple Local Containers

### Lecture 45 - App Overview

* we will create a docker container with a webapp that displayes the number of visits to the webpage
* to build it we will need 2 components. a node(express) web app and a redis server (in memory data storage) 
* one possible approach is to use a single container with node and redis running inside. if the app receives a lot of traffic this will be abottleneck.
* as we get more traffic we will need more web servers (more instances of docker containers)
* redis server will run multiple times as well. so between redis servers it will be inconsisency on the data they store... so whichever conteiner we connect to will give a different number
* so a scaled up app will have mulptiple docker node containers and a simngle docker redis container
* first we will build an app with 2 containers and then we will scale  it up

### Lecture 46 - App Server Code

* we make a new project dir /visits and cd into it
* we will create a package.json file
```
{
	"dependencies": {
		"express": "*",
		"redis": "2.8.0"
	},
	"scripts": {
		"start": "node index.js"
	}
}
```
* redis is a lib for connecting redis -server to node
* we also create an index.js file
```
const express = require("express");
const redis = require("redis");

const app = express();
const client = redis.createClient();
client.set('visits',0);

app.get('/', (req,res)=> {
	client.get('visits', (err,visits)=>{
		res.send('Number of visits is '+ visits);
		client.set('visits', parseInt(visits)+1);
	});
});

app.listen(8081, ()=> {
	console.log('Listening on port 8081');
});
```
* we do not config redis client (ports etc) yet
* data come from redis as strings we need to parse tehm to int  to make operations

### Lecture 47 - Assembling a Dockerfile

* we compose a dockerfile to compose the node app so essentially is same with what we wrote for simpleweb app (relative paths are different but result is same)
```
# specify base image
FROM node:alpine
## install some dependencies
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
# default command
CMD ["npm","start"]
```
* we build it `docker build -t achliopa/visits .`

### Lecture 48 - Introducing Docker Compose

* we run our newly created image of the node server of our app wiith `docker run achliopa/visits`
* we get a bunch of errors as node cannot connect to redis. redis lib tries to connect to redis default port on the localhost '127.0.0.1:6379'
* our first thought is to run a redis container.. we do it `docker run redis` no customization
* even if we have now a running docker container with redis on our host if we rerun our node docker container it still cannot connect...
* docker containers need to be set in the same docker network cluster to talk to each other
* localhost in docker container refers to the alpine linux running instance in the container
* to setup a network infrastructure for docker containers we have 2 options:
	* use Docker CLI's Network Features
	* use Docker Compose

* using docker cli network feats to connect containers together
* docker-compose is a separate toolused much more frequently to do the job.
* it gets installed with docker on our machine 
* docker-compose is a separate cli tool
	* it is used to start-up multiple docker containers at the same time
	* automates some of the long-winded arguments we are passing to docker run

### Lecture 49 - Docker Compose Files

* using docker-compose we use the same docker cli commands we use to run containers
* we encode thes e commands into a YAML file in our project folder. the file is called 'docker-compose.yml'
* cli commands are not cp'ed in docker-compose.yml. a sp[ecial syntax is used]
* for our app the YAML file will be like:
```
# Here are the created containers for the build
	redis-server
		# make it using 'redis' image
	node-app
		# make it using the Dockerfile in the current dir
		# map port 8081 to 8081
```
* we add a docker-compose.yml file in our project root
* we start to implemetn it
* first we set the yaml version `version: '3'`
* then we list the services to be used. 
```
services:
	redis-server:
		image: 'redis'
	node-app:
		build: .
		ports:
			- "4001:8081"
```
* services means containers (type of)
* with image: we specify an available imge to use (localy or in dockerhub
* `build: .` means to search in the durrent dir for a Dockerfile and use it to build the image for the service-container
* indentation is important for YAML files
* - in YAML means an array
* "8081:8081" => <port on our machine>:<port in container>

### Lecture 50 - networking with Docker Compose

* we still have put no config for the network infrastructure
* actually we dont need it. because we pute them in teh same docker-compose file docker-compose will put them in the same network infrastructure (virtual vlan) without the need to open ports between them (SWEET!!)
* as the services are in different containers aka vms. we need to add configuration in redis node lib createCLient() connection method in index.js
* we dont know theys IPs in the virtual vlan so we refer them by service name.
```
const client = redis.createClient({
	host: 'redis-server',
	port: 6379
});
```

### Lecture 51 - Docker Compose Commands

* an equivalent of `docker run <image>` is `docker-compose up` which creates instances of all services specked in the YAML file
* if we want to rebuild the images of the servicdes in the YAML and run them we use `docker-compose up --build` 
* we run `docker-compose up` in our project folder.
* the log says that a netrwork 'visits-default' is created with default drivers
* this network joins images together
* the images created are <project_folden_ame>_<service_name>_<number> eg. visits_redis-server_1
* we run localhost:4001 in browser and our app works ok

### Lecture 52 - Stopping Docker Compose Containers

* we can run a container from an image without attaching to its STOUT with flag -d `docker run -d <image>` . it executed in teh background
* with docker-compose we start multiple containers. is a pain to stop them one by one
* we can stop all with `docker-compose down`
* flag -d (run in background works also for docker-compose e.g `docker-compose up -d`)

### Lecture 53 - Container Maintenance with Compose

* what we do if a container started with compose crashes???
* to test it we add a crash every time someone visits the toute '/crash'
```
const process = require('process');
app.get('/crash',(req,res)=>{
	process.exit(0);
	});
```
* we start our containers and visit localhost:4001/crash.
* our log says visits_node-app_1 exited with code 0
* if we run `docker ps` we see that only visits_redis-server_1 runs

### Lecture 54 - Automatic Container Restarts

* we will look how to make docker-compose maintain and auto restart our containers
* in our process.exit() call we pass 0 
* in unix status codes 0 means that we exited but everything is OK, exit status code >0 means something went wrong
* to make containers restart we will add 'restart policies' inside our docker-compose .yml file:
	* "no": never attempt to restart this contatiner if it stops or crashes
	* always: if this container stops 'for any reason' always attempt to restart it
	* on-failure: only restart5 if the container stops with an error code
	* unless-stopped: always restart unles we (the developers) forcibly stop it
* no restart policy is the default one (if we dont specify otherwise)
* in node-app service we add a new policy `restart: always`
* the 'restart' plocy is per service
* we test . our container stops and becomes green again.
* when restarting docker reuses the stoped container so does not create a new instance
* no is put in quotes 'no' because in YAML no is interpreted as false
* in webapps we usually choose always
* in a worker process we choose on-failure

### Lecture 55 - Container Status with Docker Compose

* currenlty we use docker cli to check contianer status with `docker ps`
* the docker-compose equivalent is `docker-compose ps` it will return status of the containers in the docker-compose file in our current dir (project folder)
* docker-compose commands need to go to docker-compose.yml file to execute. if we run it from the smae dir we dont need to specify the route to the docker-compose file

## Section 6 - Creating a Production-Grade Workflow

### Lecture 56 - Development Workflow

* we will see the entire workshop of buildng an app on docker and publishing it on a hosting service
* we will go through the whole workflow: development -> testing->deployment cycle

### Lecture 57 - Flow Specifics

* we will create a github repo: a central point of coordination for the code we write and deploy
* our github repo will have 2 branches: feature branch (development branch) and master branch (cclean working ready to deploy code)
* we will pull the code from the features branch (like joining a dev tema)
* we will make changes to the codebase
* we will push back to feature branche (neven on MASTER)
* we will make a Pull request from feature to master (to take all changes in features branch and merge them with master branch)
* this pull request will trigger a series of actions:
	* a workflow we will setup will take the app and push it to Travis CI (run tests)
	* travis after tests pass will take the codebase and push it over to AWS hosting

### Lecture 58 - Docker's Purpose

* docker is not mentioned in the flow. is not needed
* docker is a tool that will make some of these steps a lot easier

### Lecture 59 - Project Generation

* we will generate a project and wrap it up in a docker container. so no custom code
* our app will be a React frontend
* we should have node installed locally

### Lecture 60 - More on Project Generation

* we install globally the create-react-app tool (we have it) `npm install -g create-react-app` if we dont have it already
* inside our projects folder we create a new app `create-react-app frontend`
* we go in the project root folder

### Lecture 61 - Necessary Commands

* the 3 main commands we need are
	* npm run start: starts up the dev server. For development use only
	* npm run test: runs tests associated with the project
	* npm run build: builds a production version of the application
* we should run this command before running on host the scripts
```
echo 999999 | sudo tee -a /proc/sys/fs/inotify/max_user_watches && 
echo 999999 | sudo tee -a /proc/sys/fs/inotify/max_queued_events && 
echo 999999 | sudo tee -a /proc/sys/fs/inotify/max_user_instances && 
watchman shutdown-server
```
* we run `npm start`
* we run `npm run test`
* we run `npm run build`
* build produces a build/ folder inside static/ folder th e js file is our app packaged and ready for deployment together with the html file
* start command launches the dev server

### Lecture 62 - Creating the Dev Dockerfile

* in the frontend project folder we create a new Dockerfile and name it Dockerfile.dev
* this is only for development. (command will be npm run start)
* later in the workflow we will add a Dockerfile named Dockerfile for production (CMD will npm run build)
* we start building the dev dockerfile like our previous node app
```
FROM node:alpine

WORKDIR '/app'

COPY package.json .
RUN npm install
COPY . .

CMD ["npm","run","start"]

```
* we need to run a dockerfile with a customname. we ll add the -f flag specifiyng the dockerfile to use `docker build -f Dockerfile.dev .`

### Lecture 63 - Duplicating Dependencies

* we come back into a problem we foresaw some sections ago...
* we have already run 'npm start'  in our local machine so npm modulkes were installed in the project dir in a folder anmed node_modules. this folder is big
* when we issued COPY . . in our dockerfile we copied this entire folder from local workingindir to the container. so build command issues a warning for size
* we need fast build so we should ignor ethis folder as it is recreated in the container
* a dumb solution is to delete the node_modules folder before buildign the image

### Lecture 64 - Starting the Container

* we cp the image id and  use it to run it with `docker run`
* the log says we can view the app at localhost:3000 and on our machine at 172.17.0.2:3000
* localhost:3000 does not work but 172.17.0.2:3000 works on our host machine browser
* to access the app at localhost on our machine we need to map ports so we run `socker run -p  3000:3000 id`
* we make a change. we mod the text in App.js. we refresh the page and no change in the app.
* the change we did was in our local copy not in the container we need to rebuild or find an automated solution

### Lecture 66 - Docker Volumes

* volumes are a way of mapping a  local HD folder in the container
* so far we have seen only how to copy data from localhost to container.
* with volumes we set a reference in the container that maps back to the localhost
* seting volumes is more difficult than COPY
* the docker run command with volumes is like: `docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app <image_id>`
	* -v /app/node_modules : put a bookmark on the node_modules folder
	* -v $(pwd):/app : map the pwd (current dir) into the /app folder
* the second switch is the one doing the mapping we want
* the first switch dows not have a colon. if we omit it and run the command `docker run -p 3000:3000 -v $(pwd):/app e84a4e3ebcf6` we get a node error 

### Lecture 67 - Bookmarking Volumes

* why we saw the error? as node libs (node_modules) are not found. the libs are not found why? because container is mapping to our local dir and in our local dir we have deleted the node_modules
*  to bookmark a volume (not map it to the local machine) we write the path in container but skip the colon and the first part (path on our machine) `-v /app/node_modules` so we make it a placeholder in teh container but dont map it to the localmachine
* we  run the full command `docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app e84a4e3ebcf6` and app start instantly
* now our local code changes get instantly reflected in the running app inside the docker container dev server. COOL!!!! (auto refresh is a create-react-app feat)

### Lecture 68 - Shorthand with Docker Compose

* writing docker cli command with volumes is very long....
* we will use docker-compose to fix that.
* docker-compose is legit even for single container  builds as it simplifies the commands
* we assemble a dodker-compose.yml file for our project
```
version: '3'
services:
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - /app/node_modules
      - .:/app
```
* docker-compose up breaks.. it cannot find Dockerfile as the name is not the default

### Lecture 69 - Overriding Dockerfile Selection

* we tweak the docker-compose file to fix the issue
* we mod the build: setting adding subsettings
```
    build:
      context: .
      dockerfile: Dockerfile.dev
```
* the dockerfile: setting does the trick

### Lecture 70 - Do  We Need Copy?

* we run `docker-compose up` 
* app is running ok
* we do a code change. app rebuilds and chrome refreesh. all ok
* with volume mapping set we dont need the COPY . . in the Dockerfile.
* we still need COPY package.json . as we need it to build the libs
* if in the future we dont use docker-compose. then Dockerfile wont build without the COPY . . 
so we opt on leaving it in

### Lecture 71 - Executing Tests

* we ll first run the tests on dev env and then will do it on Travis CI
* we build our container with Dockerfile `docker build -f Dockerfile.dev .` we get the image id
* we run the tests with `docker run <image_id> npm run test`
* we see the test output but we cannot interact with the console. we need the -it flag for that

### Lecture 73 - Live Updating Tests

* we modify the test file App.test.js adding one more test. tests do not rerun automaticaly
* even if we manually rerun tests the new test is NOT included
* we have the usual problem we copied our folder at build time so any local change is not reflected in the container project dir
* we can follow a docker-compose approach with volumes like in dev, setting maybe a test service looking in a test dockerfile with the test command
* another approach is to bring up an instance with `docker-compose up`
* we can attach to the existing container and run our test command
* we open a second terminal and run `docker ps` to get the instance id.
* we use it to run `docker exec -it <instance_id> npm run test`
* changes now trigger rerun of tests
* is a good solution but not optimal to mix compose with cli

### Lecture 73 - Docker Compose for Running Tests

* we will impleemnt the first approach adding in docker-compose a second service for testing
```
  tests:
    build:
      context: .
      dockerfile: Dockerfile.dev
    volumes:
      - /app/node_modules
      - .:/app
    command: ["npm","run","test"]
```
* to avoid adinga separate dockerfile we use the one for dev overriding the command withthe command property
* it work but we get the test outputin the login interface. no styling and no interaction

### Lecture 74 - Shortcomings on Testing

* we want the docker-compose service approach with the -it flag ability
* with  docker compose we cannot set up a connection between our host terminal and teh test conteiner stdin
* we ll open another terminal  and run `docker attach`  to the tests running ontainer. attach attaches the terminal to the stdin,stdout and stderr of the running contrainer
* in the other terminal we run `docker ps` to see the ids then `docker attach  <id>` but it does not seem to work
* we cannot do much more with this solution
* to see why this happens we open a 3rd terminal and run `docker exec -it <container id> sh` for the testing container to open a shell. in the shell we run 'ps'
* we see 1 npm and 2 node processes running... strange... the first is the script in the command we call and the 2 node ones the process that does the test
* `docker attach` alwways attaches the host terminal with the std strams of the primary process and this is NOT the one doing the tests as we saw
* so docker attach is not the way for us...
* if we can memorize the exec vcommand it is the better one

### Lecture 75 - Need for Nginx

* there is a great difference with our app running in a dev environent and in production environment
* in dev environemnt thereis a dev server running in the we container. the browser interacts withthe dev server that uses the build folder to serve content. namely the one html file (index.html) and the bundled up js application code
* in prod enviornment we have the public files (index.html + optimized js bundle + any other files) but the dev server is missing. it is not appropriate for a production environment. it consumes far too many resources to handle code changes fast. in production our code base is stable
* for production environment container we will use the [nginx](https://www.nginx.com/) server a lightweight ans stable production grade webserver

### Lecture 76 - Multi-step Docker Bulds

* we ll look how to get nginx into our production container
* we will add a seconf dockerfile in our project dit named 'Dockerfile' for our production image.
* in our new Dockerfile we need to:
	* use node:alpine as base
	* copy the package.json file
	* install dependencies (ISSUE: deps only needed to execute 'npm run build')
	* run npm run build
	* start nginx and serve build dir (ISSUE: how we install nginx?)
* we look in docker hub for nginx. there are nginx image to host simple context (no node inside) by we need node to build
* in looks like we need 2 diff base images
* we will use a dockerfile with a `multistep build process` with 2 blocks of configuration
	* Build Phase: Use npde:alpine => copy package.json file => install dependencies => run 'npm ruin build'
	* Run Phase: use nginx => copy over the result of 'npm run build' => start nginx

### Lecture 77 - Implementing Multi-Step Builds

* the first step in our dockerfile looks like
```
FROM node:alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
RUN npm run build
```
* steps start with `as <stagename>` in a FROM block.
* we dont need volume as we wont work on the files after the container is prepared
* we dont need a CMD as our container puprose is to build the project and exit
* our build artifacts for production will be in /app/build in the container FS we need to copy it to the next step container
* we implement the run phase
* we dont need to name it. FROM block signamls the end of previous step (phase) and the start of a new
```
FROM nginx
COPY --from=builder /app/build /usr/share/nginx/html
```
* in this step we specify the base image
* we use the --from= flag to tell dockerfile that we want to copy files not from the defualt location (host) but from an other location as parameter we pass the previous step `--from=builder`
* we copy the build artifacts fromt he outout folder in the builder container fs to the nginx container defualt location (according to nginx image documentation)
* we dont neeed to set a CMD as nginx contianer primary command starts the nginx server

### Lecture 78 - Running Nginx

* we are ready to test with `docker build .` no need for -f flag as we use default dockerfile name
* we run the container  `docker run -p 8080:80 <container_id>`
* we see no output. but we visit browser at localhost:8080 annd see our app running

## Section 7 - Continuous Integration and Deployment with AWS

### Lecture 79 - Services Overview
we ll look how to get nginx into our production container
* we will add a seconf dockerfile in our project dit named 'Dockerfile' for our production image.
* in our new Dockerfile we need to:
	* use node:alpine as base
	* copy the package.json file
	* install dependencies (ISSUE: deps only needed to execute 'npm run build')
	* run npm run build
	* start nginx and serve build dir (ISSUE: how we install nginx?)
* we look in docker hub for nginx. there are nginx image to host simple context (no node inside) by we need node to build
* in looks like we need 2 diff base images
* we will use a dockerfile with a `multistep build process` with 2 blocks of configuration
	* Build Phase: Use npde:alpine => copy package.json file => install dependencies => run 'npm ruin build'
	* Run Phase: use nginx => copy over the result of 'npm run build' => start nginx

### Lecture 77 - Implementing Multi-Step Builds

* the first step in our dockerfile looks like
```
FROM node:alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
RUN npm run build
```
* steps start with `as <stagename>` in a FROM block.
* we dont need volume as we wont work on the files after the container is prepared
* we dont need a CMD as our container puprose is to build the project and exit
* our build artifacts for production will be in /app/build in the container FS we need to copy it to the next step container
* we implement the run phase
* we dont need to name it. FROM block signamls the end of previous step (phase) and the start of a new
```
FROM nginx
COPY --from=builder /app/build /usr/share/nginx/html
```
* in this step we specify the base image
* we use the --from= flag to tell dockerfile that we want to copy files not from the defualt location (host) but from an other location as parameter we pass the previous step `--from=builder`
* we copy the build artifacts fromt he outout folder in the builder container 
* we have now all set up om the docker part of the deployment process
* we will see how to use the containers we have prepared in our workflow
* we will use in our workflow : github, Travis CI, AWS

### Lecture 80 - Github setup

* our flow on github:
	* create github repo
	* create local git repo
	* connect local git to github remote
	* push work on github
* we create a new repo on github: docker-react
* we set it to public (private wont do the trrick...)
* working with a private repo is possible we just have to add keys on the servers that use our codebase
* we start a local repo in frontend and push it up to github (at docker-react repo)
* as we use a main gihub repo for our course we nest this a s a submodule
```
git submodule add git@github.com:achliopa/docker-react myCode/docker-react
```
* now in order to commit and push to the subrepo we need to do it in the master one
* if we need to update changes we do a pull inside the folder or outside `git submodule update --init --recursive`
* also when we clone our outer repo if we want to include the submodule we use `git clone --recursive git@github.com:achliopa/udemy_DockerKubernetesCourse.git`

### Lecture 81 - Travis CI setup

* we ll setup travis CI on github. travis works as follows
	* whenever we puch code on our github repo travis is triggered
	* travis does its work
* travis can test, deploy
* we will use it for test and deployment (on AWS)
* we setup travis-ci for our repo
* we go to  we signin/up and grant access
* we go in the dashboard
* in our avatar pro we see our github repos. we enable it for docker-react
* our repo is added in the list in the dashboard
* we see our repo

### Lecture 82 - Travis YML file configuration

* we need to direct Travis to test our code when we push
* we add a config file .travis.yml in our root project dir:
* in the file we need to:
	* tell travis we need a copy of docker running
	* build our image using Dockerfile.dev (our test container is set thjere)
	* tell travis to run our test suite
	* tell travis how to deploy our code to AWS
* our .travis.yml is
```
sudo: required
services:
	- docker

before_install:
	- docker build -t achliopa/docker-react -f Dockerfile.dev .
```
* every time we run docker in travis ci we need superuser permissions `sudo: required`
* we need to tell travis on teh services we will use (docker). travis will run docker
* 'before_install' sets what will run before our tests run (test setup)
* first we need to create the test container image `- docker build -t achliopa/docker-react -f Dockerfile.dev .`
* we need the generaated id for the next command. we add a tag to use it later
* we can add a simple tag as its going to be used only in the travis ci temporary vm

### Lecture 83 - A Touch More Travis Setup

* we need to tell travis what to do with our code (test)
```
script:
	- docker run achliopa/docker-react npm run test -- --coverage
```
* if travis gets a result other that 0 from the scripts it will assume our scripts (test) failed
* every time we run tests on travis it assumes that test run and exit automatically
* npm run test runs continuously and does not exit (travis will think as error)
* to make sure the test run exits after it test we run `npm run test -- --coverage` instead
* this option gives a coverage report

### Lecture 84 - Automatic Build Creation

* we push our code to github
* travis ci is triggered. in dashboard we see that our tests pass (if we go to travis when tests are run we can see log)

### Lecture 85 - AWS Elastic Beanstalk

* our app is tested and ready to be deployed to AWS
* we go to amazon aws and create an account
* we go to dashboard
* to deploy our project we will use elastic beanstalk
* elastic beanstalk is the easiest way to start with production docker instances. it is best suited in single containers
* we click create new application => give a name => create
* we have a workspace and we are prompted to create an environment. we create one
* we select web server environment => select
* in Base Configuraton => Platform we select `Docker`
* DO NOT FORGET TO SHUT DOWN THE INSTANCE AFTER FINISHING TO AVOID BILLING

### Lecture 86 - More on Elastic Beanstalk

* wehn setup is compete our docker-env is healthy and running
* why we use elastic beanstalk?
	* when our user tries to acces our app from hist browser the request will be handled by a Load Balancer in teh AWS ElastiBeanstalk Environment we created
	* Load balancer will direct the request to a VM running Docker in the AWS Env. in that machine a docker container runs with our app
	* The benefit  of elastic beanstalk is that it monitors traffic to our VM. when traffic is high Elastic beanstalk will start additional VMs (+containers) to handle traffic
* So Elasticbeanstalk == Auto Scale of our app
* our aws env workspace has the url of the env we can call vfrom browser4

### Lecture 87 - Travis Config for Deployment

* we mod the .travis.yml file to do the deployment after test pass
* travis has preconfigured scripts to auto deploy for a number of known providers like digital ocean, aws ...
* we also need to set in which region our app is tunnin int (it is a part of the url)
* we put the name of the app
* we put the env name
* we put the bucketname (S3 bucket) where AWS puts our repo for the app. we go to S3 in AWS dashboard and look for the environments bucket (elasticbeanstalk.region.id)
* we add the bucket_path which is the folder in the bucket where our app resides. typically is the app name we gave to teh Elasti beanstalk 
* we said that we will triger the deploy (and test when master gets a new version) so we tell travis to deploy only when there is a push to master
```
deploy:
  provider: elasticbeanstalk
  region: "eu-central-1"
  app: "docker-react"
  env: "DockerReact-env"
  bucket_name: "elasticbeanstalk-eu-central-1-448743904882"
  bucket_path: "docker-react"
  on:
    branch: master
```

### Lecture 88 - Automated Deployments

* we need to generate and give API keys to our EWS env to travis so that it is authorized to do the deployment
* we go to AWS IAM service to set keys => users => add user => 
* we set a name 'docker-react-travis-ci' and give it programmatic access only => next.permissions => attach existing policies directly
* we select 'AWSElasticBeanstalkFullAccess ' => next => create user
* we get a pair of keys an access key id and a secret access key
* we have only one time acces to the secret key
* we dont want to put these keys directly in teh .travis.yml as this is in a github public repo
* we use enviroment features prvided by travis ci
* we go to travis-ci => project => settings => environment variables
* we enter access key and secretaccess key
* accesskey: name: AWS_ACESS_KEY . do not dispaly it on build log => add
* secret: name: AWS_SECRET_KEY do not dispaly it on build log => add
* we add them to deploy script in .travis.yml
```
deploy:
  provider: elasticbeanstalk
  region: "eu-central-1"
  app: "docker-react"
  env: "DockerReact-env"
  bucket_name: "elasticbeanstalk-eu-central-1-448743904882"
  bucket_path: "docker-react"
  on:
    branch: master
  access_key_id: $AWS_ACCESS_KEY
  secret_access_key:
    secure: "$AWS_SECRET_KEY"
```
* we will comit and push our changes to github to see the flow take action
* we see in travis the log of the deploy

### Lecture 89 - Exposing Ports Through the Dockerfile

* in the travis build log we see. that deploy is done
* we visit the elastibgeanstalk app url in browser but we dont see the app
* also the state of the environemnt says degraded
* we need to expose the port in elasticbeanstalk when we run the docker container in travis.yml
* we do the expose after the first deploy (not sure why) and we do it in the production dockerfile in the prod phase. `EXPOSE 80`
```
FROM node:alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
RUN npm run build
FROM nginx
EXPOSE 80
COPY -from=builder /app/build/ /usr/share/nginx/html
```
* this command has no effect in local dev environment but it is meaningful in elastibeanstalk . as aws looks into the dockerfile for port mapping
* we repeat commit push and see the deploy
* travis work is about 4min

### Lecture 91 - Workflow with Github

* we will create the secont working branch 'features' so when we pull to master and merge it will trigger build
* we checkout code to a new branch `git checkout -b feature`
* we modify App.js => add => commit => push origin feature
* this creates a new branch on github (feature)
* we now have to do a pull request and merge
* in github repo we see the new branch. on the right it has a  compare & pull request
* on top it says we will attempt to take all in features and merge them in master branch
* we leave a message and click create pull request.
* an issue page is created for other developers to make comments
* we see there ar no issue fro merge but travis has pending checks

### Lecture 92 - Redeploy on Pull Request merge

* after some time we see that ravis checks passed. travis attempted to merge our changes and run the tests which passed. so its safe to merge
* as we merge it equalls a push to master so the whole test+deploy flow reruns

### Lecture 93 - Deployment Wrapup

* docker had nothing to do int eh deployment process. 
* docker is a tool that eases up the deployment process
* travis pipeline is reusable for other projects as well

## Section 8 - Building a Multi-Container Application

### Lecture 95 - Single Container Deployment Issues

* some issues with our first production grade app
	* the app was simple . no outside dependencies
	* our image was built multiple times
	* how do we connect to a ddatabase from a container?
* we would like to not do the build in an active running web server
* the next level is to deploy to elasticbeanstalk a multicontainer app

### Lecture 96 - Application overview

* the app will be a super complicated fibonacci series calculator
* user will enter the index in teh series on the web page
* a backend process will calculate the fibonacci number for this index
* the result will be presented on screen
* also we will diplay the result in a list of items (together with previou sones)
* and  alist of previous searched indexes

### Lecture 98 - Application Architecture 

* the development architecture of our app:
	* browser hits nginx web server
	* nginx will do some routing. it will see if the browser is trying to access a view (html pr js frontend file) it will route to the react server
	* if the request is trying to access a backend API it will route to an ecpress node API server
	* express api server will hit an imemory redis data storage or a postres db
	* redis will exchange info with a worker process that will calculate the fibonacci series
	* postgress will store the visited indexes
	* redis will have teh calculated values
* Redis: Stores all indices and calculated values as key value pairs
* Postgres: stores a permanent list of indices that have been received
* worker: watches redis for new indices. pull seach new index. calculates new value and then puts it back to redis

### Lecture 99 - Worker Process Setup

* we make a new project dir 'complex'
* we make a new folder inside named 'worker'
* we add a package.json file into worker folder
```
{
	"dependencies": {
		"nodemon":"1.18.3",
		"redis": "2.8.0"
	},

	"scripts": {
		"start": "node index.js",
		"dev": "nodemon"
	}
}
```
* its going to be a node daemon talking to redis store
* we add a js file in worker named index.js where we add `const keys = require('./keys');`
* so all keys for connecting to redis will come from a separate js file named keys.js
* keys will come as env params
```
module.exports = {
	redisHost: process.env.REDIS_HOST,
	redisPort: process.env.REDIS_PORT
}
```
* our index.js complete
```
const keys = require('./keys');
const redis = require('redis');

const redisClient = redis.createClient({
	host: keys.redisHost,
	port: keys.redisPort,
	retry_strategy: () => 1000
});

const sub = redisClient.duplicate();

function fib(index) {
	if (index < 2) return 1;
		return fib(index-1) + fib(index-2);
}

sub.on('message', (channel,message)=>{
	redisClient.hset('values',message,fib(parseInt(message)));
});
sub.subscribe('insert');
```
* we create two redis clients. one subscribes to events and listens to new messages. the other actually sets the value running a fibonacci recursive function (takes along time so that why we need a second client to not miss incoming events)
* in the client we pass a callback to restart if connection is lost after a second

### Lecture 100 - Express API Setup

* we add a new folder 'server' inside the root folder
* we add a package.json as well
```
{
	"dependencies": {
		"express": "4.16.3",
		"pg": "7.4.3",
		"redis": "2.8.0",
		"cors": "2.8.4",
		"nodemon": "1.18.3",
		"body-parser": "*"
	},
	"scripts": {
		"start": "node index.js",
		"dev": "nodemon"
	}
}
```
* we add a keys.js file same as workers with reference to the env params with keys for redis and prostgres

### Lecture 101 - Connecting to Postgress

* we create a new file called index.js in 'server'
* the file will host all the logig to:
	* connect to redis
	* connect to postgres
	* broke information between them and the react app
* cors will allow us to make cross origin requests
* everytime we connect to a PostgresDB we need to create a table to host values

### Lecture 102 - More Express API Setup

* we add a connection to redis to pass the inserted value
```
const keys = require('./keys');

//express app setup
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(bodyParser.json());

// postgres client setup
const { Pool } = require('pg');
const pgClient = new Pool({
	user: keys.pgUser,
	host: keys.pgHost,
	database: keys.pgDatabase,
	password: keys.pgPassword,
	port: keys.pgPort
});
pgClient.on('error', ()=> console.log('Lost PG connection'));

pgClient.query('CREATE TABLE IF NOT EXISTS values (number INT)')
	.catch((err)=>console.log(err));

//Redis Client Setup
const redis = require('redis');
const redisClient = redis.createClient({
	host: keys.redisHost,
	port: keys.redisPort,
	retry_strategy: () => 1000
});
const redisPublisher = redisClient.duplicate();


// express route handlers
app.get('/', (req,res)=>{
	res.send('Hi');
});

app.get('/values/all', async (req,res)=>{
	const values = await pgClient.query('SELECT * FROM values');

	res.send(values.rows);
});

app.get('/values/current', async (req,res)=>{
	redisClient.hgetAll('values', (err,values) => {
		res.send(values);
	});
});

app.post('/values', async (req,res) => {
	const index = req.body.index;

	if(parseInt(index)>40){
		return res.status(422).send('index too high');
	}

	redisClient.hset('values',index, 'Nothing yet!');
	redisPublisher.publish('insert',index);
	pgClient.query('INSERT INTO values(number) VALUES($1)',[index]);

	res.send({working:true});
});

app.listen(5000, err => {
	console.log('Listening');
});
```
* we set a hash in redis with key index and value emty and we publish an event of type inser with the index
* we add express routes and handlers

### Lecture 103 - Generating the React App

* in base project folder we run `create-react-app client`

### Lecture 104 - Fetching Data in the React App

* we want to implement  2 pages in the frontend. a dummy page and a full flejed form with a list
* we create teh dumm page `OtherPage.js` in  src asimple functional component witha Link
* we add the main page component in Fib.js in src
* we implement the compoent as stateful class component
* we add a lifecylemethod and 2 helpers to fetch data from backend and set state

### Lecture 105 - Rendering Logic in the App

* we add a render method
* we add a form and event handler
* we add 2 lists in helpers methods
* when we get back data from postgres we get an array of objects
```
import React, { Component } from 'react';
import axios from 'axios';

class Fib extends Component {
	state = {
		seenIndexes: [],
		values: {},
		index: ''
	};

	componentDidMount() {
		this.fetchValues();
		this.fetchIndexes();
	}

	async fetchValues() {
		const values = await axios.get('/api/values/current');
		this.setState({ values: values.data });
	}

	async fetchIndexes() {
		const seenIndexes = await axios.get('/api/values/all');
		this.setState({
			seenIndexes: seenIndexes.data
		});
	}

	handleSubmet = async (event) => {
		event.preventDefault();
		await axios.post('/api/values', {
			index: this.state.index
		});
		this.setState({index: ''});
	};

	renderSeenIndexes() {
		return this.state.seenIndexes.map(({number}) => number).join(', ');
	}

	renderValues() {
		const entries = [];

		for (let key in this.state.values) {
			
		}
	}

	render() {
		return(
			<div>
				<form onSubmit={this.handleSubmit}>
					<label>Enter your index:</label>
					<input 
						value={this.state.index}
						onChange={event => this.setState({index: event.target.value})}
					/>
					<button>Submit</button>
				</form>
				<h3>Indices I have seen:</h3>
				{this.renderSeenIndexes()}
				<h3>Calculated Values:</h3>
				{this.renderValues()}
			</div>
		);
	}
}

export default Fib;
```

### Lecture 107 - Routing in the React App

* we ll use react-router-dom lib to route between the 2 pages (React componetns) we hav eimplemented
* we add `"react-router-dom": "4.3.1"` in package.json dependencies and `"axios": "0.18.0"`
* in App.js we add the BrowserRouter and Route and implement basic router
* we add 2 links in the header

## Section 9 - "Dockerizing" Multiple Services

### Lecture 110 - Dockerizing a React App - Again!

* we will make a Dockerfile for each process we have implemented (React App , Express Server, Worker Daemon)
* the dockerfiles will be for development not production
* again like before we dont want npm install to run whenever we modify the code base. only when we mod the package.json
* so we will do what we did in the single service application dev dockerfile in a previous section
	* xopy over package.json
	* run npm install
	* copy over all else
	* use docker compose to set a volume to 'share files' between local and container
* all our component folders are similar (node based apps) and we add the dockerfile inside each one
* in client fodler we add Dockerfile.dev
```
FROM node:alpine
WORKDIR '/app'
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm","run","start"]
```
* in client folder we run `docker build -f Dockerfile.dev .` to build our client image
* we add Dockerfile.dev files for server and worker (identical). we use npm run dev to start nodemon so that we listen to code changes as we do changes to code base
```
FROM node:alpine
WORKDIR "/app"
COPY ./package.json ./
RUN npm install
COPY . .
CMD["npm","run","dev"]
```
* we build our custem images and run them `docker run <id>`

### Lecture 112 - Adding Postgres as a Service

* we ll assempble a docker-compose file to connect and build/run our iamges easily
	* postgres: which image from hub?
	* redis: which image from hub?
	* server: specify build, volumes, env variables
	* later add worker and client
* volumes we need to speed up devleopment 
* env are needed for process.env params
* in project root we add the file
* start of file
```
version: '3'
services:
    postgres:
      image: 'postgres:latest'
```
* in docker run of postgres image we can spec the password to use as a param

### Lecture 113 - Docker-Compose Config

* in the same way we add redis
```
    redis:
      image: 'redis:latest'
```
* we add server service specifying the build (file and folder)
* also we add volums to map dev folder to container. aslo we skip node_modules so we dont overwrite it
* all forders are relative to the project root folder
```
    server:
      build: 
        dockerfile: Dockerfile.dev
        context: ./server
      volumes:
        - /app/node_modules
        - ./server:/app
```

### Lecture 114 - Environment Variables with Docker Compose

* we add another section to the server config in compose file to set environment variables
* there are 2 ways to set env variables in a docker compose file.
	* variableName=value : sets variable to value in the container at 'run time'. the value is not stored in the image
	* variableName; setsa variable in the container at 'run time' value is taken from host machine
* the params are (we look them in dockerhub image docs)
* hostname in docker viurual private network is the docker-compose service name
```
      environment:
        - REDIS_HOST=redis
        - REDIS_PORT=6379
        - PGUSER=postgres
        - PGHOST=postgres
        - PGDATABASE=postgres
        - PGPASSWORD=postgres_password
        - PGPORT=5432
```

### Lecture 115 - The Worker and Client Services

* in the same way we add worker and client config
```
    client:
      build: 
        dockerfile: Dockerfile.dev
        context: ./client
      volumes:
        - /app/node_modules
        - ./client:/app
    worker:
      build: 
        dockerfile: Dockerfile.dev
        context: ./worker
      volumes:
        - /app/node_modules
        - ./worker:/app
      environment:
        - REDIS_HOST=redis
        - REDIS_PORT=6379
```
* we have note added port mapping.. this is because nginx exposes the whole to the outside world
* so we will add nginx service and dd there the port mapping

### Lecture 116 - Nginx Path Routing

* in our previous project nginx was used in production environment to host hte production built files for serving our content
* in this project nginx will exist in the developlemnt env (to do routing)
	* our browser will request content like index.html and the included bundle.js these requsests go to React server
	* after frontside rendering browser will request data from API to feed the app
	* browser will request /values/all and balues/current content from Express API server
* we need an infrastructure to do top level routing... this will be done by nginx
* in our code client side app (react) requests API data with Axios from /a[i/values/* routes
* our backend API serves them in /values/* routes
* nginx will have a simple job. 
	* when request has / will direct to react server
	* wehen request has /api/ will redirect to the express server
* an obvious question is why we dont assigne different ports to exress and react server and make this routing based on port to avoid nginx
* in production env we want things transparent and avoid using ports in our urls
* also ports might be used or changed
* nginx after routing /api/ chops off api and passes the rest to the express APi

### Lecture 117 - Routing with Nginx

* to setup nginx and give it a set of routing rules we will create a file called default.conf
* default.conf adds configuration rules to Nginx:
	* tell nginx that there is an 'upstream' server at client:3000
	* tell nginx that there is an 'upstream' server at server:5000
	* tell nginx to listen on port 80
	* if anyone comes to '/' send them to client upstream
	* if anyone comes to '/api' send them to server upstream
* Upstream servers are inaccessible without nginx
* we mention client:3000 and server:5000 as in out index.js we set up our servers to listen to these ports inm tehir respective hosting or virtual machoines or alpine linux container  os.
* client and server hostnames are not random but are the service names or the running docker containers in our docker compose virtual network
* we add a new project subfolder named 'nginx' and inside we add the default.conf file
```
upstream client {
	server client:3000;
}

upstream api {
	server api:5000;
}

server {
	listen 80;

	location / {
		proxy_pass http://client;
	}

	location /api {
		rewrite /api/(.*) /$1 break;
		proxy_pass http://api
	}
}
```
* server is a reserved word in ngingx conf so we rename our server service as api
* routing is done using the location rules and proxy_pass directive
* rewrite is using regex to chop off the /api part of the path.
* break means skip any further rule and pass on
* to get default.conf in the nginx container we will the Dockerfile command COPY

### Lecture 118 - Building a Custom Nginx Image

* according to nginx image docs in dockerhub we need to copy the config file in /etc/nginx/conf.d/folder in the image
* if a file exists with the same name it will be overwritten
```
FROM nginx
COPY ./default.conf /etc/nging/conf.d/default.conf
```
* we add nginx as a service in the compose file
```
    nginx:
      restart: always
      build:
        dockerfile: Dockerfile.dev
        context: ./nginx
      ports:
        - '3050:80'
```
* we do port mapping ans add restart policy to always. nginx is the dorr to our app , is lightweight so it should run anytime

### Lecture 119 - Starting Up Docker Compose

* our first `docker-compose up` is likely to fail. why?
	* as there are dependencies of services to others (e.g redis) there is no guarantee that these will be up and running before they will be required
* in that case we kill it  'ctrl+c' and force rebuilt `docker-compose up --build`

### Lecture 121 - Troubleshooting Startup Bugs

* our app loads but we get websocket error in console
* this is because everytime react up boots up it needs an active websocket conenction to the dev server
* when we enter a val our app does not rerender we need to refresh to see the result

### Lecter 122 - Opening Websocker Connections

* nginx is not setup to pass through websocket connections so our app does not rerender
* we need to add one more routing rule to default.conf. our console error shows the websocket connection is done on /sockjs-node/.
```
	location /sockjs-node {
		proxy_pass http://client;
		proxy_http_version 1.1;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection "Upgrade";
	}
```
* we rebuild compose and see it working

## Section 10 - A Continuous Integration Workflow for Multiple Containers

### Lecture 123 - Production Multi-Container Deployments

* our workflow for a single container workflow was
	* push code to github
	* travis autmatically pulls repo
	* travis builds an image , tests code
	* travis pushes code to ews EB
	* EB builds image, deploys it
* building our project in EB (prod env) was suboptimal as production server should reserve its power to serve content not do builds
* our modified optimized flow for multicontainer deployment wiil be
	* push code to github
	* travis automatically pulls repo
	* travis builds a test image, tests code
	* travis builds a test image, tests code
	* travis builds prod images
	* travis pushes build prod images to dockerhub
	* travis pushes project to AWS EB
	* EB pulls images from Docker Hub, deploys
* dockehub allows us to have our own personal images online

### Lecture 124 - Production Dockerfiles

* worker and server dockerfiles for production are same as dev just the command changes
* nginx prod dockerfile is the same as dev
* an optimization for nginx would be to remove the roting of websockets in default.conf creating a production version of it as in production there is no devserver

### Lecture 125 - Multiple Nginx Instances

* we move to client to build its production Dockerfile. we cp the prod dockerfiel from teh single container application of previous section
```
FROM node:alpine as builder
WORKDIR '/app'
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
FROM nginx
EXPOSE 80
COPY --from=builder /app/build/ /usr/share/nginx/html
```
* then we did a multi step build building and then running an nginx image cping files from build phase to serve
* in our single container deployment AWS EB was running an nginx  server with production files serving content at port 80.
* now things will be different. with multicontainer env. in production AWS EB will
	* run multiple containers
	* nginx router will listen at port 80
	* nginx router will proxy / calls to port 3000 where another nginx server will serve production artifcts of react app
	* nginx will proxy /api calls to port 5000 where the express API server will serv JSON content
	* all will run on the same machine (EB) that can scale transparently
* we could use one nginx server to do both. route and serve. with using 2 we gain on flexibility later on

### Lecture 126 - Altering Nginx listen port

* we add an nginx subfolder in client. in there we add a default.conf config file
* we need to change the listening port to 3000. to do so we mod the nginx server config
```
server {
	listen 3000;

	location / {
		root /usr/share/nginx/html;
		index index.html index.htm;
	}
}
```
* we set the / route serving content from nginx default location
* in the dockerfile we use the multistep approach.. we need to copy the default.conf file inside
* only the nginx step is different from signle container app prod dock file
```
FROM nginx
EXPOSE 3000
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/build/ /usr/share/nginx/html
```

### Lecture 127 - A FIX

* now our react app uses react-router... the default.conf of nginx websrv that will serve bundled content needs one more line `try_files $uri $uri/ /index.html;` that basically sends all routes to index.html (single page app)

### Lecture 128 - Cleaning Up Tests

* in client (crea-react-app autogenerated) test it tests App component rendering.  our component is doing async axios backend calls so we need to do proper async friendly testing (using moxios). for now we jist erase test contents so that it passes alwways

### Lecture 129 - Github and Travis CI Setup

* we follow the submodule path to add a nested repo in our course repo for deployment purpose
	* cp our complex folder away
	* create a local repo,ad,commit
	* create github repo. push to github
	delete temp complex folder../
	* delete complex folder from course
	* commit and push to course repo
	* add a submodule to course local repo. in couser root folder run `git submodule add git@github.com:achliopa/multi-docker myCode/complex`
	* add , commit push master repo
* we navigate to travis ci to link to our new multi-docker repo
* in our dashboard we sync account and enable travis in our new repo
* we need a .travis.yml file

### Lecture 130 - Travis Configuration Setup

* in the travis file we will:
	* specify docker as dependency
	* build test version of react project (we could add tests for worker and api server)
	* run tests
	* build prod versions of all projects
	* push all to docker hub
	* tell Elastic Beanstalk to update
```
sudo: required
services:
  - docker

before_install:
  - docker build -t achliopa/react-test -f ./client/Dockerfile.dev ./client

script:
  - docker run achliopa/react-test npm test -- --coverage

after_success:
  - docker build -t achliopa/multi-client ./client
  - docker build -t achliopa/multi-nginx ./nginx
  - docker build -t achliopa/multi-server ./server
  - docker build -t achliopa/multi-worker ./worker
```

### Lecture 131 - Pushing Images to Docker Hub

* we need to login to  docker-cli
* we ll put the docker login command in .treavis.yml hidding our username and password from common view (will be passed as travis env params)
* travis -> muulti-docker project -> settings we add environment vars. DOCKER_ID DOCKER_PASSWORD
* add login to .travis.yml `- echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_ID" --password-stdin` we retreive the password from env params and pass it to stdin sending it to the next command with pipe. next command uses password from stdin
* being logedin we just have to push our images to dockerhub (our account)
```
  # takes those images and push them to docker hub
  - docker push achliopa/multi-client
  - docker push achliopa/multi-nginx
  - docker push achliopa/multi-server
  - docker push achliopa/multi-worker
```
* we need to push to github to ttrigger travis to test our flow  so far (should see images on dockerhub). its a success

## Section 11 - Multi-Container Deployments to AWS

### Lecture 133 - Multi-Container Definition Files

* we ll pull the images from dockerhub and push them to AWS EB for deploy
* in single container app we puched the project with the dockerfile to EB. EB sees thatwe have adockerfile and builds and runs the image.
* in our multicontainer app we have multiple containers with each one having its Dockerfile
* AWS EB doens not know which one to run
* we ll add one more AWS specific config file in our project teling AWS how to run our project 'Dockerrun.aws.json' this file looks like docker-compose.yml
* docker-compose is meant for dev environments. it defines how to build images and sets their interconnections
* dockerrun does not need to build images. images are already build. we have container definitions secifying the images to use and where to pull them from

### Lecture 134 - Docs on COntainer Definitions

* elastic beanstalk does not know how to run containers
* when we tell AWS EB to host our containers it uses Amazon Elastic Container Service (ECS)
* in ECS we create files called 'task definitions' with instructions on how to run a single container. these files are similar to the *container definitions* we will write in 'Dockerrun.aws.json'
* to customize Dockerrun file we need to read about  [AWS ECS task definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html) -> task definition parameters -> container definitions

### Lecture 135 - Adding Container Definition in Dockerrun

* we add 'Dockerrun.aws.json' in project root
* we specify its version
* we add our containeDefinitions as an arra of config objects
* in the config obj we specify the name of the container, 
* we spec the image to use (AWS ECS understands from syntax we are refering to dockerhub so it pulls it from the dockerhub)
* we spec the hostname for the running container in the app virtual private network in the EB.
* we mark if the container is essential. at least one showuld be marked as essential. if it is essential and it crashes all others will be stopped
```
{
	"AWSEBDockerrunVersion": 2,
	"containerDefinitions": [
		{
			"name": "client",
			"image": "achliopa/multi-client",
			"hostname": "client",
			"essential": false,
			
		}
	]
}
```

### Lecture 137 - Forming Container Links

* for nginx we dont add hostname. no container in the group needs to look out and connect to it. same holds for worker
* nginx is essential as if it fail noone can access the app
* nginx needs portmapping as in  compose as it is the  connection to the outside world
* portmapping is set as an array of mappings
```
			"portMappings": [
				{
					"hostPort": 80,
					"containerPort": 80
				}
			]	
```
* in nginx we add one more param 'links' to set the link to the other containers in the group
* in dockercompose interconnection between containers was set automatically based on their names(hostnames) but in AWS ECS it needs to be se explicitly `"links": ["client","server"]`
* links in container definitions need to be specified in one end (the comm originator) as links are unidirectional. 
* links use the container name not the hostname
* it is recommended to validate json using online tools before deploy'

### Lecture 138 - Creating the EB environment

* we go to AWS => ElasticBeanstalk => create new app => give name (multi-docker) => create => actions => create environment => web server env => select => base config, platform , preconfig platform, choose 'Multi-container Docker' => click create env
* we will use 2 official images from dockerhub for data storage (postgrs and redis)
* we gave specified nothing on .travis.yml or dockerrun file for them
* this is a discussion of architecture on seeting data storage inside of containers

### Lecture 139 - Managed Data Service Providers

* in dev environment we run the data services as containers
* in production environment:
	* all our business logic(API server, worker daemon, nginx router and nginx web content server with react prob build files) will run in AWS EB(elastic beanstalk) instance.
	* Redis will run in AWS Elastic Cache
	* Postgres DB will run in AWS Relational Database Service (RDS)
* why use external dedicated AWS services for data storage?
	* we use  general data services not docker specific
	* AWS Elastic Cache: automaticaly creates and maintains (production grade) Redis instances for us, very easy to scale, built in logging and maintenance, better OTB security than our custom logic, easy to migrate
	* AWS realtional Database Service (RDS): same advanatages like fo AW Elastic Cache + automatice rollbacks and backups
* Automatic DB backups are not easy. auto rollback to backups on failure is not trivial to implement on your own
* in a next project will use Postgres and Redis in containers in aproduction environment

### Lecture 140 - Overview of AWS VPC and Security Groups

* we want to be able to connect our instance in the AWS EB Instance to the AWS RDS instance (postgres) and AWS EC Instance (Redis). By default these serices can't talk to each other
* we need to set the connection ourselves.
* when we created our EB instance it was created in a specific region (data center) 'eu-central-1'
* in each of these regions we get a VPC (Virtual Private Cloud). a private network so that any service or instance we create is isolated only to our account.
* so when we create an instance only our account has access to that
* VPC is used to apply security rules and to connect different services we create on AWS
* we get one VPC by default created for our account in any region (AWS data center)
* our instances are assigned to the VPC nearest to our local ip unless programmatically directed otherwise
* from services dashboard we go to VPC. => we click your vpcs and see our default vpc. we note thte id
* we switch to another region and see our default vpc for that region. it has a different id
* after starting our services we have them running (by default) to our default vpc of our closest region
* to make our different services talk to each other inside the vpc we need to set a 'security group' (Firewall Rules)
	* allow any incoming traffic on Port 80 from any IP
	* allow traffic on port 3010 from IP 172.0.40.2 (custom rules)
* our EB instance has a security group created that accepts conenction on prot 80. beign in ourdefault region VPC we can click security groups and see the rules for our running EB instance. in inbound rules er see  it is accepting http requests in port 80- from any source, outbound rules allow anything
* TO allow intercom between our services we will create a new security group and attach all thre AWS instances (EB,EC,RDS) adding a rule that says allow any communication between instances that belong to this security group

### Lecture 141 - RDS Database Creation

* (being in our default region) we search for rds in services => close modal => click create database => postgreSQL => (click enable options for free tier usage) => next => we leave all default only set master username and password same as our dockercompose values and db instance identifier 'multi-docker-postgres'. if we choose another usr/password we need to pass the m in config file => next => we put the instance in our default vpc leave the rest default => we set a db name (we use the same as in dockercompose file) => leave the rest default and click create db

### Lecture 142 - ElasticCache Redis Creation

* in our default region => services => ElastiCache => Redis => Create => (We get a cluster) => Set up some settings
	* name: multi-docker-redis
	* node type: => t2 => cache.t2.micro
	* replicas: none
	* subnet group name : redis-group
	* vpc id : our defualt
	* subnets : select all
* click create

### Lecture 143 - Creating a Custom Security Group

* we go back to vpc => security groups (we have one more created by rds) => we create  a new one => name it and add description , use default vpc =. create
* we see ti  in the list => we go down to rules (we need to allow comm inside this group) => inbound Rules => add rule (type:custom tcp, protocol: tcp, port range: 5432-6379, source: sg-dsds|multi-docker) => save
* we need to assign this security roup to services

### Lecture 144 - Applying Security Groups to Resources

* first we do EC (redis) +> serivces => elasticcache => redis => select instance => modify => vpc groups edit => select also multi-docker => save => modify
* do rds => service => rds => instances => select our postgres instance => scroll down to details section =>  modify => network and security => security group => add multi-docker => continue +> select apply immediately in scheduling => modify db instance
* do eb => services => elasticbeanstalk => our instance => configuration => instances => modify => ec2 security groups => add multi-docker => apply

### Lecture 145 - Setting Environment variables

* we need to set a number of environment variable in our custom inages production config files to connect to the outer services in other AWS instances like we did in compose file for dev environment
* we go to services => eb => our instance => configuration => software => modify => environment properties => add env properties (not hidden) . if we  want them hidden we hsould try AWS secrets manager
	*  REDIS_HOST: our elastic instance url (elasticcache => our instance => primary endpoint)
	* REDIS_PORT: 6379
	* PGUSER: <user we set in RDS config>
	* PGPASSWORD: <passwoird we set in RDS configuration>
	* PGHOST: go to services => RDS => instances => our instance => connect +> get enpoint and cp it
	* PGPORT  5432
	* PGDATABASE: the anme we gave to the db at rds configuration
* apply
* In Elastibeanstalk the env variables we set are added to all containers created inside

### Lecture 146 - IAM Keys for deployment

* our production images are now on dockerhub. we will tell AWS EB (from travis yaml file) to go and pull the images and deploy them
* we ll sent all the project to EB but the only file it needs to do the deploy is the Dockerrun.aws.json
* in our signel container travis.yml file  we added deploy configuration for EB and a set of keys to allow travis to get access to  EB and copy files (we did not expose them travis pulled them from his env vars we set)
* we need new keys for our new instance. we go to services => IAM => USERS => add user ->name it, set programatic access => next => attach existing policies directly => filter by beanstalk =?> select all => create. 
* cp access key and secret to travic ci repo(multi-docker)env variables (settings) as AWS_ACCES_KEY and AWS_SECRET_KEY

### Lecture 147 - Travis Deploy Script

* we finish off travis.yml adding the deploy config (almost identical to the single container app)
* for bucket name and path we go to services => S3 => (find bucket)
```
deploy:
  provider: elasticbeanstalk
  region: eu-central-1
  app: multi-docker
  env: MultiDocker-env
  bucket_name: elasticbeanstalk-eu-central-1-448743904882
  bucket_path: docker-multi
  on:
    branch: master
  access_key_id: $AWS_ACCESS_KEY
  secret_access_key:
    secure: $AWS_SECRET_KEY
```
* we commit and push to github our project to trigger build test and deploy

### Lecture 148 - Container Memory Allocations

* with a successful deploy in travis we got o aws ed instance (we see an error)
* it comlains  that we didnt specify an option called memory. we need this when we use dockerrun and multiple containers
* eb needs it to know how much memeory to allocate in each container. we add a mem option to each container definition in dockerrun file
* we add `"memory": 128` to all 4.
* we commit and push to master on github to trigger the build

### Lecture 149 - Verifying Deployment

* if sthing wernt wrong and EB instance is not healthy goto Logs => request logs (last 100lines)
logs are for all containers running
* we hit the url and OUR APP IS RUNNINGGG!!!

### Lecture 150 - A quick App Change

* we do a small change and  see redeploploy


### Lecture 152 - Cleaning UP AWS Resources

* aws EB -> delete application
* delete rds no snapshot
* delete redis
* go to vpc -> security groups -> multi-docker -> security groups action -> delete
* delete iam users

## Section 12 - Onwards to Kubernetes!

### Lecture 153 - The Why's and What's of Kubernetes

* in our previous lulti container application we had 4 different containers running in the same Elastic beanstalk Instance at the same time
* scaling this app would force us look into themore resource intencive service the worker. we would like to spawn more instances of it to handle load
* scaling strategy for elastic beanstalk  creates more machines or more copies of elasticbeanstalk instance. in that sense