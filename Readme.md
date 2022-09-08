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
   Docker
