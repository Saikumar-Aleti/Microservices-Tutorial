Microservices with NodeJS and ReactJS

Different between a monolithic server and microservice?
A monolithic server can be characterized as a server that contains all the routing, middlewares, database access and business logic to implement all features of our application.
A single microservice contains all the routing, middlewares, database access and business logic to implement one feature of our applicaion.

What's the big challenge with microservices?
Data management between services if the biggest challenge in Microservices. With microservices, we store and access data in sort of a strange way, each service gets it own database (If it needs one) and serivces will never ever reach into another service database. Giving every service its own database is a pattern and the pattern is called Database-Per-Service. The reasons for Database-Per-Service include,

1. We want every service to run independently of other services.
2. Database schema or structure might change unexpectedly.
3. Some services might function more efficiently with different type of a database (SQL Vs NoSQL).

Two general communication strategies used to communicate between the services include, Sync and Async (In microservices, these words do not mean what they mean in the JavaScript world!).
Sync Communication Strategy - Services communicate with each other using direct request. For example a service D, that needs informatfion from Service A, B and C make direct request to service A, B and C to get the required responses and then it responds to the original request. This is a valid strategy as Service D have not accessed Service A, B and C's database directly at any point in time. This strategy comes with its own upsides and downsides, upsides include,

1. Conceptually easy to understand.
2. Service D won't need a database.

Downsides include,

1. Introduces a dependency between services.
2. If any inter-service requests fail, the overall request fails.
3. The entire request is only as fast as the slowest request.
4. Can easily introduce a web of requests. For example, in the above case, Service A might need to make a request to Service X and Y to work correctly.

Async Communication Strategy - Services communicate with each other using events. The are different ways of using async communication strategy.

First Style
We introduce something called an event bus into our application and and when ever request comes into Service D, it will emit an event first to Service A with a type and data, in this case, type may be UserQuery and data may include UserID. Event bus processes the incoming event and send a copy of it to Service A. Service A then emits an event with a type of UserQueryResult and data may include UserID and UserName. Event bus sends a copy of this event to Service D. You can imagine the same process of gets repeated for Service B and Service C. This style is not used often as it shares all the downsides of synchronous communication and it also comes with additional downsides,

1. Introduces a dependency between services.
2. If any inter-service event fail, the overall request fails.
3. The entire request is only as fast as the slowest event.
4. Can easily introduce a web of events.
5. It is not conceptually easy to understand as synchronous communication.

The second way of async communication is similar to Database-Per-Service pattern. Unlike the first style, here we give Service D it's own database and emit events from Service A, B and C whenever something meaningful happens (UserCreated, ProductCreated, OrderCreated etc.) to get the necessary data to Service D.

Advantages of Second Style

1. Service D has 0 direct dependencies on other services. Since Service D has its own database, it does not have an immediate need of any other service to answer an incoming request.
2. Service D will be extremely fast.

Disadvantages of Second Style

1. Data duplication. Paying for the extra storage and an extra database.
2. Harder to understand.

Docker
With Docker, we are going to create a series of containers. Container is an isolated computing environment containing everything to run one single program.
There is a one to one pairing between docker containers and each instance of our running program. If we need to multiple copies of a program, we will start a second container for that particular program.

Why Docker?

1. Running our app right now make big assumptions about our environment, for example, we are assuming that node and npm are installed.
2. Running our app also requires precise knowledge on how to start it (npm start).

Docker solves both these issues. Containers wrap up everything that is needed for a program and they also contain information about how to start and run it.

Kubernetes
Kubernetes is a tool for running a bunch of different containers together. When we make use of Kubernetes, we are going to give it some configuration files describing how we want our containers to run and interact with each other.Kubernetes will then create these containers to run our programs and handle communication i.e., network requests between all the different containers. We can really imagine kubernetes as a tool for running different programs and making communication between different programs easy and straightforward.

With Kubernetes, we create something called a cluster. A cluster is a set of different virtual machines. It can have as few as one virtual machine or as many as hundred or thousands of virtual machines. All these virtual machines are referred to as nodes. All nodes are managed by a master. Master is essentially a program to manage everything in a cluster (All virtual machines and all the different programs that are running on them.).

Example
When we say "Run two copies of posts services and allow the posts to be accessible from a network" in our configuration file and feed it to master. Master will read the configuration file and try to implement all the steps that are written in there. In this case, it will attempt to create two copies of the posts service wrapped up inside of containers. These containers will more or less be randomly assigned to be executed by different virtual machines or nodes inside our cluster. Now in this scenario, we're kind of back in the same problem we had before where it could potentially be really hard for something like our event bus to figure out in how to reach out to say this copy of the post service and that post service.

So the big thing that Kubernetes does for us is give us the ability to just kind of arbitrarily send requests or communicate between these different services with some kind of third party thing that we are going to create. So in short, you can kind of imagine down here that we're going to create something inside of our cluster that we can just send requests to, and it will automatically figure out how to route requests off to the appropriate service that is running inside of our application.

So the big reason that you and I are using Kubernetes around microservices is that it's going to make communication very easy and straightforward.

It's also going to make creating services like launching new copies and scaling the number of copies we are running very easy and straightforward as well.

Why use Docker?
Docker wants to make it really easy and really straightforward for you to install and run software on any given computer, not just your computer, not just your personal laptop or your personal desktop,but on web servers as well or any cloud based computing platform.
Docker makes it really easy to install and run software without worrying about setup or dependencies.

