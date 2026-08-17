---
base: "[[Reading List.base]]"
Rating: ⭐️
Category:
  - Code Design
  - Testing
Author: Gerard Meszaros
Status: Completed
---
## Chapter 2 - Test Smells

A smell is a symptom of a problem. A smell doesn’t necessarily tell us what is
wrong, because a particular smell may originate from any of several sources.
Most of the smells in this book have several different named causes; some causes
even appear under several smells. That’s because a root cause may reveal itself
through several different symptoms (i.e., smells).

These code smells were described in a chapter coauthored by Kent Beck that
started with the famous quote from Grandma Beck: “If it stinks, change it.” The
context of this quote was the question, “How do you know you need to change
a baby’s diaper?” And so a new term was added to the programmer’s lexicon.

Some smells are inevitable simply because they take too much effort to elimi-
nate. The important thing is that we are aware of the smells and know what
causes them. We can then make a conscious decision about which ones we must
address to keep the project running effi ciently.

### The Project Smells

Project smells are symptoms that something has gone wrong on the project. Their
root cause is likely to be one or more of the code or behavior smells. Because proj-
ect managers rarely run or write tests, however, project smells are likely to be the
fi rst hint they get that something may be less than perfect in test automation land.
Project managers focus most on functionality, quality, resources, and cost.
For this reason, the project-level smells tend to cluster around these issues. The
most obvious metric a project manager is likely to encounter as a smell is the
quality of the software as measured in defects found in formal testing or by
users/customers. If the number of Production Bugs (page 268) is higher than
expected, the project manager must ask, “Why are all of these bugs getting
through our safety net of automated tests?”
The project manager may be monitoring the number of times the daily in-
tegration build fails as a way of getting an early indication of software quality
and adherence to the team’s development process. The manager may become
worried if the build fails too frequently, and especially if it takes more than a
few minutes to fi x the build.

### The Behavior Smells

Behavior smells are encountered when we compile or run tests. We don’t have
to be particularly observant to notice them, as these smells will take the form of
compile errors or test failures.
The most common behavior smell is Fragile Tests. It arises when tests that
once passed begin failing for some reason. The Fragile Test problem has given
test automation a bad name in many circles, especially when commercial
“record and playback” test tools fail to deliver on their promise of easy test
automation. Once recorded, these tests are very susceptible to breakage. Often
the only remedy is to rerecord them because the test recordings are diffi cult to
understand or modify by hand.

### The Code Smells

Code smells are the “classic” bad smells that were fi rst described by Martin
Fowler in Refactoring [Ref]. Indeed, most of the smells identifi ed by Fowler are
code smells. These smells must be recognized by test automaters as they main-
tain test code. Although code smells typically affect maintenance cost of tests,
they may also be early warning signs of behavior smells to follow.
When reading tests, a fairly obvious—albeit often overlooked—smell is
Obscure Test. It can take many forms, but all versions have the same impact: It
becomes diffi cult to tell what the test is trying to do, because the test does not
Communicate Intent (page 41). This ambiguity increases the cost of test main-
tenance and can lead to Buggy Tests when a test maintainer makes the wrong
change to the test.

## Chapter 3 - Goals of Test Automation

Here are some high-level objectives that might
apply:
• Tests should help us improve quality.
• Tests should help us understand the SUT.
• Tests should reduce (and not introduce) risk.
• Tests should be easy to run.
• Tests should be easy to write and maintain.
•
Tests should require minimal maintenance as the system evolves
around them.

## Chapter 4 - Philosophy of Test Automation

The “big picture” questions include whether we write tests fi rst or last, whether we think of them as tests or examples, whether we build the software from the inside-out or from the outside-in, whether we verify state or behavior, and whether we design the fixture upfront or test by test.

- “Test after” versus “test fi rst”
- Test-by-test versus test all-at-once
- “Outside-in” versus “inside-out” (applies independently to design and
coding)
-  Behavior verifi cation versus state verifi cation
-  “Fixture designed test-by-test” versus “big fi xture design upfront”

## Chapter 5 - Principles of Test Automation

