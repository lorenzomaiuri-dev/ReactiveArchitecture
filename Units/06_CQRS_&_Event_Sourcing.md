# CQRS & Event Sourcing

## Table of Contents

- [CQRS \& Event Sourcing](#cqrs--event-sourcing)
  - [Table of Contents](#table-of-contents)
  - [What is CQRS/ES?](#what-is-cqrses)
  - [Event Sourcing](#event-sourcing)
    - [State Based Persistence](#state-based-persistence)
    - [Events Based Persistence](#events-based-persistence)
    - [Evolving the Model](#evolving-the-model)
    - [Command Sourcing](#command-sourcing)
  - [CQRS](#cqrs)
    - [Read Models Vs Write Models](#read-models-vs-write-models)
  - [Consistency, Availability \& Scalability with CQRS](#consistency-availability--scalability-with-cqrs)
    - [Fine Grained Microservices](#fine-grained-microservices)
      - [Consistency](#consistency)
      - [Availability](#availability)
      - [Scalability](#scalability)
    - [Cost of CQRS](#cost-of-cqrs)

## What is CQRS/ES?

CQRS stands for: **Command Query Responsibility Segregation**\
ES stands for: **Event Sourcing**

You should consider using **CQRS/ES** if:

- It will give you a competitive advantage
- Your system experiences relatively low traffic
- You need auditability

## Event Sourcing

### State Based Persistence

Requirements in the domain are not always fixed

State Based Persistence means:\
Each update **replaces** the previous state with a new state

When using State Based Persistence, if a change needs to be made to the domain we can usually apply that change:\
To **any new entries** into the system

If the persisted state in our system is incorrect due to an error in our code, we can usually:\
**Fix the code, but not the incorrect state**

### Events Based Persistence

When using State Based Persistence, in order to **recover** from errors in state, or **evolve** our domain we can:\
**Persist an audit log** alongside our state so we understand where the state came from

When an audit log is stored alongside the state, we must be wary of the following:

- If the audit log and the state disagree, we have **two potential sources of truth**
- The audit log and the state **must be updated in a transactional way**
- A bug in the code may cause the state and the audit log to become **out of sync**

Event Sourcing Characteristics:

- Captures intent
- Eliminates the persistence of state
- Uses only the audit log

In an Event Sourced system, state is recovered by:\
**Replaying events and performing the same state updates** as when the event originally occured

A snapshot should be created:\
When the time to restore from the log is becoming excessive

Benefits of Event Sourcing include:

- The ability to correct errors in state by replaying events
- The ability to **rewind state** to fix or diagnose issues
- **Auditability** in cases where that is critical (eg. Legal requirements)

### Evolving the Model

Events are in the log are **facts**, they are the history\
The log in an Event Sourced system should be **append only**\
The **integrity of the log is critical** to the success of the system

Events are **immutable**, they should never be updated or deleted\
If you need to evolve your data model, you should do so by:\
Creating a **new version** of the event

**Protobuf** or **JSON** are used for serialization

### Command Sourcing

It is similar to Event Sourcing\
Commands are **persisted before being executed**\
Domain objects can be constructed or updated by executing the commands\
Commands can be **processed asynchronously**

Commands must be **idempotent** since they could be executed multiple times\
If they are not validated, they could get **stuck in the queue** as they continue to fail\
If there is a bad command we **can not inform the sender** since we are decoupled from it\
It is safer to perform **validation first** and then emit the the event to update the state

## CQRS

### Read Models Vs Write Models

Entities or Aggregate Roots create Events that which are persisted\
Queries could not be answered by a single aggregate root\
An Entity or Aggregare Root must be **rebuilt from events**\
You can't do a simple database query\
The model used to persist could **not be compatible** with the model for queries

As complexity increases, supporting all queries through aggregate roots becomes problematic
Requirements for **read and write are different**\
Supporting both in a single model is not a good idea\
Command Query Responsibility Segregation (CQRS) aims to **separate the two**

The write model is used for **processing commands** (**write**)\
**One or more** read models are produced to **handle queries** (**read**)\
Both read and write models are **optimized for their purpose**

CQRS and Event Sourcing are **often combined**\
Commands go through the write model and events are persisted\
A separate process consumes the events\
A **Projection**, a denormalized model, is used by the read side

CQRS allows for **flexible models**\
Read and Write sides can use **different databases** (Polyglot Persistence)\
Creating new Projection is easy\
Projections are **retroactive** since history is captured in events\
The read and write models are **decoupled**

## Consistency, Availability & Scalability with CQRS

### Fine Grained Microservices

CQRS allows you to **further break the bounded context** into microservices\
The read and write models can live in separate microservices\
Even each projection can live in separate microservices\
You should **be careful** with this approach

#### Consistency

Simple CQRS has the **same consistency** of non-CQRS systems\
CQRS/ES can be implemented with **different consistency** for read and write

About the **write model**:

- **Strong consistency** is often implemented
- Decisions are made based on the current state
- Stale data could result in incorrect decisions and states
- It uses transactions, locks or sharding

About the **read model**:

- It does **not need strong consistency**
- Pure reads are never strongly consistent
- After reading, data could have already changed
- Pure reads are always working with **stale data**

#### Availability

The **write** model **sacrifices Availability** because of strong consistency\
The **read** model is **highly available**\

Options to provide availability in a CQRS system include:

- **Disabling writes**, but continuing to allow reads if the write model fails
- **Disabling reads**, but continuing to allow writes if the read model fails
- **Using cached data** for the read model when current data is unavailable
- **Sharding the writes** so that only a subset of users are affected by an outage

#### Scalability

Read and write are separated and **scaled indipendently**\
Most applications are read heavy, the read side is highly scalable because of eventual consistency\
**Caches** can be used\
**Multiple copies** of projections can be created\
The write side can be scaled using sharding

### Cost of CQRS

CQRS/ES is **often complex**, but it can be made simples\
Without CQRS, models are complex and rigid\
CQRS allows for **smaller models** easier to modify\
**Eventual consistency can be isolated** to where it is necessary

Disadvantages:

- Increased number of **objects** (commands, events, read models)
- Multiple potentially **different data sources**
- **UI** must be designed for **Eventual Consistency**
- Supporting **old versions** is challenging
- Duplication of read model and events history could require **additional storage**
- Data duplication can result in **synchronization issues**, you need to **rebuild projections**
