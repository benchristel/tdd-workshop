# TDD Workshop

A three hour, interactive workshop to teach test-driven
development.

## What's included

- how to isolate test subjects for unit testing
- the differences between unit, integration, and integrated
  tests
- a discussion of what can't easily be tested (e.g. memory
  usage, in many languages)
- a discussion of myths about TDD
- dependency injection and dependency inversion
- how to use the Factory pattern in the context of
  dependency injection
- how to architect a program for testability

## What's not included

- how to set up a project with specific testing tools
- how to test legacy code
- how to undertake a big refactoring (there's [another
  workshop](https://github.com/benchristel/refactoring-workshop)
  for that)
- acceptance testing / end-to-end testing

## Procedure

- (15 minutes) brief talk on the motivations behind
  test-driven development and the red-green-refactor
  workflow.
- (2 hours) mob programming on (what?)
  - a json pretty-printer?
  - a y-combinator?
  - a server report generator
    - given a list of hostnames in a file
    - and a REST API client that checks whether a host is working
    - output a report file containing a list of working hosts and a list of non-working hosts

  - choose-your-own-adventure game engine
    - read file and display it
    - ask the player to choose an option
    - read another file and display it based on what they
      chose
    - in some rooms, the player wins - show a special screen
    - in some rooms, the player loses - show a special screen
    - some rooms have a time limit. If the player doesn't
      respond within the time limit, they lose!
- (30 minutes) debrief and time for questions

## Possible process for testable architecture

1. List the side effects that will be input/output to/from
  the program. E.g. user input via keyboard/mouse, graphics,
  sound, files, network, time, random numbers
2. Identify the mathematical properties of the output
  side effects. E.g.
  - ordering (as in a list, stream, or terminal output)
  - uniqueness (as in a set, hashmap, or filesystem—each key maps to at most one value)
  - associativity of certain operations (like string concatenation and terminal output: `print("a");print("b")` is the same as `print("ab")`)
  - commutativity (order of operands does not matter, as in set intersection)
  - idempotency (doing something once is equivalent to doing it any number of times—e.g. setting a pixel in a graphics buffer)
  - nilpotency (doing something has no observable effect)
3. Decide which responsibilities will be assigned to the
  system under test, and which will be assigned to other
  systems. E.g. the responsibility of figuring out which
  button a user clicked on might fall on a graphics library,
  so it won't be covered by our tests.
4. Based on (2) and (3), sketch an API for the system under
  test. It can be as simple as listing the input and output
  operations and their properties
  ```
  SUT | -> filesystem (idempotent writes, nilpotent reads)
      | <- clock (calls a function every frame)
      | -> logging output (ordered, associative writes)
      | <- keyboard (keydown, keyup functions)
      | <- mouse (move(x, y), click())
  ```
5. Based on (4), sketch a class for the SUT and the
   interfaces it will interact with. Outputs should be
   injected into the constructor and should be typed as
   abstractly as possible, while conforming to the properties
   identified in (2). Inputs should be method calls on the SUT.

  ```java
  class AdventureGame {
    AdventureGame(Map filesystem, Stream logger) {
      this.logger = logger
      this.filesystem = filesystem
    }

    clockTick(Integer millisElapsed) {
      // ...
    }

    keyDown(Integer key, Boolean shift) {
      // ...
    }

    keyUp(Integer key, Boolean shift) {
      // ...
    }

    mouseMove(Integer x, Integer y) {
      // ...
    }

    mouseClick(Integer button) {
      // ...
    }
  }
  ```

## Discussion questions

- Some libraries and frameworks make code harder to test.
  What are some red flags to look for when choosing a
  library/framework?