- Principle: Write the Tests First
- Principle: Design for Testability
- Principle: Use the Front Door First
    - When all choices are equally effective, we should use round-trip tests to test
our SUT. To do so, we test an object through its public interface and use State
Verifi cation (page 462) to determine whether it behaved correctly. If this is not suf-
fi cient to accurately describe the expected behavior, we can make our tests layer-
crossing tests and use Behavior Verifi cation to verify the calls the SUT makes to
depended-on components (DOCs). If we must replace a slow or unavailable DOC
with a faster Test Double (page 522), using a Fake Object (page 551) is preferable
because it encodes fewer assumptions into the test (the only assumption is that the
component that the Fake Object replaces is actually needed).
- Principle: Communicate Intent
- Principle: Don’t Modify the SUT
    - Modifying the SUT is a dangerous thing whether we are putting in Test
Hooks (page 709), overriding behavior in a Test-Specifi c Subclass, or replacing
a DOC with a Test Double. In any of these circumstances, we may no longer
actually be testing the code we plan to put into production.
We need to ensure that we are testing the software in a confi guration that is
truly representative of how it will be used in production. If we do need to replace
something the SUT depends on to get better control of the context surrounding the
SUT, we must make sure that we are doing so in a representative way.
- Principle: Keep Tests Independent
- Principle: Isolate the SUT
- Principle: Minimize Test Overlap
- Principle: Minimize Untestable Code
    - Some kinds of code are diffi cult to test using Fully Automated Tests. GUI com-
ponents, multithreaded code, and Test Methods immediately spring to mind as
“untestable” code. All of these kinds of code share the same problem: They are
embedded in a context that makes it hard to instantiate or interact with them
from automated tests.
- Principle: Keep Test Logic Out of Production Code
- Principle: Verify One Condition per Test
    - We design each test to have four distinct phases (see Four-Phase Test on
page 358) that are executed in sequence: fi xture setup, exercise SUT, result
verifi cation, and fi xture teardown
    - In the fi rst phase, we set up the test fi xture (the “before” picture) that
is required for the SUT to exhibit the expected behavior as well as any-
thing we need to put in place to observe the actual outcome (such as
using a Test Double)
    - In the second phase, we interact with the SUT to exercise whatever
behavior we are trying to verify. This should be a single, distinct behav-
ior; if we try to exercise several parts of the SUT, we are not writing a
Single-Condition Test
    - In the third phase, we do whatever is necessary to determine whether
the expected outcome has been obtained and fail the test if it has not
    - In the fourth phase, we tear down the test fi xture and put the world
back into the state in which we found it.
- Principle: Test Concerns Separately
- Principle: Ensure Commensurate Effort and Responsibility

## Chapter 6 - Test Automation Strategy

What makes a decision “strategic”? A decision is strategic if it is “hard to
change.” That is, a strategic decision affects a large number of tests, especially
such that many or all the tests would need to be converted to a different approach 

at the same time. Put another way, any decision that could cost a large amount of
effort to change is strategic.

Roughly speaking, we can divide tests into the following two categories:
• Per-functionality tests (also known as functional tests) verify the behavior
of the SUT in response to a particular stimulus.
• Cross-functional tests verify various aspects of the system’s behavior
that cut across specifi c functionality.

|   | Per functional → Support Development | Cross functional → Critique Prouduct |
| --- | --- | --- |
| Business Facing | Customer Tests | Usability Testing |
|   | Component Tests | Exploratory Testing |
| Technology Facing | Unit Tests | Property Testing |

A summary of the kinds of tests we write and why. The left column
contains the tests we write that describe the functionality of the product at
various levels of granularity; we perform these tests to support development.
The right column contains tests that span specifi c chunks of functionality; we
execute these tests to critique the product.

### Per functional

**Customer tests **verify the behavior of the entire system or application. They typi-
cally correspond to scenarios of one or more use cases, features, or user stories.
These tests often go by other names such as functional tests, acceptance tests, or
end-user tests.

