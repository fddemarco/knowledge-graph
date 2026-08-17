---
base: "[[Reading List.base]]"
Rating: ⭐️
Category:
  - Testing
  - Management
Author: ISTQB
Status: Completed
---
## Test Levels

- **Component testing **(also known as **unit testing**) focuses on testing components in isolation. It
often requires specific support, such as test harnesses or unit test frameworks. Component
testing is normally performed by developers in their development environments.
- **Component integration testing** (also known as unit **integration testing**) focuses on testing the
interfaces and interactions between components. Component integration testing is heavily
dependent on the integration strategy like bottom-up, top-down or big-bang.
- **System testing **focuses on the overall behavior and capabilities of an entire system or product,
often including functional testing of end-to-end tasks and the non-functional testing of quality
characteristics. For some non-functional quality characteristics, it is preferable to test them on a
complete system in a representative test environment (e.g., usability). Using simulations of sub-
systems is also possible. System testing may be performed by an independent test team, and is
related to specifications for the system.
- **System integration testing **focuses on testing the interfaces of the system under test and other
systems and external services. System integration testing requires suitable test environments
preferably similar to the operational environment.
- **Acceptance testing** focuses on validation and on demonstrating readiness for deployment,
which means that the system fulfills the user’s business needs. Ideally, acceptance testing should
be performed by the intended users. The main forms of acceptance testing are: user acceptance
testing (UAT), operational acceptance testing, contractual acceptance testing and regulatory
acceptance testing, alpha testing and beta testing.

## Test Types

A lot of test types exist and can be applied in projects.

- **Functional testing **evaluates the functions that a component or system should perform. The functions
are “what” the test object should do. The main objective of functional testing is checking the functional
completeness, functional correctness and functional appropriateness.
- **Non-functional testing** evaluates attributes other than functional characteristics of a component or
system. Non-functional testing is the testing of “how well the system behaves”. The main objective of non-functional testing is checking the non-functional quality characteristics. The ISO/IEC 25010 standard provides the following classification of the non-functional quality characteristics:
    - Performance efficiency
    - Compatibility
    - Usability (also known as interaction capability)
    - Reliability
    - Security
    - Maintainability
    - Portability (also known as flexibility)
    - Safety
- **Confirmation testing **confirms that an original defect has been successfully fixed. Depending on the risk, one can test the fixed version of the software in several ways, including:
    - executing all tests that previously have failed due to the defect, or, also by
    - adding new tests to cover any changes that were needed to fix the defect
However, when time or money is short when fixing defects, confirmation testing might be restricted to
simply exercising the test steps that should reproduce the failure caused by the defect and checking that the failure does not occur.
- **Regression testing **confirms that no adverse consequences have been caused by a change, including a fix that has already been confirmation tested. These adverse consequences could affect the same component where the change was made, other components in the same system, or even other connected systems. Regression testing may not be restricted to the test object itself but can also be related to the environment. It is advisable first to perform an impact analysis to recognize the extent of the regression testing. Impact analysis shows which parts of the software could be affected.
    ## Test Techniques
- **Black-box test techniques **are based on an analysis of the specified behavior of the test object without reference to its internal structure. Therefore, the test cases are independent of how the software is implemented. Consequently, if the implementation changes, but the required behavior stays the same, then the test cases are still useful.
    - **Equivalence Partitioning**. Divides data into partitions (known as equivalence partitions) based on the expectation that all the elements of a given partition are to be processed in the same way by the test object.
    - **Boundary Value Analysis**. Exercises the boundaries of equivalence partitions.
    - **Decision Table Testing**. When creating decision tables, the conditions and the resulting actions of the system are defined. These form the rows of the table. Each column corresponds to a decision rule that defines a unique combination of conditions, along with the associated actions.
    - **State Transition Testing. **A state diagram models the behavior of a system by showing its possible states and valid state transitions. A transition is initiated by an event, which may be additionally qualified by a guard condition. The transitions are assumed to be instantaneous and may sometimes result in the software taking action. The common transition labeling syntax is as follows: “event [guard condition] / action”.
- **White-box test techniques **are based on an analysis of the test object’s internal structure and processing. As the test cases are dependent on how the software is designed, they can only be created after the design or implementation of the test object.
    - **Statement testing**. In statement testing, the coverage items are executable statements. The aim is to design test cases that exercise statements in the code until an acceptable level of coverage is achieved.
    - **Branch testing**. In branch testing the coverage items are branches and the aim is to design test cases to exercise branches in the code until an acceptable level of coverage is achieved.
- **Experience-based test techniques** effectively use the knowledge and experience of testers for the
design and implementation of test cases.
    - **Error guessing**
        - How the application has worked in the past
        - The types of errors the developers tend to make and the types of defects that result from these
errors
        - The types of failures that have occurred in other, similar applications
    - **Exploratory testing.** In exploratory testing, tests are simultaneously designed, executed, and evaluated while the tester learns about the test object. The testing is used to learn more about the test object, to explore it more deeply with focused tests, and to create tests for untested areas.
    - **Checklist-based testing.** Checklist items are often phrased in the form of a question. It should be possible to check each item separately and directly. These items may refer to requirements, graphical interface properties, quality characteristics or other forms of test conditions. Checklists can be created to support various test types, including functional and non-functional testing (e.g., 10 heuristics for usability testing (Nielsen 1994)).
