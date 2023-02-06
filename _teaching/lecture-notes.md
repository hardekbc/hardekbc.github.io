# RETROSPECTIVE
## timing

- remember that this quarter i had to cancel the entire first week, so it's only 9 weeks + finals)

    + week 2.1: up through 'the language we're analyzing'
    + week 2.2: up through 'control-flow graph (CFG)'
    + week 3.1: up through 'abstract execution (MOP vs MFP) :: EXAMPLE 1'
                (assigned hw1)
    + week 3.2: up through 'second-order DFA analyses'
    + week 4.1: up through middle of LATTICES
    + week 4.2: up through middle of general set constraint language description
    + week 5.1: ???
                (hw1 due)
    + week 5.2: ???
    + week 6.1: ???
    + week 6.2: ???
    + week 7.1: ???
    + week 7.2: ???
    + week 8.1: ???
    + week 8.2: ???
    + week 9.1: ???
    + week 9.2: ???
    + week 10.1: ???
    + week 10.2: ???

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

    + someone asked in class if we really need separate fake vars for each $alloc, and i don't think we do (they'll all be updated to the same values anyway, similar to the reasoning for having one fake var per struct field type).

    + from questions that come up, it seems like i need to give more in-class examples for these analysis (though unfortunately it would slow lecture down even more).

    + is having them do sign analysis and reaching definitions together too much for one assignment? maybe i should space them out more?

    + maybe explicitly lay out for them how to approach the assignment: take an analysis, write a set of tests with no pointers, implement until tests pass, write tests with no pointer-type args, implement until tests pass, etc.

- maybe i can modify the lecture to replace just enumerating abstract semantics: have example programs and go through them live with student help, then generalize from each statement's specifics to the general rules. that is, make the connection between the abstract semantics and the analysis more clear.

    + this might slow things down, though.

    + if i have a specific example for each rule, that might be providing too much info for the assignments (or i need to reevaluate the grading scheme).

- i should maybe rename/reduce the test programs in 260-public/example-c-programs (maybe to example-X.c?), because some students seem to think they're tests for the analyses in assignment 1 (this is bad for the sign analysis particularly, because they contain operations beyond + and < that students shouldn't need for the assignment).

    + definitely emphasize to the students that they shouldn't use them as tests for the assignments; some students are using them instead of creating their own tests at all. maybe i need to think of a way to ensure that they write their own tests instead of taking the lazy way out.

    + maybe only have one or two examples instead of a bunch of them?

- the grading scheme has a bit of a flaw: if a student is failing a test but can't create their own test that fails, it's hard to provide guidance.

    + maybe just be more precise about what each test contains?

    + it's also hard to diagnose simple things like they forgot to order the output correctly (which only causes an error if they write a test where the functions/blocks are out of order and the output preserves that incorrect order, so it's hard for them to write a failing test case when this is the problem).

- maybe abbreviate the math definitions and theorems for DFA; instead of building up gradually i could just go straight to lattices and kleene's theorem.

## set constraint-based analysis

- i could speed things up by skipping the general constraint language and going straight to inclusion constraints (just mention that we can make more complicated languages but it quickly becomes undecidable).

- i'm skipping a lot of things that were in the old lecture notes (or not, but are covered in my existing analysis implementations); depending on how the final timing works out i might want to add them back in

    + solver optimization details (e.g., cycle elimination)
    + details of field-{based, sensitive} analysis
    + equality-based analysis, steensgaard, type inference

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

- $load:

    + let DEF be the lhs of the instruction; USES contains the VarPtr_t operand and also all type-appropriate addressable objects, and also if there are any pointer-type function parameters of the appropriate type then USES contains nullptr to signify an external object.

    + for each use in USES: soln[inst] += store[use]

    + store[DEF] = {inst}

- $store:

    + let DEFS contain all type-appropriate addressable objects and USES contain the VarPtr_t operands of the instruction.

    + for each use in USES: soln[inst] += store[use]

    + for each def in DEFS: store[def] += {inst}

- $call, $icall:

    + let STRONG_DEF contain the lhs of the instruction

    + if there are no pointer-type arguments then WEAK_DEFS is empty, otherwise it contains all type-appropriate addressable objects (i.e., any type reachable from the pointer argument)

    + USES contains all VarPtr_t arguments and, if any arguments are pointer-typed: all type-appropriate addressable objects (the same set as for WEAK_DEFS) and if there are any pointer-type function parameters then nullptr to signify an external object.

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

        - ??? FIXME

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

    + `GROUND TERM`: a term c(E1,...,En) s.t. E1,...,En are all ground terms. the base cases are nullary constructors. in simple terms, a ground term is a constructor whose arguments don't contain any variables or constraints.

    + `HERBRAND UNIVERSE`: the set of all ground terms (denoted H)

    + `ASSIGNMENT`: a mapping σ ∈ Variable → P(H)

    + `SOLUTION`: an assignment that satisfies all of the constraints (there may be 0, 1, or many solutions)