What is Docker?
Docker is a platform or ecosystem around creating and running containers.
When I ran that command, something called the Docker CLI reached out to something called the Docker Hub, and it downloaded a single file called An Image. An image is a single file containing all the dependencies and all the configuration required to run a very specific program. For example, Redis, which is what the image that I just downloaded was supposed to run.
This is a single file that gets stored on your hard drive, and at some point in time you can use this image to create something called a container. A container is an instance of an image, and you can kind of think of it as being like a running program.

Docker for Windows or Mac comes with two pieces of software, Docker Client and Docker Server.
The Docker client, also known as the Docker CLI, is a program that you and I are going to interact with quite a bit from our terminal. We're going to enter in commands to our terminal, issue them to Docker client. It's going to take our commands and figure out what to do with them.
Now, the Docker client itself, doesn't actually do anything with containers or images. Instead, the Docker client is really just a tool or a portal of sorts to help us interact with another piece of software that is included in this Docker for Windows or Mac package called the Docker server. This is also frequently called the Docker Daemon. This program right here is the actual tool or the actual piece of software that is responsible for creating containers, images, maintaining containers, uploading images, and doing just about everything you can possibly imagine around the world of Docker.
Example, so at the terminal, you and I executed the command docker run. Hello world that starts up the Docker client or the Docker cli. Again, the Docker cli is in charge of taking commands from you, kind of doing a little bit of processing on them and then communicating the commands over to something called the Docker server. And it's that Docker server that is really in charge of the heavy lifting. When we ran the command Docker run Hello World, that meant that we wanted to start up a new container using the image with the name of Hello World.
The Hello World Image has a tiny little program inside of it whose sole purpose sole job is to print out the message that you see right here. That's the only purpose of that image.

Now, when we ran that command and it was issued over to the Docker server, a series of actions very quickly occurred in the background. The Docker server saw that we were trying to start up a new container using an image called Hello World. The first thing that the Docker server did was check to see if it already had a local copy, like a copy on your personal machine of the Hello World image or that hello world file. So the Docker server looked into something called the image cache. Now because you and I just installed Docker on our personal computers, the image cache is currently empty. We have no images that have already been downloaded before, so because the image cache was empty, the Docker server decided to reach out to a free service called Docker Hub. The Docker Hub is a repository of free public images that you can freely download and run on your personal computer. So Docker Server reached out to Docker Hub and said, Hey, I'm looking for an image called Hello World. Do you have one? Of course the Docker hub does. So Docker Server downloaded this hello world file and stored it on your personal computer in this image cache where it can now be rerun at some point in the future very quickly without having to re-download it from the Docker hub. So essentially we downloaded it over here like so after that the Docker server then said, okay, great, I've got this image and now it's time to use it to create an instance of a container. And remember what we just said about a container a moment ago. An instance container is an instance of an image. Its sole purpose is to run one very specific program. So the Docker server then essentially took that single file, loaded it up into memory, created a container out of it, and then ran a single program inside of it. And that single program's purpose was to print out the message that you see right here. So that's pretty much it. That's what happens when you run this Docker run command.

Containers
Now, to understand a container, you first need to have a little bit of background on exactly how your operating system runs on your computer. Most operating systems have something called a kernel. Kernel is a running software process that governs access between all the programs that are running on your computer and all the physical hardware that is connected to your computer as well. The other important thing to understand here is that these running programs interact with the kernel through things called system calls. These are essentially like function invocations.
The kernel exposes different endpoints to say, hey, if you want to write a file to the hard drive, call this endpoint or this function right here. It takes some amount of information and then that information will be eventually written to the hard disk or memory or whatever else is required.
Example, Chrome, NodeJS and Python V2 and V3 example.
So the entire process of segmenting a hard or a hardware resource based on the process that is asking for it is known as namespacing. With namespacing, we are allowed to isolate resources per a process or a group of processes.Now, namespacing is not only used for hardware, it can be also used for software elements as well. So for example, we can namespace a process to restrict the area of a hard drive that is available or the network devices that are available, or the ability to talk to other processes or the ability to see other processes. These are all things that we can use namespacing for to essentially limit the resources or kind of redirect requests for resources from a particular process.
Very closely related to this idea of name spacing is another feature called control groups. A control group can be used to limit the amount of resources that a particular process can use. So name spacing is for saying, Hey, this area of the hard drive is for this process. A control group can be used to limit the amount of memory that a process can use, the amount of CPU, the amount of hard drive, input output, and the amount of network bandwidth as well. So these two features put together can be used to really kind of isolate a single process and limit the amount of resources it can talk to and the amount of bandwidth essentially that it can make use of.
And so when people say, Oh yeah, I have a Docker container, you really should not think of these as being like a physical construct that exists inside of your computer. Instead, a container is really a process or a set of processes that have a grouping of resources specifically assigned to it.
Any time that we think about a container, we've got some running process that sends a system call to a kernel. The kernel is going to look at that incoming system call and direct it to a very specific portion of the hard drive, the RAM, CPU or whatever else it might need. And a portion of each of these resources is made available to that singular process.

Any time that we talk about an image, we're really talking about a file system snapshot. So this is essentially kind of like a copy paste of a very specific set of directories or files. And so we might have an image that contains just Chrome and Python. An image will also contain a specific startup command. So here's what happens behind the scenes when we take an image and turn it into a container. First off, the kernel is going to isolate a little section of the hard drive and make it available to just this container. And so we can kind of imagine that after that little subset is created, the file snapshot inside the image is taken and placed into that little segment of the hard drive.
And so now inside of this very specific grouping of resources, we've got a little section of the hard drive that has just chrome and python installed and essentially nothing else. The start up command is then executed, which we can kind of imagine in this case is like start up chrome, just run chrome for me. And so chrome is invoked. We create a new instance of that process and that created process is then isolated to this set of resources inside the container. So that's pretty much it. That is the relationship between a container and an image, and it's how an image is eventually taken and turn into a running container.

