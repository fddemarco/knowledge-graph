---
base: "[[Reading List.base]]"
Rating: ⭐️⭐️⭐️
Category:
  - System Design
  - Course
Author: FIUBA
Status: Completed
---
Playlist: https://www.youtube.com/watch?v=P0a7PwRNLVU&list=PLA2i5ySS1-JuafCZXdKodhachgDH5zQSc&index=41&pp=gAQBiAQB

https://c4model.com/
https://zeromq.org/get-started/
https://www.rabbitmq.com/tutorials

## 1st Class - Intro a la materia

- Tendencias de hardware y software
    - IoT
- Characteristics of Distributed Systems
    - Centralizados vs Distribuidos
- Virtualization
    - Docker

## 2nd Class - Concurrent Programming Practices

- Modelos de Multi-programming
    - Multi-threading
    - Multi-processing
    - Multi-computing
- Properties
    - Safety
        - Mutual Exclusion
        - Absence of deadlocks
    - Liveness
        - Starvation absence
        - Fairness
- Synchronization Mechanisms
    - Busy Waiting
        - Spin lock
            - Dekker, Lamport, Peterson → Algoritmos
            - CAS, contadores atomicos, → Abstracciones
    - Semaphores
        - Signal, wait
        - Mutex
    - Monitor
        - Condition variables
            - acquire, release, wait, notify, notify all
    - Classical Problems
        - Barriers
        - Rendezvous
        - Consumer - Producer
        - Writers - Readers
- Inter-process Communication (IPC)
    - Semaphore
    - Shared Memory
    - File Lock
    - Signal
    - Queue
    - Pipe / FIFOs
    - Sockets
- Computational Models
    - Amdahl’s Law → naive
    - Work-Span Model → more precise
- Parallelization Strategies
    - Functional decomposition
    - Data partitioning
- Processing Patterns
    - Fork-Join
    - Pack
    - Split
    - Pipeline
    - Map - Reduce

## 3rd Class - Multi-computing

- SIMD, MIMD, SISD, MISD
- UMA, NUMA
- TCP/IP, OSI Model
- IP, TCP, UDP
- Sockets
    - TCP
        - Socket → bind → listen → accept
        - Socket → Connect
        - write → read → write → read → …
        - close → close
    - UDP
        - Socket → bind
        - Socket → sendTo
        - recvFrom → sendTo → recvFrom → sendTo → …
        - close → close

## 4th Class - Middlewares y Docs

- Clase 4.1 - Middlewares
    - Transparency
        - Access, Location, Migration, Replication, Concurrency, Persistency
        - Interoperability, Portability
    - Fault Tolerance
        - Availability, Reliability, Safety, Maintainability
    - Access to shared resources
        - Efficient, transparent, controlled
    - Transactional, Object, Procedural, Message Oriented
    - Message Oriented
        - Rings
        - Peer to Peer
        - Hierarchical Groups
- Clase 4.2 - Documentacion
    - 4+1 architectural view model
        - Logical view
            - Classes, States
        - Process view
            - Activity, Sequence diagrams
        - Development view
            - Packages (components)
        - Physical view
            - Deployment, robustness diagrams
        - Scenario view
            - use cases
        - ToC
            - Scope, Software Architecture
            - Architectural Goals and Constraints
            - Logical, Process, Development, Physical, Scenarios views
            - Size and Performance
            - Quality Appendices
                - Acronyms and Abbreviations
                - Definitions
                - Design Principles
    - [C4 model](https://c4model.com/)
        - Context
            - System
        - Containers
            - Cohort of Components
        - Components
            - Cohort of services
        - Code
            - Classes, State

## 5th Class - Protocols and Interfaces

- Clase 5.1 - Tiers and Layers
    - Divide and Conquer
    - Promotes Interface use
    - Tiers (Physical)
        - Physical distribution of components of a system
        - 2-tier
            - Client
            - Database
        - 3-tier deployment
            - Client
            - App Server
            - Database
        - Multi tier deployment
            - Client
            - App Server
            - Core Logic Server
            - Database
    - Layer (Logical)
        - Upcalls between layers should be the exception, not the rules
            - Lower layers should not call higher layers
            - Callbacks could be used to avoid circular dependencies
        - Web Client
        - Presentation
        - API
        - Services
        - Core Business
        - Persistence
        - Audit
        - Security
- Clase 5.2 - Interfaces
    - A public contract that allows the communication between two or more component
    - Inter-application
        - APIs
    - Intra-application
        - Facade
        - Mediator
        - Abstract classes / Interfaces
    - APIs
        - Entity Oriented, Process Oriented
        -  Web API, Remote API, Library-based / Frameworks, OS related

## 8th Class - Simple Distributed Architectures

- Client - Server
- Peer to Peer
- RPC, gRPC
    - Proxies / Stubs
    - Marshaling (serialization)
    - Stateless
- RMI
    - Newer, simplified implementation of CORBA
    - Object Oriented
    - Java specific
    - Stateful

## 9th Class - Message Oriented Middlewares

- MOMs
    - Message Oriented Communication
    - Solve Location, Fault Tolerance, Performance, and Scalability
    - Centralized vs Distributed
    - [ZeroMQ](https://zeromq.org/get-started/)
        - Socket based
        - MOM Patterns
            - Request - Reply
            - Pub - Sub
            - Pipeline (Push Pull)
            - Router Dealer (Broker)
    - [RabbitMQ](https://www.rabbitmq.com/tutorials)
        - Queue based
        - Anonymous, Named, TaskQueues
        - Follows Broker Pattern
        - Exchanges
            - Anonymous queues
            - Fan out
            - Direct
        - Patterns
            - Publisher Subscriber
            - Routing
            - Topic

## 10th Class - Communication Patterns

- Request Reply
    - Synchronous
        - Receive, process, respond
    - Asynchronous
        - Receive, queue request, acknowledge
        - Receive, check status, respond with status
- Publisher Subscriber
    - Event Oriented
    - Publishers generate events, Subscribers listen to events
    - Topic based
    - Channel based
- Pipeline
    - Workers by Filter
    - Workers by Item
    - Stages
        - Sequential
        - Parallel
    - Online algorithms
    - Continuous processing
    - DAGs
