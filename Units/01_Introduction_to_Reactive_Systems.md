# Introduction to Reactive Systems

## Table of Contents

- [Introduction to Reactive Systems](#introduction-to-reactive-systems)
  - [Table of Contents](#table-of-contents)
  - [Why Reactive?](#why-reactive)
    - [Data at rest](#data-at-rest)
    - [Streaming Data](#streaming-data)
    - [Modern Software Users](#modern-software-users)
    - [Failures](#failures)
  - [The Reactive Principles](#the-reactive-principles)
    - [What are the Reactive Principles?](#what-are-the-reactive-principles)
      - [Responive](#responive)
      - [Resilient](#resilient)
      - [Elastic](#elastic)
      - [Message Driven](#message-driven)
  - [Reactive Systems vs Reactive Programming](#reactive-systems-vs-reactive-programming)
    - [Reactive Programming](#reactive-programming)
    - [The Actor Model](#the-actor-model)

## Why Reactive?

The scale of modern systems far exceeds that of legacy systems, consisting of **hundreds** or even **thousands** of nodes

### Data at rest

The data is not consumed at the time it is injested. It is stored, and then **consumed later** in a batch process

### Streaming Data

A firehose of data. Data can flow through the system at **any time** with **no upper bound** on the amount of data

### Modern Software Users

Users of modern web based software expect: That the software will be available when they need it. There is **no downtime**

### Failures

**Failure in systems is inevitable**. Our goal should be to design systems that handle those failures gracefully. We accept that failure is a fact of life, and we build a system that will lessen the impact of those failures on the users. The system should continue to provide some functionality despite failures

## The Reactive Principles

### What are the Reactive Principles?

Responsive, Resilient, Elastic, Message Driven

#### Responive

A reactive system consistently **responds in time**

Responsiveness is the **primary goal**

#### Resilient

A reactive system remains **responsive even when failures occur**

Failures are **isolated to a single component** in the system.

#### Elastic

A reactive system remains **responsive despite changes to system load**

Elasticity means **we don't sacrifice responsiveness** even during a sudden and dramatic increase in load\
Resources are scaled both up or down based on the load

#### Message Driven

A reactive system is built on top of **asynchronous non-blocking messages**

Non-blocking messaging means:\
After sending a message, the sender **doesn’t have to wait for a reply** from the recipient before doing other work

## Reactive Systems vs Reactive Programming

### Reactive Programming

Reactive Systems apply the Reactive Principles at the **Architectural level**

Reactive Programming techniques such as **Futures/Promises** can be used to build Reactive Systems

In a Reactive System, the primary form of communication between major parts (eg. Microservices) should be **asynchronous**

A Reactive System, at a minimum, must be deployed to a **cluster of machine nodes**

### The Actor Model

The Actor Model enables **elasticity** and **resilience**

All computation occurs **inside** of Actors\
Each actors has an **address**\
They communicate using **asynchronous messages**

An Actor doesn’t care if it communicates with another Actor **locally** or **remotely**. It accomplishes this because:\
All communication between Actors is treated as **remote communication**, regardless of the location of the actor.
The messages are sent to the routers that figures out where to send them

**Transparent Remoting** makes **remote calls look like local calls**\
**Location Transparency** makes **local calls look like remote calls**

**Location transparency** enables Actors to be both **resilient** and **elastic**

It’s possible to build a Reactive System without using the Actor Model just using various Reactive Programming techniques (such as Futures/Promises, Streams).\
You could need **Service Registry**, **Message Bus**