Container is a running process, along with a subset of physical resources on your computer that are allocated to that process specifically.

Namespacing and Control Groups are Linux specific features. When you installed Docker for Windows or Docker for Mac just a moment ago, in the last couple of sections, you installed a Linux virtual machine. So, as long as Docker is running, you technically have a Linux virtual machine running on your computer. Inside of this virtual machine is where all of these containers are going to be created. So inside the virtual machine, we have a Linux kernel and that Linux kernel is going to be hosting running processes inside of containers.

docker run <Image Name> command is used to create and run a container from an image. docker in the command references to Docker client, run is for creating and running a container from an image and <Image Name> indicates the name of the image to use for this container.

With Image, we also get a default that was supposed to be executed after the container is created. You can override the default command by appending the new command to the docker run <Image Name> command.
Example, docker run busybox echo Hey there! - This command will echo Hey there!
docker run busybox ls
ls command will list all the files and folders in the container.
Hello World image doesn't have these two programs in its file system, you will get an error if you try to run them using Hello World image. busybox had these programs in its file system so it is able to run them.

Listing All the Running Containers
command - docker ps
docker references to docker client and ps PS command will list all the different running containers that are currently on your machine.
docker run busybox ping google.com - This command is used to ping google.com for quite some time.
Ctrl+C can be used to close a running container.
docker ps --all - this command can be used to list all the container has been created.
docker ps command gives the ID of a running container.

Life Cycle of a Container
Creating a container and actually starting it up are actually two separate processes. So there's actually two additional commands in addition to Docker run that we can use to start up a new container. Docker run, running that at your command line is identical to running two other commands together, first Docker Create and then Docker Start. As you might guess, the Docker Create Commands is used to create a container out of an image and then start is used to actually start an image.
But at this point you might be a little bit curious of, Hey, what's the distinction between creating a container and actually starting a container?
When we create the container, we're just talking about kind setting up the file system snapshot to be used to create the container. To actually start the container, that's when we actually execute this start up command that might start up the process.
Commands
docker run <Image Name>
docker create <Image Name>
docker start -a <Container ID>
-a is used to print the output in the terminal. By default docker run prints the output to terminal.
Just because a container stopped doesn't mean that it's like dead or cannot be used again. We can very easily stop and then start containers again at some point in the future to start a container back up we can take its ID and then execute Docker start and paste that ID in. Remember that if we use Docker start without the -a flag, however, we won't see any output from it. When you have a container that's already been created, we cannot replace that default command. It can only be replaced when you first run the container using docker run command or when you first create the container using docker create.
Examples,
docker run busybox echo Hey there!
docker create busybox echo Hey there!

docker system prune - command is used to delete,

- all stopped containers
- all networks not used by at least one container
- all dangling images
- all dangling build cache (Downloaded images)

How get all the output from the container without having to add in that -a flag?
command - docker logs <Container ID>
One thing to be really clear about is that by running docker logs, we're not rerunning or restarting the container to in any way, shape or form. we're just getting a record of all the logs that have been emitted from that container.

How to stop a running container?
You can stop a running container by using,
docker stop <Container ID>
docker kill <Container ID>
Remember, the ping command is going to essentially go on forever and we have to actually stop it.
In the past, we've just been hitting control C on our keyboard, or we've allowed the container to automatically stop itself, as was the case with the Echo Command. So in order to stop a container that seems to be running on its own, we can issue either the Docker Stop Command or the Docker Kill Command.
Both these are going to stop the running container, but they look like they kind of do the same thing here. So what's the difference between them?
Well, here's what happens behind the scenes. When you issue a Docker stop command, a hardware signal is sent to the process or the primary process inside that container. In the case of Docker stop, we send a SIGTERM message which is short for terminate signal. It's a message that's going to be received by the process, telling it essentially to shut down on its own time.
SIGTERM is used any time that you want to stop a process inside of your container and shut the container down and you want to give that process inside there a little bit of time to shut itself down and do a little bit of cleanup.
On the other hand, the Docker kill command issues a SIGKILL or kill signal to the primary running process inside the container. SIGKILL essentially means you have to shut down right now and you do not get to do any additional work. So ideally we always stop a container with the Docker stop command in order to get the running process inside of it a little bit of time to shut itself down.
When you issue Docker stop to a container. If the container does not automatically stop in 10 seconds, then Docker is going to automatically fall back to issuing the Docker Kill Command.
Example where you need to use docker kill is busybox ping google.com command.

How do you execute an additional command in a container?
docker exec -it <Container ID> <Command>
Docker references to docker client
exec is for running another command
-it allows us to provide input to the container
To understand -it flag, the first thing you need to understand is a little bit more about how processes run inside of a Linux. Every process that we create in a Linux environment has three communication channels attached to it that we refer to as standard in (STDIN), standard out (STDOUT) and standard error (STDERR). These channels are used to communicate information either into the process or out of the process. Standard in, as you might guess, is used to communicate information into the process. standard out channel that is attached to any given process is going to convey information that is coming from the process. So standard out might be redirected over to your running terminal and that's going to end up as being stuff that is going to show up on the screen. Standard error is very similar to STDOUT, but it conveys information out of the process that is kind of like an error in nature.
-it is -i -t, to shorten we write it as -it
-i means when we execute this new command inside the container, we want to attach our terminal to the standard in channel of that new running process. we are make sure that any stuff that we type gets directed to standard in of process.
-t flag is what kind of makes all this checks to show up a little bit pretty.
But at the end of the day, the real effect that the -ti flag is to make sure that all the text that you are entering in and that is coming out shows up in a nicely formatted manner on your screen.

