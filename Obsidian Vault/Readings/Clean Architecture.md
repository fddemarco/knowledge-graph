---
base: "[[Reading List.base]]"
Rating: ⭐️⭐️⭐️⭐️⭐️
Category:
  - Code Design
Author: Robert C. Martin
Status: Completed
---

# PART II - Programming Paradigms

- When the source code in a component changes, only that component
needs to be redeployed. This is independent deployability.
- If the modules in your system can be deployed independently, then they can
be developed independently by different teams. That’s independent
developability.

# PART III - Design Principles

## **Single Responsibility Principle** 

- *A module should have one, and only one, reason to change.*
- A module should be responsible to one, and only one, user or stakeholder.
- A module should be responsible to one, and only one, actor.

## **Open-Closed Principle**

- *A software artifact should be open for extension but closed for modification.*
- the behavior of a software artifact ought to be extendible,
without having to modify that artifact.

## **Liskov’s Substitution Principle**

- *If for each object o1 of type S there is an object o2 of type T such that for all programs P defined in terms of T, the behavior of P is unchanged when o1 is substituted for o2 then S is a subtype of T.*
- A simple violation of substitutability, can cause a system’s architecture to be polluted with a significant amount of extra mechanisms.

## **Interface Segregation Principle**

- The source code of User1 will inadvertently depend on op2 and
op3, even though it doesn’t call them.
- This dependence means that a change to the source code of op2 in OPS will force User1 to be recompiled and redeployed, even though nothing that it cared about has actually changed.
- In general, it is harmful to depend on modules that contain more than you need.

## **Dependency Inversion Principle**

- The most flexible systems are those in which source code dependencies refer only to abstractions, not to concretions.
    - In a statically typed language, the use, import and include statements should refer only to source modules containing interfaces, abstract classes, or some other kind of abstract declaration. **Nothing concrete should be depended on.**
    - The same rule applies for dynamically typed languages. Source code dependencies should not refer to concrete modules.
        - However, in these languages it is a bit harder to define what a concrete module is. In particular, it is any module in which the functions being called are implemented.
- Treating this idea as a rule is unrealistic, because software systems
must depend on many concrete facilities.
    - It is the **volatile **concrete elements of our system that we want to avoid
depending on. Those are the modules that we are actively developing, and that are undergoing frequent change.
- Stable software architectures are those that avoid depending on volatile concretions, and that favor the use of **stable abstract interfaces**.
    - **Don’t refer to volatile concrete classes**. This puts severe constraints on the creation of objects and generally enforces the use of Abstract Factories.
- DIP violations cannot be entirely removed, but they can be gathered into a small number of concrete components and kept separate from the rest of the system.

# PART IV - Component Principles

The ***morning after syndrome*** occurs in development environments where many developers are modifying the same source files. It is not uncommon for weeks to go by without the team being able to build a stable version of the project. Instead, everyone keeps on changing and changing their code trying to make it work with the last changes that someone else made.
Over the last several decades, two solutions to this problem have evolved, both of which came from the telecommunications industry. The first is ***the weekly build***, and the second is the c***omponentization***.

### Weekly build

The weekly works like this. **All the developers ignore each other for the first four days **of the week. They all work on private copies of the code, and don’t worry about integrating their work on a collective basis. Then, **on Friday, they integrate all their changes and build the system**.

Unfortunately, as the project grows, it becomes **less feasible **to finish integrating the project on Friday. As the duty cycle of development versus integration decreases, **the efficiency of the team decreases**, too.

Eventually, this scenario leads to a **crisis**. To maintain efficiency, the build schedule has to be continually lengthened—but lengthening the build schedule increases **project risks**. Integration and testing become increasingly **harder **to do, and the team loses the benefit of **rapid feedback**.

### Componentization

The solution to this problem is to partition the development environment into ***releasable components***. Components are the **units of deployment**. They are the smallest entities that
can be deployed as part of a system.  Well-designed components always retain the ability to be **independently deployable **and, therefore, **independently developable**.

The components become units of work that can be the responsibility of a single developer, or a team of developers. When developers get a component working, they **release it **for use by the other developers. They give it a **release number **and move it into a directory for other teams to use. They then continue to modify their component in their own **private areas**. Everyone else uses any of the currently **released versions**.

