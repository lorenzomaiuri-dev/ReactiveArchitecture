# Distributed Messaging Platforms

## Table of Contents

- [Distributed Messaging Platforms](#distributed-messaging-platforms)
  - [Table of Contents](#table-of-contents)
  - [Message Driven Architecture](#message-driven-architecture)
  - [Sagas](#sagas)
    - [Compensating Actions vs Rollbacks](#compensating-actions-vs-rollbacks)
  - [Messaging Patterns](#messaging-patterns)
    - [Delivery Guarantees](#delivery-guarantees)
      - [At Most Once](#at-most-once)
      - [At Least Once](#at-least-once)
      - [Exactly Once](#exactly-once)
    - [Point to Point](#point-to-point)
    - [Publish/Subscribe](#publishsubscribe)

## Message Driven Architecture

Reactive Systems are based on Asynchronous Non-Blocking messages\
The rensponses to messages are sent asynchronously\
Resources can be freed immediately\
Reduced contention means higher scalability\
Messages can be **queued** in case the receiver is offline

Synchronous messages can be used but only if it's a **domain requirement**

Transactions in Distributed Systems are **difficult** and **slow**

## Sagas

The Saga Pattern is a way of **representing long running transactions**\
Multiple requests are managed by a Saga\
Individual requests are run in **sequence** or in **parallel**\
When all requests are completed the Saga is complete\
Sagas are often modelled as **Finite State Machines**

Each request is paired with a **compensating action**\
If any request fails, compensating actions are executed for **any completed steps**\
The Saga is completed with a failure\
If a compensating actions fails, it is **retried** (idempotence is required)

If there is a **timeout** we run a compensating action (the request could have succeeded or could not)\
The compensating action could end before the request if the request was queued\
Compensating actions and requests must be **commutative**

### Compensating Actions vs Rollbacks

Rollbacks:

- Implies the transaction has not completed
- Removes evidence of the transaction

Compensating Actions:

- Applied on top of a completed action
- Evidence of the original action remains

An alternative approach to compensating actions is to move forwards despite failures with:

- Retries
- Cached or default values
- Move to an error queue and process them separately

## Messaging Patterns

### Delivery Guarantees

Over an unreliable network we **can not guarantee message receipt**\
**Exactly Once** delivery is **impossible**\
We must choose between **At Most Once** and **At Least Once**
Exactly Once delivery **can be simulated** using **At Least Once** and **idempotence**

#### At Most Once

Guarantees that no message will be delivered more than once\
It tries to deliver the message, if it fails there is **no retry**\
Messages are **never duplicated**, but they **could be lost**\
**No storage** of messages is required

#### At Least Once

Guarantees that all messages **will eventually be delivered**\
Failures result in **retry**\
Messages may be **delivered more than once**, but they are **never lost**\
Messages must be **stored by the sender** to enable retries

#### Exactly Once

It is **simulated** using **At Least Once** delivery\
It requires **idempotence**\
Messages must be **stored by the sender and the receiver**

There are two approachs that can be taken

- **Point to Point** in which microservices depend directly on each other
- **Publish/Subscribe** in which microservices depend on a message broker or bus

### Point to Point

Each microservice **depends directly** on other services\
Services are directly **coupled** to each other's API\
Services know and **understand their dependencies**

Advantages:

- Dependencies are clear
- Complexity is observable

Disadvantages:

- Coupling is high

### Publish/Subscribe

Services **publish** messages to a **common message bus**\
Other services **subscribe** to the messages\
The publishing service has **no knowledge of the subscribing services**\
Subscribing services has **no knowledge of the publishing service**\
Services are **coupled only to the message format**

Advantages:

- Dependencies are flexible
- Coupling is low

Disadvantages:

- Complexity can be hard to see