How to open shell in a running container?
docker exec -it <Container ID> sh
sh is a command processor similar to PowerShell, bash and zsh.

How to open shell in a new container?
docker run -it busybox sh
But this will prevent the default command from running in the image.

If ctrl+C doesn't exit you from a container, you can also use ctrl+D or you can also just type exit.

Two containers do not automatically share their file system by default unless we specify them to.

we can build our own custom images so that we can run our own applications inside of our own personalized containers. How to create our own image?
The process for making an image is somewhat straightforward. We're going to create something called a Docker file. A docker file is essentially a plain text file that is going to have a couple of lines of configuration placed inside of it. This configuration is going to define how our container behaves, or more specifically, what different programs it's going to contain and what it does when it starts up as a container. Once we create that Docker file, we'll then pass it off to the Docker client, which you will recall is the Docker CLI that we've been using at our terminal. In turn, the Docker client will provide the file to the Docker server. The Docker server is what is doing the heavy lifting for us. It's going to take the Docker file, look at all the lines of configuration that we have inside of it, and then build a usable image that can then be used to start up a new container.

# Use an existing an docker image as a base

FROM alpine

# Download and install a dependency

RUN apk add --update redis

# Tell the image what to do when it starts as a container

CMD ["redis-server"]

# FROM RUN CMD are instructions telling docker server what to do

# alpine apk add --update redis ["redis-server"] are arguments to the instruction

The real takeaway that I want you to understand here is that basically along every step along the way with every additional instruction, we essentially take the image that was generated during the previous step. We create a new container out of it. We execute a command in the container or make a change to its file system. We then look at that container. We take a snapshot of its file system and save it as an output image for the next instruction along the chain. And then when there is no more instructions to execute, the image that was generated during that last step is output from the entire process as the final image.

Any time that we make a change to our Docker file, we're going to have to only rerun the series of steps from the change line on down. For the first steps it is going to use cache. When the order of operations is different, cache cannot be used. The real lesson to learn here is that if you ever expect to have to change your Docker file, you always want to put those changes like as far down as possible.
By changing the order of operations inside of our Docker file, we can dramatically change how long it takes to rebuild our image.
How to tag a name to an image?
docker build -t saikumaraleti/redis:latest
docker references to the docker client
build is to build an image
-t YourDockerID/ProjectName:Version is to tag an image to a name or vice versa.
So we call this process tagging the image, but technically just the version on the end is the tag. Everything else on here is actually like the repository or the project name.

There is a fluid relationship between images and containers. You can create an image from a running container.
Example,
docker run -it alpine sh
apk add --update redis
docker commit -c "CMD 'redis-server'" <Container ID>
docker run <Image ID>

Finding repositories on hub.docker.com?
Alpine is a term in the Docker world for an image that is as small and compact as possible. Many popular repositories are going to offer Alpine versions of their image. An alpine version of Node in particular means that you're not going to get a bunch of additional preinstalled programs.

You cannot reach to the inside port of a container unless you specify it in the docker file but the container can reach out to the outer world automatically. There is only a limitation on the incoming traffic, there is no strict limitation on the outgoing traffic.
To get around this limitation we need to forward the incoming requests to your local host to the container. We do not set up port forwarding inside the Docker file. The port forwarding stuff is strictly a runtime constraint.

Docker Run with Port Mapping
docker run -p 8080:8080 <Image Name>
8080:8080 routes incoming requests to the port 8080 on local host to port 8080 on the container.

The port from the source machine or the source network that we are mapping from to the port that we are mapping to inside the container do not have to be identical.

Docker Commands
docker build -t saikumaraleti/post .
Build an image based on the docker file in the current directory. Tag it as saikumaraleti/posts.

docker run <Image ID or Image Tag>
Create and start a container based on the provided image id or image tag.

docker run -it <Image ID or Image Tag> <Command>
Creates and starts a container but also overrides the default command and give us the ability to provide input into the container.

docker ps
Prints out information about all running containers.

docker exec -it <Container ID> <Command>
Executes the given command in a running container and gives us the ability to provide input into the container.

docker logs <Container ID>
Prints out logs form the given container.

An instance of an image is a container.

Kubernetes
Kubernetes is a tool for running a bunch of different containers together. It will automatically manage the creation and management of all these different containers. And it's also going to make it really easy for us to communicate between these containers as well.