Changes made to one component **do not need to have an immediate affect **on other teams. Each team **can decide for itself **when to adapt its own components to new releases of the components. Moreover, integration happens in **small increments**.

To make it work successfully, however, you must manage the dependency structure of the components. **There can be no cycles**. If there are cycles in the dependency structure, then the *morning after syndrome *cannot be avoided.

A cycle in the dependency structure, in effect, becomes **one large component**—which means that all of the developers working on any of those components will experience the dreaded *morning after syndrome*. They will be stepping all over one another because they must all use exactly the same release of one another’s components.

## Component Cohesion

### The Reuse/Release Equivalence Principle

*The granule of reuse is the granule of release.*

This principle means that the classes and modules that are formed into a component must belong to a **cohesive group**. The component cannot simply consist of a random hodgepodge of classes and modules; instead, there must be some overarching theme or purpose that those modules all share.

**Classes and modules **that are grouped together into a component **should be releasable together**. The fact that they share the same version number and the same release tracking, and are included under the same release documentation, should make sense both to the author and to the users.

### The Common Closure Principle

*Gather into components those classes that change for the same reasons and at
the same times. Separate into different components those classes that change at
different times and for different reasons.*

The CCP prompts us to gather together in one place all the classes that are
likely to change for the same reasons. If two classes are so tightly bound,
either physically or conceptually, that they always change together, then they
belong in the same component.

### The Common Reuse Principle

*Don’t force users of a component to depend on things they don’t need.*

We want to make sure that the classes that we put into a component are inseparable—that it is impossible to depend on some and not on the others.

### Cohesion Principles are in Tension

You may have already realized that the three cohesion principles tend to fight each other. The REP and CCP are **inclusive principles**: Both tend to make components larger. The CRP is an **exclusive principle**, driving components to be smaller. It is the tension between these principles that good architects seek to resolve.

An architect who focuses on just the REP and CRP will find that **too many components **are impacted when simple changes are made. In contrast, an architect who focuses too strongly on the CCP and REP will cause **too many unneeded releases **to be generated.

A good architect finds a position in that tension triangle that meets the **current concerns **of the development team, but is also aware that those concerns will change over time. For example, early in the development of a project, the CCP is much more important than the REP, because develop-ability is more important than reuse.

Generally, projects tend to start on the right hand side of the triangle, where the only sacrifice is reuse. As the project matures, and other projects begin to draw from it, the project will slide over to the left. This means that the component structure of a project can vary with time and maturity. It has more to do with the way that project is developed and used, than with what the project actually does.

## Component Coupling

### The Acyclic Dependencies Principle

*Allow no cycles in the component dependency graph.*

It is always possible to break a cycle of components and reinstate the dependency graph as a DAG. There are two primary mechanisms for doing so:

1. Apply the ***Dependency Inversion Principle***.
2. Create a new component with the classes that both components depend on.

Note that **the component structure is volatile **in the presence of changing requirements. Indeed, as the application grows, the component dependency structure **jitters and grows. **This means that the component structure **cannot be designed from the top down.** It is not one of the first things about the system that is designed, but rather evolves as the system grows and changes.

In fact, component dependency diagrams have **very little do to with describing the function of the application**. Instead, they are **a map to the buildability and maintainability of the application**. But as more and more modules accumulate in the early stages of implementation and design, there is a growing need to manage the dependencies so that the project can be developed without the “morning after syndrome.” Moreover, we want to keep changes as localized as possible, so we start paying attention to the SRP and CCP and collocate classes that are likely to change together.

One of the overriding concerns with this dependency structure is the **isolation of volatility**. We don’t want components that change frequently and for capricious reasons, such as the GUI, to affect components that otherwise ought to be stable, such as the business rules.

### The Stable Dependencies Principle

*Depend in the direction of stability.*

