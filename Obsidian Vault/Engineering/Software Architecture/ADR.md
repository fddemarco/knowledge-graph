[ADR Github Organization](https://adr.github.io/)

An [Architectural Decision (AD)](https://en.wikipedia.org/wiki/Architectural_decision) is a justified design choice that addresses a functional or non-functional requirement that is architecturally significant. An [Architecturally Significant Requirement (ASR)](https://en.wikipedia.org/wiki/Architecturally_significant_requirements) is a requirement that has a measurable effect on the architecture and quality of a software and/or hardware system. An _Architectural Decision Record (ADR)_ captures a single AD and its rationale; Put it simply, ADR can help you understand the reasons for a chosen architectural decision, along with its trade-offs and consequences. The collection of ADRs created and maintained in a project constitute its _decision log_. All these are within the topic of Architectural Knowledge Management (AKM), but ADR usage can be extended to design and other decisions (“any decision record”).

The aim of the [GitHub adr organization](https://github.com/adr) is to:

1. Motivate the need for and benefits of AD capturing and establish a common vocabulary.
2. Strengthen the tooling around ADRs, in support of agile practices as well as iterative and incremental engineering processes.
3. Provide pointers to public knowledge in the context of AKM and ADRs.

# Architecture decision record (ADR)
[ADR - Joel Parker Henderson](https://github.com/architecture-decision-record/architecture-decision-record)

An architecture decision record (ADR) is a document that captures an important architecture decision made along with its context and consequences.

Contents:

- [What is an architecture decision record?](#what-is-an-architecture-decision-record)
- [How to start using ADRs](#how-to-start-using-adrs)
- [How to start using ADRs with tools](#how-to-start-using-adrs-with-tools)
- [How to start using ADRs with git](#how-to-start-using-adrs-with-git)
- [File name conventions for ADRs](#file-name-conventions-for-adrs)
- [Suggestions for writing good ADRs](#suggestions-for-writing-good-adrs)
- [ADR example templates](#adr-example-templates)
- [Teamwork advice for ADRs](#teamwork-advice-for-adrs)
- [Teamwork questions for ADRs](#teamwork-questions-for-adrs)
- [Next step concepts for ADRs](#next-step-concepts-for-adrs)
- [Architecture diagrams \& views \& viewpoints](#architecture-diagrams--views--viewpoints)
- [Fitness functions for decisions as code](#fitness-functions-for-decisions-as-code)
- [Decision guardrails for pull requests](#decision-guardrails-for-pull-requests)
- [For more information](#for-more-information)

Templates:

- [Decision record template by Jeff Tyree and Art Akerman](locales/en/templates/decision-record-template-by-jeff-tyree-and-art-akerman/)
- [Decision record template by Michael Nygard](locales/en/templates/decision-record-template-by-michael-nygard/)
- [Decision record template by EdgeX](locales/en/templates/decision-record-template-by-edgex/)
- [Decision record template by arc42](locales/en/templates/decision-record-template-by-arc42/)
- [Decision record template for Alexandrian pattern](locales/en/templates/decision-record-template-for-alexandrian-pattern/)
- [Decision record template for business case](locales/en/templates/decision-record-template-for-business-case/)
- [Decision record template of the MADR project](locales/en/templates/decision-record-template-of-the-madr-project/)
- [Decision record template using Planguage](locales/en/templates/decision-record-template-using-planguage/)
- [Decision record template by Paulo Merson](https://github.com/pmerson/ADR-template)
- [Decision record template by Olaf Zimmermann](https://medium.com/olzzio/y-statements-10eb07b5a177)
- [Decision record template by Gareth Morgan](locales/en/templates/decision-record-template-by-gareth-morgan/)
- [Decision record template by GIG Cymru NHS Wales](locales/en/templates/decision-record-template-by-gig-cymru-nhs-wales/)
- [Decision record template for Important Technical Decisions (ITDs) by Ignacio Larrañaga](locales/en/templates/decision-record-template-for-important-technical-decisions/)
- [Translations into more languages](locales/)

Examples:

- [CSS framework](locales/en/examples/css-framework/)
- [Environment variable configuration](locales/en/examples/environment-variable-configuration/)
- [Metrics, monitors, alerts](locales/en/examples/metrics-monitors-alerts/)
- [Microsoft Azure DevOps](locales/en/examples/microsoft-azure-devops/)
- [Monorepo vs multirepo](locales/en/examples/monorepo-vs-multirepo/)
- [Programming languages](locales/en/examples/programming-languages/)
- [Secrets storage](locales/en/examples/secrets-storage/)
- [Timestamp format](locales/en/examples/timestamp-format/)
- [Many more...](locales/en/examples/)

[Translations into more languages](locales/)

## What is an architecture decision record?

An **architecture decision record** (ADR) is a document that captures an important architectural decision made along with its context and consequences.

An **architecture decision** (AD) is a software design choice that addresses a significant requirement.

An **architecture decision log** (ADL) is the collection of all ADRs created and maintained for a particular project (or organization).

An **architecturally-significant requirement** (ASR) is a requirement that has a measurable effect on a software system’s architecture.

All these are within the topic of **architecture knowledge management** (AKM).

The goal of this document is to provide a fast overview of ADRs, how to create them, and where to look for more information.

Abbreviations:

- **AD**: architecture decision
- **ADL**: architecture decision log
- **ADR**: architecture decision record
- **AKM**: architecture knowledge management
- **ASR**: architecturally-significant requirement

## Suggestions for writing good ADRs

Characteristics of a good ADR:

- Rationale: Explain the reasons for doing the particular AD. This can include the context (see below), pros and cons of various potential choices, feature comparisons, cost/benefit discussions, and more.
- Specific: Each ADR should be about one AD, not multiple ADs.
- Timestamps: Identify when each item in the ADR is written. This is especially important for aspects that may change over time, such as costs, schedules, scaling, and the like.
- Immutable: Don't alter existing information in an ADR. Instead, amend the ADR by adding new information, or supersede the ADR by creating a new ADR.

Characteristics of a good "Context" section in an ADR:

- Explain your organization's situation and business priorities.
- Include rationale and considerations based on social and skills makeups of your teams.
- Include pros and cons that are relevant, and describe them in terms that align with your needs and goals.

Characteristics of good "Consequences" section in an ADR:

- Explain what follows from making the decision. This can include the effects, outcomes, outputs, follow ups, and more.
- Include information about any subsequent ADRs. It's relatively common for one ADR to trigger the need for more ADRs, such as when one ADR makes a big overarching choice, which in turn creates needs for more smaller decisions.
- Include any after-action review processes. It's typical for teams to review each ADR one month later, to compare the ADR information with what's happened in actual practice, in order to learn and grow.

A new ADR may take the place of a previous ADR:

- When an AD is made that replaces or invalidates a previous ADR, then a new ADR should be created

## ADR example templates

ADR example templates that we have collected on the net:

- [ADR template by Michael Nygard](locales/en/templates/decision-record-template-by-michael-nygard/) (simple and popular)
- [ADR template by Jeff Tyree and Art Akerman](locales/en/templates/decision-record-template-by-jeff-tyree-and-art-akerman/) (more sophisticated)
- [ADR template for Alexandrian pattern](locales/en/templates/decision-record-template-for-alexandrian-pattern/) (simple with context specifics)
- [ADR template for business case](locales/en/templates/decision-record-template-for-business-case/) (more MBA-oriented, with costs, SWOT, and more opinions)
- [ADR template of the Markdown Any Decision Records (MADR) project](locales/en/templates/decision-record-template-of-the-madr-project/) (both simple and elaborate version; the latter emphasizes options and their pros and cons)
- [ADR template using Planguage](locales/en/templates/decision-record-template-using-planguage/) (more quality assurance oriented)
- [Template for Important Technical Decisions (ITDs) by Ignacio Larrañaga](locales/en/templates/decision-record-template-for-important-technical-decisions/) (lean and decision-first, optimized for fast executive review)

## Next step concepts for ADRs

[Arc42](https://arc42.org/) answers two questions in a pragmatic way and can be tailored to your specific needs. What should you document/communicate about your architecture? How should you document/communicate? Arc42 includes architecture decision records plus guidance on goals, constraints, contexts, quality, risks, and more.

[The C4 model](https://c4model.com/) is an easy to learn, developer friendly approach to software architecture diagramming. C4 is a set of hierarchical diagrams for context, containers, components, code, plus supporting diagrams for system landscape, dynamic, and deployment.

## Architecture diagrams & views & viewpoints

An architecture diagram is called an "architecture view". An "architecture view" is an instance of a "architecture viewpoint". An "architecture viewpoint" has a specific audience with specific concerns in mind. Architecture viewpoint examples, view examples, and diagram examples:

- Business Capabilities
- High-level Business Processes
- [Value Streams](https://en.wikipedia.org/wiki/Value_stream)
- Software Functions mapped to application components
- [C4 Model](https://en.wikipedia.org/wiki/C4_model) Context Diagram (TO-BE / AS-IS)
- [C4 Model](https://en.wikipedia.org/wiki/C4_model) Container Diagram (TO-BE / AS-IS)
- [Entity-relationship Diagram](https://en.wikipedia.org/w/index.php?title=Entity_relationship_diagram) (ERD) to map data entities to application components
- [Sequence Diagrams](https://en.wikipedia.org/wiki/Sequence_diagram) to describe functional flows within systems and for integrations
- [Business Process Model and Notation](https://en.wikipedia.org/wiki/Business_Process_Model_and_Notation) (BPMN) diagrams to describe data flows across application components
- [Business Process Model and Notation](https://en.wikipedia.org/wiki/Business_Process_Model_and_Notation) (BPMN) diagrams to describe business processes / user Scenarios
- [Identity and Access Management](https://en.wikipedia.org/wiki/Identity_and_access_management) (IAM) diagrams
- [Role-Based Access Control](https://en.wikipedia.org/wiki/Role-based_access_control) (RBAC) diagrams with roles per application component
- [Attribute-Based Access Control](https://en.wikipedia.org/wiki/Attribute-based_access_control) (ABAC) diagrams with attributes per application component
- Privacy diagrams

Related diagrams:

- A Use Case Diagram shows use cases to management/customers, which precedes requirements, which precedes the software architecture.
- A Deployment Diagram shows the physical hardware/computers that the software components are deployed to.
- A Data Flow Diagram shows how data moves through the system and is transformed.
- A Sequence Diagram is used to show how protocols like HTTP work on a time axis.
- An Activity Diagram depicts the workflow of activities a software system undertakes, like an NPC AI.

## Fitness functions for decisions as code

Fitness functions are objective automated checks, written with programming code, that verify decisions are being maintained.

- Fitness functions make decisions testable and assurable.
- Fitness functions for decisions can greatly help quality assurance, regulatory processes, and governance goals.

### How fitness functions connect to decisions

A decision record documents the decision, while a fitness function assures the decision.

- Example decision: We use event sourcing for audit requirements.
- Example fitness function: We use the continuous integration server to test that all state changes must produce events.

### Why fitness functions help decisions

- Objective measurements: Fitness functions pass or fail, so work is visible and clear.
- Continuous use: Fitness functions are your living rules, run on every commit and build.
- Confidence to refactor: Fitness functions automatically catch decision rule errors.
- Scalable governance: Fitness functions assure standards without creating bottlenecks.

### Architecture unit testing

[ArchUnit](https://www.archunit.org/): check architecture rules of Java code by using any plain Java unit test framework.

[ArchUnitTS](https://github.com/LukasNiessen/ArchUnitTS): check architecture rules of TypeScript code and JavaScript code by using Jest, Vitest, Jasmine, etc.

## Decision guardrails for pull requests

[Decision Guardian](https://github.com/DecispherHQ/decision-guardian) automatically surfaces the right decision records at the right moment — when a developer is actively modifying the code those decisions cover. Instead of hoping developers read a docs folder before merging, the relevant context appears directly on the pull request.

This works for any kind of decision record: architecture decisions, data decisions, compliance decisions, clinical and medical decisions, security decisions, and more.

Works with any CI system (GitLab, Jenkins, CircleCI) and as a pre-commit hook.
Open source. MIT license. [ADR Guard](https://github.com/chohan-sarmad-ali/delivery-gates) is a GitHub Action that fails a pull request when watched code paths change without an architecture decision record being added or updated. Waivers are explicit: an `ADR-Exempt:` line with a reason passes the gate and is written into the job summary. Template-agnostic, no dependencies. Open source. MIT license.

## For more information

Introduction:

- [Architectural decision (wikipedia.org)](https://wikipedia.org/wiki/Architectural_decision)
- [Architecturally significant requirements (wikipedia.org)](https://wikipedia.org/wiki/Architecturally_significant_requirements)

Templates:

- [Documenting architecture decisions - Michael Nygard (thinkrelevance.com)](http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions)
- [Markdown Architectural Decision Records (adr.github.io)](https://adr.github.io/madr/)
- [Template for documenting architecture alternatives and decisions (stackoverflow.com)](http://stackoverflow.com/questions/7104735/template-for-documenting-architecture-alternatives-and-decisions)

In-depth:

- [ADMentor XML project (github.com)](https://github.com/IFS-HSR/ADMentor)
- [Architectural Decision Guidance across Projects: Problem Space Modeling, Decision Backlog Management and Cloud Computing Knowledge (ifs.hsr.ch)](https://www.ifs.hsr.ch/fileadmin/user_upload/customers/ifs.hsr.ch/Home/projekte/ADMentor-WICSA2015ubmissionv11nc.pdf)
- [The Decision View's Role in Software Architecture Practice (computer.org)](https://www.computer.org/csdl/mags/so/2009/02/mso2009020036-abs.html)
- [Documenting Software Architectures: Views and Beyond (resources.sei.cmu.edu)](http://resources.sei.cmu.edu/library/asset-view.cfm?assetID=30386)
- [Architecture Decisions: Demystifying Architecture (utdallas.edu)](https://www.utdallas.edu/~chung/SA/zz-Impreso-architecture_decisions-tyree-05.pdf)
- [ThoughtWorks Technology Radar: Lightweight Architecture Decision Records (thoughtworks.com)](https://www.thoughtworks.com/radar/techniques/lightweight-architecture-decision-records)
- [A Skeptic’s Guide to Software Architecture Decisions (infoq.com)](https://www.infoq.com/articles/architecture-skeptics-guide/)
- [Architectural Decisions — The Making Of](https://ozimmer.ch/practices/2020/04/27/ArchitectureDecisionMaking.html)
- [Architectural Retrospectives: the Key to Getting Better at Architecting](https://www.infoq.com/articles/architectural-retrospectives/)
- [Software Architecture Monday with Mark Richards](https://developertoarchitect.com/lessons/) - free monthly software architecture lesson
- [Solution Architecture Decisions - By Gareth Morgan](https://www.linkedin.com/pulse/solution-architecture-decisions-gareth-morgan-0r5xe/)
- ["Keep the Why: Code Becomes Legacy When Nobody Remembers Why"](https://blog.technopathy.club/keep-the-why-code-becomes-legacy-when-nobody-remembers-why)

Tools:

- [Command-line tools for working with Architecture Decision Records](https://github.com/npryce/adr-tools)
- [Command line tools with python - by Victor Sluiter](https://bitbucket.org/tinkerer_/adr-tools-python/src/master/)
- [Architectural Design Decision Support Framework (ADvISE)](https://swa.univie.ac.at/Software_Architecture/research-projects/architectural-design-decision-support-framework-advise/)
- [Decision Guardian](https://github.com/DecispherHQ/decision-guardian)
- [Mneme HQ - ADR enforcement for AI coding agents](https://github.com/TheoV823/mneme)
- [Keep the Why - a repo-native agent skill that continuously captures, or retrospectively recovers, the reasoning behind a codebase](https://github.com/oliver-zehentleitner/keep-the-why)
- [ADR Guard - GitHub Action that fails a pull request changing watched code without an architecture decision record](https://github.com/chohan-sarmad-ali/delivery-gates)

Company-Specific Guidance:

- [Amazon: AWS Prescriptive Guidance: ADR Process](https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/adr-process.html)
- [GitHub: ADR GitHub organization](https://adr.github.io/)
- [RedHat: Why you should use ADRs](https://www.redhat.com/architect/architecture-decision-records)

Examples:

- [Repository of Architecture Decision Records made for the Arachne Framework](https://github.com/arachne-framework/architecture)

Videos:

- [An introduction to arc42 with Savvas Kleanthous](https://www.youtube.com/watch?v=V5clR8c6D7o)
- [The C4 model for visualising software architecture - by Simon Brown](https://www.youtube.com/watch?v=KvoBrUd1-5E)

Podcasts:

- [Software Architecture Bookclub Podcast](https://www.developertoarchitect.com/bookclub-podcast.html)

Books:

- [Software Architecture Metrics: Case Studies to Improve the Quality of Your Architecture - by Christian Ciceri, Dave Farley, Neal Ford, Andrew Harmel-Law, Michael Keeling and Carola Lilienthal](https://www.amazon.com/Software-Architecture-Metrics-Christian-Ciceri-ebook/dp/B0B1NZ8Z5V)
- [Software Systems Architecture: Working With Stakeholders Using Viewpoints and Perspectives - by Nick Rozanski and Eoin Woods](https://www.amazon.com/Software-Systems-Architecture-Stakeholders-Perspectives/dp/032171833X)
- [Software Architecture in Practice (SEI Series in Software Engineering)](https://www.amazon.com/Software-Architecture-Practice-SEI-Engineering-ebook/dp/B094CPJ96B)
- [Documenting Software Architectures: Views and Beyond (SEI Series in Software Engineering)](https://www.amazon.com/Documenting-Software-Architectures-Beyond-Engineering-ebook/dp/B0046XS3RO)
- [The Software Architect Elevator: Redefining the Architect's Role in the Digital Enterprise](https://www.amazon.com/Software-Architect-Elevator-Redefining-Architects-ebook/dp/B086WQ9XL1)
- [Fundamentals of Software Architecture: An Engineering Approach - by Mark Richards and Neal Ford](https://www.amazon.com/Fundamentals-Software-Architecture-Engineering-Approach-ebook/dp/B0849MPK73)
- [Building Evolutionary Architectures - by Neal Ford, Rebecca Parsons, Patrick Kua, Pramod Sadalage](https://www.amazon.com/Building-Evolutionary-Architectures-Neal-Ford-ebook/dp/B0BN4T1P27?crid=37FA31IFLAS0Z)
- [Foundations of Decision Analysis by Ronald Howard and Ali Abbas](https://www.amazon.com/Foundations-Decision-Analysis-Ronald-Howard-ebook/dp/B00SZECJTI?crid=14BK5SDP76UN6)
- [Head First Software Architecture - by Raju Gandhi, Neal Ford and Mark Richards](https://www.amazon.com/Head-First-Software-Architecture-Architectural-ebook/dp/B0CW1JMNF2)
- [Communication Patterns: A Guide for Developers and Architects - by Jacqui Read](https://www.amazon.com/Communication-Patterns-Guide-Developers-Architects/dp/1098140540)

See also:

- REMAP (Representation and Maintenance of Process Knowledge)
- DRL (Decision Representation Language)
- IBIS (Issue-Based Information System)
- QOC (Questions, Options, and Criteria)
- IBM’s e-Business Reference Architecture Framework
- [Decision Reasoning Format (DRF)](https://github.com/reasoning-formats/reasoning-formats) - A vendor-neutral, machine-readable YAML/JSON format for representing decisions with explicit reasoning, assumptions, cognitive state, and trade-offs. Complements ADRs by adding structured, validatable reasoning to decision documentation.

Refers to:
- [[Software Architecture]]
- [[Software Documentation]]