Kubernetes Tour
Whenever we want to use Kubernetes, we have to have a set of images that are already ready to go. So of course, we're going to want to actually create a container out of this thing at some point in time.
A node is a virtual machine. It is essentially a computer that's going to run some number of containers for us. We are currently running Kubernetes on our own personal computers. If you're running Kubernetes on your own computer, it's extremely likely that you are only running one node by default. So you have one virtual machine. It's only once we start deploying to some cloud provider that we're going to have multiple nodes. And we're going to want to deploy it into our Kubernetes cluster.
To create a container out of image, we are going to first create a configuration file. In this configuration file, we're going to list out some very explicit directions for Kubernetes. We're going to say, please create two copies of the post image for us, make containers and host them somewhere on these nodes. Inside that config file, we are also going to say please allow these copies of posts to be accessible. So in other words, set up some networking on these things. So we're going to feed that thing into Kubernetes. KUBECTL is used to interact with our Kubernetes cluster.
The Kubernetes cluster is going to read this config file, read it over and say, okay, well, I guess I got to create two containers out of posts. So Kubernetes is going to look out among the world and try to find a copy of this post image. It's going to first take a look at the Docker Daemon or the Docker server running on your personal computer. So on your computer, we have built up that post image already. So Kubernetes is going to look at your local machine and see if there is a copy of that image already available. If it is not available, then it will default to looking out at Docker Hub and try to find a copy of that image there instead. Either way, Kubernetes is going to try to find that image. Once it finds that image. It's going to create two containers and randomly distribute among these nodes.
Each container that is created is going to be hosted or essentially created inside of something called a pod. For the purposes of this course, this is really important. For the purposes of this course, you and I are going to be pretty much using the terms pod and container interchangeably. Pods and containers in reality are not the same thing, but in the context of this course it is good enough and I feel pretty strongly about this. A pod, technically, wraps up a container and technically a pod can have multiple containers inside of it. We've asked for two containers, so Kubernetes is going to create two pods. Each of them is going to have a copy of the post container running inside of it. In order to manage these two pods, Kubernetes is also going to create something called a deployment. So this deployment is going to be in charge of managing both these pods if anything ever goes wrong with these pods, so if it crashes for some reason or if it just stops running or whatever else might possibly happen, this deployment right here is going to make sure that that pod automatically gets recreated for us. So the deployment is in charge of making sure that we've always got the right number of pods and that these pods are running successfully.
So now second step here in the config file. We had also said, please allow copies of post service to be accessible from network. Kubernetes is going to create something else called a service. Kubernetes service is something that provides a very easy to remember URL so that other running pods or containers inside of our cluster can very easily access another pod.

Terminology
Kubernetes Cluster - A collection of nodes and a master to manage them.
Node - A virtual machine that will run our containers.
Pod - More or less a running container. Technically, a pod can run multiple containers.
Deployment - Monitors a set of pods, make sure they are running and restarts them if they crash. Deployments are going to monitor a set of identical pods or pods that are meant to run the same container inside them if anything ever happens to those pods, so for example, if the pod crashes or just stops running for any reason, the deployment is going to see that and automatically restart that pod for us.
Service - Kubernetes service is something that provides a very easy to remember URL so that other running pods or containers inside of our cluster can very easily access another pod.

Config Files
Config files that we're going to write are going to tell Kubernetes about the different deployments, pods and services that we want to create inside of our cluster. Collectively deployments, pods and services are called objects.
Config files are entirely written in YAML syntax.
Always store the config files with our project source code. The reason for that is that these config files that we write are really documentation of what our Kubernetes cluster is doing. It's really the best documentation.
We can create an object without using config files but do no do this. Config files provides a precise definition of what your cluster is running. It is highly not recommended even if kubernetes docs and other blog posts say otherwise.

Config File Example
apiVersion: v1
kind: Pod
metadata:
name: posts
spec:
containers: - name: posts
image: saikumaraleti/posts:0.0.1

apiVersion: v1 - It turns out that Kubernetes is actually a really extensible when you install Kubernetes on your local machine. It comes with a preset list of different objects we can create, so we can create things like pods, deployment, services and so on. We can also tell Kubernetes about custom objects that we want to create as well. The goal of API version right here is to tell Kubernetes about the pool of objects that we want to draw from. So in this case, we are telling Kubernetes, look at the default V1 Kubernetes list of objects that we can create.

kind: Pod - We specify the exact type of object that we want to create from that pool. So in this case, we want to create an object of type pod. And remember, a pod is going to wrap up a container.

metadata:
name: posts - Metadata is going to include some different configuration options we want to apply to the object we're about to create. By far, the most common option that we're going to apply is the name property.

spec:
containers: - name: posts
image: saikumaraleti/posts:0.0.1 - spec or specification is going to have a list of configuration options for the port that we're about to create. This is a very precise definition that controls exactly what should be going on inside this pod and how the pod should behave. The only required property we have to throw in here is containers. Containers is going to be an array. That's what the little dash right there meant inside of our YAML file. So we could technically have many containers inside this one pod. But in our case we have just one. So one array entry. For that one array entry we're going to provide some configuration for this one container that's going to be created inside the pod. The container that's going to be created will be given a name of posts as well. Now, one thing I want to mention really quickly is that the name properties here don't really have a lot of big importance. You can provide a name of whatever makes sense to you. We also provide the image property and this is going to be the exact tag applied to some image.

Important Kubernetes Commands
kubectl get pods - Print out information about all running pods.
kubectl exec -it <Pod Name> <Command> - Executes the given command in a running pod.
kubectl logs <Pod Name> - Prints out logs from a given pod.
kubectl delete pod <Pod Name> - Deletes the given pod.
kubectl apply -f <Config File Name ending with .yml> - Tells Kubernetes to process the config file.
kubectl describe pod <Pod Name> - Prints out information about the running pod.

