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