**Designs cannot be completely static. **Some volatility is necessary if the design is to be maintained. By conforming to the Common Closure Principle (CCP), we create components that are sensitive to certain kinds of changes but immune to others. **Some of these components are designed to be volatile**. We expect them to change.
**Any component that we expect to be volatile should not be depended on by a component that is difficult to change. Otherwise, the volatile component will also be difficult to change. **It is the perversity of software that a module that you have designed to be easy to change can be made difficult to change by someone else who simply hangs a dependency on it. Not a line of source code in your module need change, yet your module will suddenly become more challenging to change. By conforming to the Stable Dependencies Principle (SDP), we ensure that modules that are intended to be easy to change are not depended on by modules that are harder to change.

**Stability**

What is meant by “stability”? **Stability **is related to the amount of work required to make a change. One sure way to make a software component difficult to change, is to make **lots of other software components depend on it**. A component with lots of incoming dependencies is **very stable **because it requires a great deal of work to **reconcile any changes with all the dependent components**.

How can we measure the stability of a component? One way is to count the
number of dependencies that enter and leave that component. These counts
will allow us to calculate the positional stability of the component.

- **Fan-in**: Incoming dependencies. This metric identifies the number of classes
**outside **this component that depend on classes **within **the component.
- **Fan-out**: Outgoing dependencies. This metric identifies the number of classes
**inside **this component that depend on classes **outside **the component.
- **Instability**: `I = Fan-out / (Fan-in + Fan-out)`. This metric has the range
[0, 1]. I = 0 indicates a maximally stable component. I = 1 indicates a
maximally unstable component.

**When the I metric is equal to 1**, it means that no other component depends on this component (Fan-in = 0), and this component depends on other components (Fan-out > 0). This situation is as **unstable **as a component can get; it is irresponsible and dependent. **Its lack of dependents gives the component no reason not to change, and the components that it depends on may give it ample reason to change.**
**When the I metric is equal to 0**, it means that the component is depended on by other components (Fan-in > 0), but does not itself depend on any other components (Fan-out = 0). Such a component is responsible and independent. It is as **stable **as it can get.** Its dependents make it hard to change the component, and its has no dependencies that might force it to change.**
The SDP says that a component should only depend  on more stable components.*** That is, I metrics should decrease in the direction of dependency.***

### The Stable Abstractions Principle

*A component should be as abstract as it is stable*

On the one hand, it says that a stable component should also be abstract so that its stability does not prevent it from being extended. On the other hand, it says that an unstable component should be concrete since it its instability allows the concrete code within it to be easily changed.

# PART V - Architecture

## What Is Architecture?

The way you keep software soft is to leave as many options open as possible, for as long as possible. What are the options that we need to leave open? They are the details that don’t matter.

All software systems can be decomposed into two major elements: policy and details. The policy element embodies all the business rules and procedures. The policy is where the true value of the system lives.

The details are those things that are necessary to enable humans, other systems, and programmers to communicate with the policy, but that do not impact the behavior of the policy at all. They include IO devices, databases, web systems, servers, frameworks, communication protocols, and so forth.

The goal of the architect is to create a shape for the system that recognizes policy as the most essential element of the system while making the details irrelevant to that policy. This allows decisions about those details to be delayed and deferred.

## Independence

Architecture plays a significant role in supporting the development
environment. This is where Conway’s law comes into play. Conway’s law says:
Any organization that designs a system will produce a design whose structure is
a copy of the organization’s communication structure.

A system that must be developed by an organization with many teams and
many concerns must have an architecture that facilitates independent actions
by those teams, so that the teams do not interfere with each other during
development. This is accomplished by properly partitioning the system into
well-isolated, independently developable components. Those components can
then be allocated to teams that can work independently of each other.

Back to modes. There are many ways to decouple layers and use cases. They
can be decoupled at the source code level, at the binary code (deployment)
level, and at the execution unit (service) level.
• **Source **level. We can control the dependencies between source code
modules so that changes to one module do not force changes or
recompilation of others (e.g., Ruby Gems)

In this decoupling mode the components all execute in the same address
space, and communicate with each other using simple function calls. There
is a single executable loaded into computer memory. People often call this a
monolithic structure.
• **Deployment **level. We can control the dependencies between deployable
units such as jar files, DLLs, or shared libraries, so that changes to the
source code in one module do not force others to be rebuilt and redeployed.
Many of the components may still live in the same address space, and com-
municate through function calls. Other components may live in other pro-
cesses in the same processor, and communicate through interprocess com-
munications, sockets, or shared memory. The important thing here is that
the decoupled components are partitioned into independently deployable
units such as jar files, Gem files, or DLLs.
• **Service **level. We can reduce the dependencies down to the level of data
structures, and communicate solely through network packets such that
every execution unit is entirely independent of source and binary changes
to others (e.g., services or micro-services).