It turns out that we usually do not create pods in this style. So in the world of Kubernetes, we have pods, and those are intended to run some container really inside them. Rather than creating these pods directly, however, we're usually going to create something called a deployment. A deployment is a Kubernetes object that is intended to manage a set of pods, so it might be just one single pod, or it might be ten different pods or even 100 different pods. Each of these pods are going to be identical in nature. They will all be running the same configuration, same container inside them. The deployment has two big jobs assigned to it. The deployment, first off, is going to make sure that if any pod mysteriously just disappears for any reason. In other words, if the pod crashes for any reason whatsoever, then the deployment will automatically create that pod for us. That's the primary job of the deployment.
The second reason is that, well, at some point in time you and I are going to decide to update the code inside of our different containers. In other words, we might start off with all of our pods running a version 1 of some applications we put together. But then at some time we might tell the deployment that we want to roll out a new version, maybe version two. The deployment is going to take care of this update for us automatically. Behind the scenes, the deployment is going to create same number of new pods running this new version of our application. After these pods are up and running successfully, the deployment is then going to start to manage that set of pods and the deployment will start to sunset or essentially delete the old pods for us automatically.
So we can really think of the deployment as just being a manager. It manages a set of pods. That is what it is all about.

Config For a Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
name: posts-depl
spec:
replicas: 1
selector:
matchLabels:
app: posts
template:
metadata:
labels:
app: posts
spec:
containers: - name: posts
image: saikumaraleti/posts:0.0.1

Deployment Commands
kubectl get deployments - Lists all the running deployments.
kubectl describe deployment <Deployment Name> - Prints out details about a specific deployment.
kubectl apply -f <Config File Name> - create a deployment out of a configuration file.
kubectl delete deployment <Deployment Name> - Deletes a deployment.
kubectl rollout restart deployment <Deployment Name> - To update the image used by a deployment

Updating Image in Our Deployment

Method - 1

1. Make an update to our code.
2. Create a new image with a different version tag, for example, docker build -t saikumaraleti/posts:0.0.2
3. Update the Kubernetes configuration file to include the new version in the spec section under image.
4. Run the command kubectl apply -f posts-depl.yaml to re-configure the deployment.

Method -2

1. We're going to always use the latest tag
2. Make an update to our code
3. Push the image to Docker using docker push <Image ID or Image Tag>
4. Run the command kubectl rollout restart deployment <Deployment Name> and we're good to go.

Our cluster is now running the latest version of all of our code.

How do we actually make a request to the application?
A service is another kind of object in Kubernetes. It's an object very similar to how a pod is an object or a deployment is an object. We create services using config files just as we do pods and deployments. We are going to make use of services to set up some communication between all of our different pods or to get access to a pod from the outside of our cluster.
So just about every pod or every deployment we create is always going to have some kind of matching service along with it.
Behind the scenes, there are actually several different kinds of services. There are four different types of services that we need to be aware of, but effectively there's only two. Cluster IP and Load Balancer are the services that we're going to be using on a very daily basis.
Cluster IP - Cluster IP services are used any time that we want to set up communication between different pods inside of our cluster.
NodePort - NodePorts are used anytime you want to access a pod from outside of our cluster, but we only usually use a node port for development purposes.
Load Balancer - Make a pod available form outside the cluster. This the right way to expose a pod to the outside world.
External Name - Redirects an in-cluster request to a CNAME url.

Sample Configuration File of a Service
apiVersion: v1
kind: Service
metadata:
name: posts-srv
spec:
type: NodePort
selector:
app: posts
ports: - name: posts
protocol: TCP
port: 4000
targetPort: 4000

targetPort is the actual port that our application is listening for traffic on. That's where we ultimately send traffic to.
port is the port on the NodePort service. NodePort will re-direct the traffic on the port to the targetPort.
NodePort is a randomly assigned port that is used to get access to the NodePort service form outside of our cluster. This values ranges from 30000 to 32000.

Cluster IP Service
Let's take the example of posts communicating with Event Bus and vice versa. They cannot communicate between each other directly instead they are going to reach out to other services Cluster IP service. So, posts and event bus will having their own cluster IP services.
The cluster IP services are going to give us a well known, very easy to predict URL that we can type in any time we want to have our event bus communicate over to posts or anytime we want post to communicate over to event bus.

Load Balancer
Our React application is actually the Create React App Development server. We're going to house this thing inside of a pod. We're going to create a new Docker image for our React application or the development server. We're then going to create a container inside of a pod and place it inside of our cluster.
One thing I want to clarify about what this React app dev server does is that whenever a user opens up their browser and navigates to our application. We're going to really be attempting to connect to that React app dev server. The only goal of that dev server is to take code that you and I write inside of a React application and then generate some HTML, JavaScript and CSS files out of it. So during the initial navigation attempt to our application running inside this cluster, the only thing this React app dev server is doing is returning some HTML, CSS and JavaScript. What this React app server is not doing is making any requests at any point in time, over to posts, over to comments, query anything like that.

The actual requests for data are being issued from the user's browser. So we send over that HTML, CSS and JavaScript stuff, then the React app boots up inside the user's browser and then inside of the browser, the React app is going to try to reach out to the query service and get some posts and comments after serving up the initial HTML, CSS and JavaScript stuff. The development server, it is no longer relevant whatsoever, so no requests are being issued from that dev server directly over to our other pots. All these requests are coming from the browser.

How are we going to make sure that the React app can somehow make requests to all of these different pods?
So to make sure that the React app can reach out to all these different pods and the containers running inside them, we have two options available to us. One option is going to be pretty bad and we're definitely not going to do it.

