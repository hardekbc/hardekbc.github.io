# FINAL GRADING REMINDERS

- for assign1 i allowed late submissions up to a week but said i would subtract 11 points from the overall score as a late penalty.

    + i should make sure that students don't get a lower score than they had before turning in late: look at everyone's (who turned it in late) submission history and take the higher of (pre-deadline, post-deadline).

    + one group turned it in slightly late (a few minutes) but it's kind of my fault since i moved the deadline from 1am to 12:05am at the last moment, so don't count it against them.

# RETROSPECTIVE
## timing

- remember that this quarter i had to cancel the entire first week, so it's only 9 weeks + finals); also apparently i actually have 1 hr 50 min but i've only been using 1 hr 40 min.

    + week 2.1: through 'the language we're analyzing'
                (ended 20 minutes early)
    + week 2.2: through 'control-flow graph (CFG)'
    + week 3.1: through 'abstract execution (MOP vs MFP) :: EXAMPLE 1'
                (hw1 out)
    + week 3.2: through 'second-order DFA analyses'
    + week 4.1: through middle of LATTICES
    + week 4.2: through middle of general set constraint language description
    + week 5.1: through solver optimization
                (hw1 due)
    + week 5.2: through andersen constraint generation for direct calls
                (hw2 out)
    + week 6.1: through program slicing intro
    + week 6.2: up to dominance frontier example
    + week 7.1: ???
    + week 7.2: ???
                (hw2 due, hw3 out)
    + week 8.1: ???
    + week 8.2: ???
    + week 9.1: ???
    + week 9.2: ???
                (hw3 due, hw4 out)
    + week 10.1: ???
    + week 10.2: ???
    + week 11 (finals): (hw4 due)

## grading

- see assignment-specific notes in the sections below.

- copy the autograder tests to the unit tests for the analysis implementations.

- for the autograder, rather than having entirely separate versions for each assignment that share a huge amount of code, it would probably be better to have a single integrated autograder that can be easily configured to grade a specific assignment just by setting a flag.

- i might also want some tests to be worth more than others; maybe add in points values to their names a la mentor (e.g., a complex test that explores many aspects of the problem together would be worth more than a simple, focused test).

- a common student habit is to neglect to write any of their own tests and just use the autograder as a debugger. one way to combat this is to require them to submit their own tests.

    - problem 1: i need to write a mutation test suite to assess their tests.

    - problem 2: can't they just throw together one huge test that does bunches of stuff to try to get good coverage without really thinking about it? what we really want is a large set of small, targeted tests, but that will require some thought on how to enforce.

- if i divide the tests into suites by what they're testing (as i do now), then perhaps generating random tests for each suite and grading a suite by percentage of tests passed would be fine. this would allow more thorough testing and allow me to give the failing test as feedback to the student (since there could be thousands of them).

    + the difficulty is ensuring that the tests are non-trivial, since a randomly generated program (or set of constraints) may not do anything interesting analysis-wise. maybe a filtering heuristic based on the analysis solution for the generated test?

- the tests aren't as comprehensive and/or targeted as i would like, for example for assignment 2 part 1 the constraint inputs mix testing various combinations of constraint types plus various combinations of constructor types in an ad-hoc way. it would maybe be better to vary exactly one thing between tests (at least along a particular test chain, like 'simple-chain' vs 'simple-cycle') so that failing a test pinpoints exactly one issue.

    + another thing to consider is the weight of the tests; since they're all worth equal amounts, if there's only one test that exercises a specific thing then that thing is only worth a small percentage of the total test suite (even if it's important). to combat this either we need multiple tests that all test that thing or we need to be able to manually specify test weights. or make the entire test suite pass/fail as a whole, but this seems unfair if the students can't figure out what a specific failing test is actually testing since they can't see the inputs.

    + something to consider is to have a number of very targeted tests vs having a small number of more general tests, and the spectrum in-between. the more tests there are the less each is worth and so the less a student is penalized for a mistake; the fewer tests the more each is worth and the more students are penalized for a mistake. whether small or large penalties are better is something to decide. for example, for "test chains" of the form `<analysis>-<test-suite>-<name>_<index>`, i could conflate all the tests that are part of that test chain into a single test.

## intro to DFA

- in the basics of dataflow analysis section, i had to skip the parity example and barely had time to get through the sign example (in fact, i didn't get all the way through it). maybe remove parity altogether and just talk about the sign domain.

    + if i do this, then i need to change the MOP vs MFP example that currently uses parity

- ask students who use languages other than C++ if i can use their parsing/datastructure for future students.

    + also collect some hints about how to set up the autograder submission for different cases (e.g., for using python3)

- to speed things up, maybe move the ir-specific analysis implementation details (e.g., for sign analysis and reaching definitions, etc) to the assignment description instead of covering it in lecture.

    + if i do this, i need to modify the lecture to still talk about how to handle pointers conservatively for sign analysis and reaching definitions; currently i cover it when going over the ir-specific analysis details.

- really what i should do is to split things up differently: first cover signs and reaching definitions for programs without pointers (this covers 80% of the assignment points), then cover pointers but no pointer arguments, then pointer arguments. basically, mirror the way the assignment is set up.

    + this doesn't help with getting through the material faster, but should help the students make sense of it better.

- assignment 1 notes

    + for assign1 reaching definitions, i don't think the tests are exploring all the possibilities outlined in the explanation of the abstract semantics in the lecture notes. i should beef them up to ensure students are covering all the possible cases.

    + someone asked in class if we really need separate fake vars for each $alloc, and i don't think we do (they'll all be updated to the same values anyway, similar to the reasoning for having one fake var per struct field type). i fact, i think we actually only need one fake variable per _type_ that is either alloc'd _or_ is a struct field (since we're assuming any load/store can access any type-appropriate object).

    + from questions that come up, it seems like i need to give more in-class examples for these analysis (though unfortunately it would slow lecture down even more).

    + is having them do sign analysis and reaching definitions together too much for one assignment? maybe i should space them out more?

    + maybe explicitly lay out for them how to approach the assignment: take an analysis, write a set of tests with no pointers, implement until tests pass, write tests with no pointer-type args, implement until tests pass, etc.

    + for the reaching defs solution, we don't take type information into account for loads in terms of `external-def`; it would be an easy change and might reduce student confusion.

    + for the sign analysis it sometimes isn't clear to the students when they need to add a variable that maps to BOTTOM to the store---sometimes my solution does and sometimes it doesn't. it would probably be easiest and most consistent to just never print a variable that maps to BOTTOM (and assume that any unmapped variable has value BOTTOM). i would need to change my implementation for this.

    + people seem to have real difficulty with the address-taken objects for reaching definitions; i need to figure out a better way to explain it.

    + i need to remind students that they can answer a lot of their own questions about output format and analysis behavior by using the solution that i provide.

- maybe i can modify the lecture to replace just enumerating abstract semantics: have example programs and go through them live with student help, then generalize from each statement's specifics to the general rules. that is, make the connection between the abstract semantics and the analysis more clear.

    + this might slow things down, though.

    + if i have a specific example for each rule, that might be providing too much info for the assignments (or i need to reevaluate the grading scheme).

- i should maybe rename/reduce the test programs in 260-public/example-c-programs (maybe to example-X.c?), because some students seem to think they're tests for the analyses in assignment 1 (this is bad for the sign analysis particularly, because they contain operations beyond + and < that students shouldn't need for the assignment).

    + definitely emphasize to the students that they shouldn't use them as tests for the assignments; some students are using them instead of creating their own tests at all. maybe i need to think of a way to ensure that they write their own tests instead of taking the lazy way out.

    + maybe only have one or two examples instead of a bunch of them?

- the grading scheme has a bit of a flaw: if a student is failing a test but can't create their own test that fails, it's hard to provide guidance.

    + maybe just be more precise about what each test contains?

    + it's also hard to diagnose simple things like they forgot to order the output correctly (which only causes an error if they write a test where the functions/blocks are out of order and the output preserves that incorrect order, so it's hard for them to write a failing test case when this is the problem).

    + maybe the answer is to say that they can't share code between groups, but they *can* share tests. this does mean that there isn't as much incentive to write their own tests, but some groups aren't doing that anyway (and the ones that are would probably write them regardless).

- maybe abbreviate the math definitions and theorems for DFA; instead of building up gradually i could just go straight to lattices and kleene's theorem.

## set constraint-based analysis

- i could speed things up by skipping the general constraint language and going straight to inclusion constraints (just mention that we can make more complicated languages but it quickly becomes undecidable).

- i'm skipping a lot of things that were in the old lecture notes (or not, but are covered in my existing analysis implementations); depending on how the final timing works out i might want to add them back in

    + solver optimizations
    + details of field-{based, sensitive} analysis
    + equality-based analysis, steensgaard, type inference

## things i left out that could be added somewhat easily

- simple widening (integer interval analysis)

- more DFA analyses:

    + string analyses (string constants, prefix, regex)
    + uninitialized variables / necessarily initialized variables
    + available expressions
    + very busy expressions
    + liveness analysis

- constraint solver optimizations

- field-based and field-sensitive pointer analysis details

- using andersen-style pointer analysis to optimize DFA pointer analysis

- equality-based set constraints

    + steensgaard pointer analysis
    + type inference

- SSA and sparse analysis

- abstract interpretation stuff

- dealing with languages that don't have easily-computed control-flow (JSAI)

    + kind of needs abstract interpretation stuff as a setup

- abstract garbage collection (?)

## things i left out that could be added with more work

- flow- and/or context-sensitive pointer analysis

- symbolic/concolic execution

- more context-sensitivity options

    + transfer functions (summary-based)
    + CFL
    + object-sensitivity
    + IFDS

- demand-driven program analysis

- incremental program analysis

- declarative program analysis (DOOP, souffle)

# admin

- introduce myself and this course

- rocky start to the quarter, still playing catch-up (and still not 100%); please be patient while i figure things out, i am not as prepared as i wanted to be

- don't need any background in program analysis, but i expect some mathematical maturity (discrete math such as sets, relations, functions; computability and complexity; etc)

- no textbook (there are a couple of texts out there, but none that are good for teaching from); you'll need to heavily rely on your notes from these lectures

- assessment will be entirely project-based (originally 5 programming assignments, may need to be adjusted); projects can be done in singles or pairs; more details when i give the first assignment

- there will be a class slack (not made yet); all questions/discussions should be done there

- my office hours will be immediately after class on tuesdays plus by appointment (dm me on slack to set up a time)

- please ask questions during lecture! this lets me know that you're engaged with the material and gives me feedback about what i need to explain better

    + this material is mostly cumulative; if you fall behind it will snowball. it's up to you to not let that happen (for example, by stopping me and asking questions)

# intro: what is program analysis

- fundamental goal: compute invariants about program behavior, i.e., "what must definitely happen (or definitely not happen) when this program is executed"

- examples: say we have a program that sits on the server side of a web application; it accepts input from the user, maybe consults a database backend, then sends output back to the user. what questions might we want to ask about that program?

    + can user input make its way to the SQL commands being sent to the database without being sanitized?

    + does the sanitization function actually prevent all malicious input?

    + are these pieces of the program independent so that we can safely run them in parallel?

    + are these pieces of the program that are being run in parallel actually independent, or at least properly synchronized?

    + can the program possibly raise an uncaught exception (e.g., array out of bounds, or division by zero)?

    + given a specific line of the program (e.g., where a bug has manifested), what other parts of the program can affect the result at this line?

    + in languages like C: can this program exhibit undefined behavior? e.g., integer overflow or pointer arithmetic past the end of an object

- let's look at this goal from a 10,000 foot view

    + what is a program behavior? for now, let's define it as an execution trace [go through some behaviors of the following program]

        ```
          int x = input();
          int y = input();
          int diff;

          if (x < y) {
            diff = y - x;
          }
          else {
            diff = x - y;
          }

          output(diff);
        ```

    + [draw a circle] for a given program (like the one above), let this be the set of all possible true behaviors (give some examples of false behaviors too)

    + if we inspect every element of this set and find that something is true of all of them, then that is a program invariant. what are some invariants of the example program?

        + at the output, diff is non-negative
        + in the true branch, diff is positive

    + so: compute set of all possible true behaviors, inspect all the elements of the set to determine invariants...right?

    + the fly in the ointment: computing this set is undecidable, i.e., it's provably impossible to do what we want to do. of course, we don't give up---we do what we usually do when we need to solve an unsolvable problem, we approximate