My preference is to push the decoupling to the point where a service could
be formed. should it become necessary; but then to leave the components in
the same address space as long as possible. This leaves the option for a
service open.
With this approach, initially the components are separated at the source code
level. That may be good enough for the duration of the project’s lifetime. If,
however, deployment or development issues arise, driving some of the
decoupling to a deployment level may be sufficient—at least for a while.
As the development, deployment, and operational issues increase, I carefully
choose which deployable units to turn into services, and gradually shift the
system in that direction.
Over time, the operational needs of the system may decline. What once
required decoupling at the service level may now require only deployment-
level or even source-level decoupling.

## Boundaries: Drawing Lines

You draw lines between things that matter and things that don’t. The GUI doesn’t matter to the business rules, so there should be a line between them. The database doesn’t matter to the GUI, so there should be a line between them. The database doesn’t matter to the business rules, so there should be a line between them.

## Boundary Anatomy

At runtime, a boundary crossing is nothing more than a function on one side
of the boundary calling a function on the other side and passing along some
data. The trick to creating an appropriate boundary crossing is to manage the
source code dependencies.
Why source code? Because when one source code module changes, other
source code modules may have to be changed or recompiled, and then
redeployed. Managing and building firewalls against this change is what
boundaries are all about.

The simplest and most common of the architectural boundaries has no strict
physical representation. I**t is simply a disciplined segregation of functions and
data within a single processor and a single address space.** In a previous
chapter, I called this the source-level decoupling mode.

The simplest physical representation of an architectural boundary is a
dynamically linked library like a .Net DLL, a Java jar file, a Ruby Gem, or a
UNIX shared library. Deployment does not involve compilation. Instead, the

components are delivered in binary, or some equivalent deployable form. This
is the deployment-level decoupling mode. The act of deployment is simply the
gathering of these deployable units together in some convenient form, such as
a WAR file, or even just a directory.

A much stronger physical architectural boundary is the local process. **A local
process is typically created from the command line or an equivalent system
call.** Local processes run in the same processor, or in the same set of
processors within a multicore, but run in separate address spaces. Memory
protection generally prevents such processes from sharing memory, although
shared memory partitions are often used.

The strongest boundary is a service. **A service is a process, generally started
from the command line or through an equivalent system call. **Services do not
depend on their physical location. Two communicating services may, or may
not, operate in the same physical processor or multicore. The services assume
that all communications take place over the network.

## Policy and Level

**A strict definition of “level” is “the distance from the inputs and outputs.”
**The farther a policy is from both the inputs and the outputs of the system,
the higher its level. The policies that manage input and output are the lowest-
level policies in the system.

## Business Rules

Strictly speaking, **business rules **are rules or procedures that make or save the business money. Very strictly speaking, these rules would make or save the business money, irrespective of whether they were implemented on a computer. They would make or save money even if they were executed manually.

An **Entity **is an object within our computer system that embodies a small set of critical business rules operating on Critical Business Data. The Entity object either contains the Critical Business Data or has very easy access to that data.

Not all business rules are as pure as Entities. Some business rules make or save money for the business by defining and constraining the way that an automated system operates. These rules would not be used in a manual environment, because they make sense only as part of an automated system. This is a **use case**.

The use case class accepts simple request data structures for its input, and returns simple response data structures as its output. These data structures are not dependent on anything. They do not derive from standard framework interfaces such as HttpRequest and HttpResponse. They know nothing of the web, nor do they share any of the trappings of whatever user interface might be in place.

This lack of dependencies is critical. If the request and response models are not independent, then the use cases that depend on them will be indirectly bound to whatever dependencies the models carry with them. You might be tempted to have these data structures contain references to Entity objects. You might think this makes sense because the Entities and the request/response models share so much data. Avoid this temptation! The purpose of these two objects is very different. Over time they will change for very different reasons, so tying them together in any way violates the
Common Closure and Single Responsibility Principles. The result would be lots of tramp data, and lots of conditionals in your code.

