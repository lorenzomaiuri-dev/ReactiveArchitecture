# Domain Driven Design

## Table of Contents

- [Domain Driven Design](#domain-driven-design)
  - [Table of Contents](#table-of-contents)
  - [What is Domain Driven Design?](#what-is-domain-driven-design)
    - [What is a Domain](#what-is-a-domain)
  - [Decomposing the Domain](#decomposing-the-domain)
    - [Maintaining Purity](#maintaining-purity)
  - [Domain Building Blocks](#domain-building-blocks)
    - [Domain Activites](#domain-activites)
      - [Commands](#commands)
      - [Events](#events)
      - [Queries](#queries)
    - [Domain Objects](#domain-objects)
      - [Value Object](#value-object)
      - [Entities](#entities)
      - [Aggregates](#aggregates)
    - [Domain Abstractions](#domain-abstractions)
      - [Services](#services)
      - [Factories](#factories)
      - [Repositories](#repositories)
  - [Hexagonal Architecture](#hexagonal-architecture)

## What is Domain Driven Design?

Many of the guidelines and rules in Domain Driven Design are compatible with those in Reactive Architecture

Domain Driven Design Key Goals are:

- Take a large domain or system and **break it down** into smaller pieces or subsections
- Create communication channels between **domain experts** and **software developers**

Often a single all-inclusive model of a very large domain can contain much, and at times overwhelming, complexity. Breaking those large domains into smaller pieces can result in **making the complexity more manageable and understandable**

If we break down a large domain into smaller pieces we need to:\
Clearly **determine the boundaries** of the smaller pieces and **how each piece does or doesn't interact** with the other pieces in the larger domain.

### What is a Domain

In the world of software architecture and development a domain can be discribed as:\
Referring to a **business or idea that we are trying to model**

A domain model is not part of the software application\
Domain experts should be able to **easily understand the domain model**

The name of the form of communication that domain experts and software developers can understand is: **Ubiqutous Language**

If developers find a process or abstraction in a domain that doesn't appear to have a name, the developers should talk to the domain experts about whether it already exists or co-create a new term to describe it

## Decomposing the Domain

Business Domain are often large and complicated

**Sub domains** are created by grouping related business ideas, actions and rules into separate subdomains

It is possible for a concept to exist in **multiple** sub domains

Each subdomain has its own **ubiqutious language**

The ubiqutious language and model of a subdomain is called **Bounded Context**

When initially defining microservices, it's often a good idea to start with your bounded contexts: one bounded context = one microservice

From one bounded context to another the meaning of a word or concept, may dramatically change

Some guidelines we can use to determine a bounded context:

- Consider **human interactions** and communication that seem to form a subculture within a broader organization
- Look for **changes in the ubiqutous language**
- **Job classifications** within organizational subgroupings might suggest various bounded contexts
- A clear **grouping of business processes** and workflows could point to a bounded context

**Event First** Domain Driven Design identifies bounded contexts by looking for activities or events that occur in the domain

### Maintaining Purity

Concepts in one bounded context could mean another thing in another bounded context. Thus, there is the need to **translate concepts between bounded contexts**

If we always treated a concept exactly the same across many different bounded contexts, we may introduce **coupling between contexts** in our system

**Anti-Corruption Layers** are introduced to translate concepts between bounded contexts\
We can prevent one bounded context from leaking into another by using an Anti-Corruption Layer\
The abstract interface can be a pure domain representation, while concrete classes extending the interface can be specific infrastructure implementations. This keeps the core job of the Anti-Corruption layer (stating what it does or provides) without locking into the specific system details needed to do it\
We should use Anti-Corruption Layers when our bounded contexts interact with **legacy systems**\
Its sometimes neccessary to have Anti-Corruption layers on **both sides** of the interaction between bounded contexts or a bounded context and legacy system

## Domain Building Blocks

### Domain Activites

The following are types of activities in Domain Driven Design:

- Commands
- Events
- Queries

They are the **messages** in a Reactive System\
They form the **API** for a Bounded Context

#### Commands

Commands represent a **request to perform an action**\
The action **can be rejected**\
It is delivered to a **specific destination**\
It **causes a change** to the state of the domain

#### Events

Events represent an **action that happened in the past**\
The action already happened, it **cannot be rejected**\
It could be broadcasted to **multiple destinations**\
It **records a change** to the state of the domain\
It can be the **result of a command**

#### Queries

Queries represent a **request for information** about the domain\
A **response** is **always expected**\
It is delivered to a **specific destination**\
It **should not alter** the state of the domain\

### Domain Objects

#### Value Object

A Value Object is defined by its **attributes**\
Two Objects are equivalent if their attributes are the same\
Value Objects are **immutable**\
They can contain **business logic**\
Messages in a Reactive System are implemented as Value Objects

#### Entities

An Entity is defined by its **unique identity**\
An Entity **can change its attributes** but not its identity\
If the idenity changes, it is considered **a new entity**\
They are the **source of truth** for a particular id\
They can contain **business logic**

#### Aggregates

An Aggregate is a **collection of domain objects** bounded to a root Entity\
The root Entity is called **Aggregate Root**\
Objects in an Aggregate are **treated as one**\
Access to the objects must pass through the Aggregate Root\
They are **good for distribution**

The following questions can help you determine an aggregate root(s) in a bounded context:

- Can an entity be broken up into multiple entities?
- Will a single transaction span multiple entities?
- If you delete the entity, do you need to delete other entities?
- Is the entity involved in most operation in that bounded context?

### Domain Abstractions

#### Services

When **business logic doesn't fit** with an existing entity or value object we encapsulate it in a Service\
A Service should have **stateless** business processing logic\
Often used to **abstract Anti-Corruption Layers**\
Too many services could mean you could be missing a domain object

#### Factories

A Factory is often an abstract interface defining the **general create method** or contract without the details coded\
They are usually implemented as **domain interface** with concrete implementations\
They may require access to **external resources**

#### Repositories

They **abstract operations** with objects\
They are used to **retrieve, modify or delete** objects\

Because Factories and Repositories are related, they are often combined containing all the create, read, update and delete functionality for a specific object

## Hexagonal Architecture

The Hexagonal Architecture is an **alternative to the N-tiered Architecture**

N-tiered Architecture is comprised of layers usually **laid out linearly**:
User Interface (View/Controller) -> Domain (Model) -> Database (Persistence)

Hexagonal Architecture resembles a kind of onion, with a center and subsequent layers wrapping the **Domain at the center**

It ensures proper **separation between Infrastructure and Domain**\
It can be enforced even with **project structure** in the application

In Hexagonal Architecture, **Ports are a preferred API** for infrastructure components to interface with the domain

In Hexagonal Architecture, the position and role of the **Infrastructure** layer is to **wrap the API layer** and adapt the incoming and outgoing call traffic to the API layer

If the Domain has no knowledge of any other layers in the Hexagonal Architecture, it's possible for it to be **portable**