- three strategies for approximating the ideal:

    + [draw bigger circle] over-approximate the set of possible behaviors. guarantee: all true behaviors are in this set, but it contains some false behaviors too (and we can't tell which are which)

        + if something is invariant over all members of the set, then it must be an invariant of all the true behaviors, and thus a true invariant

        + if something is _not_ invariant over all members of the set, then we don't know if it's a true invariant or not

        + we're getting a one-way guarantee: either something is definite a true invariant, or we don't know

        + example: suppose for the above program we have all the true behaviors, plus the false behaviors that have x == y but we take the true branch. then we can't derive the invariants that diff is positive in the true branch, but we can still derive the invariant that diff is non-negative at the output.

    + [draw smaller circle] under-approximate the set of possible behaviors. guarantee: all behaviors in this set are true behaviors, but not all true behaviors are in the set

        + if something is invariant over all members of the set, then we don't know if it's a true invariant or not

        + if something is _not_ invariant over all members of the set, then it is definitely not a true invariant

        + we're getting the opposite one-way guarantee: either something is definitely not an invariant, or we don't know

        + example: suppose for the above program that we only have behaviors for x, y \in [0--10]. then we can't say for sure that diff is always non-negative at the output, but we can say for certain that it isn't always positive (becase we have samples where it's 0).

    + [draw an off-set circle] neither over- nor under-approximate. no guarantees at all.

        + why do this? basically to make the analysis simpler and easier to implement and (hopefully) allow it to be more precise

        + for example, we could make some assumptions that technically aren't guaranteed to be true, but we happen to know that for almost all real programs will be true

        + this strategy is only useful for applications where we don't need correctness guarantees (e.g., bug-finding)

- all program analyses are trying to compute program invariants via some kind of approximation. the art of analysis design and implementation is to balance guarantees, precision, and performance

    + guarantees: in some cases we need to maintain a guarantee (either over- or under-approximation), which can make the analysis much more complex and expensive; example: compiler optimizations must have guarantees or they can break the program being compiled

    + precision: how close the approximation is to the real set of true behaviors. we can get arbitrarily close, but at the cost of getting arbitrarily more expensive. less precision is less expensive, but also less useful.

    + performance: if we get really precise answers but it takes centuries to get them, then practically speaking it isn't helpful.

    + so: we need to design and implement an analysis that gives us the desired guarantee, is precise enough to provide useful answers, and performant enough to give us the answers is a useful amount of time/resources. this is an extremely hard problem in general.

- there are many different approaches to program analysis (literally 1000s and 1000s of papers since the beginning of CS as a discipline); we'll be looking at two of the major and most common approaches: dataflow analysis (DFA) and set constraint-based analysis

    + there are other approaches (which you'll see if you look at the program analysis literature), but 90-odd percent of the program analyses used in the real world are one of these two

    + we'll be focusing specifically on over-approximations (also called _sound_ analysis)

# the language we're analyzing

- i want to show that this material is applicable to the real world, but i don't want to overwhelm you with the full complexities of a realistic PL

    + i also don't want to specialize things too much to a particular PL and/or infrastructure; this course is teaching the fundamentals of how to analyze _any_ language using _any_ infrastructure

- compromise: we'll analyze a language corresponding to a subset of C

    + datatypes:

        + integers (of any width, so chars, shorts, ints, longs, etc)
        + pointers
        + structs
        + [no floats, unsigned ints, unions]

    + control:

        + conditionals (if, ternary ?:)
        + loops (while, for, etc)
        + functions

    + require programs to be well-typed

    + notice that structs + pointers + functions (including function pointers) are the building blocks of object-oriented and functional programming languages

- in practice we usually don't analyze source code directly, but instead some intermediate representations (like in a compiler)

    + our IR is based on LLVM, but LLVM itself is more complex than we need

    + i've designed a "stripped-down" version of LLVM IR, which is what we'll use in this class

    + i'll provide a tool that uses LLVM to translate C programs (in our allowed subset) to our IR; you can also write IR programs directly

- [show example: analysis-impl/convert/testdata/test9.{c, nossa.ll, nossa.ir}

- i have written some basic infrastructure for this IR in C++: a parser to read IR programs from text files and a data structure to hold them

    + you can use my pre-written infrastructure if you want, but you do not have to (personally i think C++ is terrible and would prefer not to use it)

    + if you want to use a different language, then:

        + you are responsible for creating a parser and data structure for textual IR programs (not the translation from C/LLVM, you can use my tool for that)

        + whatever language you use has to be something that i can install in the autograder docker image

- [go over 260-public codebase]

- your task this week (not an official assignment, but highly recommended):

    + if you're going to use my code, then familiarize yourself with it and be sure that you can build the code and use it

    + if you're going to use something else, go ahead and implement the needed infrastructure now---don't wait for the first assignment. since this isn't an official assignment, as many people as want can work together and share the needed code

# basics of dataflow analysis (DFA)
## intro
### high-level idea

- we'll begin with the high-level idea of DFA, hand-wavy and without low-level details, just enough to give you some intuition about how it works. then we'll define some specific analyses in detail (the ones you'll implement for homework), and finally we'll go through the math behind DFA that makes it all work (i.e., guarantee it's computable).

    + we'll also limit ourselves for now to intraprocedural analysis (i.e., one function at a time) and treat function calls and pointers conservatively (i.e., we won't try to track information about them at all). treating pointers conservatively also means treating structs conservatively, since structs can only be accessed via pointers.

    + so this means: a single function, only tracking integer values and variables.

    + we'll lift these restrictions later and show how we can deal with each of these language features using DFA.

- a bit of history: kildall and original motivation to replace ad-hoc error-prone compiler analyses and optimizations with a general framework

- you'll notice that we're going to be doing a fair amount of handwaving in terms of soundness guarantees (i.e., relating the analysis to actual program behavior). that is characteristic of DFA; it's OK with just eye-balling correctness and doesn't usually care about actually proving correctness. that's one reason it's more popular than abstract interpretation amongst non-experts.

- very high-level idea:

    + replace concrete values (e.g., actual integers) with abstract values that over-approximate the concrete values (e.g., signedness)

    + replace concrete semantics (e.g., arithmetic on integers) with abstract semantics (e.g., arithmetic on signs)

    + "execute" program on abstract values to over-approximate concrete behaviors

### example: signedness analysis (i.e., 0/positive/negative)

- there are infinite possible concrete integers (assuming arbitrary precision integers); we'll approximate them using sets of integers corresponding to signedness

- abstract values:

    + 0: {0}
    + pos: {n | n > 0}
    + neg: {n | n < 0}
    + bottom (undefined): {}
    + top (any value): N

    + notice that these form a partial order based on subset, corresponding to the precision of the abstract value

    + also notice that we could have included abstract values for "non-negative" and "non-positive"; defining the set of abstract values is part of analysis design

- abstract semantics for addition:

    + 0 + 0 = 0
    + 0 + pos = pos
    + 0 + neg = neg
    + pos + pos = pos
    + pos + neg = top
    + neg + neg = neg
    + bottom + X = X
    + top + X = X (where X is not bottom)

- example program (just doing things by eye, no specific algorithm):

    ```
    int x := input(0, 100); <-- x is top
    if (x > 0) {            <-- x is pos
      y := x * x;           <-- y is pos
      z := 1 / y;           <-- z is top (because could be 0)
    }
    else {                  <-- x is 0
      y := x - x;           <-- y is 0
      z := 1 / y;           <-- division by 0
    }
    ```

- now let's get a little bit more in-depth (though still high-level)

## control-flow graph (CFG)
### basic idea

- DFA was originally defined for flowchart-style programs with explicit and obvious control-flow. thus, it is based on the idea of a 'control-flow graph' (CFG, which is confusing if you're also talking about grammars). a CFG is just a representation of a program's possible execution paths.

- [define CFG using examples; talk about basic blocks---a block will always begin with a label that can be jumped to and end in a terminator instruction (ret, jump, branch)]

### EXAMPLE 1

  ```
  int x = input(), y = 0, z = 0;
  if (x != 0) {
    x = 3;
    y = 2;
  } else {
    x = 2;
    y = 3;
  }
  z = x + y;
  return z;
  ```

  CFG (draw):

  ```
  bb1: x:int = $call input()
       y:int = $copy 0
       z:int = $copy 0
       tmp:int = $cmp neq x:int 0
       $branch tmp:int bb2 bb3

  bb2: x:int = $copy 3
       y:int = $copy 2
       $jump bb4

  bb3: x:int = $copy 2
       y:int = $copy 3
       $jump bb4

  bb4: z:int = $arith add x:int y:int
       $ret z:int
  ```

### EXAMPLE 2

  ```
  int x = input(), y = input(), z = input();
  while (z != 0) {
    x = x + 1;
    y = y + 2;
    z = x / y;
  }
  return x;
  ```

  CFG (draw):

  ```
  bb1: x:int = $call input()
       y:int = $call input()
       z:int = $call input()
       $jump bb2

  bb2: tmp:int = $cmp neq z:int 0
       $branch tmp:int bb3 bb4

  bb3: x:int = $arith add x:int 1
       y:int = $arith add y:int 2
       z:int = $arith div x:int y:int
       $jump bb2

  bb4: $ret x:int
  ```

### EXAMPLE 3

  ```
  int x = input(), y = input(), z = input();
  while (z != 0) {
    while (y <= x) {
      y = y + 2;
      z = x / y;
    }
    x = x + 1;
  }
  return x;
  ```

  CFG (draw):

  ```
  bb1: x:int = $call input()
       y:int = $call input()
       z:int = $call input()
       $jump bb2

  bb2: tmp1:int = $cmp neq z:int 0
       $branch tmp1:int bb3 bb6

  bb3: tmp2:int = $cmp lte y:int x:int
       $branch tmp2:int bb4 bb5

  bb4: y:int = $arith add y:int 2
       z:int = $arith div x:int y:int
       $jump bb3

  bb5: x:int = $arith add x:int 1
       $jump bb2

  bb6: $ret x:int
  ```

### wrap-up

- the usual assumption is that for each function there is a single entry basic block and a single exit basic block

    + if this isn't true (e.g., there are multiple return instructions) it's easy to massage the CFG to make it true (e.g., replace all the return instructions with jumps to a new basic block that contains a single return instruction).

- show IR example: analysis-impl/convert/testdata/test1.c, test1.nossa.ir; maybe show C program first and ask students to draw CFG, then show IR version

- notice that call instructions are not terminator instructions (they can appear in the middle of a basic block)

    + remember we're focusing for now on _intraprocedural analysis_, i.e., analyzing each function independently (and hence we make conservative assumptions about what calls can do); later on we'll look at _interprocedural analysis_ where the analysis crosses function boundaries.

    + while a call instruction does jump control flow, that control flow will always jump right back and so the central invariant of a basic block remains true: all instructions will be executed

## abstract domains

- RECAP: a DFA is an abstract domain + abstract semantics + abstract execution. we'll drill down on each of these in turn.

- an abstract domain is a set of abstract values that represent possible solutions to our analysis. in other words, our analysis will result in some answer that is contained in the abstract domain that we define.

- the main source of undecidability is infinite domains (e.g., the integer domain). this gives us an infinite space of behaviors. thus, we need to constrain the domain to be finite. at the same time, we need to over-approximate the actual behaviors. this means our finite 'abstract' domain must over-approximate the infinite 'concrete' domain. how?

- the usual method is to partition the infinite domain into a finite number of partitions. examples for the integer domain:

    + parity: N^e, N^o
    + signedness: N^+, 0, N^-
    + [draw these domains out, but not the full lattice just the integer partitions]

- note that the precision of the answer that we get from the analysis is bounded by the precision of the abstract domain we use. also note that different abstract domains can be non-comparable in terms of precision (as are parity and signedness).

    + there is a whole spectrum of possible abstract domains ranging from very imprecise but very fast to extremely precise but intractable, and many points in-between. there has been a lot of work in the field of program analysis designing many different abstract domains with different tradeoffs.

- what if we don't know which partition a value fits into? we also need abstract values that can represent unknown concrete values that don't fit into a single partition.

    + we need at least an abstract value to represent things that don't have any value yet (bottom) and one to represent things that could have any possible value (top)

    + we can also have domains that represent different levels of precision (e.g., the two possible signedness abstract domains)

    + [draw the full lattices for parity and signedness]

- notice that these different sets are related via subset, with more precise abstract values being subsets of less precise abstract values.

    + [show this for parity and signedness lattices]

    + sometimes we'll need to "merge" abstract values in order to find the most precise value that over-approximates the input values---we'll define this operation more precisely later, but in general it just means find the smallest superset that contains both inputs.

- we also need a way to map concrete values into the corresponding abstract values, and it can be useful mathematically to have the reverse mapping too:

    + alpha: Int -> Int#
    + gamma: Int# -> P(Int)

    + note that gamma(alpha(v)) should be a set that contains v; that's what makes the abstract domain an overapproximation. [example for signedness: gamma(alpha(1)) = N^+]

    + also note that gamma usually isn't useable in practice because it yields an infinite set. we'll see that it can still be useful later when proving properties of an analysis.

- later we'll see how we can relax the requirement for finite abstract domains while still remaining decidable. it will require some math to guarantee it will work correctly; we'll have to formalize this informal notion of abstract domain and give it some restrictions.

- in general we need to define abstract domains for every kind of concrete value that we're interested in (integers, pointers, etc).

## abstract transfer functions
### basic idea

- now that we're operating on abstract values instead of concrete values, we need to know how concrete operators map to abstract operators. when we see, e.g., `x + y` in a program, we're no longer operating on integers but on partitions of integers. the abstract versions of concrete operations are often called 'abstract transfer functions'.

- for integer abstractions the most relevant semantics to abstract are arithmetic and comparison; we show these by example for our example analyses of parity and sign.

### parity

- addition

    ```
        B | E | O | T
      +--------------
    B | B   B   B   B
    E | B   E   O   T
    O | B   O   E   T
    T | B   T   T   T
    ```

- less-than (assume result of comparison is either 0 [false] or 1 [true])

    ```
        B | E | O | T
      +--------------
    B | B   B   B   B
    E | B   T   T   T
    O | B   T   T   T
    T | B   T   T   T
    ```

### signedness

- addition

    ```
        B | + | 0 | - | T
      +------------------
    B | B   B   B   B   B
    + | B   +   +   T   T
    0 | B   +   0   -   T
    - | B   T   -   -   T
    T | B   T   T   T   T
    ```

- less-than (assume result of comparison is either 0 [false] or 1 [true])

    ```
        B | + | 0 | - | T
      +------------------
    B | B   B   B   B   B
    + | B   T   0   0   T
    0 | B   +   0   0   T
    - | B   +   +   T   T
    T | B   T   T   T   T
    ```

### wrap-up

- for both of these examples we can define the abstract transfer functions using tables because the abstract domains are finite; this isn't always the case (but again, to guarantee that infinite abstract domains still result in decidable analyses requires some math we'll get into later).

- we could combine these abstract domains if we want: an abstract value would be a pair `(parity, sign)`, with the obvious abstract semantics. this is generally true of all abstract domains, that they can be combined to get more precise domains.

- note that again we're being hand-wavy, we can't just make these transfer functions be anything we want and still ensure the analysis works---soon we'll need to formalize this notion of transfer function and give some restrictions to make sure they'll work properly.

## abstract execution (MOP vs MFP)
### intro

- given a CFG, abstract domains, and abstract transfer functions, how do we actually analyze a given program?

- DFA is inherently _flow-sensitive_: the goal is to compute an abstract value for each variable at each program point (i.e., each node of the CFG).

    + contrast with path-sensitive and flow-insensitive

### method 1: MOP

- trace through each execution path in turn, then for each program point find the most precise abstract value that contains all the abstract values computed at the program point.

    + [draw generic CFG example with two diamonds in a row, trace all four paths, then show merge at each basic block]

- at best exponential time complexity, at worst non-termination

- gives the best possible precision; serves as the "gold standard" for judging other methods

### method 2: MFP

- treat the transfer functions as a system of equations and compute a fixpoint (more particularly, the most precise fixpoint).

- fixpoint review:

    + define fixpoint of a function: an input that yields itself.

    + functions can have zero fixpoints, a single fixpoint, multiple but finite fixpoints, or infinite fixpoints.

    + example: f(x) = x²: fixpoints are 0, 1; least fixpoint is 0

- so for MFP, we want to assign abstract values to each variable at each program point s.t. if we execute the abstract semantics on the program, we get the results we started with.

    + and specifically, we want the most precise abstract values for each variable that makes this true, i.e., the 'least fixpoint'

    + intuitively, this approach will merge the abstract values along different paths _before_ propagating them further, rather than keeping each path distinct and only merging them at the end.

    + [take same example as for MOP and show the difference with MFP]

- polynomial time complexity (assuming abstract domain and transfer functions have the correct properties)

- [give the worklist algorithm for computing a fixpoint]

      ```
      GIVEN: CFG as a set of basic blocks

      create a map from each block to an empty abstract store
      initialize worklist with entry block

      while worklist isn't empty:
        pop block from worklist
        execute block using abstract semantics (drawing values from abstract store)
        if terminal instruction is a jump or branch:
          take final abstract store and merge it with abstract store for beginning of target blocks
          if the abstract store for target block has changed, add target block to worklist
      ```

- remember that fixpoints don't necessarily exist (and if they do there isn't necessarily a least fixpoint); later we'll worry about how to guarantee a least fixpoint exists and that we can find it (you guessed it: we need math)

### EXAMPLE 1

- [do parity analysis; have students do signedness analysis as exercise]

  ```
    int x = input(), y = -2, z = 0;
    while (x < 10) {
      x = x + 1;
      y = y - 2;
      z = z + 1;
    }
    z = y + z;
    return z;
  ```

  CFG (draw):

  ```
  bb1: x:int = $call input()
       y:int = $copy -2
       z:int = $copy 0
       $jump bb2

  bb2: tmp:int = $cmp lt x:int 10
       $branch tmp:int bb3 bb4

  bb3: x:int = $arith add x:int 1
       y:int = $arith sub y:int 2
       z:int = $arith add z:int 1
       $jump bb2

  bb4: z:int = $arith add y:int z:int
       $ret z:int
  ```

### EXAMPLE 2

- to get an intuitive understanding of the difference between MOP and MFP, consider the following example:

  ```
  int x = 0, y = input(), z = input();
  if (y != 0) { x = 1; }
  else { x = 2; }
  if (z != 0) { x = x + x; }
  else { x = x - x; }
  x = x + x;
  return x;
  ```

  CFG (draw):

  ```
  bb1: x:int = $copy 0
       y:int = $call input()
       z:int = $call input()
       tmp1:int = $cmp neq y:int 0
       $branch tmp1:int bb2 bb3

  bb2: x:int = $copy 1
       $jump bb4

  bb3: x:int = $copy 2
       $jump bb4

  bb4: tmp2:int = $cmp neq z:int 0
       $branch tmp2:int bb5 bb6

  bb5: x:int = $arith add x:int x:int
       $jump bb7

  bb6: x:int = $arith sub x:int x:int
       $jump bb7

  bb7: x:int = $arith add x:int x:int
       $ret x:int
  ```

- [show MOP paths (exponential) then MFP paths (linear), then show that using parity domain MOP is more precise than MFP]

- an analysis is _distributive_ if, for transfer function F, merge(F(x), F(y)) = F(merge(x, y))

    + in other words, it doesn't matter if we merge abstract values before applying the transfer function or afterwards.

    + for distributive analyses, MOP = MFP; for non-distributive domains, MOP <= MFP

# defining the full signedness analysis
## abstract domain

- the signedness domain we've been using (BOT, TOP, 0, POS, NEG); the abstract store will map from variables to their abstract value.

## abstract transfer functions

- [note that there are ways to be more precise, e.g., (1) for store, merge op's abstract value with address-taken variables instead of just using TOP; (2) for store and icall, tracking which variables have actually been address-taken by that point in the program and only setting those; (3) tracking the definedness of pointers for store, load, icall, and [i]call arguments and ignoring ones that are BOT; etc. these all revolve around being more precise about pointers, which we'll actually handle by doing pointer analysis coming up.]

- [for all instructions with lhs, ignore them if the type of lhs is not integer; for $store ignore it if the type of the operand is not integer]

- `lhs = $arith <operation> op1 op2`

    + translate op1, op2 to abstract values (using alpha or abstract store)
    + apply abstract transfer function
    + update store to map lhs to result

- `lhs = $cmp <relation> op1 op2`

    + if the operands are not integers, set lhs to T
    + otherwise same as $arith

- `lhs = $phi(ops...)`

    + merge abstract values of all arguments to the phi function
    + update store to map lhs to result

- `lhs = $copy op`

    + update store to map lhs to abstract value of op

- `lhs = $load src_ptr`

    + update store to map lhs to TOP

- `$store dst_ptr op`

    + update store to map all integer-typed address-taken variables to TOP

- `lhs = $select op1 op2 op3`

    + if op1 is BOT, ignore instruction
    + if op1 is + or -, update store to map lhs to abstract value of op2
    + if op1 is 0, update store to map lhs to abstract value of op3
    + if op1 is TOP, update store to map lhs to merge of op2 and op3 abstract values

- `lhs = $call <function>(ops...)`

    + if there is any argument that is a pointer (directly or indirectly) to an integer-type, update store to map all integer-typed address-taken variables to TOP
    + if lhs is integer-typed, update store to set lhs to TOP

- `lhs = $icall <pointer>(ops...)`

    + same as $call

- `$jump <label>`

    + propagate abstract store to <label>

- `$branch op <label1> <label2>`

    + if abstract value of op includes + or -, propagate abstract store to <label1>
    + if abstract value of op includes 0, propagate abstract store to <label2>
    + note that depending on the abstract value of op, we may propagate the abstract store only to <label1>, only to <label2>, or to both

- we can ignore:

    + `$ret` (because it doesn't do anything in an intraprocedural analysis)

    + `$addrof`, `$alloc`, `$gep` (because they only operate on pointers and we're ignoring pointers)

## MFP

- if you're using my C++ infrastructure, you'll want to use VarPtr_t to reference variables and BbPtr_t to reference basic blocks.

- use the standard worklist algorithm:

    + initialize empty abstract store for beginning of entry block (if looking up a variable that doesn't currently exist in the store, assume the value is BOT)

    + initialize worklist with entry basic block

    + merging two stores: merge the values for each variable in the two stores (again, if a variable doesn't exist assume its value is BOT)

- note that for the initial abstract stores of entry blocks for functions with integer parameters, those parameters should have the value TOP (we ignore non-integer parameters).

# second-order DFA analyses
## basic idea

- we've been looking at so-called 'first-order analysis': analyses where we care about _values_ at a particular point in the program (i.e., CFG node). a 'second-order analysis' asks questions about program _paths_ (sequence of execution states), not individual program states.

- EXAMPLE: a taint analysis might ask 'how did this string get to this sensitive system call? in particular, did it get here from user input without being sanitized?' this isn't a question we can answer just by looking at an abstract value at a particular CFG node; we have to know the execution history that brought the value to that node.

- how do we answer these questions? by abstracting program _traces_ just like we abstract program _values_

## EXAMPLE: reaching definitions

- problem statement: for each program point, for each variable, what are the set of defs (i.e., declaration or assignments) of that variable that may "reach" this program point? "reach" means there is some path from the def to the program point that does not definitely redefine that variable

- this information allows us to trace how values flow through the program and is a useful foundation for more elaborate analyses.

- notice that this is a "may" analysis: we're asking what's possibly true (i.e., not definitely false).

### example program

  ```
  int x, y, z = input();
  x = 4;
  y = 6;
  if (z) { x = 2; } else { x = 3; y = 3; }
  z := x+y;
  return z;
  ```

  CFG (draw):

  ```
  bb1: z:int = $call input()
       x:int = $copy 4
       y:int = $copy 6
       tmp:int = $cmp neq z:int 0
       $branch tmp:int if_true if_false

  if_true:
       x:int = $copy 2
       $jump if_end

  if_false:
      x:int = $copy 3
      y:int = $copy 3
      $jump if_end

  if_end:
      z:int = $arith add x:int y:int
      $ret z:int
  ```

### abstract domain

- remember that our abstract domain is a set of abstract values that represent possible solutions to our analysis.

- we want abstract values that represent executation traces...specifically, traces from the beginning of the function to the current point following a path along which a particular variable has (1) been defined at program point X; and (2) has not been redefined at any point between X and now.

    + example: focusing on variable x in the example program and the point `if_end.0`, the abstract value `x -> {if_true.0, if_false.0}` abstracts the set of traces (starting from the beginning of the function) that define variable x with no intervening redefinitions before getting to `if_end.0`.

    + example: focusing on variable y in the example program and the point `if_end.0`, the abstract value `x -> {bb1.2, if_false.1}` abstracts the set of traces (starting from the beginning of the function) that define variable y with no intervening redefinitions before getting to `if_end.0`.

- therefore, an abstract value for some variable v is a set of program points containing definitions of v.

- remember that we need to create an abstract domain s.t. there is an abstract value for all possible concrete answers.

    + what is the least precise abstract value? the set of all program points

    + what is the most precise abstract value? the empty set

    + what is the entire abstract domain? the powerset of the set of all program points, ordered via subset

- L♯ = (P(Stmt), ⊆), ⊔ = ∪

    + the elements of the abstract domain are sets of program points

    + they are ordered via subset

    + we can merge abstract values using set union

### abstract transfer functions

- now we need to create abstract semantics to replace the program's concrete semantics. in this case, we don't care about the _values_ of the variables, just whether they are being defined or not by a given instruction. so we don't need abstract semantics for arithmetic operations, relational comparisons, etc, just for variable definition.

- given instruction `var = ...` at program point `k`, we need to update the abstract values for `var` at point `k` (again, we don't care what the instruction is doing to the values of the variables).

    + general formula: `F♯ = GEN ∪ (IN − KILL)`, where:

    ```
    [k] x = e ==> GEN(x) = {k}, KILL(x) = { pp | x defined at pp }
    else      ==> GEN = KILL = {}
    ```

- what about instructions that could indirectly define a variable, e.g., `$store`? since we don't track pointer information, we treat them conservatively:

    + if we're storing a value of type `T`, then the store could potentially define any _address-taken_ variable of type `T` and so we have to include this program point in the abstract value for that variable.

    + however, it may _not_ be defining that variable, so we also can't remove any existing program points in the abstract value for that variable.

    + this is called a `weak update`, as opposed to our usual `strong updates`; a strong update _replaces_ the old abstract value with a new abstract value (like concrete assignment) while a weak updated _merges_ the old abstract value with a new abstract value.

    + example:

    ```
    bb1:
      z:int* = addrof x:int

    [...more program...]

    bb2:
      x:int = $copy 42
      w:int = $copy 34
      $store y:int* 12
      $ret x:int
    ```

    + what should the abstract value at the return instruction be for variable x? `{bb2.0, bb2.2}`

    + what about for variable w? `{bb2.1}` (_not_ including `bb2.2` because w isn't address-taken)

- a similar reasoning for any `$call` or `$icall` that passes a pointer as an argument---since we don't know what that pointer refers to, and since we don't know what the callee will do with it, we have to assume that it _could_ define any address-taken variable of the appropriate type (not just the type of the pointer itself, but any type reachable from the pointer)

    + example:

    ```
    bb:
      x:int = $copy 0
      y:int* = $addrof x:int
      z:int** = $addrof y:int*
      w:int = $call foo(z:int**)
      $ret x:int
    ```

    + what is the abstract value for variable x at the return instruction? `{bb.0, bb.3}`

### MFP

- now we use the same worklist algorithm as before to compute the analysis solution.

- [go through example program]

- [use a solution in addition to the abstract store the same as the actual assignment]

# defining the full reaching defs analysis
### abstract domain

- abstract store: for each variable, a set of program points (`label.index`)

    + if using my C++ infrastructure: i suggest a map from VarPtr_t to set of InstPtr_t where an InstPtr_t is an alias for `const ir::Instruction*`.

    + if using my C++ infrastructure, defs should be represented as InstPtr_t; for external defs just use nullptr.

        - note that parameters and objects reachable from parameters are defined outside the current function (more discussion below); in our analysis we will consolidate all such objects and just say `external-def` in the printed solution.

- we'll represent other relevant objects using VarPtr_t as well (we didn't have to worry about this for sign analysis because there we only cared about values of variables; here we care about any def/use of anything):

    + $alloc'd objects (create a VarPtr_t for each $alloc instruction, with the name "alloc")

    + struct fields (create a VarPtr_t for each field type [not individual field], with the name "field). for example, there would be one VarPtr_t for all fields of type Int.

    + all of these 'fake' VarPtr_t will count as addressable objects, along with any variables that are operands of a $addrof instruction.

- the actual solution will be a map from each instruction that uses some variables to the set of reaching defs for those variables.

    + if using my C++ infrastructure: i suggest a map from InstPtr_t to set of InstPtr_t

### abstract transfer functions

- $arith, $cmp, $copy, $phi, $alloc, $gep, $select, $addrof, $ret:

    + let DEF be the lhs of the instruction (if there is one) and USES be all VarPtr_t used by the instruction (note that $addrof doesn't use its argument)

    + for each use in USES: soln[inst] += store[use]

    + store[DEF] = {inst}

- `lhs = $load op`:

    + let DEF be the lhs of the instruction; USES contains the VarPtr_t operand and also all type-appropriate addressable objects, and also if there are any pointer-type function parameters of the appropriate type then USES contains nullptr to signify an external object.

    + for each use in USES: soln[inst] += store[use]

    + store[DEF] = {inst}

- `$store dst_ptr op`:

    + let DEFS contain all type-appropriate addressable objects and USES contain the VarPtr_t operands of the instruction.

    + for each use in USES: soln[inst] += store[use]

    + for each def in DEFS: store[def] += {inst}

- `lhs = $[i]call <func/ptr>(args...)`:

    + let STRONG_DEF contain the lhs of the instruction

    + if there are no pointer-type arguments then WEAK_DEFS is empty, otherwise it contains all type-appropriate addressable objects (i.e., any type reachable from the pointer argument)

    + USES contains all VarPtr_t arguments and, if this is an $icall, the function ptr being called and, if any arguments are pointer-typed: all type-appropriate addressable objects (the same set as for WEAK_DEFS) and if there are any pointer-type function parameters then nullptr to signify an external object.

    + for each use in USES: soln[inst] += store[use]

    + store[STRONG_DEF] = {inst}

    + for each def in WEAK_DEFS: store[def] += {inst}. note that we're using the call instruction as a proxy for any defs that happen inside the callee (that's why we don't use `external-def` for these).

- $jump, $branch:

    + propagate store to target block(s); if the abstract store for the block changes then add to worklist

### MFP

- same worklist algorithm as for sign analysis

- All parameters are defined before entering the function, so the abstract store at the  beginning of the function should map all parameters to `external-def`.

# the math behind DFA
## preliminaries

- we've been really hand-wavy so far. how can we guarantee that the analysis is decidable (i.e., terminates)? and can we be more generous with how precise the abstract domains are (e.g., can we allow infinite domains safely)?

- to make guarantees about computability we can't allow arbitrary abstract domains; instead we need to impose an algebraic structure to them that will allow us to prove computability.

- we'll go into a bunch of definitions and theorems in abstract algebra, and then afterwards connect it back to abstract domains and computability.

- these definitions are about _ordering_: given a set S, an ordering is a binary relation on that set which meets certain requirements.

    + examples of well-known orderings: (ℤ, ≤), (Strings, ≤).

    + we often use the notation ⊑ for an arbitrary ordering.

## definitions
### BINARY RELATION

- a binary relation on set S ⊆ P(S × S)

- example: ({1, 2, 3, 6}, ⊑) where ⊑ means 'divides evenly'. the binary relation ⊑ is {(1,1), (1,2), (1,3), (1,6), (2,2), (2,6), (3,3), (3,6), (6,6)}

- instead of saying '(1,3) ∈ ⊑' we usually say '1 ⊑ 3'

- [review reflexive, transitive, symmetric, anti-symmetric]

### PREORDER

- a binary relation that is _reflexive_ and _transitive_

- example: reachability relation on a directed graph (note that it can contain cycles)

- counterexample: "person X likes person Y"

    + a person may not like themselves (not reflexive) and if X likes Y and Y likes Z, X does not necessarily like Z (not transitive)

- recall that a preorder that is also symmetric is an _equivalence relation_

    + (ℤ, =)

    + "has same birthday as"

### PARTIAL ORDER aka POSET

- a preorder that is _anti-symmetric_

- examples:

    + reachability on a DAG (anti-symmetric because no cycles)
    + ℕ and divisibility: (ℕ, |)
    + P(S) and ⊆

- counterexample:

    + (ℤ, |) (not anti-symmetric, e.g., 2 | -2 and -2 | 2)

- we can visualize partial orders as Hasse diagrams:

    + example: P({1, 2, 3}), ⊆

    + example: ({1, 2, 3, 5, 6, 10, 15, 30}, "divides into")

- a partial order can be a _total order_, i.e., a partial order s.t. for any two elements x and y, either x ⊑ y or y ⊑ x

    + examples: (ℤ, ≤), (Strings, ≤)

    + counterexample: the partial order examples given above

### LEAST UPPER BOUND, aka JOIN, aka ⊔

- for D ⊆ S, the least x ∈ S s.t. ∀d ∈ D, d ⊑ x.

- denoted ⊔D or (if D = {x, y}) x ⊔ y

- examples: use posets from above

### GREATEST LOWER BOUND, aka MEET, aka ⊓

- the dual of ⊔

- examples: taken from the above partial and total orders

- note that ⊔ and ⊓ are not guaranteed to exist

    + DAG reachability [e.g., isolated nodes]

    + (ℤ, ≤), join(D) where D = the set of even integers [no least integer greater than all even integers]

### CHAIN

- a totally ordered subset of a partial order

- example: taken from previous examples of partial orders

### LATTICE

- a partial order s.t. any two elements are guaranteed to have a ⊔ and ⊓

- examples:

    + (ℕ, |) [BOT = 1, TOP = 0]

        - join(x, y) == least common multiple
        - meet(x, y) == greatest common divisor [BOT = 1]

    + (P({1, 2, 3}), ⊆) [BOT = {}, TOP = {1,2,3}]

        - join = union
        - meet = intersection

- QUIZ: lattice or not? draw Hasse diagram.

  1. ({1, 2, 3}, |)             [NO]
  2. ({1, 2, 3, 6}, |)          [YES]
  3. ({1, 2, 3, 12, 18, 36}, |) [NO]
  4. (ℕ, ≤)                     [YES]

### NOETHERIAN LATTICE

- a lattice s.t. the length of any ascending chain must be finite.

    + aka 'meets the finite ascending chain condition'

- examples:

    + any finite lattice
    + (ℕ, >=) [TOP is 0]

- counterexample: (ℕ, ≤) [BOT is 0]

### COMPLETE LATTICE

- a lattice S s.t. every subset has a ⊔ and ⊓

    + contrast with a regular lattice which only requires that any two elements have a ⊔ and ⊓

    + notice this implies there is a single greatest element (⊔S) and a single least element (⊓S)

- examples:

    + any finite lattice

        - just take join/meet of each element in turn

    + (P(ℕ), ⊆)

        - join = union
        - meet = intersection

    + (ℕ, |) [see example from LATTICE section for details]

- counterexamples:

    + ({ S ∈ P(ℕ) | S is finite }, ⊆)

        - no join/meet for infinite set

    + (ℕ, ≤)

        - meet for infinite set, but no join

### SUMMARY OF RELATIONS

```
binary relation
  --> preorder
    --> equivalence relation
    --> partial order (includes total order)
      --> lattice (may or may not be noetherian)
        --> complete lattice (also may or may not be noetherian)
```

## relation to program analysis

- we will require our abstract domains to be (complete) lattices with a partial order based on precision (more advanced versions can use less retrictive structures, but we'll stick with lattices)

  + examples: +/0/- (more than one possibility); even/odd

  + example: tainted strings to track user input

- so when we've said "the most precise abstract value", what we mean is the lowest possible element of the lattice that still over-approximates the given concrete values.

- note that traditionally DFA actually flips the lattices, so the most precise element is on the top and the least precise is on the bottom; a.i. does it like we're doing it here. i'm keeping it consistent to make our lives easier, but it can be confusing when reading both the dfa and a.i. literature.

- why is all this math useful? because we can formally define when the MFP exists and is computable.

## computability
### FIXPOINTS

- recall:

    + a fixpoint of F is an input x s.t. F(x) = x

    + lfp_x F = u s.t. x ⊑ u, F(u) = u, ∀y. F(y) = y ⇒ u ⊑ y

    + pre-fixpoint: x ⊑ F(x)

    + post-fixpoint: F(x) ⊑ x

- notice that these last three definitions only make sense for an ordered set (and now we know exactly what that means).

- note that a function may have 0—∞ fixpoints, and just because there's more than one _doesn't_ mean that there's a least one.

    + f(x) = x + 2  [0]

    + f(x) = x² - x [2]

    + f(x) = x² ÷ x [∞]

- our worklist algorithm for computing the MFP is trying to compute this, but how can we be sure that it exists and is computable?

### MONOTONICITY

- function F is monotone iff x ⊑ y ⇒ f(x) ⊑ f(y), or equivalently f(x) ⊔ f(y) ⊑ f(x ⊔ y)

- [SKIP] PROOF (1st direction; 2nd direction left as exercise):

    1. f(x) ⊔ f(y) ⊑ f(x ⊔ y) [given]
    2. x ⊑ y ⇒ x ⊔ y = y [by def.]
    3. x ⊑ y ⇒ f(x) ⊔ f(y) ⊑ f(y) [by 1, 2]
    4. f(x) ⊑ f(x) ⊔ f(y) [by def.]
    5. x ⊑ y ⇒ f(x) ⊑ f(y) [by 3,4]

- example: f(x) = x - 1

- counterexample: f(x) = x+2 if x ≤ 0, x else

- an _extensive_ function is one for which x ⊑ F(x). note that monotone and extensive are orthogonal properties; people often confuse them.

### FIXPOINT THEOREMS

- TARSKI: let L be a complete lattice and F:L→L be monotone; then the
  set of fixpoints of F is a complete lattice (and thus there exists a
  least fixpoint). however, it doesn't tell us how to find the least
  fixpoint.

- KLEENE: let L be a complete _noetherian_ lattice and F:L→L be monotone
  and x be a pre-fixpoint of F; then ∃n ≥ 0 s.t. Fⁿ(x) is stationary
  and lfp_x F = Fⁿ(x)

    + KLEENE ITERATIONS: ⊥ ⊑ F(⊥) ⊑ F(F(⊥)) ⊑ ... ⊑ Fⁿ(⊥) ⊑ ...

    + it turns out that this is what we're doing when we use our worklist algorithm (though in a rather optimized way).

### ABSTRACT SEMANTICS

- these theorems tell us that if (1) our abstract domains are noetherian lattices, and (2) our transfer functions are monotone, then the least fixpoint exists and is computable, and our worklist algorithm will compute it.

    + note that the worklist is an optimization of kleene iterations that avoids computing transfer functions that are guaranteed to have not changed their values.

    + give the generic DFA functions:

      ```
      IN_k = ⊔ { OUT_i | i→k ∈ CFG }
      OUT_k = F♯(IN_k)
      ```

### EXAMPLES

- show for the following integer abstract domains that they are noetherian lattices and the appropriate transfer functions are monotone:

#### signedness {-, 0, +}

-  [show lattice, abstract +]

- monotone if: a ⊑ a' and b ⊑ b' => a + b ⊑ a' + b'

    + case 1: a' or b' = ⊥. then a or b = ⊥, a + b = ⊥, a' + b' = ⊥, thus a + b = a' + b'.

    + case 2: a' or b' = ⊤. then a' + b' = ⊤, thus a + b ⊑ a' + b'.

    + case 3: a' \in {+,0,-}, b' \in {+,0,-}, a,b != ⊥. then a = a', b = b', thus a + b = a' + b'.

#### constant lattice

- note that this lattice is infinite

- [show abstract +, sketch monotonicity proof]

#### [SKIP] parity

- [generalize to modular arithmetic?]

#### [SKIP] finite-size interval lattice:

                    [-∞, ∞]
                   /   |   \
            [-∞, -1]   |    [0, ∞]
            /          |         \
           /        [-9, 9]       \
          /         /     \        \
    [-∞, -10]  [-9, -1] [0, 9] [10, ∞]
            \         | |      /
             ---------\ /------
                       ⊥

- however, note that the general integer interval lattice is infinite but _not_ noetherian, and thus cannot be used with DFA. there are techniques that would allow us to use non-noetherian lattices safely; these are the realm of abstract interpretation rather than DFA.

# set constraint-based analysis
## intro

- we're going to use the problem of pointers to explore a totally different method for program analysis: set constraint-based analysis.

- we've had to treat pointers conservatively, and this has really hurt precision. pointers allow indirect data- and control-flow: we can't tell syntactically what is being used/defined by an instruction or where control is going (e.g., function pointers). if we want useful, non-trivial analysis information we really need to deal with pointers somehow.

- in theory we can use DFA to analyze pointer information, using our standard formula: abstract domain, abstract transfer functions, abstract execution. in practice, it turns out that the domain is large enough and transfer functions complex enough that this isn't really scalable (we'd be lucky to be able to analyze programs with just 10,000 LOC).

    + it's possible to make it more scalable (part of my phd dissertation was about how to make DFA-based pointer analysis two orders of magnitude more scalable), but (1) it isn't commonly used because it's still expensive; and (2) doing so is complex and requires set constraint-based analysis as a sub-procedure anyway.

- to be clear, set constraint-based analysis isn't specifically for pointer analysis, it can be used for many different things (just like DFA). we're going to be using pointers as the motivating example because it turns out that it's probably the most common technique for doing pointer analysis in current use.

- recall that DFA gives us a `flow-sensitive` analysis (one solution per program point); set constraint-based analysis is going to give us a `flow-insensitive` analysis (one solution over the entire program).

    + a flow-sensitive sign analysis: at this program point `x` is ZERO, at this other program point `x` is POS.

    + a flow-insensitive sign analysis: over the entire program, `x` is NON-NEG (or TOP if we're using the simple sign abstract domain as in assignment 1).

- flow-insensitive analysis is clearly less precise because it can't give different answers per program point but instead must give a single answer that is sound over the entire program. the tradeoff is that it is usually must more scalable than flow-sensitive analysis.

- we'll begin by talking about set constraint-based analysis in general, then show how to apply it to pointer analysis.

## general set constraint language

- language of set constraints:

  ```
  0 = empty set
  1 = universal set

  X ∈ Variable
  c ∈ Constructor

  E ∈ Expr ::= X | 0 | 1 | ¬E | E1 ∪ E2 | E1 ∩ E2 | c(E1,...,En) | c^-i(E)
  S ∈ Stmt ::= E1 ⊆ E2 | S1 ∧ S2
  ```

- what are constructors? think of them as _uninterpreted functions_ that take sets and return sets. the analysis doesn't care what they mean; it's up to us to give them a meaningful interpretation when we're defining an analysis.

    + example: type constructors

        - nullary constructors: int, string, bool
        - unary constructors: Set[·], List[·]
        - binary constructors: ·→·, Map[·,·]

    + characteristics:

        - _arity_: the number of arguments it takes
        - _variance_: each argument position is either:

            + _covariant_ (the constructor is monotone wrt that position)
            + _contravariant_ (it is antitone wrt that position)

- variance is the same as type variance if you've dealt with that before. basically, consider a constructor `foo` with arity 1. let's consider two cases, one where `foo`'s argument is covariant and one where it's contravariant:

    + `foo's argument is covariant`: then `foo(A) ⊆ foo(B)` iff `A ⊆ B`. notice that the direction of the subset is preserved.

    + `foo's argument is contravariant`: then `foo(A) ⊆ foo(B)` iff `B ⊆ A`. notice that the direction of the subset is reversed.

    + we need to have this information specified because remember that the constructors are essentially uninterpreted functions---what they really _mean_ is up to us when we design our analyses. we need to explicitly specify this piece of information to allow the set constraints to be correctly solved wrt what the constructors really stand for.

    + for now we'll just assume everything is covariant; we'll need contravariant things later but we'll talk more about it then.

- what does projection do? take the constructors denoted by the given expression, filter out the ones that don't match the given constructor, take the i'th argument of each remaining constructor, and put all of them in a set.

    + we'll number positions starting from 0.

    + example projection: suppose that

      ```
      X -> { a(b, C), a(D, e), f(g), h(i, j, k) }
      C -> { l, m }
      D -> { n, o, p }
      ```

    + then `a^-1(X) = { e, l, m }`

- example set constraint system:

  ```
        a ⊆ X        ∧
        a ⊆ Y ∪ X    ∧
        b ⊆ Y ∪ X    ∧
        c ⊆ Y ∪ X    ∧
    X ∩ Y ⊆ b        ∧
        Z ⊆ ¬(X ∩ Y)
  ```

    + X, Y, Z are set variables.

    + a, b, c are _nullary constructors_ (aka constants, usually written without parentheses).

- what is a solution to a set constraint system?

    + let H = the set of all constructor calls

    + a solution is an assignment σ ∈ Variable → P(H) that maps each set variable to a set of constructor calls s.t. all constraints are satisfied.

- for the above example, a valid solution would be:

  ```
  X -> { a, b }
  Y -> { b, c }
  Z -> { a, c }
  ```

- unfortunately, finding solutions for the general set constraint language is undecidable. therefore we can only use fragments of that language, restricted to ensure decidability. the complexity of solving set constraints is determined by what restrictions we put on the language (not by the particular analysis that we're doing).

## restricted set constraint language

- one possible restricted language is the _inclusion constraint language_:

  ```
  X ∈ Variable
  c ∈ Constructor

  T ∈ Term ::= X | c(T1,...,Tn)
  E ∈ Expr ::= T | c^-i(X)
  S ∈ Stmt ::= E1 ⊆ E2 | S1 ∧ S2
  ```

- where E1 and E2 cannot both be projections. also note that projections cannot be call arguments according to the grammar. these rules don't actually restrict the language at all---it's simple to put the result of a projection in a set variable and use that set variable as the call argument or as one side of a constraint with another projection. The rules just make implementing a solver for the constraint language simpler, with fewer cases to worry about.

- this is not the only possible restricted language that yields a decidable analysis, but this one has two nice properties:

    1. it is guaranteed to have a minimal solution
    2. it is guaranteed to have cubic complexity

- analyses defined using this particular restricted set constraint language are called _inclusion-based analyses_.

    + as a sidenote, another common class of analyses is _equality-based analyses_, which uses the same language except with `E1 = E2` instead of `E1 ⊆ E2`. this is even less precise than inclusion-based, but can run in near-linear time complexity. an equality-based pointer analysis is also often called _steensgaard-style_ pointer analysis, after the person who first described it. we probably won't have time to go into any more detail about this class of analyses, unfortunately.

## solving inclusion constraints
### representing constraints as a graph

- it will be convenient for solving the constraints to represent them as a graph.

    + a node for each set variable, projection, and constructor call
    + an edge for each constraint from the lhs to the rhs

- edges can be represented in many ways; we'll use two kinds:

    + successor edges: the edge is stored in the source node
    + predecessor edges: the edge is stored in the destination node

- the rules are:

    + any edge from a constructor call is a predecessor edge
    + any edge to a projection is a predecessor edge
    + all other edges are successor edges
    + [we disallow edges between projections]

- why we do this will become evident when we show how to solve the constraints.

- example:

  ```
  f(X,Y) ⊆ A
  A ⊆ B
  B ⊆ C
  C ⊆ E
  g ⊆ D
  h ⊆ D
  h ⊆ f^-1(B)
  D ⊆ f^-0(C)
  f^-0(C) ⊆ E
  f^-1(B) ⊆ F
  ```

  ```
  f(X,Y) --> A ==> B ==> C
                          \\
  g --> D --> f^-0(C) =======> E
       /
  h --<
       \
        f^-1(B) ==> F
  ```

### solving the constraint graph

- solving the constraints corresponds to computing the dynamic transitive closure of the graph. the predecessor edges from the constructor calls to the set variables are the solution to the constraints.

    + remember that a solution maps set variables to sets of constructors, so we map each set variable to the set of constructors that have a predecessor edge to that set variable.

- [solve above example]

  ```
  A -> { f(X,Y) }
  B -> { f(X,Y) }
  C -> { f(X,Y) }
  D -> { g, h }
  E -> { f(X,Y), g, h }
  F -> { h }
  X -> { g, h }
  Y -> { h }
  ```

- initialize worklist with all variables that have a predecessor edge (i.e., all variables that currently have something in their solution set). the worklist will only contain set variables.

- perform the usual worklist algorithm. when popping set variable X:

    1. propagate X's predecessor edges along all of X's successor edges. if the predecessor edges of the destination node changed, put it onto the worklist.

    2. if X has any projections, process each projection node:

        + compute the given projection of X (which can be different for each projection node) to get a set of variables and/or constructor calls; call that set Y.

        + for each y_i ∈ Y and predecessor p_i and successor s_i of the projection node, add edges p_i -> y_i -> s_i (using the standard rules for pred vs succ edges).

        + if a node has a new pred or succ edge added to it, add that node to the worklist.

- what if we add an edge between two constructors? e.g., `c(T1,...,Tn) ⊆ c(T1',...,Tn')`. this is where knowing the variance of each constructor argument position matters:

    + `c(T1,...,Tn) ⊆ c(T1',...,Tn')` =>

        - Ti  ⊆ Ti' if position i of constructor c is covariant
        - Ti' ⊆ Ti  if position i of constructor c is contravariant

    + what if the constructors don't match? technically this is an error, but in a practical implementation we usually just ignore mismatched constructors.

- when the worklist is empty, we've computed a solution to the original constraints: for each set variable, look at its predecessor edges to get its solution.

### implementing the constraint graph and solver

- define the following classes:

    + `Constructor`: containing name, arity, and variance info for that constructor

    + `SetVariable`: containing name, list of predecessors (e.g., a set of nodes), and list of successors (e.g., another set of nodes)

    + `ConstructorCall`: containing a reference to the Constructor and a list of arguments (e.g., a vector of nodes)

    + `Projection`: containing a reference to the SetVariable the projection is being done on, a reference to the Constructor being projected, the position of the Constructor argument being projected, the list of predecessors, and the list of successors

- SetVariables, ConstructorCalls, and Projections are the nodes of the constraint graph, and the lists of predecessors and successors are the edges of the constraint graph.

- note that you need a way for the user of the constraint engine to register constructors, set variables, constructor calls, and projections. generally the easiest way to do this is to have the register function take all the relevant information (_except_ for edges) and return some handle to the created constructor or node (e.g., a pointer or an index into a vector); then there is a separate `add_edge` function that takes two handles and adds an edge between them (either predecessor or successor depending on what kind of nodes they are).

## solver optimization

- cubic time complexity is still not great; we can't make the complexity go down but we can still optimize the constraint solver to make it much faster in practice (e.g., reduce the n in O(n^3)).

- there are a variety of known optimizations; we won't spend time on them here but there are a number of relevant papers that describe them (including some of mine).

## defining a program analysis

- there are two steps for defining an analysis using set constraints:

    1. define the universe of discourse, i.e., the set variables and the set of constructors

    2. define constraint generation, mapping a program to a set of constraints whose solution will be the solution to the analysis

- notice that we don't need to describe how to solve the analysis, because no matter what the analysis is they're always inclusion constraints and can be solved using the method we described earlier. that is, the only new thing we need to do to define an analysis is the two points above; we can always use the same solver for all analyses.

- this process is easiest to explain by example; the example we'll be using henceforth is pointer analysis.

# andersen-style pointer analysis
## intro

- recall that pointer analysis is computing an overapproximation of program behavior wrt pointer; i.e., for a given pointer access (load, store, etc) what object might be accessed?

- andersen-style pointer analysis is just pointer analysis defined using the inclusion set constraint language we defined earlier.

    + this is perhaps the most common kind of pointer analysis in regular use.

- we need an abstraction for memory (which is conceptually infinite); that is, instead of (for example) abstract values that over-approximate integers we need abstract values that over-approximate memory addresses. a common choice is _static allocation site_.

    + we'll create a fake 'variable' for each instruction where memory is allocated (aka static allocation site) and it will represent all concrete memory allocated by that instruction.

    + example: for `x:int* = $alloc` the object returned by the $alloc will be represented with a variable named `alloc.<function name>.<basic block label>.<index>`.

    + note that a single such abstract value can actually represent an unbounded number of concrete values (e.g., if the instruction is located inside a loop); this is why it's called a _static_ allocation site.

    + there are other abstractions, but they tend to be much more expensive and hence not very scalable in practice.

- recall that a program analysis is defined as a particular set of variables and constructors along with constraint generation (i.e., a way to map a program to a set of inclusion constraints).

    + unlike a DFA, a set constraint-based analysis can be implemented using a single linear traversal of the program, e.g., using IrVisitor (assuming that we already have a solver available).

## start with no structs or calls
### setup

- we'll assume for now programs that contain no struct definitions or function calls.

- remember that a solution is a map from set variables to sets of constructor calls. we want to define the analysis so that set variable solutions map to the information we're trying to compute, i.e., points-to sets.

- universe of discourse:

    + a constant and set variable for each program variable and static allocation site

        - the constant represents the program variable / allocated object

        - the set variable represents the associated points-to set

    + a `ref/2` constructor s.t. that first argument is a constant and the second argument is a set variable, connecting a program variable / allocated object with its points-to set.

### constraint generation

- [for all instructions with lhs, ignore them if the type of lhs is not a pointer; for $store ignore it if the type of the operand is not a pointer]

- given a program variable `x`, we'll use the notation `[x]` to mean translate `x` into a set variable (creating it if it doesn't already exist) and return the set variable, and `const(x)` to mean translate `x` into a constant (creating it if it doesn't already exist) and return the constant.

- given an $alloc instruction, we'll use the notation `abstract(here)` to mean create a fake program variable named `alloc.<function name>.<basic block label>.<index>` (if it doesn't already exist).

- `lhs = $copy var`

    + [var] ⊆ [lhs]

- `lhs = $phi(vars...)`

    + for var in vars: [var] ⊆ [lhs]

- `lhs = $select op var2 var3`

    + [var2] ⊆ [lhs]
    + [var3] ⊆ [lhs]

- `lhs = $alloc`

    + ref(const(abstract(here)), [abstract(here)]) ⊆ [lhs]

    + notice that since the left-hand side is a constructor call this constraint will be represented as a predecessor edge in the constraint graph---i.e., it will be part of the solution for the [lhs] set variable.

- `lhs = $addrof var`

    + ref(const(var), [var]) ⊆ [lhs]

    + same as $alloc, this call will be part of [lhs]'s solution.

- `lhs = $gep src_ptr op`

    + [src_ptr] ⊆ [lhs]

    + since there are no structs (for now), there can't be a fieldname argument to the $gep

    + note that we're being 'array-insensitive', which is common in these kinds of analyses

- `lhs = $load src_ptr`

    + ref^-1([src_ptr]) ⊆ [lhs]

    + why? we're asking for the current solution of [src_ptr] (which will be a set of ref calls representing what src_ptr points to), projecting out the second argument (which is the set variable representing the pointed-to variable's points-to set), and saying that those set variable flow into [lhs]. the projection is essentially modeling pointer dereference.

- `$store dst_ptr var`

    + [var] ⊆ ref^-1([dst_ptr])

    + why? similar to load except the information is flowing _into_ the pointed-to variables instead of _from_ them.

- we can ignore:

    + `$arith`, `$cmp` (because they don't affect pointer values)

    + `$jump`, `$branch` (because the analysis is flow-insensitive)

    + `$call`, `$icall`, `$ret` (because we're not considering function calls for now)

### example

CODE
```
def function main() -> int {
  entry:
    a:int** = $alloc
    b:int = $copy 42
    c:int* = $addrof b:int
    d:int** = $gep a:int** 42
    $store d:int** c:int*
    $branch b:int bb1 bb2

  bb1:
    e:int** = $copy d:int**
    $jump bb3

  bb2:
    f:int** = $addrof c:int*
    $jump bb3

  bb3:
    g:int** = $phi(e:int**, f:int**)
    h:int* = $load g:int**
    i:int = $load h:int*
    $ret 0
}
```

CONSTRAINTS
```
ref(alloc.main.entry.0, ALLOC.MAIN.ENTRY.O) ⊆ A
ref(b, B) ⊆ C
A ⊆ D
C ⊆ ref^-1(D)
D ⊆ E
ref(c, C) ⊆ F
E ⊆ G
F ⊆ G
ref^-1(G) ⊆ H
```

GRAPH
```
ref(alloc) --> A ==> D ==> E ====> G
ref(c) ------------> F =======//
ref(b) --> C --> ref^-1(D)
ref^-1(G) --> H
```

SOLUTION
```
A -> { ref(alloc.main.entry.0, ALLOC.MAIN.ENTRY.0) }
C -> { ref(b, B) }
D -> { ref(alloc.main.entry.0, ALLOC.MAIN.ENTRY.0) }
E -> { ref(alloc.main.entry.0, ALLOC.MAIN.ENTRY.0) }
F -> { ref(c, C) }
G -> { ref(alloc.main.entry.0, ALLOC.MAIN.ENTRY.0), ref(c, C) }
H -> { ref(b, B) }
ALLOC.MAIN.ENTRY.0 -> { ref(b, B) }
```

## adding structs
### intro

- what if we have structs in the program? recall that the $gep instruction is how we access the fields of a struct (and how we do all pointer arithmetic in general):

    + `lhs = $gep src_ptr op fieldname`

    + `src_ptr` is a pointer to the struct

    + `op` is an array index (0 if this isn't an array)

    + `fieldname` is the name of the field we want to access

    + `lhs` is set to a pointer to the desired field

- when a struct is allocated with $alloc it creates an object that has distinct fields and we need to decide how to represent that in the analysis.

- we have three choices: field-insensitive, field-based, field-sensitive. the most common choices are field-insensitive or field-sensitive, that's what we'll cover.

### field-insensitive

- the simplest and easiest option: treat a struct object as if it doesn't have distinct fields, i.e., all fields are merged together into a single blob.

    + just keep the $gep constraint generation that we're already doing, ignoring any `fieldname` that it might specify.

- example:

  ```
  struct foo {
    x: int*
    y: int*
  }

  def function main(i:int) -> int* {
    entry:
      p:int* = $addof i:int
      a:foo* = $alloc
      b:int** = $gep a:foo* 0 x
      c:int** = $gep a:foo* 0 y
      $store b:int** p:int*
      d:int* = $load c:int**
      $ret d:int*
  }
  ```

    + the pointer analysis will treat the `x` and `y` fields as the same thing, so `b:int**` and `c:int**` will have the same abstract value (i.e., the pointer analysis says that both pointers can point to both fields).

    + it says that `d:int*` can point to `i:int` even though it really can't.

### field-sensitive

- the most complex and most precise option: treat each struct field independently.

- we need to define a constructor for each struct type, whose name is the struct type, arity is the number of fields, and variance is all covariant.

- $gep instructions that are accessing a field need to turn into projections of the correct field.

- this only works because we require the program to be strictly typed (and there's no inheritance); if we can have the same pointer point to structs with different numbers of fields things become more complicated.

## adding direct calls
### constraint generation

- our final analysis will be _interprocedural_, that is, we'll track how information flows back and forth between functions. this means we need to add constraint generation for $call (we'll ignore indirect calls for now).

    + we'll also ignore any $call instructions to an externally-defined function (i.e., one that is not defined in the program itself). these come up often in program analysis, and the typical method to handle them is to create _stub_ functions that approximate any effect the function might have wrt to the analysis being done.

- let the notation `retval(func)` mean to the variable operand of the $ret instruction inside function `func` (undefined if the operand is not a variable or if there is more than one $ret instruction).

- `lhs = $call <function>(args...)`

    + if `<function>` is not defined in the program, ignore this instruction

    + if lhs is pointer-typed: [retval(`<function>`)] ⊆ [lhs]

    + for arg in args if arg is pointer-typed: [arg] ⊆ [parameter] s.t. parameter is the corresponding parameter of `<function>`.

- these constraints account for pointer-related information being passed to the callee via the call arguments and back from the callee via the return instruction.

### example

CODE
```
def function main() -> int {
  entry:
    a:int = $copy 42
    b:int* = $addrof a:int
    c:int = $copy 12
    d:int* = $addrof c:int
    e:int** = $addrof d:int*
    f:int* = $call foo(b:int*, e:int**)
    g:int = $load f:int*
    $ret g:int
}

def function foo(p1:int*, p2:int**) -> int* {
  entry:
    h:int* = $load p2:int**
    $store p2:int** p1:int*
    $ret h:int*
}
```

CONSTRAINTS
```
ref(a, A) ⊆ B
ref(c, C) ⊆ D
ref(d, D) ⊆ E
B ⊆ P1
E ⊆ P2
H ⊆ F
ref^-1(P2) ⊆ H
P1 ⊆ ref^-1(P2)
```

GRAPH
```
ref(a) --> B ==> P1 --> ref^-1(P2) ==> H ==> F
ref(c) --> D
ref(d) --> E ==> P2
```

SOLUTION
```
 A -> {}
 B -> { ref(a) }
 C -> {}
 D -> { ref(a), ref(c) }
 E -> { ref(d) }
 F -> { ref(a), ref(c) }
 H -> { ref(a), ref(c) }
P1 -> { ref(a) }
P2 -> { ref(d) }
```

## adding indirect calls
### setup

- unlike direct calls, we can't generate constraints that link a call with its callee and the callee return value with the lhs of the call (because we don't know what the callee is). instead we need to generate constraints that can somehow figure out these links dynamically during constraint solving.

- we'll define a constructor `lam_X` for each function type `X` of an address-taken function; constructor calls to this constructor will represent address-taken functions of type `X` (i.e., for each such function its parameters and return value) and also any indirect calls via a function pointer to type `X`.

### example

- we'll start with an example:

  CODE:
  ```
  def function foo(p1:int*, p2:int, p3:int*) -> int* {
    entry:
      p1:int* = $copy p3:int*
      $ret p1:int*
  }

  def function main() -> int {
    entry:
      a:int* = $alloc
      b:int* = $alloc
      f:int*[int*,int,int*]* = $copy @foo:int*[int*,int,int*]*
      c:int* = $icall f:int*[int*,int,int*]*(a:int*, 42, b:int*)
      $ret 0
  }
  ```

  CONSTRAINTS:
  ```
  let X = int*[int*,int,int*]
  define lam_X/3

  lam_X(P1, _P1_, _P3_) ⊆ @FOO

  ref(alloc.main.entry.0, ALLOC.MAIN.ENTRY.0) ⊆ A
  ref(alloc.main.entry.1, ALLOC.MAIN.ENTRY.1) ⊆ B
  @FOO ⊆ F
  F ⊆ lam_X(C, _A_, _B_)
  ```

### constraint generation

- for each global function pointer `@<func>:X*` (e.g., `@foo:int*[int,int]*`):

    + let N = (number of pointer-typed parameters) + (number of pointer-typed return values). note that the latter will always be 0 or 1.

    + create a constructor `lam_X/N` if it doesn't already exist. if there is a pointer-type return value then the first position must be covariant; all remaining positions must be contravariant.

    + for `<func>`s pointer-typed parameters `param1`, `param2`, ... (i.e., ignoring any non-pointer-typed parameters):

        + if `<func>` returns a pointer `retval`: let args = ([retval], [param1], [param2], ...)

        + otherwise let args = ([param1], [param2], ...)

    + generate constraint `lam_X([args]) ⊆ [@<func>]`; this says that the global function pointer points to a function of the given type.

- for each indirect call instruction `lhs = $icall func_ptr(args...)`:

    + let the pointer-typed arguments be `arg1`, `arg2`, ...

    + if lhs is pointer-typed let args be the list ([lhs], [arg1], [arg2], ...)

    + otherwise let args = ([arg1], [arg2], ...)

    + generate constraint `[func_ptr] ⊆ lam_X(args)`; this says that anything `func_ptr` points to should flow into the given constructor call.

- if we generate constraints this way, then eventually the `lam_X` constructor calls containing the retval and parameters of an address-taken function will flow into the `lam_X` constructor call containing the lhs and arguments of the indirect call.

    + since the retval position (if it exists) is covariant, retval will flow into lhs.

    + since the remaining positions are contravariant, the arguments will flow into the parameters.

# program slicing (using pointer info; control analysis; pdg)
## intro

- [the point is to (1) show why pointer info is useful now that we have it; (2) introduce a different application for program analysis than optimization; (3) describe some generally useful things like dominance and PDG]

- now that we have a way to compute pointer information for our analyses, let's use it on a practical application: program slicing.

    + problem statement: given a specific instruction I, return the set of instructions that influence the result of I. the name is inspired by the idea that we're computing a "slice" of the program that only contains relevant instructions.

    + slicing is a well-known and heavily-used analysis and there are many variations; we're using one of the basic definitions here but there are many more possible versions.

    + sign analysis was a stand-in for constant propagation (i used it instead of constant propagation because the abstract semantics are more interesting; for constant propagation it's just standard arithmetic), so that was exploring program analysis for program optimization. slicing is usually used for things like program understanding and debugging, so we're now exploring a different use-case for program analysis.

- example:

  ```
  main() {
    int i, j, x = input(), y = 0, z = 1;

    for (i = 0, j = 0; i < x; i++, j +=2) {
      y += 2;
      z *= j;
    }

    return y;
  }
  ```

  SLICE WRT RETURN STATEMENT:
  ```
  main() {
    int i, x = input(), y = 0;

    for (i = 0; i < x; i++) {
      y += 2;
    }

    return y;
  }
  ```

- however, we can't go straight to slicing; we need to build up to it:

    + reaching defs with pointer info + control analysis ==> PDG ==> slicing

    + remember that i said reaching definitions was more for follow-on analyses than for human consumption; this is the kind of thing i meant. also note that, as i promised earlier, assignment 1 is important to the remaining assignments (the above set of things will be assignment 3).

    + notice that if we treat pointers conservatively like in assignment 1 then slicing is pretty useless; the "slice" it computes will contain many irrelevant instructions and overwhelm the user with useless info.

    + also note that the PDG is a very useful data structure in its own right and can be used for things besides slicing; i'll talk more about that later.

- to keep things simpler and more focused we'll stay with intraprocedural analysis for this problem; we'll move to interprocedural analysis after we're done with slicing.

## reaching defs with pointer info
### intro

- we'll revisit our old friend reaching defs, but now with pointer information. we'll keep it intraprocedural, but we'll use pointer information to improve precision for loads, stores, and calls.

- we'll assume that we have the solution of a flow-insensitive pointer analysis available (i.e., it maps each program variable / static allocation site to its points-to set, which itself contains program variables and static allocation sites).

    + we'll assume a field-insensitive pointer analysis since that's what we implemented for assignment 2. this means that we can basically ignore structs altogether.

- we'll also pre-process the program we're analyzing to compute "mod/ref" information: for each function, what set of inputs to the function can be defined by a $store ("mod") or used by a $load ("ref") either directly by that function or by some function that it transitively calls.

    + this mod/ref info will allow us to be less conservative when processing calls during our intraprocedural analysis.

### mod/ref info

- step 1: compute the call graph of the program.

    + a node for each function.

    + an edge from function A to function B if A contains a call to B (via either $call or $icall; use the points-to solution for $icall).

    + this is an very common and useful data structure that a lot of program analysis tools use.

- step 2: compute the transitive closure of the call graph.

    + augment the call graph so that there is an edge from A to B if A can directly or transitively reach B in the call graph.

- step 3: compute initial mod/ref info.

    + for each function `<func>`, compute (1) the set of pointed-to objects that can be defined by a $store in `<func>` (`Mods(<func>)`) and (2) the set of pointed-to objects that can be used by a $load in `<func>` (`Refs(<func>)`).

- step 4: propagate the mod/ref info using the transitively closed call graph.

    + for `<func2>` s.t. `<func> --> <func2>` in the closed graph: `Refs(<func>) = Refs(<func>) \union Refs(<func2>)`; `Mods(<func>) = Mods(<func>) \union Mods(<func2>)`.

- what does this tell us? given a function `<func>`, `Refs(<func>)` is the set of objects that can be used by a $load in `<func>` or any function that `<func>` may call, and `Mods(<func>)` is the set of objects that can be defined by a $store in `<func>` or any function that `<func>` may call.

    + this info will help us be more precise when processing calls during the reaching defs analysis.

### the improved analysis

- no more need for "fake variables"; that's handled by the pointer analysis (i.e., the static allocation sites) and we'll just use its results.

- all parameters still have a def of `external-def` (same as before); also all variables reachable via the parameters (per the points-to solution) have a def of `external-def` (this is new).

    + the second part was handled before by being conservative and assuming that any $load could be using an `external-def` object, but now the pointer analysis will allow us to be more precise.

    + as before, we record this info in the initial abstract store of the entry
      basic block.

- all the instructions stay the same except for $load, $store, $call, $icall.

- `lhs = $load src_ptr`:

    + USES = { src_ptr } \union points-to[src_ptr]

    + for each use in USES: soln[inst] += store[use]

    + store[lhs] = {inst}

- `$store dst_ptr op`:

    + USES = { dst_ptr } \union { op if it's a var }

    + DEFS = points-to[dst_ptr]

    + for each use in USES: soln[inst] += store[use]

    + for each def in DEFS: store[def] += {inst}

- `lhs = $[i]call <func/ptr>(args...)`:

    + REACHABLE = all objects reachable from the arguments per the points-to solution

    + CALLEES = all possible callee functions for this call (using the points-to solution if this is an $icall)

    + REFS = (\Union_{callee \in CALLEES} Refs(callee)) \intersect REACHABLE

        + all objects reachable from the arguments that are used by a $load in the callee directly or transitively.

    + USES = { args that are variables } \union { ptr if this is an $icall } \union REFS

    + WEAK_DEFS = (\Union_{callee \in CALLEES}: Mods(callee)) \intersect REACHABLE

        + all objects reachable from the arguments that are defined by a $store in the callee directly or transitively.

    + for each use in USES: soln[inst] += store[use]

    + store[lhs] = {inst}

    + for each def in WEAK_DEFS: store[def] += {inst}

## control analysis
### intro

- the subject of this analysis is control dominance and control dependence: how the execution of the basic blocks depend on each other. it is conducted on the CFG, and while there is a relatively simple DFA-based algorithm for computing dependence info it will be convenient to use a more optimized version.

    + this optimized version is from Cooper et al, "A Simple, Fast Dominance Algorithm".

- for this analysis we don't care about individual instructions, just entire basic blocks---because of the way we defined basic blocks, if one instruction is executed all the others in the same block necessarily must be executed as well.

### control dominance
#### definitions:

- the DOM relation: `x DOM y iff every path from function entry to y must pass through x`

   + that is, we can't get to y without going through x first

   + reflexive, transitive, anti-symmetric (i.e., a partial order)

- strict dominance: `x DOM! y iff x DOM y and x != y`

   + that is, the irreflexive version of DOM

- immediate dominance: `x IDOM y iff x DOM! y and x is dominated by all other strict dominators of y`

   + intuitively, this is the "closest" strict dominator of y

- post-dominance: `x PDOM y iff every path from y to the function exist must pass through x`

   + the dual of dominance; just reverse the CFG and PDOM becomes DOM (and vice-versa).

#### examples

- EXAMPLE 1

  CFG:
  ```
  A --> B ----> C --> E
          \          /
           \__> D __/
  ```

  RELATIONS:
  ```
  A DOM { A, B, C, D, E }
  B DOM { B, C, D, E }
  C DOM { C }
  D DOM { D }
  E DOM { E }

  A DOM! { B, C, D, E }
  B DOM! { C, D, E }
  C DOM! {}
  D DOM! {}
  E DOM! {}

  A IDOM { B }
  B IDOM { C, D, E }
  C IDOM {}
  D IDOM {}
  E IDOM {}
  ```

- EXAMPLE 2:

  CFG:
  ```
          +-----+
          |     |
          v     |
  A ----> B --> C
    \       \
     \       \
      \_> D --> E
  ```

  RELATIONS:
  ```
  A DOM { A, B, C, D, E }
  B DOM { B, C }
  C DOM { C }
  D DOM { D }
  E DOM { E }

  A DOM! { B, C, D, E }
  B DOM! { C }
  C DOM! {}
  D DOM! {}
  E DOM! {}

  A IDOM { B, D, E }
  B IDOM { C }
  C IDOM {}
  D IDOM {}
  E IDOM {}
  ```

#### why do we care?

- we'll show how it's useful for program slicing in a bit, but it's useful information even by itself:

    + security: every use of user input is dominated by a sanitizer
    + concurrency: every lock is post-dominated by an unlock

#### dominator tree

- a convenient data structure to represent dominance information is a `dominator tree`:

    + a node for each basic block

    + an edge x --> y iff x IDOM y

- given a dominator tree, we can easily compute DOM and DOM! info

- notice that this representation only works if we're guaranteed that no node can be IDOM by more than one other node (otherwise this wouldn't be a tree).

    + we want to prove that if a IDOM c and b IDOM c, then a = b
    + by definition of IDOM, a DOM! c and b DOM! c
    + by definition of DOM!, all strict dominators of c must dominate a (which includes b)
    + by definition of DOM!, all strict dominators of c must dominate b (which includes a)
    + therefore a DOM b and b DOM a
    + since DOM is anti-symmetric, a = b

- example 1: [show dominator tree for example 1 above]

- example 2: [show dominator tree for example 2 above]

- note that the DOM relation is a partial order, and the dominator tree is essentially the Hasse diagram of that partial order.

#### computing dominance

- there is a simple, straightforward DFA analysis for dominance, but it is very slow and also makes computing control-dependence information (which we'll get to soon) somewhat annoying.

- this algorithm is taken from Cooper et al, "A Simple, Fast Dominance Algorithm", which is as advertised and also makes computing control-dependence information easy.

- instead of computing dominance directly, it computes the dominator tree (from which dominance information can be extracted very easily).

- given: the CFG as a graph of basic blocks, with entry point `entry`. let `idom` be a map from a basic block to its immediate dominator basic block (i.e., it represents the dominator tree, mapping a block to its parent in the tree).

- worklist algorithm (slightly different than the one in the paper, which round-robins all basic blocks instead of using a worklist):

  ```
  idom[entry] = entry
  push successors of entry onto worklist

  while worklist is not empty:
    pop bb from worklist

    // A predecessor meeting this requirement must necessarily exist.
    let bb_idom = some predecessor of bb s.t. idom[bb_idom] exists

    // Computes the intersection of the immediate dominators of all predecessors. Some
    // predecessors may not have been computed yet; we'll revisit bb when they are in order to
    // update bb's information.
    for pred in predecessors of bb:
      if idom[pred] doesn't exist, skip
      bb_idom = intersect(bb_idom, pred)

    // The immediate dominator of bb is the computed intersection.
    if idom[bb] doesn't exist OR idom[bb] != bb_idom:
      idom[bb] = bb_idom
      push successors of bb into worklist
  ```

- the slightly tricky part is the intersection algorithm: given the current `idom`, how do we compute the immediate dominator intersection of two basic blocks bb1 and bb2 in the tree?

    + the idea is to place two `fingers` on the dominator tree, one at bb1 and one at bb2, and then move the fingers up the tree to find the least common ancestor of both bb1 and bb2. [show on some made-up tree]

    + remember that dominance is a partial order and the dominator tree is its hasse diagram; then this 'intersection' operation is just the join operation on the partial order (as long as there is one basic block that dominates all others, the join is guaranteed to exist).

    + to make this fast and simple, number each basic block in the CFG using post-order:

      ```
      let index = 0
      call DFS(entry) where:

      define DFS(bb) {
        mark bb visited
        for each successor succ of bb:
          if succ is not yet visited, call DFS(succ)
        postorder[bb] = index
        index++
      }
      ```

    + now we can define the intersection algorithm:

      ```
      define intersect(bb1, bb2) {
        finger1 = bb1
        finger2 = bb2

        while finger1 != finger2:
          while postorder[finger1] < postorder[finger2]:
            finger1 = idom[finger1]
          while postorder[finger2] < postorder[finger1]:
            finger2 = idom[finger2]

        return finger1
      }
      ```

- examples: [use examples 1 and 2 above]

### control dependence
#### intro

- dominance tells us what basic blocks _must_ be executed before (or after for post-dominance) other blocks, but it can also be used to tell us what blocks are _control dependent_ on what other blocks.

- intuitively, block X is control dependent on block Y if the execution of block Y directly determines whether block X will be executed or not (including how many times it will be executed, for loops).

#### examples

- EXAMPLE 1

  ```
  entry:
    secret:int = $call boolean_input()
    $branch secret:int bb2 bb3

  bb2:
    public:int = $copy 1
    $jump exit

  bb3:
    public:int = $copy 0
    $jump exit

  exit:
    d:int = $call output(public:int)
    $ret 0
  ```

    + bb2 and bb3 are both control dependent on bb1; entry and exit are _not_ control dependent on any other blocks

    + this also shows one reason why control dependence is interesting (other than for slicing which we'll show later): notice that the value of `public` is equal to the value of `secret` even though there is no explicit assignment between them. it can only leak one bit of information, but if we put something like this in a loop we could leak arbitrary bits.

- EXAMPLE 2

  ```
        +-----------+
        |           |
        V           |
  A --> B --> C --> F --> G
        |           ^
        |           |
        + --> D --> E
              ^     |
              |     |
              +-----+
  ```

  CONTROL DEPENDENCIES:
  ```
  A: { }
  B: { F }
  C: { B }
  D: { B, E }
  E: { B, E }
  F: { F }
  G: { }
  ```

    + B is control dependent on F because F controls how often B is executed; similarly for (D, E), (E, E), and (F, F).

#### dominance frontiers

- to compute control dependence info we need something called _dominance frontiers_.

- the dominance frontier of block X is the set of all blocks Y s.t. X dominates a predecessor of Y but does not strictly dominate Y.

    + intuitively, the dominance frontier is the set of blocks that are _almost_ dominated by X, but just outside its control. or, its the set of blocks that are the earliest point where some competing control-flow path may reach that block.

- given the dominator tree, computing dominance frontiers is simple:

  ```
  for all blocks X:
    if the number of predecessors of X >= 2:
      for all predecessors P of X:
        runner = P
        while runner != idom[X]:
          add X to runner's dominance frontier
          runner = idom[runner]
  ```

- intuitively:

    + X cannot be in a dominance frontier if it doesn't have at least two predecessors (because that's the only way it can "escape" from another block's dominance).

    + X cannot be in the dominance frontier of its immediate dominator (by definition of dominance frontier).

    + otherwise, X is in the dominance frontier of Y if Y dominates a predecessor of X (recall that we can compute dominance from the dominator tree by following the path from a leaf to the root).

- [demonstrate on example 2 above]

  DOMINATOR TREE:
  ```
          A
          |
          B
        / | \
       /  |  \
      C   D   F
          |   |
          E   G
  ```

  DOMINANCE FRONTIERS:
  ```
  A: { }
  B: { B }
  C: { F }
  D: { D, F }
  E: { D, F }
  F: { B }
  G: { }
  ```

#### computing control dependence

- X is control dependent on Y iff Y is in the post-dominance frontier of X

    + remember that post-dominance (and post-dominance frontiers) are exactly the same as dominance (and dominance frontiers) except on a reversed CFG.

    + if Y is in the post-dominance frontier of X, then that means Y has multiple successors s.t. if a specific one is taken then X must necessarily execute, and if it is not taken then X will definitely not execute. in other words, X is control dependent on Y.

- algorithm:

    + step 1: compute post-dominator tree (i.e., flip the edges of the CFG and compute the dominator tree on that).

    + step 2: compute post-dominance frontiers (i.e., compute dominance frontiers but using the post-dominator tree).

- that's it: each block X is control dependent on any block in its post-dominance frontier.

- [demonstrate on example 2 above]

  POST-DOMINATOR TREE:
  ```
          G
          |
          F
        / | \
       /  |  \
      C   E   B
          |   |
          D   A
  ```

  POST-DOMINANCE FRONTIERS:
  ```
  A: { }
  B: { F }
  C: { B }
  D: { B, E }
  E: { B, E }
  F: { F }
  G: { }
  ```

## pdg

- the PDG is a data structure that embodies the reaching defs and control dependence information we computed earlier.

- a node for for each instruction.

- a _data dependence_ edge from A to B if the definition at A reaches a use at B.

    + we get this directly from the reaching defs analysis.

- a _control dependence_ edge from A to B if B is control dependent on A.

    + we expand the basic block control dependence info into individual instructions: if basic block X is control dependent on block Y, then all instructions in X have control dependence edges from the terminator instruction in Y.

- it will be useful to have bi-directional edges so that we can go forwards or backwards in the PDG.

- [demonstrate using the original example for program slicing]

  ORIGINAL:
  ```
  main() {
    int i, j, x = input(), y = 0, z = 1;

    for (i = 0, j = 0; i < x; i++, j +=2) {
      y += 2;
      z *= j;
    }

    return y;
  }
  ```

  IR:
  ```
   1. entry:
   2.   i:int = $copy 0
   3.   j:int = $copy 0
   4.   x:int = $call input()
   5.   y:int = $copy 0
   6.   z:int = $copy 1
   7.   $jump loop_hdr

   8. loop_hdr:
   9.   tmp:int = $cmp lt i:int x:int
  10.   $branch tmp:int loop_body exit

  11. loop_body:
  12.  y:int = $arith add y:int 2
  13.  z:int = $arith mul z:int j:int
  14.  i:int = $arith add i:int 1
  15.  j:int = $arith add j:int 2
  16.  $jump loop_hdr

  17. exit:
  18.  $ret y:int
  ```

  PDG:
  ```
  [12--16] control dependent on [10]

   [9] data dependent on [2, 4, 14]
  [10] data dependent on [9]
  [12] data dependent on [5, 12]
  [13] data dependent on [3, 6, 13, 15]
  [14] data dependent on [2, 14]
  [15] data dependent on [3, 15]
  [18] data dependent on [5, 12]
  ```

## slicing

- recall: a slice of a function for instruction X consists of all instructions that affect the behavior of X.

- if X is data dependent on Y, then clearly Y affects X's behavior...and so do the instructions that Y is data dependent on, and so on.

    + this is the transitive closure of the data dependence edges in the PDG (going backwards from X).

- if Y controls whether X even executes or not, then clearly Y affect's X's behavior...and so do all the instructions that control whether Y executes or not, and so on.

    + this is the transitive closure of the control dependence edges in the PDG (going backwards from X).

- if you think about it, if X is data dependent on Y and Y is control dependent on Z, then Z also affects X's behavior; and if X is control dependent on Y and Y is data dependent on Z, then same thing.

- so to compute a slice wrt X: compute the transitive closure of all data and control dependence edges from X (going backwards in the PDG).

    + this is called a _backwards slice_; we could also compute a _forwards slice_ by doing the same thing except going forwards in the PDG. this kind of slice tells us what instructions the execution of X can directly or indirectly affect.

- for human consumption, rather than a simple set of instructions it's useful to present the slice as a new function that only contains the relevant instructions.

- [demonstrate using the original example for program slicing]

  SLICED:
  ```
  entry:
    i:int = $copy 0
    x:int = $call input()
    y:int = $copy 0
    $jump loop_hdr

  loop_hdr:
    tmp:int = $cmp lt i:int x:int
    $branch tmp:int loop_body exit

  loop_body:
    y:int = $arith add y:int 2
    i:int = $arith add i:int 1
    $jump loop_hdr

  exit:
    $ret y:int
  ```

# SUMMARY SO FAR

- [this can go wherever it makes sense to put it; i'm putting it here for now because i need to buy some time for my prep to catch up to the class]

- basics of DFA: abstract domain + abstract semantics (transfer functions) + abstract execution (MOP vs MFP; worklist algorithm)

    + flow-sensitive
    + intraprocedural
    + no pointer info

- two examples thereof:

    + sign analysis (a stand-in for constant propagation)
    + reaching definitions

- the math behind DFA: neotherian lattices, monotone functions, fixpoint theorems

    + make the abstract domain a noetherian lattice
    + make the abstract transfer functions monotone
    + then the worklist algorithm is guaranteed to terminate with the most precise answer

- set constraint-based analysis:

    + picking a constraint language (specifically, inclusion constraints)
    + implementing a constraint solver
    + analysis = constraint generation
    + example: andersen-style pointer analysis (flow-insensitive, field-insensitive)

- using pointer info: program slicing (still intraprocedural for DFA)

    + revisiting reaching definitions, now with pointer info
    + control analysis: dominance and dependence
    + program dependence graph (PDG)
    + backwards slicing

- now on to something new!

# TODO taint analysis (interprocedural dfa; icfg; context sensitivity)
## intro

- we've seen applications of program analysis for optimization and debugging, and we've seen how to overcome the difficulty of pointers. let's explore how to deal with function calls and interprocedural analysis at the same time as we expand our applications to include security.

- we'll look at _taint analysis_. the goal of taint analysis is to track "tainted" information (e.g., input from users) as it propagates through a program and to ensure that it doesn't influence sensitive operations.

    + example: sql injection.

    + we can also use taint analysis for the inverse: track sensitive information (e.g., from a user's private data) to ensure that it doesn't influence information output from the program (e.g., that a browser addon doesn't read a user's credit card info and send it out over the network).

- to make this analysis useful, it needs to be interprocedural---that is, it needs to track information across function call boundaries.

    + andersen-style pointer analysis was interprocedural, but not in a DFA setting.

- when dealing with the intraprocedural CFG we had two choices: flow-sensitive vs flow-insensitive.

    + flow-sensitive: respect the flow of control; a solution per program point. a variable can have a difference abstract value at different places in the program.

    + flow-insensitive: disregard the flow of control and assume any statement can execute at any time; one solution for the entire program. a variable must have one abstract value that over-approximates all possible concrete values it might have anywhere.

- when dealing with interprocedural analysis we have a similar choice: context-insensitive vs context-sensitive.

    + concretely during program execution, every function call results in a unique function instance (typically using stack frames). conceptually, this results in an infinite domain of possible function instances.

    + for program analysis to be decidable, we need to abstract this infinite domain into something tractable while still over-approximating the conrete domain.

    + context-insensitive: we'll abstract all function instances of the same function into a single abstract function instance that over-approximates all of them. that is, the information for every call to the same function is merged together.

    + context-sensitive: we'll partition the set of function instances for the same function into a set of different abstract function instances, treating each one as distinct. the different ways that we partition the set result in different context-sensitivity schemes.

        - unlike flow-sensitivity (for which there is just one definition), there are many different versions of context-sensitivity.

- we'll look at these choices in more detail one at a time, starting with interprocedural context-insensitive taint analysis and then exploring a couple of different context-sensitivity strategies to make it more precise.

## FIXME[onenote] icfg

- we need to revisit our old program representation of the CFG. now that we're propagating information to calls and back, we need something that shows the connections between functions.

- Interprocedural Control-Flow Graph (ICFG): take the individual function CFGs and add edges:

    + from a call instruction to the entry nodes of all callee functions.
    + from a ret instruction to the callsites for that function.

- problem: the call edges can be from the middle of a basic block and the return edges can be into the middle of a basic block.

- solution 1: allow it to happen without changing anything. this is easy to implement because it doesn't involve changing anything but makes the analysis implementation more complex because we have to track whether we're making a call or returning from a call and do different things depending.

    + the worklist has to contain program points, not basic blocks, because when you pop an entry off the worklist it could be the beginning of a basic block or it could be returning from a call inside the basic block.

    + this is what i did, though you don't have to do the same thing as me.

- solution 2: split the basic blocks at call instructions and separate each call instruction into two instances: (1) the actual call, placed at the end of the basic block; (2) the return site, placed at the beginning of a new basic block. this complicates things at the beginning because you have to transform the basic blocks, but it makes the analysis implementation simpler.

    + the worklist can be the normal one on basic blocks, but you need to add a new instruction representing a 'call return' point.

    + this is the more traditional way to do things.

- example:

  ```
  def main() -> int {
    entry:
      x:int = $call input()
      $branch x:int call_f1 call_f2

    call_f1:
      y:int = $call func1(x:int)
      z:int = $arith add z:int 2
      $jump exit

    call_f2:
      y:int = $call func2(x:int)
      z:int = $arith mul z:int 4
      $jump exit

    exit:
      $ret z:int
  }

  def func1(p1:int) -> int {
    entry:
      a:int = $call func3(p1:int)
      a:int = $arith add a:int 1
      $ret a:int
  }

  def func2(p2:int) -> int {
    entry:
      b:int = $call func3(p2:int)
      b:int = $arith mul b:int 2
      $ret b:int
  }

  def func3(p3:int) -> int {
    entry:
      $ret p3:int
  }
  ```

## TODO context-insensitive taint analysis
### FIXME[onenote] intro

- we'll suppose that there are a set of functions that create tainted information, called _sources_, and a set of functions that consume information, called _sinks_.

    + what these sets are will dependent on the specific program we're analyzing.

    + example: we could say that `user_input()` is a source, `database_query()` is a sink.

- the taint analysis solution should be a map from each sink to the set of sources that _reach_ that sink. that is, there is some path along which the information from some source can reach an argument to that sink without being sanitized.

    + we'll only care about what sources and sinks are involved, not which specific call instructions were involved; e.g., if there are two calls to the same source `source()` and two calls to the same sink `sink()` and one source reaches each sink, the solution will just say `sink --> { source }`.

- example

  ```
  def main() -> int {
    entry:
      v1:int* = $alloc
      v2:int* = $call foo(v1:int*)
      _d:int  = $call sink1(v1:int*)
      _d:int  = $call sink2(v2:int*)
      $ret 0
  }

  function foo(p1:int*) -> int* {
    entry:
      x:int   = $call source1(p1:int*)
      y:int*  = $addof x:int
      _e:int  = $call sink1(y:int*)
      p2:int  = $call source2()
      p3:int* = $addrof p2:int
      $ret p3:int*
  }
  ```

  SOLUTION
  ```
  sink1 --> { source1 }
  sink2 --> { source2 }
  ```

### setup and assumptions

- the sets of sources and sinks are given as input to the analysis; they are collectively known as `special functions`.

    + they should be disjoint sets.

    + special functions can be internal or external; either way we don't actually analyze them (we treat them as a black box that operates on taint).

    + sources that return a pointer must be internal (so that the pointer analysis can work correctly).

- all objects possibly reachable from a pointer passed as an argument or returned as a result from a source function, and all values returned from a source function, are considered to be tainted.

- all arguments and all objects possibly reachable from an argument passed to a sink function are considered to reach that sink.

- we'll be operating on the ICFG. unlike previous analyses which analyze each function in turn, here we always start at the entry to `main` and only analyze those functions that are reachable via call instructions.

- we have the results of a field-insensitive andersen-style pointer analysis available; our analysis will also be field-insensitive.

### abstract domain

- an abstract value will be a set of sources, signifying which sources may have contributed to the concrete value.

    + this is a second-order analysis like reaching defs; we don't care what the value is just where it came from.

    + the empty set means that this data hasn't been influenced by any source.

- the join operation is set union.

- the abstract store will map variables to abstract values as usual.

### abstract semantics, no calls (except to special functions)

- helpers:

    + let `taint(op)` = store[op] if op is a variable, else {}

    + let `reachable(v...)` be the set of pointed-to objects reachable from the given arguments according to the points-to solution.

- `lhs = $call <source>(args...)`

    + store[lhs] = {source}

    + for v in (reachable(lhs) \union reachable(args)): store[v] |= source

    + note the weak updates for pointed-to objects.

- `lhs = $call <sink>(args...)`

    + for v in (args \union reachable(args)): soln[sink] |= store[v]

- `lhs = $icall fptr(args...)`

    + process all sink callees then all source callees (unioning the taints for lhs).

    + sources need to be processed last because any taints from them shouldn't affect sink callees.

- `lhs = $arith <operation> op1 op2`

    + store[lhs] = taint(op1) | taint(op2)

- `lhs = $cmp <relation> op1 op2`

    + store[lhs] = taint(op1) | taint(op2)

- `lhs = $copy op`

    + store[lhs] = taint(op)

- `lhs = $select op1 op2 op3`

    + store[lhs] = taint(op1) | taint(op2) | taint(op3)

- `lhs = $phi(ops...)`

    + store[lhs] = \union_{op \in ops} taint(op)

- `lhs = $alloc`

    + store[lhs] = {}

- `lhs = $addrof var`

    + store[lhs] = {}

- `lhs = $gep src_ptr op [fieldname]`

    + store[lhs] = store[src_ptr] | taint(op)

    + note that gep is basically pointer arithmetic, i.e., `src_ptr + op`.

- `lhs = $load src_ptr`

    + store[lhs] = store[src_ptr] | \union_{v \in ptsto(src_ptr)} store[v]

- `$store dst_ptr op`

    + for v in ptsto(dst_ptr): store[v] |= (store[dst_ptr] | taint(op))

    + note the weak update.

- `$jump` and `$branch`: propagate abstract store as normal

- we're assuming no calls other than to special functions, so we can ignore other `$call` and `$icall` instructions and also `$ret`.

### FIXME[onenote] examples

- example 1: with direct calls to special functions

  ```
  def src1(s1:int*) -> int* {
    entry:
      s2:int* = $alloc
      $ret s2:int*
  }

  def main() -> int {
    entry:
      v1:int* = $alloc
      v2:int* = $call src1(v1:int*)
      v3:int  = $call src2()
      v4:int* = $addrof v3:int
      v5:int* = $gep v1:int* v3:int
      v6:int* = $alloc
      $store v6:int* v3:int
      v7:int  = $load v5:int*
      _x:int  = $call sink1(v1:int*, v4:int*)
      _y:int  = $call sink2(v7:int)
      _z:int  = $call sink3(v2:int*, v6:int*)
      $ret 0
  }
  ```

  POINTS-TO
  ```
  v1 --> { main.entry.0 }
  v2 --> { src1.entry.0 }
  v4 --> { v3 }
  v5 --> { main.entry.0 }
  v6 --> { main.entry.5 }
  ```

  FINAL ABSTRACT STORE
  ```
  v1 --> {}
  v2 --> { src1 }
  v3 --> { src2 }
  v4 --> {}
  v5 --> { src2 }
  v6 --> {}
  v7 --> { src1, src2 }
  main.entry.0 --> { src1 }
  main.entry.5 --> { src2 }
  src1.entry.0 --> { src1 }
  ```

  SOLUTION
  ```
  sink1 --> { src1, src2 }
  sink2 --> { src1, src2 }
  sink3 --> { src1, src2 }
  ```

- example 2: with indirect calls to special functions

  ```
  def src1(s1:int) -> int {
    entry:
      $ret 0
  }

  def src2(s2:int) -> int {
    entry:
      $ret 0
  }

  def sink1(p1:int) -> int {
    entry:
      $ret 0
  }

  def main() -> int {
    entry:
      fptr:int[int]* = $copy @src1:int[int]*
      fptr:int[int]* = $copy @src2:int[int]*
      fptr:int[int]* = $copy @sink1:int[int]*
      x:int = $call src1(42)
      y:int = $icall fptr(x:int)
      z:int = $call sink2(y:int)
      $ret 0
  }
  ```

  FINAL ABSTRACT STORE
  ```
  fptr --> {}
  x --> { src1 }
  y --> { src1, src2 }
  z --> {}
  ```

  SOLUTION
  ```
  sink1 --> { src1 }
  sink2 --> { src1, src2 }
  ```

### TODO, FIXME[example, onenote] adding calls
#### TODO abstract semantics

- `lhs = $call <func>(args...)`

- `lhs = $icall fptr(args...)`

- `$ret op`

#### TODO abstract garbage collection
#### FIXME example

  ```
  FIXME TODO (including copying to onenote)
  ```

## TODO context-sensitivity
### TODO call-string context-sensitivity
### TODO functional context-sensitivity
### TODO handling recursive cycles
### TODO other kinds of context-sensitivity
### TODO context-sensitive heap models