So option number one, to make sure that the React application can reach out to these different pods, we could create a NodePort service for posts, comments, query and moderation services. Remember that a node port service is going to expose a pod to the outside world. So inside of our React application, we could write out some code that would make requests to these different NodePort services and access the underlying pods to get all the information that is required. The reason that this is not a good approach is that, remember, when we create a NodePort service, we are essentially opening up a random port. We can't technically specify an exact port, but more or less let's just say it's a random port inside of our cluster. And so if we ever changed a NodePort services, there's the possibility that, who knows, maybe the port that we get assigned would be different. And so if we ever start to change these ports, we would have to go back to a React application and change the port inside of our code and update it to reflect the new NodePort.
The right way to do this stuff is by creating a load balancer service. The goal of this load balancer service is to have one single point of entry into our entire cluster. We're then going to make sure that our React application is going to make requests to access that load balancer service, and then we're going to place some logic so actual code or configuration inside of this load balancer service that's going to take these incoming requests and then route them off to the appropriate pod. And just to be clear, when I say write it off to the appropriate pod, once again, what we're really reaching out to is the cluster IP service that we're going to create for each of these different pods. Response comes through the load balancer and back to the React application.

Now, it turns out that this entire idea of a load balancer service is a little bit more intricate than at first glance. So when we talk about a load balancer service, we're really talking about two distinct things in the world of Kubernetes. So first off, there is something in Kubernetes called a load balancer service. Second off, there is something that is also very closely related Load Balancer service referred to as ingress or an ingress controller. Now, technically, an ingress and ingress controller are two different things, but for the purposes of this course, we're going to kind of refer to them with interchangeable terms.
A load balancer service is going to be something that tells our cluster to reach out to its provider and provision a load balancer. The goal of a load balancer service is to get traffic into a single pod.
On the other hand, an ingress or ingress controller is a pod that has a set of routing rules in it that are going to distribute traffic to other services inside of our cluster.
The goal of a load balancer service tell our cluster to reach out to its provider and provision a load balancer with the goal of getting some traffic into a pod in our cluster.
A load balancer by itself is not super useful for you and I right now. Instead, you want to get traffic distributed to a set of different pods and we want to have some routing rules inside of something to decide where to send this traffic to. And that is where this idea of an ingress or ingress controller is going to come into play. So the ingress or ingress controller is a pod that has a set of routing rules inside of it. It's going to work alongside this load balancer service.
Example,
we've got our cluster, it's being hosted with some cloud provider and we want to get some outside traffic into these set of different pods. And these are different pods, say, the posts, comments, query, whatever else. So we are going to have a request coming in still to a load balancer that has been provisioned with our cloud provider but now this load balancer is going to send that request on to this ingress controller and this ingress controller is going to have a set of routing rules inside of it. Ingress controller is going to look at the path of the incoming request and then decide based upon that path to send to the appropriate pod. The load balancer is just about getting traffic into our cluster. Ingress is about routing rules or having some routing configuration that's going to send requests off to the appropriate pod.
Now that we've got some semblance of an idea between the difference of a load balancer and an ingress controller, let's take a look at the actual implementation of this ingress controller and load balancer that we're going to use inside of our cluster. So we are going to make use of a project or open source library called ingress-nginx. This is going to create a load balancer service inside of our cluster, plus an ingress automatically.

Sample Ingress Config File
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
name: ingress-srv
annotations:
kubernetes.io/ingress.class: nginx
spec:
rules: - host: posts.com
http:
paths: - path: /posts
pathType: Prefix
backend:
service:
name: posts-clusterip-srv
port:
number: 4000

This entire annotation section is what's going to help the ingress controller understand that we are trying to feed it some routing rules, so the ingress controller is going to continuously scan all the different config files we're throwing into our cluster and it's going to try to find one that has this exact annotation right here. When it finds this, the ingress controller is going to say, Oh, this must have some routing rules for me.
Rules is going to have all the different routing rules that we want to apply to teach the ingress controller how to take incoming traffic and route them off to our different pots. Rules is going to be an array.

We just put together our first ingress config file, but inside of here was a kind of mysterious rule that said host. With Kubernetes we can host a ton of infrastructure, we're not necessarily limited to just hosting one singular application. So in other words, we could host many different apps at many different domains inside of a single Kubernetes cluster. The infrastructure for different applications at different domains can be hosted inside of one single Kubernetes cluster. Ingress Engine X is set up, assuming that you might be hosting many different apps at different domains so that's what this host property right here is all about. Now there's kind of a good side and bad side to this. The bad side is that in the development environment, we are used to accessing all of our different running servers at localhost, So how does this kind of routing rule or this idea of having some specific domain really stack up with ingress engine X?
Well, in the development environment, what we have to do is trick our local machine into thinking that posts.com is equivalent to localhost. So in other words, whenever we try to connect to post, we're going to trick our computer to connecting to our local machine rather than the real post that I'm sure exists out there somewhere online.
You can make this change at C:\Windows\System32\drivers\etc\hosts
127.0.0.1 posts.com

Now we are writing out routing configuration that's going to be interpreted by nginx and nginx does not support route params, instead, we write up paths and if we ever expect to have some kind of wild card inside of it, we have to use a regular expression. Regular expression is ?(.\*).
In oder to use regular expression in our path, we need a piece of configuration to our annotation section, nginx.ingress.kubernetes.io/use-regex: 'true'
This is going to allow us to use regular expressions in all of our paths.

Skaffold
Making changes to our code base right now is a real pain.
The process we are following right now is,

1. We're going to always use the latest tag
2. Make an update to our code
3. Push the image to Docker using docker push <Image ID or Image Tag>
4. Run the command kubectl rollout restart deployment <Deployment Name> and we're good to go.

It was still important for us to understand this process right here, because this is how we're going to eventually deploy code in a production style environment. But in a development environment, clearly this is not a very great approach. So when we are developing code actively inside of a Kubernetes cluster, we are going to instead use a tool called Skaffold. SKaffold is a command line tool that we're going to use to automatically do a lot of different tasks in our Kubernetes dev environment. The number one thing that we are interested in with Skaffold is that it makes it super easy to update code in a running pot. The other really great thing about scaffold is that it makes it very easy for us to create and delete all the different objects that are tied to some project very, very quickly.