**Unit tests **verify the behavior of a single class or method that is a consequence
of a design decision. This behavior is typically not directly related to the require-
ments except when a key chunk of business logic is encapsulated within the
class or method in question. These tests are written by developers for their own
use; they help developers describe what “done looks like” by summarizing the
behavior of the unit in the form of tests.

Component tests verify components consisting of groups of classes that collec-
tively provide some service. They fi t somewhere between unit tests and customer
tests in terms of the size of the SUT being verifi ed. Although some people call
these “integration tests” or “subsystem tests,” those terms can mean something
entirely different from “tests of a specifi c larger-grained subcomponent of the
overall system.”

Fault insertion tests typically show up at all three levels of granularity within
these functional tests, with different kinds of faults being inserted at each level.
From a test automation strategy point of view, fault insertion is just another set
of tests at the unit and component test levels. Things get more interesting at the
whole-application level, however. Inserting faults here can be hard to automate
because it is challenging to automate insertion of the faults without replacing
parts of the application.

### Cross functional

Property Tests
Performance tests verify various “nonfunctional” (also known as “extra-functional”
or “cross-functional”) requirements of the system. These requirements are different
in that they span the various kinds of functionality. They often correspond to the
architectural “-ilities.” These kinds of tests include
• Response time tests
• Capacity tests
• Stress tests
From a test automation perspective, many of these tests must be automated (at
least partially) because human testers would have a hard time creating enough
load to verify the behavior under stress. While we can run the same test many
times in a row in xUnit, the xUnit framework is not particularly well suited to
automating performance tests.

Usability tests verify “fi tness for purpose” by confi rming that real users can use
the software application to achieve the stated goals. These tests are very diffi cult
to automate because they require subjective assessment by people regarding how
easy it is to use the SUT. For this reason, usability tests are rarely automated

Exploratory testing is a way to determine whether the product is self-consistent.
The testers use the product, observe how it behaves, form hypotheses, design
tests to verify those hypotheses, and exercise the product with them. By its very
nature, exploratory testing cannot be automated, although automated tests can
be used to set up the SUT in preparation for doing exploratory testing.

### Test Fixture Strategies

The test fi xture management strategy is strategic because it has a large impact on
the execution time and robustness of the tests. The effects of picking the wrong
strategy won’t be felt immediately because it takes at least a few hundred tests
before the Slow Tests (page 253) smell becomes evident and probably several
months of development before the High Test Maintenance Cost smell starts to
emerge. Once these smells appear, however, the need to change the test automa-
tion strategy will become apparent—and its cost will be signifi cant because of
the number of tests affected.

Every test consists of four parts, as described in Four-Phase Test (page 358). In
the fi rst phase, we create the SUT and everything it depends on and put them
into the state required to exercise the SUT. In xUnit, we call everything we need
in place to exercise the SUT the test fi xture, and we call the part of the test logic
that we execute to set it up the fi xture setup phase of the test.

The most common way to set up the fi xture is to use front door fi xture setup
by calling the appropriate methods on the SUT to construct the objects. When
the state of the SUT is stored in other objects or components, we can do Back
Door Setup (see Back Door Manipulation on page 327) by inserting the neces-
sary records directly into the other component on which the behavior of the
SUT depends. We use Back Door Setup most often with databases or when
we need to use a Mock Object

The fi rst and simplest fi xture management strategy requires us to worry only
how we will organize the code to build the fi xture for each test. That is, do we
put this code in our Test Methods, factor it into Test Utility Methods that we
call from our Test Methods, or put it into a setUp method in our Testcase Class?
This strategy involves the use of **Transient Fresh Fixtures **(see Fresh Fixture).
These fi xtures live only in memory and very conveniently disappear as soon as
we are done with them.
A second strategy involves the use of Fresh Fixtures that, for one reason or
another, persist beyond the single Test Method that uses it. To keep them from
turning into Shared Fixtures (page 317), these **Persistent Fresh Fixtures **(see
Fresh Fixture) require explicit code to tear them down at the end of each test.
This requirement brings into play the fi xture teardown patterns.
A third strategy involves persistent fi xtures that are deliberately reused across
many tests. This **Shared Fixture **strategy is often used to improve the execu-
tion speed of tests that use a Persistent Fresh Fixture but comes with a fair
amount of baggage. These tests require the use of one of the fi xture construc-
tion and teardown triggering patterns. They also involve tests that interact with
one another, whether by design or by consequence, which often leads to Erratic
Tests (page 228) and High Test Maintenance Costs.