- for the above example, a valid solution would be:

  ```
  X -> { a, b }
  Y -> { b, c }
  Z -> { a, c }
  ```

- unfortunately, finding solutions for the general set constraint language is undecidable. therefore we can only use fragments of that language, restricted to ensure decidability. the complexity of solving set constraints is determined by what restrictions we put on the language (not by the particular analysis that we're doing).

## restricted set constraint language

- one possible restricted language:

  ```
  X ∈ Variable
  c ∈ Constructor

  T ∈ Term ::= X | c/0
  E ∈ Expr ::= X | c(T1,...,Tn) | c^-i(X)
  S ∈ Stmt ::= E1 ⊆ E2 | S1 ∧ S2
  ```

- this is not the only possible restricted language that yields a decidable analysis, but this one has two nice properties:

    1. it is guaranteed to have a minimal solution
    2. it is guaranteed to have cubic complexity

- some of the changes don't affect the expressiveness of the language, they only make it more convenient to deal with (e.g., only allowing variables and constants as arguments for constructor calls, only allowing variables as arguments for projections).

    + also, for convenience we'll just deal with sets of inclusion constraints and assume that all constraints in a set are conjoined together, rather than explicitly using ∧. that is, `{S1, S2}` means the same thing as `S1 ∧ S2`.

- analyses defined using this particular restricted set constraint language are called _inclusion-based analyses_.

    + an inclusion-based _pointer_ analysis is also often called _andersen-style_ pointer analysis, after the person who first described it in their phd thesis.

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

- why we do this will become evident when we show how to solve the constraints.

- example:

  ```
  f(X,Y) ⊆ A
  A ⊆ B
  B ⊆ C
  C ⊆ E
  g ⊆ D
  h ⊆ D
  h ⊆ f^-2(B)
  D ⊆ f^-1(C)
  f^-1(C) ⊆ E
  f^-2(B) ⊆ F
  ```

### solving the constraint graph

- solving the constraints corresponds to computing the dynamic transitive closure of the graph. the predecessor edges from the constructor calls to the set variables are the solution to the constraints.

    + remember that a solution maps set variables to sets of constructors, so we map each set variable to the set of constructors that have a predecessor edge to that set variable.

- initialize worklist with all variables that have a predecessor edge (i.e., all variables that currently have something in their solution set). the worklist will only contain set variables.

- perform the usual worklist algorithm. when popping set variable X:

    1. propagate X's predecessor edges along all of X's successor edges. if the predecessor edges of the destination node changed, put it onto the worklist.

    2. if X has any projections, process each projection node:

        + compute the given projection of X (which can be different for each projection node) to get a set of variables v_i ∈ V.

        + for each v_i and predecessor p_i and successor s_i, add edges p_i -> v_i -> s_i.

        + if p_i -> v_i is new add p_i to the worklist; if v_i -> s_i is new add v_i to the worklist.

- what if we add an edge between two constructors? e.g., `c(T1,...,Tn) ⊆ c(T1',...,Tn')`. this is where knowing the variance of each constructor argument position matters:

    + `c(T1,...,Tn) ⊆ c(T1',...,Tn')` =>

        - Ti  ⊆ Ti' if position i of constructor c is covariant
        - Ti' ⊆ Ti  if position i of constructor c is contravariant

    + what if the constructors don't match? technically this is an error, but in a practical implementation we usually just ignore mismatched constructors.

- when the worklist is empty, we've computed a solution to the original constraints: for each set variable, look at its predecessor edges to get its solution.

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

### implementing the constraint graph and solver

- define the following classes:

    + `Constructor`: containing name, arity, and variance info for that constructor

    + `SetVariable`: containing name, list of predecessor edges (e.g., a set of nodes), and list of successor edges (e.g., another set of nodes)

    + `ConstructorCall`: containing a reference to the Constructor and a list of arguments (e.g., a vector of nodes)

    + `Projection`: containing a reference to the SetVariable the projection is being done on, a reference to the Constructor being projected, the position of the Constructor argument being projected, the list of predecessor edges, and the list of successor edges

- SetVariables, ConstructorCalls, and Projections are the nodes of the constraint graph, and the predecessor and successor edges are the edges of the constraint graph.

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

- we need an abstraction for memory (which is conceptually infinite); that is, instead of abstract values that over-approximate integers we need abstract values that over-approximate memory addresses. a common choice is _static allocation site_.

    + we'll create a fake 'variable' for each instruction where memory is allocated (aka static allocation site) and it will represent all concrete memory allocated by that instruction.

    + example: for `x:int* = $alloc` the object returned by the $alloc will be represented with a variable named `alloc.<function name>.<basic block label>.<index>`.

    + note that a single such abstract value can actually represent an unbounded number of concrete values (e.g., if the instruction is located inside a loop); this is why it's called a _static_ allocation site.

    + there are other abstractions, but they tend to be much more expensive and hence not very scalable in practice.

- recall that a program analysis is defined as a particular set of variables and constructors along with constraint generation (i.e., a way to map a program to a set of inclusion constraints).

    + unlike a DFA, a set constraint-based analysis can be implemented using a single linear traversal of the program, e.g., using IrVisitor.

## start with no structs or calls
### setup

- we'll assume for now programs that contain no struct definitions or function calls.

- remember that a solution is a map from set variables to sets of ground terms (i.e., constructor calls). we want to set up the analysis so that set variable solutions map to the information we're trying to compute, i.e., points-to sets.

- universe of discourse:

    + a constant and set variable for each program variable and static allocation site

        - the constant represents the program variable / allocated object

        - the set variable represents the associated points-to set

    + a `ref/2` constructor s.t. that first argument is a constant and the second argument is a set variable, connecting a program variable / allocated object with its points-to set.

### constraint generation

- [for all instructions with lhs, ignore them if the type of lhs is not a pointer; for $store ignore it if the type of the operand is not a pointer]

- given a program variable `x`, we'll use the notation `[x]` to mean translate `x` into a set variable (if it doesn't already exist) and return the set variable and `const(x)` to mean translate `x` into a constant (if it doesn't already exist) and return the constant.

- given an $alloc instruction, we'll use the notation `abstract(here)` to mean create a fake program variable named `alloc.<function name>.<basic block label>.<index>`.

- `lhs = $copy var`

    + [var] ⊆ [lhs]

- `lhs = $phi(vars...)`

    + for var in vars: [var] ⊆ [lhs]

- `lhs = $select op var2 var3`

    + [var2] ⊆ [lhs]
    + [var3] ⊆ [lhs]

- `lhs = $alloc`

    + ref(const(abstract(here)), [abstract(here)]) ⊆ [lhs]

- `lhs = $addrof var`

    + ref(const(var), [var]) ⊆ [lhs]

- `lhs = $gep src_ptr op`

    + [src_ptr] ⊆ [lhs]

    + note that we're being 'array-insensitive', which is common in these kinds of analyses

- `lhs = $load src_ptr`

    + ref^-1([src_ptr]) ⊆ [lhs]

- `$store dst_ptr var`

    + [var] ⊆ ref^-1([dst_ptr])

- we can ignore:

    + `$arith`, `$cmp` (because they don't affect pointer values)

    + `$jump`, `$branch` (because the analysis is flow-insensitive)

    + `$call`, `$icall`, `$ret` (because we're not considering function calls for now)

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

  FIXME: ADD TO ONENOTE

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

- our final analysis will be _interprocedural_, that is, we'll track how information flows back and forth between functions. this means we need to add constraint generation for $call (we'll ignore indirect calls for now).

    + we'll also ignore any $call instructions to an externally-defined function (i.e., one that is not defined in the program itself). these come up often in program analysis, and the typical method to handle them is to create _stub_ functions that approximate any effect the function might have wrt to the analysis being done.

- let the notation `retval(func)` mean to the variable operand of the $ret instruction inside function `func` (undefined if the operand is not a variable or if there is more than one $ret instruction).

- `lhs = $call <function>(args...)`

    + if `<function>` is not defined in the program, ignore this instruction

    + if lhs is pointer-typed: [retval(`<function>`)] ⊆ [lhs]

    + for arg in args if arg is pointer-typed: [arg] ⊆ [parameter] s.t. parameter is the corresponding parameter of `<function>`.

- these constraints account for pointer-related information being passed to the callee via the call arguments and back from the callee via the return instruction.

## adding indirect calls

- these will be trickier to deal with. we need to create a constructor for each address-taken function type to hold the parameters values and return value, then create the proper constraints during generation so make sure that things line up properly.

- we'll start with an example:

  FIXME: ADD TO ONENOTE

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
