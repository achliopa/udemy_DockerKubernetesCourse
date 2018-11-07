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

* 