The more tests that share a fi xture, the more likely that one of them will make a
mess of things and spoil everything for all the tests that follow it. The less often
we reconstruct the fi xture, the longer the effects of a messed-up fi xture will per-
sist.

We can avoid both Unrepeatable Tests and Test Run Wars by setting up the
fi xture each time the test suite is run. xUnit provides several ways to do so,
including Lazy Setup (page 435), Suite Fixture Setup (page 441), and Setup
Decorator (page 447). The concept of “lazy initialization” should be familiar to
most object-oriented developers; here we just apply the concept to the construc-
tion of the test fi xture. The latter two choices provide a way to tear down the
test fi xture when the test run is fi nished because they call a setUp method and a
corresponding tearDown at the appropriate times; Lazy Setup does not give us a
way to do this.

### Testability Patterns

When testing a particular piece of software, our tests can take one of two basic
forms.
A **round-trip test** interacts with the SUT in question only through its public
interface—that is, its “front door” (Figure 6.7). Both the control points and
the observation points in a typical round-trip test are simple method calls. The
nice thing about this approach is that it does not violate encapsulation. The test
needs to know only the public interface of the software; it doesn’t need to know
anything about how it is built.
The main alternative is the **layer-crossing test **(Figure 6.8), in which we exer-
cise the SUT through the API and keep an eye on what comes out the back door
using some form of Test Double such as a Test Spy (page 538) or Mock Object.
This can be a very powerful testing technique for verifying certain kinds of
mostly architectural requirements. Unfortunately, this approach can also result
in Overspecifi ed Software (see Fragile Test) if it is overused because changes in
how the software implements its responsibilities can cause tests to fail.

## Chapter 7 - xUnit Basics

The most common types of tests are the Simple Success Test (see Test Method),
which verifi es that the SUT has behaved correctly with valid inputs, and the
Expected Exception Test (see Test Method), which verifi es that the SUT raises an
exception when used incorrectly. A special type of test, the Constructor Test (see
Test Method), verifi es that the object constructor logic builds new objects cor-
rectly. Both “simple success” and “expected exception” forms of the Constructor
Test may be needed.

A test is considered to have failed when an assertion fails. That is, the test
asserts that something should be true by calling an Assertion Method, but
that assertion turns out not to be the case. When it fails, an Assertion Method
throws an assertion failure exception (or whatever facsimile the programming
language supports).
A test is considered to have an error when either the SUT or the test itself
fails in an unexpected way. Depending on the language being used, this problem
could consist of an uncaught exception, a raised error, or something else. 

## Chapter 8 - Transient Fixture Management

In a Fresh Fixture strategy, we set up a brand-new fi xture for every test we run
(Figure 8.2). That is, each Testcase Object (page 382) builds its own fi xture be-
fore exercising the SUT and does so every time it is rerun. That is what makes
the fi xture “fresh.” As a result, we completely avoid the problems associated
with Interacting Tests

The fixture setup logic includes the code needed to instantiate the SUT,2 the code to put the SUT into the appropriate starting state, and the code to create and initialize the state of anything the SUT depends on or that will be passed to it as an argument.

- The most obvious way to set up a Fresh Fixture is through **In-line Setup **(page 408), in which all fixture setup logic is contained within the Test Method. We construct objects, call methods on them, construct the SUT, and call methods on it to put into a specific state. We perform all of
these tasks from within our Test Method.
- This type of fixture can also be constructed by using **Delegated Setup** (page 411), which involves calling Test Utility Methods (page 599).
- Finally, we can use **Implicit Setup** (page 424), in which the Test Automation Framework (page 298) calls a special setUp method we provide on our Testcase Class.