## Screaming Architecture

Your architecture should tell readers about the system, not about the
frameworks you used in your system. If you are building a health care system,
then when new programmers look at the source repository, their first
impression should be, “Oh, this is a heath care system.” Those new
programmers should be able to learn all the use cases of the system, yet still
not know how the system is delivered.

## The Clean Architecture

The concentric circles in Figure 22.1 represent different areas of software. In general, the further in you go, the higher level the software becomes. The outer circles are mechanisms. The inner circles are policies. The overriding rule that makes this architecture work is the Dependency Rule: Source code dependencies must point only inward, toward higher-level policies. Nothing in an inner circle can know anything at all about something in an outer circle. In particular, the name of something declared in an outer circle must not be mentioned by the code in an inner circle. That includes functions, classes, variables, or any other named software entity.

**Entities **encapsulate enterprise-wide Critical Business Rules. An entity can be an object with methods, or it can be a set of data structures and functions.  They encapsulate the most general and high-level rules. They are the least likely to change when something external changes.

The software in the **use cases layer **contains application-specific business rules. It encapsulates and implements all of the use cases of the system. These use cases orchestrate the flow of data to and from the entities, and direct those entities to use their Critical Business Rules to achieve the goals of the use case.

The software in the **interface adapters layer **is a set of adapters that convert data from the format most convenient for the use cases and entities, to the format most convenient for some external agency such as the database or the web. It is this layer, for example, that will wholly contain the MVC
architecture of a GUI. The presenters, views, and controllers all belong in the interface adapters layer. The models are likely just data structures that are passed from the controllers to the use cases, and then back from the use cases to the presenters and views. Similarly, data is converted, in this layer, from the form most convenient for entities and use cases, to the form most convenient for whatever persistence framework is being used (i.e., the database). No code inward of this circle
should know anything at all about the database. If the database is a SQL database, then all SQL should be restricted to this layer—and in particular to the parts of this layer that have to do with the database.

Also in this layer is any other adapter necessary to convert data from some external form, such as an external service, to the internal form used by the use cases and entities.

The outermost layer of the model in Figure 22.1 is generally composed of frameworks and tools such as the database and the web framework. Generally you don’t write much code in this layer, other than glue code that communicates to the next circle inward. The frameworks and drivers layer is where all the details go. The web is a detail. The database is a detail. We keep these things on the outside where they can do little harm.

Note the flow of control: It begins in the controller, moves through the use case, and then winds up executing in the presenter. Note also the source code dependencies: Each points inward toward the use cases. We usually resolve this apparent contradiction by using the Dependency Inversion Principle. In a language like Java, for example, we would arrange interfaces and inheritance relationships such that the source code dependencies oppose the flow of control at just the right points across the boundary.

Typically the data that crosses the boundaries consists of **simple data structures**. You can use basic structs or simple data transfer objects if you like. Or the data can simply be arguments in function calls. Or you can pack it into a hashmap, or construct it into an object. The important thing is that isolated, simple data structures are passed across the boundaries. We don’t want to cheat and pass Entity objects or database rows. We don’t want the data structures to have any kind of dependency that violates the Dependency Rule. Thus, when we pass data across a boundary, it is always in the form that **is most convenient for the inner circle**.

## Presenters and Humble Objects

The Humble Object pattern1 is a design pattern that was originally identified as a way to help unit testers to separate behaviors that are hard to test from behaviors that are easy to test. The idea is very simple: Split the behaviors into two modules or classes. One of those modules is humble; it contains all the hard-to-test behaviors stripped down to their barest essence. The other module contains all the testable behaviors that were stripped out of the humble object.

f the application wants to display money on the screen, it might pass a Currency object to the Presenter. The Presenter will format that object with the appropriate decimal places and currency markers, creating a string that it can place in the View Model. If that currency value should be turned red if it is negative, then a simple boolean flag in the View model will be set appropriately. 

Anything and everything that appears on the screen, and that the application has some kind of control over, is represented in the View Model as a string, or a boolean, or an enum. Nothing is left for the View to do other than to load the data from the View Model into the screen. Thus the View is humble.