You can install by using the below command,
choco install -y skaffold

The above step assumes chocolatey package manager is already installed, if not, you can install it using the command,
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

Now that we've got scaffold installed, we are going to configure it by writing out yet another config file. This config file is going to tell scaffold how to manage all the different sub-projects inside of our cluster.
The config that we're going to write inside of here is going to look rather similar to a lot of the configuration that we've already written to apply to a Kubernetes cluster. But everything we write inside of here does not get applied to Kubernetes. Instead, it is just consumed by Skaffold directly and scaffold is a tool that runs outside of our cluster.
A lot of these scaffold files we're going to write are going to be just about identical so this is another scenario where you pretty much just learn it once and then start to kind of copy paste stuff around when appropriate.

deploy:
kubectl:
manifests: - ./infra/k8s/\*

This is telling scaffold that there is a collection of different config files intended for Kubernetes inside of the infra/k8s directory. So by adding in this manifest line, we are telling scaffold that we want it to watch all of these different YAML files. Any time that we make a change to one of those files, Scaffold is going to automatically reapply that config file to our Kubernetes cluster.
Scaffold is also going to make sure to create all these or apply all these any time that we start scaffold up. It's also going to delete all the objects associated with these config files whenever we stop scaffold as well. So in total, this is really doing three things. When we start up scaffold, apply these files. When we make a change, apply these files whenever we stop scaffold, find all the objects related to these config files and delete them.

build:
local:
push: false

So by default, whenever scaffold makes a change to one of our images or rebuilds an image, it's going to try to push it up to Docker Hub that is not actually required when we're using scaffold. So we're going to disable that default behavior with the setting right here.

artifacts: - image: saikumaraleti/client
context: client
docker:
dockerfile: Dockerfile
sync:
manual: - src: 'src/\*_/_.js'
dest: .

Artifacts section is telling scaffold about something inside of our project that it needs to maintain. We're essentially saying that there is going to be some pod that is running code out of the client directory inside of our project. Whenever something changes inside this directory, scaffold is going to try to take those changes and somehow update our pod. There are two ways in which scaffold is going to try to update our pod. First off, if we make a change to a JavaScript file as indicated by this little matcher right here, if we make a change to a JavaScript file, then scaffold is going to try to take that changed file and just directly throw it into our pod. It's going to literally take that file and copy it directly into the pod itself. So our pod is going to always have the latest code inside of it. If we ever make a change to anything inside this client directory that is not matched up by this src tag, then scaffold is going to instead try to rebuild the entire image.

Fortunately to start up skaffold, you're really just have to remember exactly one command, skaffold dev

The nice thing about skaffold is that it's going to take all the logs coming out of these pods and print them up easily read inside of our terminal.

When using skaffold you need something that can see the changes in the pod and restart the client or service. For example, nodemon or create-react-app (Restarts the container and refreshes the webpage). I just want to throw it out there right now that if you ever feel like you're making changes but they're not being reflected, it's probably tied to the fact that we sometimes have challenges detecting file changes inside of containers.

Typescript
What is Typescript?
Well, at the end of the day, I want you to imagine that when we write Typescript code, we are really just writing JavaScript. That's what's really happening here, we're still writing JavaScript. All the knowledge of JavaScript still applies to the world of TypeScript. The only thing that we're really doing with TypeScript is adding in a little bit additional syntax to our code to handle something called the type system.

The TS Type System
The goal of the type system is to help us catch errors during development. In other words, when we're actually writing our code in our code editor,imagine that there's a bug inside of it. How would you find that bug? Well, really, with JavaScript, the only way to do that is to actually execute your code and see if you have any errors. That's not super efficient. So as an improvement to the development workflow, we use the type system to help us catch errors during development. While you and I are writing our code, TypeScript is going to be constantly analyzing it and looking for bugs. If it finds any possible bug, it's then going to pop open an error message inside of your code editor and tell you, hey, something might be wrong here and that's going be a signal to you as the developer that you might need to fix up your code. Now to do this error checking the TypeScript compiler is going to use something called type annotations to analyze our code base. You and I are going to be responsible for adding in these type annotations. You can kind of think of these type annotations as being like little comments to describe the purpose of our code or the information that is flowing through our program.
The type system is only active during development. So in other words, once we go to deploy our application or even run it inside of our browser in a development environment, the entire type system falls away. Your browser has no idea what TypeScript is and Node.js likewise has no idea. So all this extra syntax that we're going to add into our code never makes it into the browser. It never makes it into Node.js. We're going to first compile our typescript and we're going to get some JavaScript out of that and nd that JavaScript is what we're going to actually execute. The TypeScript compiler does not do any performance optimization whatsoever.
Once we want to run our code, we're going to feed that code into the TypeScript compiler. This is a command line tool that's going to read our code, check it for any possible errors, and then convert it into plain JavaScript. You and I are then going to take that JavaScript code and feed it into the browser or into Node.js and finally execute it. So again, we do not execute TypeScript. We always are executing JavaScript.

TypeScript has no effect on how our code actually gets executed. In other words, there is no performance optimization or anything like that.

Command to install TypeScript Compiler and TS-Node
npm install -g typescript ts-node

ts-node is a command line tool that allows us to compile and execute TypeScript with one command at our terminal.
Installing typescript gives us access to tsc, type script compiler.