```python
public void testStatus_initial() {
// In-line setup
Airport departureAirport = new Airport("Calgary", "YYC");
Airport destinationAirport = new Airport("Toronto", "YYZ");
Flight flight = new Flight( flightNumber,
departureAirport,
destinationAirport);
// Exercise SUT and verify outcome
assertEquals(FlightState.PROPOSED, flight.getStatus());
// tearDown:
// Garbage-collected
}
```

We can prevent these test smells by moving the code that sets up the fi xture
out of the Test Method. The location where we move it determines which of the
alternative fi xture setup strategies we have used.

A quick and easy way to reduce Test Code Duplication and the resulting Obscure
Tests is to refactor our Test Methods to use Delegated Setup. We can use an Extract
Method [Fowler] refactoring to move a sequence of statements used in several Test
Methods into a Test Utility Method that we then call from those Test Methods.

```python
public void testGetStatus_inital() {
// Setup
Flight flight = createAnonymousFlight();
// Exercise SUT and verify outcome
assertEquals(FlightState.PROPOSED, flight.getStatus());
// Teardown
// Garbage-collected
}
```

One goal of these Creation Methods is to eliminate the need for every test
to know the details of how the objects it requires are created. This stream-
lining goes a long way toward preventing Fragile Tests caused by changes to 

constructor method signatures or semantics. When a test does not care about
the specifi c identity of the objects it is creating, we can use Anonymous Cre-
ation Methods (see Creation Method).

When a test does care about the attributes of the object being created, we
use a Parameterized Anonymous Creation Method (see Creation Method). This
method is passed any attributes that the test cares about (i.e., attributes that are
important to the test outcome), leaving all other attributes to be defaulted by the
implementation of the Creation Method.

Most members of the xUnit family provide a convenient hook for calling code
that needs to be **run before every Test Method**. Some members call a method
with a specific name (e.g., **setUp**). Others call a method that has a specifi c annota-
tion (e.g., “@before” in JUnit) or method attribute (e.g., “[Setup]” in NUnit).

The fi rst consequence is that this approach can make the tests more diffi cult to
understand because we cannot see how the pre-conditions of the test (the test
fi xture) correlate with the expected outcome within the Test Method; we have to
look in the setUp method to see this relationship.

The setUp method is most prone to misuse when it is applied to build a Gen-
eral Fixture (see Obscure Test) with multiple distinct parts, each of which is
dedicated to a different Test Method. This can lead to Slow Tests (page 253)
if we are building a Persistent Fresh Fixture. More importantly, it can lead to
Obscure Tests by hiding the cause–effect relationship between the fi xture and
the expected outcome of exercising the SUT.

## Chapter 11 - Using Test doubles

A Test Double is any object or component that we install in place of the real
component for the express purpose of running a test. Depending on the reason
why we are using it, a Test Double can behave in one of four ways

Dummy Objects are a degenerate form of Test Double. They exist solely so
that they can be passed around from method to method; they are never used.
That is, Dummy Objects are not expected to do anything except exist. Often,
we can get away with using “null” (or “nil” or “nothing”); at other times,
we may be forced to create a real object because the code expects something
non-null. In dynamically typed languages, almost any real object will do; in
statically typed languages, we must make sure that the Dummy Object is
“type-compatible” with the parameter it is being passed as or the variable to
which it is being assigned.

A Test Stub is an object that acts as a control point to deliver indirect
inputs to the SUT when the Test Stub’s methods are called. Its use allows us to
exercise Untested Code paths in the SUT that might otherwise be impossible to
traverse during testing. A Responder (see Test Stub) is a basic Test Stub that is
used to inject valid and invalid indirect inputs into the SUT via normal returns
from method calls. A Saboteur (see Test Stub) is a special Test Stub that raises
exceptions or errors to inject abnormal indirect inputs into the SUT.

A Test Spy is an object that can act as an observation point for the indirect
outputs of the SUT. To the capabilities of a Test Stub, it adds the ability to
quietly record all calls made to its methods by the SUT. The verifi cation part
of the test performs Procedural Behavior Verifi cation on those calls by using
a series of assertions to compare the actual calls received by the Test Spy with
the expected calls.

