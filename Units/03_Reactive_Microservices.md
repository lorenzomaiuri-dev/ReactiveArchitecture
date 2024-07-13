# Reactive Microservices

## Table of Contents

- [Reactive Microservices](#reactive-microservices)
  - [Table of Contents](#table-of-contents)
  - [The Software Spectrum](#the-software-spectrum)
  - [Monoliths](#monoliths)
  - [Microservices](#microservices)
    - [Service Oriented Architecture](#service-oriented-architecture)
    - [About Microservices](#about-microservices)
    - [Responsibilities of Microservices](#responsibilities-of-microservices)
  - [Principles of Isolation](#principles-of-isolation)
    - [Isolation of state](#isolation-of-state)
    - [Isolation of space](#isolation-of-space)
    - [Isolation of time](#isolation-of-time)
    - [Isolation of failure](#isolation-of-failure)
  - [Isolation Techniques](#isolation-techniques)
    - [Bulkheading](#bulkheading)
    - [Circuit Breakers](#circuit-breakers)
      - [Overloading a service](#overloading-a-service)
    - [Message Driven Architecture](#message-driven-architecture)
    - [Autonomy](#autonomy)
    - [Gateway Services](#gateway-services)

## The Software Spectrum

When deciding whether to build a system as a **monolith** or **microservices** we need to:\
Consider the benefits, and consequences of each, and try to find an **appropriate balance** between them

A single system can have some characteristics of a Monolith and other characteristics of Microservices

## Monoliths

Monolith:

- **No clear isolation** in the application
- **Complex** dipendencies
- **Hard to understand** and to modify
- Deployed as a **single unit**
- Single **shared database**
- Communicate with **synchronous** calls
- **Coupling** between libraries and components
- "**All in one**" releases
- **Long cycle** times
- Features and releases **carefully synchronized**

Scaling a monolith:

- **Multiple copies** deployed
- Each copy is **independent**, they don't communicate
- The database provides **consistency**

Advantages:

- **Easy cross module** refactoring
- Easier to **mantain consistency**
- **Single deployment** process
- Single thing to **monitor**
- **Simple scalability** model

Disadvantages:

- **Limited by size** of the machine
- Only **scale as the database** can
- Components must be **scaled as a group**
- Deep coupling leads to **inflexibility**
- Development is **slow**
- Failure in one component **bring down the whole system**
- Redistribution of load can cause **cascading faliures**

## Microservices

### Service Oriented Architecture

Additional isolation in a monolith is provided using **Service Oriented Architecture**\
Services **don't share a database**\
All access must go through the **API exposed by the service**\
Services may live in the same process (monolith) or may be separated (microservices)

### About Microservices

Microservices:

- Are a **subset** of Service Oriented Architecture
- **Logical components are separated** into isolated microservices
- Microservices can be **phisically separated** and indipendently deployed
- Each microservice has its own **independent database**
- Microservices are **independent** and self governed
- Communication can be **synchronous or asynchronous** (Reative Microservices)
- Rapid **independent deployment**
- **Loose coupling** between microservices

Scaling a microservices:

- Each microservices is **scaled indipendently**
- Coul be **one or more copies** per machine
- Each machine host a **subset of the entire system**

Advantages:

- Increased **availability** since failures are isolated to single components
- More **flexibility** provided by isolation
- Support for **multiple languages and platforms**

Disadvantages:

- Multiple **complex deployment and monitoring**
- **Cross services refactoring** more difficult
- Requires **supporting old API** versions

### Responsibilities of Microservices

The original Single Responsibility Principle, from Robert C. Martin states:\
**A class should have only one reason to change**

Part of following the Single Responsibility Principle for microservices means:\
**A change to the internals of your microservice, shouldn't force a change on other services**

## Principles of Isolation

Reactive Microservices are isolated in:

- State
- Space
- Time
- Failure

### Isolation of state

All **access** to a microservice state must **go through its API**\
**No backdoor** to access the database\
Allows microservice to **evolve internally without affecting the outside**

### Isolation of space

Microservices **should not care about where other microservices are deployed**\
It should be possible to **move a microservice** to a different machine or datacenter without issues

### Isolation of time

Microservices **should not wait** for each other\
Requests are **asynchronous and non-blocking**\
Resources can be **freed immediately**\
We expect **eventual consistency**

### Isolation of failure

Reactive microservices also **isolate failures**\
A failure in one Microservice **should not cause another failure**\
Allow the system to **remain operational**

## Isolation Techniques

### Bulkheading

Failures are **isolated to zones**\
Failures in one service will **not propagate** to other services\
The system remains **operational in a degraded state**

### Circuit Breakers

#### Overloading a service

Calls to **overloaded service may fail**\
The caller may **retry making the load worse**\
Caller needs to avoid this

Circuit breakers are a way to **avoid overloading** a service\
They quarantine a failing service so **it can fail fast**\
Allows the failing service **time to recover** without overloading it

### Message Driven Architecture

Asynchronous non-blocking messages allows us to **decouple Time and Failure**\
Resources can be **freed immediately**\
Other messages can be processed while we wait for a request to complete\
Responses are allowed to take time, resulting in a **less brittle system**

### Autonomy

Services can **operate independently** from each other\
Microservice **guarantee their own behaviour** through their API\
**Fewer dependencies** means more autonomy\
To make a service autonomous we need to ensure it has enough information to **repair failures or resolve conflicts**\
When a dependency exists on data from an external service, we can add autonomy by **creating a local copy of the data** from that service
A fully autonomous system **could be scaled indefinitely** (assuming enough resources were available)

### Gateway Services

Monolithic applications typically aggregate data by collecting the data from the database using techniques such as "joins" or "views" where necessary

Increasing the complexity of a client is problematic because:

- It becomes harder to deprecate old microservices or APIs
- Clients are not guaranteed to ever update
- Updates could be controlled by a third party (Google, Apple etc.)

Requests can be sent to a **Gateway Service**\
It sends the requests to multiple microservices and **aggregate the results**\
The **aggregation logic is moved** from the client to the gateway\
It **handle failures from each microservice**\
The client **only handle failures from the gateway**\
Gateway Services **increase isolation** in the system