Between the use case interactors and the database are the database gateways. These gateways are polymorphic interfaces that contain methods for every create, read, update, or delete operation that can be performed by the application on the database. For example, if the application needs to know the last names of all the users who logged in yesterday, then the UserGateway interface will have a method named getLastNamesOfUsersWhoLoggedInAfter that takes a Date as its argument and returns a list of last names. Recall that we do not allow SQL in the use cases layer; instead, we use gateway interfaces that have appropriate methods. Those gateways are implemented by classes in the database layer. That implementation is the humble object. It simply uses SQL, or whatever the interface to the database is, to access the data required by each of the methods. The interactors, in contrast, are not humble because they encapsulate application-specific business rules. Although they are not humble, those interactors are testable, because the gateways can be replaced with appropriate stubs and test-doubles.

## The Main Component

In every system, there is at least one component that creates, coordinates, and
oversees the others. I call this component Main.

The Main component is the ultimate detail—the lowest-level policy. It is the initial
entry point of the system. Nothing, other than the operating system, depends on
it. Its job is to create all the Factories, Strategies, and other global facilities, and
then hand control over to the high-level abstract portions of the system.
It is in this Main component that dependencies should be injected by a
Dependency Injection framework. Once they are injected into Main, Main
should distribute those dependencies normally, without using the framework.

## Services: Great and Small

One of the big supposed benefits of breaking a system up into services is that
services are strongly decoupled from each other. After all, each service runs in
a different process, or even a different processor; therefore those services do
not have access to each other’s variables. What’s more, the interface of each
service must be well defined.

There is certainly some truth to this—but not very much truth. Yes, services
are decoupled at the level of individual variables. However, they can still be
coupled by shared resources within a processor, or on the network. What’s
more, they are strongly coupled by the data they share.
For example, if a new field is added to a data record that is passed between
services, then every service that operates on the new field must be changed.
The services must also strongly agree about the interpretation of the data in
that field. Thus those services are strongly coupled to the data record and,
therefore, indirectly coupled to each other.
As for interfaces being well defined, that’s certainly true—but it is no less true
for functions. Service interfaces are no more formal, no more rigorous, and
no better defined than function interfaces. Clearly, then, this benefit is
something of an illusion.

Another of the supposed benefits of services is that they can be owned and
operated by a dedicated team. That team can be responsible for writing,
maintaining, and operating the service as part of a dev-ops strategy. This
independence of development and deployment is presumed to be scalable. It
is believed that large enterprise systems can be created from dozens,
hundreds, or even thousands of independently developable and deployable
services. Development, maintenance, and operation of the system can be
partitioned between a similar number of independent teams.
There is some truth to this belief—but only some. First, history has shown
that large enterprise systems can be built from monoliths and component-
based systems as well as service-based systems. Thus services are not the only
option for building scalable systems.
Second, the decoupling fallacy means that services cannot always be
independently developed, deployed, and operated. To the extent that they are
coupled by data or behavior, the development, deployment, and operation
must be coordinated.

## The Test Boundary

The extreme isolation of the tests, combined with the fact that they are not
usually deployed, often causes developers to think that tests fall outside of the
design of the system. This is a catastrophic point of view. Tests that are not
well integrated into the design of the system tend to be fragile, and they make
the system rigid and difficult to change.

The way to accomplish this goal is to create a specific API that the tests can
use to verify all the business rules. This API should have superpowers that
allow the tests to avoid security constraints, bypass expensive resources (such
as databases), and force the system into particular testable states. This API
will be a superset of the suite of interactors and interface adapters that are
used by the user interface.
The purpose of the testing API is to decouple the tests from the application. This
decoupling encompasses more than just detaching the tests from the UI: The goal
is to decouple the structure of the tests from the structure of the application.

## Clean Embedded Architecture

3. “First make it work.” You are out of business if it doesn’t work.
4. “Then make it right.” Refactor the code so that you and others can
understand it and evolve it as needs change or are better understood.
5. “Then make it fast.” Refactor the code for “needed” performance.

Letting all code become firmware is not good for your product’s long-term
health. Being able to test only in the target hardware is not good for your
product’s long-term health. A clean embedded architecture is good for
your product’s long-term health.