A Mock Object is also an object that can act as an observation point for the
indirect outputs of the SUT. Like a Test Stub, it may need to return information
in response to method calls. Also like a Test Spy, a Mock Object pays attention
to how it was called by the SUT. It differs from a Test Spy, however, in that the
Mock Object compares actual calls received with the previously defi ned expec-
tations using assertions and fails the test on behalf of the Test Method. As a
consequence, we can reuse the logic employed to verify the indirect outputs of
the SUT across all tests that use the same Mock Object.

A Fake Object is quite different from a Test Stub or a Mock Object in that it is nei-
ther directly controlled nor observed by the test. The Fake Object is used to replace
the functionality of the real DOC in a test for reasons other than verifi cation of indi-
rect inputs and outputs. Typically, a Fake Object implements the same functionality
or a subset of the functionality of the real DOC, albeit in a much simpler way. The
most common reasons for using a Fake Object are that the real DOC has not yet
been built, is too slow, or is not available in the test environment.

### Installing the test double

Before we exercise the SUT, we need to “install” any Test Doubles on which
our test depends. The term “install” here serves as a generic way to describe the
process of telling the SUT to use our Test Double, regardless of the exact details
regarding how we do it. The normal sequence is to instantiate the Test Double,
confi gure it if it is a Confi gurable Test Double, and then tell the SUT to use the
Test Double either before or as we exercise the SUT. There are several distinct
ways to “install” the Test Double, and the choice between them may be as much
a matter of style as of necessity if we are designing the SUT for testability. Our
choices may be much more constrained, however, when we try to retrofi t our
tests to an existing design.
The basic choices boil down to Dependency Injection (page 678), in which the
client software tells the SUT which DOC to use; Dependency Lookup (page 686),
in which the SUT delegates the construction or retrieval of the DOC to another
object; and Test Hook, in which the DOC or the calls to it within the SUT
are modifi ed.
If an inversion of control framework is available in our language, our tests
can substitute dependencies without much additional work on our part. This
removes the need for building in the Dependency Injection or Dependency
Lookup mechanism.

Dependency Injection is a class of design decoupling in which the client tells the
SUT which DOC to use at runtime (Figure 11.10). The test-driven development
(TDD) movement has greatly increased its popularity because Dependency Injec-
tion makes for more easily tested designs. This pattern also makes it possible to
reuse the SUT more broadly because it removes knowledge of the dependency
from the SUT; often the SUT will be aware of only a generic interface that the
DOC must implement. Dependency Injection comes in several specifi c fl avors,
with the choice between them being largely a matter of taste:
• Setter Injection (see Dependency Injection): The SUT accesses the
DOC through a public attribute (i.e., a variable or property). The test
explicitly sets the attribute after instantiating the SUT to installing the
Test Double

Constructor Injection (see Dependency Injection): The SUT accesses
the DOC through a private attribute. The test passes the Test Dou-
ble to the SUT via a constructor that takes the DOC to be used as
an explicit argument and initializes the attribute from it. This may be
the primary constructor used by production code clients or it may be
an alternative constructor. In the latter case, the primary constructor
should call this constructor, passing the default DOC to it as an
argument.
• Parameter Injection (see Dependency Injection): The SUT receives
the DOC as a method parameter. The test passes in a Test Double,
whereas the production code passes in the real object.5 This approach
works well when the API of the SUT takes as a parameter the object
we need to replace. Although Mock Object afi cionados might argue
that designing APIs in this way improves the design of the SUT, it is
not always possible or practical to pass everything required to each
method.

When software is not designed for testability or when Dependency Injection is
not appropriate, we may fi nd it convenient to use Dependency Lookup. This
pattern also removes the knowledge of exactly which DOC should be used from
the SUT, but it does so by having the SUT ask another piece of software to create
or fi nd the DOC on its behalf (Figure 11.11). This opens the door to changing
the DOC at runtime without modifying the SUT’s code. We do have to modify
the behavior of the intermediary somehow, and this is where the specifi c variants
of Dependency Lookup differ from one another:

