# TDD Workshop

A three hour, interactive workshop to teach test-driven
development.

## What's included

- how to isolate test subjects for unit testing
- the spectrum of unit, integration, and integrated
  testing
- a discussion of the limitations of TDD
  (Dijkstra quote about testing being unable to demonstrate the absence of bugs)
  and what can't easily be tested (e.g. memory usage, in many languages)
- a discussion of myths about TDD
- dependency injection and dependency inversion
- how to architect a program for testability
- using TDD to reveal edge cases that are missing from user stories

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
- (2 hours) mob programming on a **choose-your-own-adventure game engine.**
  (Why this project? Because it involves both interaction with side-effecting systems (the filesystem, terminal I/O, and time) and data transformation, without involving undue complexity.)

  - (5 minutes) present a live demo of the game, so participants get a sense for how it will work.
  - tell them "this isn't a real implementation of the game, but a mockup designed to give you a sense of how the real
    game should work. Our task is to implement the game. We have a walking skeleton already built for us" (explain the
    concept of a walking skeleton)
  - show them this backlog of user stories:
    - The game launches when I run `adventure-game` at the command line
      (Perhaps this first story should already be done, so we don't have to spend time building the walking skeleton.)
    - I see the starting room's text when I launch the game
    - I can navigate between rooms with the keyboard
    - I do not see the room filenames printed to the terminal
    - I can win the game
    - I can lose the game
    - I do not see the win/lose directives printed to the terminal
    - I lose the game if I overstay a room's time limit
    - I do not see the time limit directives printed to the terminal
  - Start with the first undelivered story. "What test would you write?"
    - discuss what boundary is appropriate to test at
    - is it okay for the test to invoke the actual game executable? why or why not?
      - we might want to avoid building the executable if unit tests are failing
    - is it okay for the test to read actual files from the filesystem? Why or why not?
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
    AdventureGame(Map<String, String> filesystem, WriteStream log) {
      this.log = log;
      this.filesystem = filesystem;
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