Test Hooks are the “elephant in the room” that no one wants to talk about
because they may lead to Test Logic in Production. Test Hooks, however, are
a perfectly legitimate way to get legacy code under test when it is too hard or
dangerous to introduce one of the techniques described earlier. They are best
used as a “transition” strategy to allow Scripted Tests (page 285) or Recorded
Tests (page 278) to be automated to provide a Safety Net (see page 24) while
large-scale refactoring is undertaken to improve testability. Ideally, once the
code has been made more testable, better tests can be prepared using the tech-
niques described earlier and the Test Hooks can be removed.

## Chapter 12 - Organizing our Tests

One school of thought is to put all Test Methods that verify a particular feature of
the SUT—where a “feature” is defi ned as one or more methods and attributes that
collectively implement some capability of the SUT—into a single Testcase Class
(Figure 12.3). This makes it easy to see all test conditions for that feature. (Use of
appropriate Test Naming Conventions helps achieve this clarity.) It can, however,
result in similar fi xture setup code being required in each Testcase Class.

The opposing view is that one should group all Test Methods that require the same
test fi xture (same pre-conditions) into one Testcase Class per Fixture (page 631; see
Figure 12.4). This facilitates putting the test fi xture setup code into the setUp method
(Implicit Setup; see page 424) but can result in scattering of the test conditions for
each feature across many Testcase Classes.

Testcase
Class per Fixture is commonly used when we are writing unit tests for stateful
objects and each method needs to be tested in each state of the object. Testcase
Class per Feature (page 624) is more appropriate when we are writing customer
tests against a Service Facade [CJ2EEP]; it enables us to keep all the tests for
a customer-recognizable feature together.

We can make the test coverage more
obvious by naming each Test Method systematically based on which test condi-
tion it verifi es. Regardless of which test method organization scheme we use, we
would like the combination of the names of the test package, the Testcase Class,
and the Test Method to convey at least the following information:
• The name of the SUT class
• The name of the method or feature being exercised
•The important characteristics of any input values related to the exercising
of the SUT
• Anything relevant about the state of the SUT or its dependencies

useful it is to include the “expectations” side of the
test condition:
• The outputs (responses) expected when exercising the SUT
• The expected post-exercise state of the SUT and its dependencies
This information can be included in the name of the Test Method prefi xed by
“should.”

## Chapter 14 - A Roadmap to Effective Test Automation

Some kinds of tests are harder to write than others. This diffi culty arises partly
because the techniques are more involved and partly because they are less well
known and the tools to do this kind of test automation are less readily avail-
able. The following common kinds of tests are listed in approximate order of
diffi culty, from easiest to most diffi cult:

1. Simple entity objects (Domain Model [PEAA])
• Simple business classes with no dependencies
• Complex business classes with dependencies
2. Stateless service objects
• Individual components via component tests
• The entire business logic layer via Layer Tests (page 337)
3. Stateful service objects
•Customer tests via a Service Facade [CJ2EEP] using Subcutaneous
Tests (see Layer Test)
• Stateful components via component tests
4. “Hard-to-test” code
•User interface logic exposed via Humble Dialog (see Humble
Object on page 695)
• Database logic
• Multi-threaded software
5. Object-oriented legacy software (software built without any tests)
6. Non-object-oriented legacy software

Given that some kinds of tests are much harder to write than others, it makes
sense to focus on learning to write the easier tests fi rst before we move on to the
more difficult kinds of tests. When teaching automated testing to developers, I
introduce the techniques in the following sequence.

7. Exercise the happy path code
• Set up a simple pre-test state of the SUT
• Exercise the SUT by calling the method being tested
8. Verify direct outputs of the happy path
• Call Assertion Methods (page 362) on the SUT’s responses
• Call Assertion Methods on the post-test state
9. Verify alternative paths
• Vary the SUT method arguments
• Vary the pre-test state of the SUT
• Control indirect inputs of the SUT via a Test Stub (page 529)
10. Verify indirect output behavior
• Use Mock Objects (page 544) or Test Spies (page 538) to intercept
and verify outgoing method calls
11. Optimize test execution and maintainability
• Make the tests run faster
• Make the tests easy to understand and maintain
• Design the SUT for testability
• Reduce the risk of missed bugs