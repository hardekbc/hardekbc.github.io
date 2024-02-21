# WINTER 2024
## reminders for myself

- when going over analysis details in lecture, make it an exercise first then go over it myself (something to make the lecture more interactive and make the students think)

- remind students that they can answer a lot of their own questions about output format and analysis behavior for the assignments by using the solution that i provide

- if i need to update my solution on CSIL, remember to tell students that if they've copied the solution to their local machine then they need to re-copy it [this doesn't apply if i'm using `chmod =x` on the CSIL solution]

## lecture timing

- week 1.1: through first half of `intro to dfa` -> `the basics` -> `high-level idea`; stopped 20 minutes early
- week 1.2: through `intro to dfa` -> `abtract semantics`; stopped 10 minutes early
- week 2.1: through `defining...integer-based analysis` -> `MFP algorithm`
- week 2.2: through all of `defining...integer-based analysis` (had to skim the examples)
- week 3.1: through `second-order DFA` -> `reaching definitions` -> `abstract domain`
- week 3.2: through `second-order DFA` -> `reaching definitions`; stopped 20 minutes early
- week 4.1: through `second-order DFA` -> `control analysis`; stopped 10 minutes early
- week 4.2: through `widening redux`
- week 5.1: through `practice designing DFA` (skipped abstract semantics); stopped 10 minutes early; remote due to weather
- week 5.2: halfway through `set constraint-based analysis` -> `solving inclusion constraints` -> `solving the constraint graph`
- week 6.1: through `andersen-style pointer analysis` -> `no structs or calls` -> `setup`; stopped 10 minutes early
- week 6.2: through `andersen-style pointer analysis`
- week 7.1: through `program slicing`; stopped 10 minutes early
- week 7.2: ???
- week 8.1: ???
- week 8.2: ???
- week 9.1: ???
- week 9.2: ???
- week 10.1: ???
- week 10.2: ???

## assignment timing

- week 1.1: assign-0 out (parsing LIR, not graded)

- week 2.1: assign-1 out (constants, intervals)

- week 4.1: assign-1 in, assign-2 out (reaching defs no pointer info, control analysis)

- week 6.1: assign-2 in, assign-3 out (andersen-style pointer analysis)

- week 8.1: assign-3 in, assign-4 out (slicing: reaching defs with pointer info, pdg)

- week 9.2: assign-4 in, assign-5 out (context-sensitive taint analysis)

- finals: assign-5 in

## thoughts for future course offerings
### lectures

- the initial example for sign analysis (`intro to DFA` -> `the basics` -> `high-level example`) confuses students because it refines the abstract values based on branch conditions and recognizes arithmetic identities like `x - x = 0`; i need to rewrite it to remove these elements and make it just like the version described later

- dealing with extern function calls:

    - TODO: update lecture notes: say we assume the extern function's internals have no effect on the analysis (if they did we would stub them), so we deal with the return value and nothing else

    - TODO: also modify my implementations to be consistent: some treat externs as having the same possible effects as internal function calls, some ignore externs altogether, and some treat them as like internal calls except they can't access globals

- we could also make things simpler by not using globals (except for the global function pointers needed for indirect calls, which we can just say to ignore); this may be going too far though (and this would require us to change the official analysis implementations to treat the global function pointers specially, to conform to the student solutions)

- there's a lot of redundancy between `intro to DFA` -> `the basics` and `intro to DFA` -> {`abstract domains`, `abstract semantics`}, i should collapse these together

- in `intro to DFA` -> `abstract semantics` i had the students fill in the tables for addition and less-than, which worked great but came really late in the lecture; when i integrate the sections per the above note i should try to move the student interaction part earlier

- in general for `intro to DFA` i feel like i'm throwing a lot of abstract concepts at them, maybe too fast and without sufficient context; is there a way for me to get to the actual analysis process more quickly before going into the ideas of abstract domains, abstract semantics, etc? maybe it would be sufficient to preface this stuff with a concrete program example and set of sign invariants that i want to infer, just to set the stage?

- having struct-type variables (globals, parameters, locals) makes several analyses rather complicated without a lot of benefit: reaching defs (with and without pointers), pointer analysis (constraint generation, specifically), slicing (because it relies on the two previous analyses), and taint analysis. the lecture notes don't handle this case, and neither do the analysis implementations. for now my solution is to just assume there are no struct-type variables, but i need to think whether this is a long-term solution

    - the basic difficulty is that any def/use of the struct-type variable should also be a def/use of its fields---this is possible, it just makes the analyses complicated to explain and more difficult for students

- for `intro to widening`:

    - i say to use it whenever we're propagating to a loop header, but really to be more precise we should use it only when propagating along a back-edge---the extra complexity probably isn't worth it though; think about whether i want to do this

### assignments

- maybe only give them the JSON format instead of giving them a choice?

    - TODO: check student submissions and see how many ended up using the LIR format directly and how many used the JSON version

- try to integrate all the autograders into one, or at least have a single library that they all call into

- assignment 2: because the test programs are generated from cflat there will always be global function pointers in the lir because they're automatically inserted during lowering; this makes the "no global" test suites kind of pointless (they just refrain from adding explicit globals to the generated cflat program)

    - if there are no indirect calls it would be nice to remove them so there are no globals period (this impacts the handling of function calls---since there are always globals, there are always def-use chains between calls)

    - or change the lowerer so that the global function pointers are only generated when they are needed

    - this is done in an ad-hoc way for assignment 3 by removing global pointers after the fact; maybe just backport this to assignment 2

- assignments 2--5: avoid having struct-type variables in the assignment tests; the valid cflat program generator can be tuned to not have struct-typed variables pretty easily, but when lowering to lir they can still be introduced so we need to apply a post-lowering filter to eliminate them from the test cases

    - or modify ast_gen.rs to prevent generating struct vars; see the ast_gen.rs in assign-3/test-gen for ideas

- develop tools for creating interesting test suites:

    - manually create mutants of the analysis, then a tool that creates random valid programs and runs them on the real solution and each mutant, saving the programs that kill at least one mutant; keep going until there are a minimum number of test cases that kill each mutant

    - create a list of properties for test cases and then a tool that creates random valid programs and filters out those that don't have interesting

### additional materials

- create a tool to automatically log an analysis execution and play it back for the students, in order to more easily go through examples in lecture

- see farrago issue #77 for list of additional analyses and analysis types that we could cover

- constraint solver optimizations (i cover this extremely briefly, but it seems like there's time to make a whole lecture around it and insert it right after the andersen-style pointer analysis section)

- type inference using equality-based analysis

- abstract interpretation (semantics, galois connections, soundness)

# admin

## class info

- no background in program analysis is expected or required

- no textbook (there are a couple of texts out there, but none that are good for teaching from); you'll need to heavily rely on your notes from these lectures

- please ask questions during lecture! this lets me know that you're engaged with the material and gives me feedback about what i need to explain better

    - this material is mostly cumulative; if you fall behind it will snowball...it's up to you to not let that happen (for example, by stopping me and asking questions)

- class slack workspace

    - should have gotten an invite if registered, otherwise send me an email

    - all communication (including assignments) will happen on slack

    - feel free to ask and answer each other's questions

- office hours: after lectures or by appointment

## assessment

- no exams, just assignments

- 1--2 person teams

- all will be programming-based

    - use any language you want

    - has to be something i can get running on the ubuntu 22.04 docker container used by gradescope

    - c++, python, java supported by default

- 1 ungraded pseudo-assignment, out today

    - preparing the infrastructure you'll need for all future assignments

    - i won't even check, but you'll need to do it at some point; easiest to get it out of the way now

    - feel free to share implementation between groups using the same implementation language (just for this assignment, not the rest)

- 5 graded regular programming assignments

    - each out of 100 points, weighted equally

    - lowest automatically dropped

        - use this judiciously

    - late turn-in allowed up to 5 days, but -11 points (highest grade is B+)

        - caveat: last assignment due at end of finals, no late days for that due to campus grading deadline

# what is program analysis

- fundamental goal: compute invariants about program behavior, i.e., "what must definitely happen (or definitely not happen) when this program is executed"

    - what is `program behavior`? for now, think of it roughly as "execution trace": the sequence of instructions executed, along with snapshots of memory contents at each instruction; given some input, the program's resulting execution trace is its behavior on that input

    - each input to a program will result in a different behavior (i.e., execution trace)

- examples: say we have a program that sits on the server side of a web application; it accepts input from the user, consults a database backend, then sends output back to the user. what questions might we want to ask about that program's behavior?

    + compiler optimization

        - is the value of a variable constant over all executions for a given expression? (constant propagation)

        ```
        x = 42
        y = x + 2
        z = y
        ```

        - is the result of this code snippet inside a loop always the same for every iteration? (loop invariant code motion)

        ```
        do {
            x = y + z
            a += x
            i++
        }
        while (i < n)
        ```

    + automatic parallelization

        - are two pieces of the program independent so that we can safely run them in parallel?

    + security

        - can user input make its way to the SQL commands being sent to the database without being sanitized?

        - does the sanitization function actually prevent all malicious input?

    + bug checking

        - can the program possibly raise an uncaught exception (e.g., array out of bounds, or division by zero)?

        - in languages like C: can this program exhibit undefined behavior? e.g., integer overflow or pointer arithmetic past the end of an object

        - are two pieces of the program that are being run in parallel actually independent, or at least properly synchronized?

    + program understanding and debugging

        - if i change this line of code, what other code can be affected by the change?

        - given a specific line of code (e.g., where a bug has manifested), what other code can affect the result at this line?

    + and many more...

- let's look at this goal from a 10,000 foot view

    + examples of program behavior [inputs: (1, 2); (2, 1); (1, 1)]

        ```
         1. x = input()
         2. y = input()
         4. if (x < y) {
         5.   diff = y - x
         6. }
         7. else {
         8.   diff = x - y
         9. }
        10. output(diff)
        ```

    + [give some examples of false behaviors: (1, 2) that behaves like (2, 1); (1, 1) that behaves like (1, 2)]

    + [draw a circle] for a given program let this represent the set of all possible true behaviors; anything outside this set is a false behavior

        - note that the set of true behaviors is infinite, as is the set of false behaviors

    + if we inspect every element of this set and find that something is true of all of them, then that is a program invariant. what are some invariants of the example program?

        - line 5: diff is positive
        - line 10: diff is non-negative

    + so: compute set of all possible true behaviors, then inspect all the elements of the set to determine invariants...right?

    + the fly in the ointment: computing this set is undecidable, i.e., it's provably impossible to do what we want to do. of course, we don't give up---we do what we usually do when we need to solve an unsolvable problem: we approximate

- there are three strategies for approximating the ideal:

    + [draw bigger circle] over-approximate the set of possible behaviors
    
        - guarantee: all true behaviors are in this set, but it contains some false behaviors too (and we can't tell which are which)

        - if something is invariant over all members of the set, then it must be an invariant of all the true behaviors, and thus a true invariant

        - if something is _not_ invariant over all members of the set, then we don't know if it's a true invariant or not

        - we're getting a one-way guarantee: either something is definite a true invariant, or we don't know

        - example: suppose for the above program we have all the true behaviors, plus the false behaviors that have x == y but we take the true branch. then we can't derive the invariant that diff is positive in the true branch, but we can still derive the invariant that diff is non-negative at the output.

    + [draw smaller circle] under-approximate the set of possible behaviors
    
        - guarantee: all behaviors in this set are true behaviors, but not all true behaviors are in the set

        - if something is invariant over all members of the set, then we don't know if it's a true invariant or not

        - if something is _not_ invariant over all members of the set, then it is definitely not a true invariant

        - we're getting the opposite one-way guarantee: either something is definitely not an invariant, or we don't know

        - example: suppose for the above program that we only have behaviors for x, y \in [0--10]. then we can't say for sure that diff is always non-negative at the output, but we can say for certain that it isn't always positive (because we have true behaviors where it's 0).

    + [draw an off-set circle] neither over- nor under-approximate
    
        - no guarantees at all

        - why do this? basically to make the analysis simpler and easier to implement and (hopefully) allow it to be more precise

        - for example, we could make some assumptions that technically aren't guaranteed to be true, but we happen to know that for almost all real programs will be true

        - this strategy is only useful for applications where we don't need correctness guarantees (e.g., bug-finding); for optimization (as one example) it would be a terrible strategy

- all program analyses are trying to compute program invariants via some kind of approximation. the art of analysis design and implementation is to balance guarantees, precision, and performance

    + guarantees: in some cases we need to maintain a guarantee (either over- or under-approximation), which can make the analysis much more complex and expensive; example: compiler optimizations must have guarantees or they can break the program being compiled

    + precision: how close the approximation is to the real set of true behaviors. we can get arbitrarily close, but at the cost of getting arbitrarily more expensive. less precision is less expensive, but also less useful.

    + performance: if we get really precise answers but it takes centuries to get them, then practically speaking it isn't helpful.

    + so: we need to design and implement an analysis that gives us the desired guarantee, is precise enough to provide useful answers, and performant enough to give us the answers is a useful amount of time/resources. this is an extremely hard problem in general.

- there are many different approaches to program analysis (literally thousands of papers since the beginning of CS as a discipline); we'll be looking at two of the major and most common approaches: dataflow analysis (DFA) and set constraint-based analysis

    + there are other approaches (which you'll see if you look at the program analysis literature), but probably 90-odd percent of the program analyses used in the real world are one of these two

    + we'll be focusing specifically on over-approximations (also called _sound_ analysis)

# the language we're analyzing

- in order to study program analysis we need programs to analyze, and these programs must be written in some programming language

- i want to show that this material is applicable to the real world, but i don't want to overwhelm you with the full complexities of a real PL

    + i also don't want to specialize things too much to a particular PL and/or infrastructure; this course is teaching the fundamentals of how to analyze _any_ language using _any_ infrastructure

- compromise: we'll analyze a language that's basically a well-typed subset of C

    + datatypes:

        + integers
        + pointers
        + structs
        + [no floats, unsigned ints, unions]

    + control:

        + conditionals
        + loops
        + functions

    + require programs to be well-typed

    + notice that structs + pointers + functions (including function pointers) are the building blocks of object-oriented and functional programming languages

- in practice we usually don't analyze source code directly, but instead some intermediate representation (like in a compiler)

    + our IR is based on LLVM, but LLVM itself is more complex than we need

    + i've designed a "stripped-down" version of LLVM IR, which is what we'll use in this class

- example cflat program: [show `example.cb` (should be in one-note)]

- corresponding lir program: [show `example.lir` (should be in one-note); CFG format explained soon (intro to DFA)]

# intro to dataflow analysis (DFA)
## the basics
### high-level idea

- we'll begin with the high-level idea of DFA, hand-wavy and without low-level details, just enough to give you some intuition about how it works

    + later we'll define some specific analyses in detail (like the ones you'll implement for homework), and also go through the math behind DFA that makes it all work.

    + we'll limit ourselves for now to intraprocedural analysis (i.e., one function at a time) and treat function calls and pointers conservatively (i.e., we won't try to track information about them at all)

    + so this means: a single function, only tracking integer values and variables

    + we'll lift these restrictions later and show how we can deal with each of these language features using DFA

- a bit of history: kildall and original motivation to replace ad-hoc error-prone compiler analyses and optimizations with a general framework

- you'll notice that we're going to be doing a fair amount of handwaving in terms of soundness guarantees (i.e., relating the analysis to actual program behavior)

    + that is characteristic of DFA; it's OK with just eye-balling correctness and doesn't usually care about actually proving correctness (that's one reason it's more popular than abstract interpretation amongst non-experts)

- very high-level idea behind DFA:

    + replace concrete values (e.g., actual integers) with abstract values that over-approximate the concrete values (e.g., signs)

    + replace concrete semantics (e.g., arithmetic on integers) with abstract semantics (e.g., arithmetic on signs)

    + "execute" program on abstract values to over-approximate concrete behaviors

### high-level example: sign analysis (i.e., 0/positive/negative)

- there are infinite possible concrete integers (assuming arbitrary precision integers); we'll approximate them using sets of integers corresponding to their sign (zero, positive, or negative)

- abstract values:

    + 0: {0}
    + pos: {n | n > 0}
    + neg: {n | n < 0}
    + bottom (undefined): {}
    + top (any value): 𝐍

    + notice that these form a partial order based on subset, corresponding to the precision of the abstract value

    + also notice that we could have included abstract values for "non-negative" and "non-positive"
    
        - doing so would make the analysis more precise, but also more expensive

        - defining the set of abstract values is part of analysis design

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
    x = input(0, 100)  <-- x is top
    if (x > 0) {       <-- x is pos
      y = x * x        <-- y is pos
      z = 1 / y        <-- z is top (because could be 0 if y > 1)
    }
    else {             <-- x is 0
      y = x - x        <-- y is 0
      z = 1 / y        <-- division by 0
    }
    ```

- now let's get a little bit more in-depth (though still high-level)

## control-flow graph (CFG)
### basic idea

- DFA was originally defined for flowchart-style programs with explicit and obvious control-flow; thus, it is based on the idea of a 'control-flow graph' (CFG, which is confusing if you're also talking about grammars). a CFG is just a representation of a program's possible execution paths.

- a CFG is a graph whose nodes are basic blocks and whose edges represent control-flow

- a basic block is a linear sequence of instructions s.t. (1) it can only be entered at the beginning, and (2) can only be left at the end; that is, there is no way to jump into the middle of a block or exit it early

    + a block will always begin with a label that can be jumped to and end in a terminal instruction that transfers control to another block (jump, branch, internal calls) or leaves the current function (ret)

    + usually calls aren't considered terminal instructions because control returns after the call is done, but it will be convenient for us to think of them as terminal when we look at interprocedural analysis

    + calls to extern functions aren't terminals though, since we can't analyze them at all

- the usual assumption (and ours for LIR programs) is that for each function there is a single entry basic block and a single exit basic block

    + if this isn't true (e.g., there are multiple return instructions) it's easy to massage the CFG to make it true (e.g., replace all the return instructions with jumps to a new basic block that contains a single return instruction); this has already been done for LIR programs

- we'll look at some examples of translating a program into its CFG, but for your assignments this will have already been done---a LIR program is already in the correct format

### EXAMPLE 1

  ```
  let x:int = input(), y:int = 0, z:int = 0;
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
  bb1: x = $call_ext input()
       y = $copy 0
       z = $copy 0
       tmp = $cmp neq x 0
       $branch tmp bb2 bb3

  bb2: x = $copy 3
       y = $copy 2
       $jump bb4

  bb3: x = $copy 2
       y = $copy 3
       $jump bb4

  bb4: z = $arith add x y
       $ret z
  ```

### EXAMPLE 2

  ```
  let x:int = input(), y:int = input(), z:int = input();
  while (z != 0) {
    x = x + 1;
    y = y + 2;
    z = x / y;
  }
  return x;
  ```

  CFG (draw):

  ```
  bb1: x = $call_ext input()
       y = $call_ext input()
       z = $call_ext input()
       $jump bb2

  bb2: tmp = $cmp neq z 0
       $branch tmp bb3 bb4

  bb3: x = $arith add x 1
       y = $arith add y 2
       z = $arith div x y
       $jump bb2

  bb4: $ret x
  ```

### EXAMPLE 3

  ```
  let x:int = input(), y:int = input(), z:int = input();
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
  bb1: x = $call_ext input()
       y = $call_ext input()
       z = $call_ext input()
       $jump bb2

  bb2: tmp1 = $cmp neq z 0
       $branch tmp1 bb3 bb6

  bb3: tmp2 = $cmp lte y x
       $branch tmp2 bb4 bb5

  bb4: y = $arith add y 2
       z = $arith div x y
       $jump bb3

  bb5: x = $arith add x 1
       $jump bb2

  bb6: $ret x
  ```

## abstract domains

- RECAP: a DFA is an abstract domain + abstract semantics + abstract execution, performed on a CFG. we'll drill down on each of these in turn.

- an abstract domain is a set of abstract values, where an abstract value represents a set of possible concrete values (i.e., actual values from executing the program) 

    - an abstract value represents a possible solution to our analysis; in other words, our analysis will result in some answer that is contained in the abstract domain that we define

    - it represents a set of concrete values because we're over-approximating: for a sound analysis, all actual concrete values will belong to that set but there may be extra values as well

- the main source of undecidability is infinite domains (e.g., the integer domain)---this gives us an infinite space of behaviors

    - we need to constrain the domain to be finite, BUT...
    
    - at the same time, we need to over-approximate the actual behaviors
    
    - this means our finite 'abstract' domain must over-approximate the infinite 'concrete' domain; how?

- the usual method is to partition the infinite domain into a finite number of partitions. examples for the integer domain:

    + parity: 𝐍ᵉ, 𝐍ᵒ
    + sign: 𝐍⁺, 0, 𝐍⁻
    + [draw these domains out, but not the full lattice just the integer partitions]

- the abstract domain that we define (i.e., the way that we partition the concrete domain, like the set of integers) defines the analysis that we're creating

    + parity abstract domain: parity analysis
    + sign abstract domain: sign analysis
    + etc...

- note that the precision of the answer that we get from the analysis is bounded by the precision of the abstract domain we use. also note that different abstract domains can be non-comparable in terms of precision (as are parity and signedness)

    + there is a whole spectrum of possible abstract domains ranging from very imprecise but very fast to extremely precise but intractable, and many points in-between; there has been a lot of work in the field of program analysis designing many different abstract domains with different tradeoffs

- what if we don't know which partition a value fits into? for example, `x = input()`: we don't know the value of `x` at all

    + we also need abstract values that can represent unknown concrete values that don't fit into a single partition

    + we need at least an abstract value to represent things that don't have any value yet (bottom, ⊥) and one to represent things that could have any possible value (top, ⊤)

    + we can also have domains that represent different levels of precision (e.g., the two possible sign abstract domains)

    + [draw the full lattices for parity and sign]

- notice that these different sets are related via subset, with more precise abstract values being subsets of less precise abstract values.

    + [show this for parity and signedness lattices]

    + sometimes we'll need to "merge" or "join" abstract values in order to find the most precise value that over-approximates the input values---we'll define this operation more precisely later, but in general it just means find the smallest superset that contains both inputs

- we also need a way to map concrete values into the corresponding abstract values, and it can be useful mathematically to have the reverse mapping too:

    + α: Int -> Int#

        - α for sign: α(x) = pos if x is positive, neg is x is negative, zero if x is 0
        - α for parity: α(x) = Even if x is even, Odd if x is odd

    + γ: Int# -> 𝒫(Int)

        - γ for sign: γ(x) = { n | n > 0 } if x is pos, { n | n < 0 } if x is neg, { 0 } if x is zero
        - γ for parity: γ(x) = { n | n % 2 = 0 } if x is Even, { n | n % 2 = 1 } if x is Odd

    + note that γ(α(v)) should be a set that contains v; that's what makes the abstract domain an overapproximation
    
        - example for signedness: γ(α(1)) = 𝐍⁺ and 1 ⊆ 𝐍⁺

    + also note that γ usually isn't useable in practice because it yields an infinite set; we'll see that it can still be useful later when proving properties of an analysis

- later we'll see how we can relax the requirement for finite abstract domains while still remaining decidable

    + it will require some math to guarantee it will work correctly; we'll have to formalize this informal notion of abstract domain and give it some restrictions

- in general we need to define abstract domains for every kind of concrete value that we're interested in (integers, pointers, etc)

## abstract transfer functions (aka abstract semantics)
### basic idea

- now that we're operating on abstract values instead of concrete values, we need to know how concrete operators map to abstract operators

    + when we see, e.g., `x + y` in a program, we're no longer operating on integers but on partitions of integers
    
    + the abstract versions of concrete operations are often called 'abstract transfer functions'

- for integer abstractions the most relevant semantics to abstract are arithmetic and comparison

    + we'll look at some examples for our parity and sign analyses

- [have students try to come up with transfer functions below first]

### parity

- addition

    ```
        ⊥ | E | O | ⊤
      +--------------
    ⊥ | ⊥   ⊥   ⊥   ⊥
    E | ⊥   E   O   ⊤
    O | ⊥   O   E   ⊤
    ⊤ | ⊥   ⊤   ⊤   ⊤
    ```

- less-than (result of comparison is either 0 [false] or 1 [true])

    ```
        ⊥ | E | O | ⊤
      +--------------
    ⊥ | ⊥   ⊥   ⊥   ⊥
    E | ⊥   ⊤   ⊤   ⊤
    O | ⊥   ⊤   ⊤   ⊤
    ⊤ | ⊥   ⊤   ⊤   ⊤
    ```

### sign

- addition

    ```
        ⊥ | + | 0 | - | ⊤
      +------------------
    ⊥ | ⊥   ⊥   ⊥   ⊥   ⊥
    + | ⊥   +   +   ⊤   ⊤
    0 | ⊥   +   0   -   ⊤
    - | ⊥   ⊤   -   -   ⊤
    ⊤ | ⊥   ⊤   ⊤   ⊤   ⊤
    ```

- less-than (result of comparison is either 0 [false] or 1 [true])

    ```
        ⊥ | + | 0 | - | ⊤
      +------------------
    ⊥ | ⊥   ⊥   ⊥   ⊥   ⊥
    + | ⊥   T   0   0   ⊤
    0 | ⊥   +   0   0   ⊤
    - | ⊥   +   +   ⊤   ⊤
    ⊤ | ⊥   ⊤   ⊤   ⊤   ⊤
    ```

### wrap-up

- for both of these examples we can define the abstract transfer functions using tables because the abstract domains are finite; this isn't always the case (but again, to guarantee that infinite abstract domains still result in decidable analyses requires some math we'll get into later).

- we could combine these abstract domains if we want: an abstract value could be a pair `(parity, sign)`, with the obvious abstract semantics. this is generally true of all abstract domains, that they can be combined to get more precise domains

- note that again we're being hand-wavy, we can't just make these transfer functions be anything we want and still ensure the analysis works---soon we'll need to formalize this notion of transfer function and give some restrictions to make sure they'll work properly

    + that's where the math comes in

## abstract execution (MOP vs MFP)
### intro

- given a CFG, abstract domain, and abstract transfer functions, how do we actually analyze a given program?

- essentially we want to "execute" the program, except instead of using concrete values and concrete operations we want to use abstract values and abstract transfer functions

    + because the abstract values and transfer functions over-approximate all possible concrete values and operations, a single abstract execution over-approximates all possible program behaviors

- DFA is inherently _flow-sensitive_: the goal is to compute an abstract value for each variable at each program point (i.e., each node of the CFG)

    + [contrast with flow-insensitive, which we'll see a bit later]

### method 1: MOP

- trace through each execution path in turn, then for each program point find the join of all abstract values computed for that program point along all paths

    + [draw generic CFG example with two diamonds in a row, trace all four paths, then show merge at each basic block]

- at best exponential time complexity, at worst non-termination

- this is the most like actual program execution, but is also not feasible in practice

- gives the best possible precision; serves as the "gold standard" for judging other methods

### method 2: MFP (maximal/minimal fixpoint)

- treat the transfer functions as a system of equations and compute a fixpoint (more particularly, the most precise fixpoint)

    + whether MFP stands for "maximal fixpoint" or "minimal fixpoint" depends on the way we structure the abstract domain; this won't make sense until we cover the math behind DFA, so for now don't worry about it

- fixpoint review:

    + define fixpoint of a function: an input that yields itself.

    + functions can have zero fixpoints, a single fixpoint, multiple but finite fixpoints, or infinite fixpoints.

    + example: f(x) = x²: fixpoints are 0, 1; least fixpoint is 0

- so for MFP, we want to assign abstract values to each variable at each program point s.t. if we execute the abstract semantics on the program, we get the same result

    + and specifically, we want the most precise abstract values for each variable that makes this true, i.e., the 'least fixpoint'

    + we could, for example, assign ⊤ to all variables, but this wouldn't be very useful

    + intuitively, this approach will join the abstract values along different paths _before_ propagating them further, rather than keeping each path distinct and only joining them at the end

    + [take same diamond example as for MOP and show the difference with MFP]

- polynomial time complexity (assuming abstract domain and transfer functions have the correct properties)

- there is a standard worklist algorithm for computing the MFP, which we'll go over shortly

- remember that fixpoints don't necessarily exist (and if they do there isn't necessarily a least fixpoint); later we'll worry about how to guarantee a least fixpoint exists and that we can find it (you guessed it: we need math)

### EXAMPLE

- to get an intuitive understanding of the difference between MOP and MFP, consider the following example

    - since there are no loops we don't actually need a fixpoint algorithm, just execute in topological order of the CFG (i.e., execute every predecessor of a basic block before executing that basic block)

- [show MFP sign analysis; have students do MFP parity analysis; show MOP parity analysis (more precise than MFP parity)]

  ```
  let x:int = 0, y:int = input(), z:int = input();
  if (y != 0) { x = 1; } else { x = 2; }
  if (z != 0) { x = x + x; } else { x = 0; }
  x = x + x;
  return x;
  ```

  CFG (draw):

  ```
  bb1: x = $copy 0
       y = $call input()
       z = $call input()
       tmp1 = $cmp neq y 0
       $branch tmp1 bb2 bb3

  bb2: x = $copy 1
       $jump bb4

  bb3: x = $copy 2
       $jump bb4

  bb4: tmp2 = $cmp neq z 0
       $branch tmp2 bb5 bb6

  bb5: x = $arith add x x
       $jump bb7

  bb6: x = $copy 0
       $jump bb7

  bb7: x = $arith add x x
       $ret x
  ```

- is MFP always less precise than MOP? NO. an analysis is _distributive_ if, for transfer function F, join(F(x), F(y)) = F(join(x, y))

    + in other words, it doesn't matter if we merge abstract values before applying the transfer function or afterwards

    + for distributive analyses MOP = MFP; for non-distributive domains MOP ⊑ MFP (i.e., MOP is more precise)

# defining an intraprocedural, pointer-conservative integer-based analysis
## what we need

- the abstract domain we're using to replace concrete values

- the abstract semantics (aka transfer functions) for each instruction to operate on the abstract domain

- the MFP worklist algorithm (this is always the same for all DFA analyses)

## MFP algorithm

- standard worklist algorithm for all DFA

### setup

- create a map `bb2store` from basic block to abstract store at entry, initially empty

    - this map contains the _entry abstract store_ for that basic block: the abstract values of all variables at the point execution reaches that basic block

    - we need this because for MFP to "execute" a basic block it needs the join of the results of all predecessor basic blocks, so it needs to record that somewhere

- create an initial abstract store that maps integer-typed function parameters and integer-typed globals to ⊤

    - no local has a value yet so they're all ⊥ (i.e., not contained in the store)

    - the parameters could have been passed any value as arguments so they're ⊤

    - globals could have been set to anything by the callers of the function so they're ⊤

- insert a mapping from the entry basic block to the initial abstract store

- create a list `worklist` that will contain basic blocks, initially empty

    - this can be a queue, stack, priority queue, etc; it doesn't matter for correctness though it does affect performance

- insert the entry basic block into `worklist`

- [show the result of all this using `abstract semantics` -> `EXAMPLE 1` below]

### analysis

- while the worklist isn't empty:

    - pop a block from the worklist

    - look up the block's abstract store in `bb2store` and _create a copy_ (we shouldn't update the _entry_ values while processing the block)

    - execute the instructions in the basic block using the abstract semantics, updating the copy of the abstract store as appropriate

    - at the terminal, for each target basic block:

        - join the current abstract store with the entry abstract store of the target (from `bb2store`)

        - if the target's entry store changed, put the target on the worklist

- this loop is the fixpoint computation: as long as there is some basic block whose entry abstract store has changed from the last time it was processed, it will keep going

    - if we've created the abstract domain and abstract semantics correctly, this is guaranteed to terminate

### finishing up

- once the worklist algorithm is done we have a map from each basic block to the entry store for that block

- what we often want is a map from each basic block to the _final_ store for that block

    - we could have kept track of this during the analysis but it would be redundant work, easiest to compute it once at the end

    - just iterate through each basic block, take its entry store, execute the block's instructions to update the store, then save the result

## abstract domain

- sign integer abstract domain: ⊥ <= {pos, neg, zero} <= ⊤

    - ⊥ ⊔ X = X for any X
    - ⊤ ⊔ X = ⊤ for any X
    - X ⊔ X = X for any X
    - pos ⊔ neg, pos ⊔ zero, neg ⊔ zero = ⊤

- parity integer abstract domain: ⊥ <= { Even, Odd } <= ⊤

    - ⊥ ⊔ X = X for any X
    - ⊤ ⊔ X = ⊤ for any X
    - X ⊔ X = X for any X
    - Even ⊔ Odd = ⊤

- constants integer abstract domain: ⊥ <= { ..., -1, 0, 1, ... } <= ⊤

    - ⊥ ⊔ X = X for any X
    - ⊤ ⊔ X = ⊤ for any X
    - X ⊔ X = X for any X
    - n1 ⊔ n2 (s.t. n1 ≠ n2) = ⊤

- pointer abstract domain: since we're handling pointers conservatively we don't need one

- the abstract store will map integer-typed variables to elements of the integer abstract domain

    - to make things a little simpler, we won't include any variables that map to ⊥
    
    - when looking up a variable in the store, if it isn't there then we know the value must be ⊥

    - to join two input stores to get a new store:
    
        - the new store will have all variables that are in either input store
        - if the variable is only in one of the input stores its value will be the same as in that store
        - if the variable is in both stores its value will be the join of the values in the two stores

## abstract semantics

- [have the students try to come up with the abstract transfer functions for the instructions before giving the solution]

### reminder

- we're doing an intraprocedural analysis, so when processing a call we'll conservatively assume that it could have done anything instead of actually analyzing the callee function

    - this also means that we need to treat globals conservatively since they could be modified by other functions that we aren't analyzing (callee or caller)

- we're handling pointers conservatively, so when processing a pointer access we'll assume that it could access anything of the appropriate type instead of actually analyzing what things the pointers may actually point to

- we'll be lifting each of these restrictions later in the quarter

### prep

- compute the set of integer-typed global variables; call this `global_ints`

- compute the set of integer-typed address-taken local variables by inspecting the function for `$addrof` instructions; call this `addrof_ints`

    - `addrof_ints` should also include everything in `global_ints`, because a global could have had its address taken in some other function

### non-pointer, non-call related instructions

- `x = $copy op`

    - if `x` is a pointer then ignore this instruction
    - translate `op` to abstract value using α or store
    - update store to map `x` to result

- `x = $arith <aop> op1 op2`

    - translate `op1`, `op2` to abstract values using α or store
    - apply abstract transfer function (`add` given earlier; rest left as exercise)
    - update store to map `x` to result

- `x = $cmp <rop> op1 op2`

    - if `op1`, `op2` are not integer-typed then set `x` to T
    - otherwise same as `$arith` (`lt` abstract semantics given earlier; rest left as exercise)

- `$jump <bb>`

    - propagate store to `<bb>`

- `$branch op <bb1> <bb2>`

    - translate `op` to abstract value
    - if value is definitely not 0, propagate store only to `<bb1>`
    - if value is definitely 0, propagate store only to `<bb2>`
    - else propagate store to both `<bb1>` and `<bb2>`

### EXAMPLE 1 (using signs)

- [have students work through it first, then go over it]

- cflat

  ```
  fn foo(p:int) -> int {
    let x:int = -2, y:int = 2;
    if p < x {
        p = -p;
        x = -x;
    }
    else if p > y {
        p = p + y;
    }
    return x + y;
  }
  ```

- lir:

  ```
  fn foo(p:int) -> int {
  let _t1:int, _t2:int, _t3:int, _t4:int, _t5:int, _t6:int, _t7:int, x:int, y:int

  entry:
    _t1 = $arith sub 0 2
    x = $copy _t1
    y = $copy 2
    _t2 = $cmp lt p x
    $branch _t2 bb2 bb3

  bb1:
    _t7 = $arith add x y
    $ret _t7

  bb2:
    _t3 = $arith sub 0 p
    p = $copy _t3
    _t4 = $arith sub 0 x
    x = $copy _t4
    $jump bb1

  bb3:
    _t5 = $cmp gt p y
    $branch _t5 bb5 bb4

  bb4:
    $jump bb1

  bb5:
    _t6 = $arith add p y
    p = $copy _t6
    $jump bb4
  }
  ```

### pointer-related instructions

- `x = $load y`

    - if `x` is a pointer then ignore this instruction
    - update store to set `x` to ⊤

- `$store x op`

    - if `op` is a pointer then ignore this instruction
    - translate `op` to abstract value
    - for each variable `v` in `addrof_int`, update store to map `v` to the join of its current value and the value of `op`

- `x = $alloc op [id]`
- `x = $addrof y`
- `x = $gep y op`
- `x = $gfp y field`

    - these always produce pointers, which we aren't tracking so we can ignore these instructions

### EXAMPLE 2 (using signs)

- [have students work through it first, then go over it]

- cflat

  ```
  let g1: int, g2:&int;

  fn foo(p:int, q:&int) -> int {
    let x:int = 0, y:int = -2;
    g1 = 4;
    *q = 12;
    y = *q;
    q = &x;
    return x + y;
  }
  ```

- lir

  ```
  g1:int
  g2:&int

  fn foo(p:int, q:&int) -> int {
  let _t1:int, _t2:int, _t3:&int, _t4:int, x:int, y:int

  entry:
    x = $copy 0
    _t1 = $arith sub 0 2
    y = $copy _t1
    g1 = $copy 4
    $store q 12
    _t2 = $load q
    y = $copy _t2
    _t3 = $addrof x
    q = $copy _t3
    _t4 = $arith add x y
    $ret _t4
  }
  ```

### call-related instructions

- `[x =] $call_ext <id>(op1, ...)`
- `[x =] $call_dir <id>(op1, ...)`
- `[x =] $call_idr fop(op1, ...)`

    - for all `v` in `global_ints`, update store to map `v` to ⊤

    - if `x` is integer-typed, update store to map `x` to ⊤

    - if (1) any global is a pointer that can reach an `int`; OR (2) any argument is a pointer that can reach an `int`: for all `v` in `addrof_ints`, update store to map `v` to ⊤

        - "reach an int" means (1) the basetype is `int`, or (2) the basetype is a struct containing a pointer that reaches an `int`

    - note that we're making an important assumption that external calls are independent, i.e., that they do not influence each other (e.g., by using their own globals that are invisible to us)

        - if we don't make this assumption then, for example, `$call_ext foo(x)` where `x` is a pointer to a local or global could store the value of `x` in an invisible global, then `$call_ext bar()` could modify that local/global even though it didn't get any arguments, or `$call_ext foo(y)` could do the same even though it isn't getting `x` this time---this would force us to be very conservative about handling external calls

        - in the case where the external code really does do something like that, we can keep our analysis sound by stubbing those external functions as internal functions that summarize their behavior wrt the analysis we're implementing (this is a common technique in program analysis)

    - for convenience we treat external calls the same as internal calls (including how they can effect globals) even though they don't have access to the globals; this is correct though conservative

- `$ret [op]`

    - since we're doing intraprocedural analysis we don't care about the caller and so we can ignore this instruction

### EXAMPLE 3 (using signs)

- [have students work through it first, then go over it]

- cflat

  ```
  extern bar(&int) -> int;
  extern baz() -> int;

  let g1:int, g2:&int;

  fn foo(p:int) -> int {
    let x:int = 0, y:int = -2, z:int = 4;
    g2 = &x;
    y = bar(g2);
    x = 0;
    g1 = 12;
    z = baz();
    return x + y + z;
  }
  ```

- lir

  ```
  g1:int
  g2:&int

  extern bar:(&int) -> int
  extern baz:() -> int

  fn foo(p:int) -> int {
  let _t1:int, _t2:&int, _t3:int, _t4:int, _t5:int, _t6:int, x:int, y:int, z:int

  entry:
    x = $copy 0
    _t1 = $arith sub 0 2
    y = $copy _t1
     z = $copy 4
    _t2 = $addrof x
    g2 = $copy _t2
    _t3 = $call_ext bar(g2)
    y = $copy _t3
    x = $copy 0
    g1 = $copy 12
    _t4 = $call_ext baz()
    z = $copy _t4
    _t5 = $arith add x y
    _t6 = $arith add _t5 z
    $ret _t6
  }
  ```


# intro to widening

- given the DFA framework outlined above, we can modularly plug in different abstract domains to get different analyses (e.g., sign, parity, constants, etc); however we can't just use any abstract domain and still guarantee a computable analysis

- example: the integer interval abstract domain

    - elements: `⊥` and `[a, b]` s.t. `a` ∈ 𝐙 ∪ {-∞} and `b` ∈ 𝐙 ∪ {∞} and `a` <= `b` (note that ⊤ = [-∞, ∞])

    - γ(⊥) = {}
    - γ([a, b]) = { n ∈ 𝐙 | n >= a and n <= b }

    - [maybe have the students figure out the operations below]

    - JOIN
    - ⊥ ⊔ X = X ⊔ ⊥ = X
    - [a1, b1] ⊔ [a2, b2] = [min(a1, b1), max(a2, b2)]

    - ADD
    - ⊥ + X = ⊥
    - [a1, b1] + [a2, b2] = [a1 + a2, b1 + b2]

    - LTE
    - ⊥ <= X, X <= ⊥ = ⊥
    - [a1, b1] <= [a2, b2] =
        - [0, 0] if a1 > b2
        - [1, 1] if b1 < a2
        - [0, 1] otherwise

- this is clearly more precise than the constants abstract domain, because it contains the constants abstract domain inside of it ([-1, -1], [0, 0], [1, 1], etc)

- EXAMPLE 1: let's try it out and see what happens

  ```
  let x:int = 0, y:int = input();
  while x <= y {
    x = x+1;
  }
  return x;
  ```

  ```
  entry:
    x = $copy 0
    y = $call_ext input()
    $jump while_hdr

  while_hdr:
    t = $cmp lte x y
    $branch t while_body exit

  while_body:
    x = $arith add x 1
    $jump while_hdr

  exit:
    $ret x
  ```

- the analysis never terminates! what went wrong?

    - this is why we need to study the mathematical structures behind DFA that give us the tools to determine whether a given abstract domain and abstract semantics results in a computable analysis

- there is a way to get around this problem, called _widening_

    - if we know that an abstract domain isn't computable but it still has certain properties then we can modify the DFA algorithm to make it computable

    - the key ingredient is something called a _widening operator_ `▿` (there may be many possible such operators for a given analysis with different tradeoffs, the analysis designer needs to create or pick one of them)

    - shortly we'll be looking at the math behind it all to see how this works, but for now we'll just see a practical example

- a widening operator for the integer interval abstract domain (one of many possible widening operators)

    - ⊥ ▿ X = X ▿ ⊥ = X
    - [a1, b1] ▿ [a2, b2] = [a3, b3] s.t.
        - a3 = a1 if a1 <= a2, otherwise -∞
        - b3 = b1 if b1 >= b2, otherwise ∞

- the basic idea is that we use the widening operator instead of join to combine abstract stores

    - this is overly pessimistic and can lose precision unnecessarily because widening is less precise than join

    - where can non-termination actually happen? loops!

    - only apply the widening operator at loop headers, otherwise use join

- [redo previous example, using the widening operator at the loop header]

- how do we identify loop headers in the CFG?

    - just do a post-order labeling on the CFG; if you ever reach a node that you have already visited but not yet labeled then it's a loop header

    - [demo using the previous example's CFG]

- IMPORTANT NOTE: this widening operator isn't commutative, so it matters what order we visit the basic blocks in during out fixpoint computation

    - example: two edges into a loop header, one that propagates `a -> [0, 0]` and one `a -> [1, 1]`; depending on which one propagates first we'll get either `[0, ∞)` or `(-∞, 1]`

    - the assignment description tells you what you need to ensure you get the same solution as the autograder

- we'll look at exactly when we need widening operators and what makes a valid widening operator soon when we look at the math behind DFA

- EXAMPLE 2 (exercise for students)

  ```
  let x:int = 0, y:int = 10, z:int = input();
  while x - y < z {
    if x < z {
        x = x + y;
    } else {
        y = y - 1;
    }
  }
  return x + y
  ```

  ```
  let x:int, y:int, z:int, t1:int, t2:int, t3:int, t4:int

  entry:
    x = $copy 0
    y = $copy 10
    z = $call_ext input()
    $jump loop_hdr

  loop_hdr:
    t1 = $arith sub x y
    t2 = $cmp lt t1 z
    $branch t2 if_hdr exit

  if_hdr:
    t3 = $cmp lt x z
    $branch t3 if_true if_false

  if_true:
    x = $arith add x y
    $jump if_end

  if_false:
    y = $arith sub y 1
    $jump if_end

  if_end:
    $jump loop_hdr

  exit:
    t4 = $arith add x y
    $ret t4
  ```

# second-order DFA
## basic idea

- we've been looking at so-called 'first-order analysis': analyses where we care about _values_ at a particular point in the program. a 'second-order analysis', in contrast, asks questions about program _paths_ (i.e., execution traces), not individual program states

    - example: a taint analysis might ask 'how did this string get to this sensitive system call? in particular, did it get here from user input without being sanitized?'
    
    - this isn't a question we can answer just by looking at the value of a variable at a particular point---we have to know the execution history that brought the value to that point.

- how do we answer these questions?

    - by abstracting program _traces_ just like we abstract program _values_

    - instead of using an abstract value to over-approximate the set of concrete values a variable might have, we use abstract values to over-approximate the set of program paths that the program could take to reach a given location

- the easiest way to understand this idea is to go through some examples

## first analysis example: reaching definitions
### intro

- problem statement: for each "use" of a variable, what are the set of "defs" of that variable that may "reach" this program point?

    - "use" means that we require the variable's value

    - "def" means definition, i.e., assignment

    - "reach" means there is some path from the def to the use that does not _definitely_ redefine that variable

- this information allows us to trace how values flow through the program and is a useful foundation for more elaborate analyses

    - we wouldn't compute reaching definitions for its own sake, we compute it for the sake of some follow-on analysis or transformation

    - we'll see several examples of such follow-on useful analyses later in the quarter

- notice that this is a "may" analysis: we're asking if it's possible for a def to reach a given use (and over-approximating the exact answer)

- as always for DFA, we need an abstract domain, abstract semantics, and abstract execution (which is the same MFP worklist algorithm as before)

### EXAMPLE 1

- [go through example intuitively, without referring to abstract domain or semantics]

    - for each use of a variable, what defs reach it?

  ```
  let x:int = 4, y:int = 6, z:int = input();
  if (z) { x = 2; } else { x = 3; y = 3; }
  z := x+y;
  return z;
  ```

  ```
  bb1: x = $copy 4
       y = $copy 6
       z = $call input()
       tmp = $cmp neq z 0
       $branch tmp if_true if_false

  if_true:
       x = $copy 2
       $jump if_end

  if_false:
      x = $copy 3
      y = $copy 3
      $jump if_end

  if_end:
      z = $arith add x y
      $ret z
  ```

### abstract domain

- remember that our abstract domain is a set of abstract values that represent possible answers for our analysis

    - for reaching defs, an answer to "what defs reach this use?" is a set of program points where the defs happen

    - we can represent a program point as `<basic block label>`.{`<instruction index>`, `term`}; this uniquely identifies a particular point in a function

    - [give some examples from EXAMPLE 1]

- more precisely, our abstract values represent "executation traces"---specifically, traces from the beginning of the function to the current point following a path along which a particular variable has (1) been defined at program point PP; and (2) has not been redefined at any point between PP and now

    - example: focusing on variable `x` in the example program and the point `if_end.0`, the abstract value `x -> {if_true.0, if_false.0}` abstracts the set of traces (starting from the beginning of the function) that define variable `x` with no intervening redefinitions before getting to `if_end.0`

    - example: focusing on variable `y` in the example program and the point `if_end.0`, the abstract value `x -> {bb1.2, if_false.1}` abstracts the set of traces (starting from the beginning of the function) that define variable `y` with no intervening redefinitions before getting to `if_end.0`

    - therefore, an abstract value for some variable `v` is a set of program points containing definitions of `v`

- so an abstract value (i.e., an element of the abstract domain) is a set of program points; what is the entire domain?

- remember that we need to create an abstract domain s.t. there is an abstract value for all possible concrete answers

    - what is the least precise (most over-approximate) abstract value? the set of all program points

    - what is the most precise (most under-approximate) abstract value? the empty set

    - what is the entire abstract domain? the powerset of the set of all program points, ordered via subset

- L♯ = (𝒫(PP), ⊆), ⊔ = ∪

    - the elements of the abstract domain are sets of program points

    - they are ordered via subset

    - we can join abstract values using set union

- finally, the abstract store will map objects (variables and allocated objects) to abstract values

    - previously for the first-order integer analyses we only cared about variables, but now we care about defs/uses of anything, not just variables

    - things allocated on the heap can be def'ed/used via pointers, so we need to keep track of them

    - we have a problem: things allocated on the heap don't have names, we can't refer to them directly

        - solution: give them fake names so that our analysis can refer to them

        - for each type of object on the heap, we'll create a fake variable that represents all heap objects of that type (we're still being very conservative with pointers and function calls)

        - like before we'll have a set of address-taken variables `addr_taken` that includes the operands of `$addrof` and also globals, though now it will be all such operands and all globals, not just `int`

        - here's how we create the fake variables the represent objects in the heap:

            - for type τ, let `reachable_types(τ)` be the set of types 'reachable' via pointer dereferences and/or struct field accesses from τ, excluding function types

                - we don't include function types (e.g., `(int) -> int)`) because functions aren't allocated on the heap
                - example:

>                 struct foo {
>                   f1: &bar
>                   f2: &int
>                 }
>
>                 struct bar {
>                   f3: &(int) -> int
>                   f4: &&int
>                 }
>
>                 g: &foo
>
>                 // reachable_types(&foo) = { foo, &bar, bar, &&int, &int, int, &(int)->int }

            - let `PTRS` = all pointer-typed globals, parameters, and locals of the function being analyzed, and `PTRS_τ` be the set of types of these variables

            - for `τ ∈ reachable_types(PTRS_τ)` create a fake variable to represent all heap-allocated objects of type `τ`, then put all the fake variables in `addr_taken`

    - later we'll see a version of reaching defs that treats pointers and function calls much more precisely

### abstract semantics [assuming no variables with struct type]

- now we need to create abstract semantics to replace the program's concrete semantics, but in this case we don't care about the _values_ of the variables, just whether they are being defined or not by a given instruction

    - our abstract semantics will update the abstract store, mapping each variable to its set of reaching defs at a particular point in the program

    - our final solution will map program points containing uses to the combined set of reaching defs for all the uses; we can either do this during the analysis (but this will be a lot of redundant work) or just do it once when the analysis is finished

    - [example: show abstract stores and final solution for EXAMPLE 1]

- we don't need abstract semantics for arithmetic operations, relational comparisons, etc, just for variable definition (assignments and stores)

- let the abstract store be σ, pp represent a program point, DEF be the set of definitions for an instruction, and USE be the set of uses for an instruction

    - the set USE won't be needed during the analysis itself, only afterwards when we translate the analysis results into a final solution

- [do the first one below as an example, then go through each of them but ask the students for the answer before giving the official answer]

- pp: `x = {$alloc, $arith, $cmp, $copy, $gep, $gfp} <op>...`

    - DEF = `{ x }`
    - USE = `{ <op> | <op> is a variable }`

    - `∀v ∈ USE, soln[pp] = soln[pp] ∪ σ[v]`
    - `σ[x] = { pp }`

- pp: `x = $addrof y`

    - DEF = `{ x }`
    - USE = `{}`

    - `σ[x] = { pp }`

- pp: `x = $load y`

    - DEF = `{ x }`
    - USE = `{ y } ∪ { var ∈ addr_taken | type(var) = type(x) }`

    - `∀v ∈ USE, soln[pp] = soln[pp] ∪ σ[v]`
    - `σ[x] = { pp }`

```
    EXAMPLE:

    let a:&int, b:int, c:&int, d:int
    entry:
      a = $addrof b
      c = $alloc 1 [_a1]
      d = $load c  // <-- USE = { c, b, fake_int }
```

- pp: `$store x <op>`

    - DEF = `{ var ∈ addr_taken | type(var) = type(<op>) }`
    - USE = `{ x } ∪ { <op> | <op> is a variable }`

    - `∀v ∈ USE, soln[pp] = soln[pp] ∪ σ[v]`
    - `∀v ∈ DEF, σ[v] = σ[v] ∪ { pp }`

    - [discuss strong update vs weak update]

```
    EXAMPLE:

    let a:int, b:&int, c:int
    entry:
      b = $addrof a
      a = $copy 42
      c = $copy 12
      $store b 6
      $ret x // <-- σ[a] = { entry.1, entry.3 }; σ[c] = { entry.2 }
```

- pp: `[x =] $call_{dir, idr, ext} id/<fp>(<arg_op>...)`

    - SDEF = `{ x }` // <-- strong def if `x` exists, otherwise `{}`
    - WDEF = `{ var ∈ addr_taken | type(var) ∈ reachable_types(<arg_op>...) ∪ reachable_types(<globals>...) } ∪ { <globals>... }` // <-- weak defs
    - USE = `{ <fp> } ∪ { <arg_op> | <arg_op> is a variable } ∪ WDEF`

    - `∀v ∈ USE, soln[pp] = soln[pp] ∪ σ[v]`
    - `∀v ∈ WDEF, σ[v] = σ[v] ∪ { pp }`
    - `σ[x] = { pp }` (if `x` exists)

    - the order here is important, handle the strong def _after_ the weak defs (in case `x` is address-taken and included in the weak defs too)

    - we have to assume that a callee function could define and/or use any variable reachable from its arguments or from a global, and also define/or use any global (again we're treating external calls the same as internal calls, which is overly conservative but convenient)

```
    EXAMPLE:

    let a:int, b:&int, c:&&int
    entry:
      a = $copy 0
      b = $addrof a
      c = $addrof b
      b = $call_dir foo(c) then exit

    exit:
      $ret a // <-- σ[a] = { entry.0, entry.term }, σ[b] = { entry.term }, σ[c] = { entry.2 }
```

- pp: `$jump bb`, `$branch <op> bb1 bb1`, `$ret <op>`

    - DEF = `{}`
    - USE = `{ <op> | <op> is a variable }`

    - `∀v ∈ USE, soln[pp] = soln[pp] ∪ σ[v]`
    - no change to σ

### abstract semantics [adding in variables with struct type---SKIP]

- if a variable is a struct, then defining or using it also defines/uses its fields (which we're representing with the fake variables)

- assume in the following that `x` has a struct type, and let `FLDS(x)` = `{ var ∈ addr_taken | fld ∈ fields(x.type()), type(var) = type(fld), var is a fake variable }`

- pp: `x = $copy y`

    - SDEF = `{ x }`
    - WDEF = `FLDS(x)`
    - USE = `{ y } ∪ FLDS(y)`

- pp: `x = $load y`

    - DEF = `{ x }`
    - WDEF = `FLDS(x)`
    - USE = `{ y } ∪ { var ∈ addr_taken | type(var) = type(x) } ∪ FLDS(x)`

- pp: `$store y x`

    - DEF = `{ var ∈ addr_taken | type(var) = type(x) } ∪ FLDS(x)`
    - USE = `{ x, y } ∪ FLDS(x)`

- pp: `[x =] $call_{dir, idr, ext} id/<fp>(<arg_op>...)`

    - in addition to the previous sets for calls:
    - WDEF ∪= `FLDS(x)`
    - USE ∪= `FLDS(arg)` s.t. `arg` has a struct type

 - pp: `$ret x`

    - DEF = `{}`
    - USE = `{ x } ∪ FLDS(x)`

### abstract execution

- [go through previous example again, this time using MFP worklist algorithm]

- this gives us the reaching defs for every program point, now we need to transform it into the reaching defs for every use

    - just "execute" each basic block one more time, and for each instruction look at what variables are used and look them up in the abstract store to find their reaching defs

    - we end up with a solution mapping a program point (the location of a set of uses) to a set of program points (the locations of the reaching defs for those uses)

### EXAMPLE 2

- [have students do this one on their own first]

```
fn bar(p:int, q:&int) -> int {
  let a:int, b:int, c:&int;

  a = p - *q;
  b = a;
  c = &b;

  while a < p {
    *c = b + a;
    a = a + 1;
  }

  return *c;
}
```

```
fn bar(p:int, q:&int) -> int {
let a:int, b:int, c:&int, _t1:int, _t2:int, _t3:int, _t4:int
entry:
  _t1 = $load q
  a = $arith sub p _t1
  b = $copy a
  c = $addrof b
  $jump while.hdr

while.hdr:
  _t2 = $cmp lt a p
  $branch _t2 while.body exit

while.body:
  _t3 = $arith add b a
  $store c _t3
  a = $arith add a 1
  $jump while.hdr

exit:
  _t4 = $load c
  $ret _t4
}
```

## second analysis example: control analysis
### intro

- the subject of this analysis is control-flow: how the executions of the basic blocks depend on each other

    - e.g., "basic block A must always execute before basic block B" or "if basic block A executes, then basic block B must execute", etc

    - some potential uses of this analysis:

        - security: every execution that reaches a use of user input must first go through a sanitizer function
        - concurrency: every execution that enters a lock must then go through an unlock

    - in general, anything related to execution control dependencies

        - program slicing for debugging, program understanding, etc, which we'll implement in a later assignment
        - converting a compiler IR into static single assignment form (SSA), which we'll hopefully get a chance to talk about later
        - more...

- for this analysis we don't care about individual instructions, just entire basic blocks---because of the way we defined basic blocks, if one instruction is executed all the others in the same block necessarily must be executed as well

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

    + the dual of dominance; just reverse the CFG and PDOM becomes DOM (and vice-versa)

    + similarly, can have `PDOM!` and `PIDOM`

- we'll only discuss the forward cases (`DOM`, `DOM!`, `IDOM`); everything we do for those works for the "post" cases too just by reversing the CFG

    + remember that we've defined our CFG to have exactly one "exit" basic block (the one with the `$ret` instruction), so reversing it still leaves us with a single entry node

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

#### computing dominance
##### abstract domain

- what are the potential analysis solution? sets of basic blocks, thus these are the abstract values

- therefore, abstract domain is 𝒫(BasicBlock)

- what is the most and least precise safe answers (i.e., ⊥ and ⊤)?

    - remember that we want to be safe: if we say that bb1 dominates bb2 then it definitely does
    - ⊥ = BasicBlock
    - ⊤ = {}

- what is join?

    - set intersection

- notice that this is flipped from reaching defs, where we had a powerset abstract domain but ⊥ = {} and ⊤ = PP

    - this is because our notion of "safe" (the guarantee we want to provide) is different

    - for reaching defs, we wanted to know all defs that _may_ reach this point

    - for dominance, we want to know all basic blocks that _must_ dominate this one

##### abstract semantics

- the semantics are trivial because we don't care about the actual instructions, just the edges of the CFG

- the abstract store is _not_ a mapping from variables to abstract values because variables are irrelevant to dominance; it's just a single abstract value (the set of dominating basic blocks)

- to "execute" basic block bb, just insert it into the abstract store (because every basic block dominates itself)

- everything else is handled by the fixpoint worklist algorithm

##### examples 

- [use examples 1 and 2 above]

### control dependence
#### intro

- dominance tells us what basic blocks _must_ be executed before other blocks (or after for post-dominance), but it can also be used to tell us what blocks are _control dependent_ on what other blocks

- intuitively, block X is control dependent on block Y if the execution of block Y directly determines whether block X will be executed or not (including how many times it will be executed, for loops)

#### EXAMPLE 1

  ```
  entry:
    secret = $call boolean_input()
    $branch secret bb2 bb3

  bb2:
    public = $copy 1
    $jump exit

  bb3:
    public = $copy 0
    $jump exit

  exit:
    d = $call output(public)
    $ret 0
  ```

-  bb2 and bb3 are both control dependent on bb1; entry and exit are _not_ control dependent on any other blocks

- this also shows another reason why control analysis is interesting: notice that the value of `public` is equal to the value of `secret` even though there is no explicit assignment between them---this can only leak one bit of information, but if we put something like this in a loop we could leak arbitrary bits

#### EXAMPLE 2

- CFG:

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

- CONTROL DEPENDENCIES:

  ```
  A: { }
  B: { F }
  C: { B }
  D: { B, E }
  E: { B, E }
  F: { F }
  G: { }
  ```

-  B is control dependent on F because F controls how often B is executed; similarly for (D, E), (E, E), and (F, F).

#### dominance frontiers

- to compute control dependence info we need something called `dominance frontiers`

- the dominance frontier of block X is the set of all blocks Y s.t. X dominates a predecessor of Y but does not strictly dominate Y

    + intuitively, the dominance frontier is the set of blocks that are _almost_ dominated by X, but just outside its control; or, its the set of blocks that are the earliest point where some competing control-flow path may reach that block

- given DOM for each basic block, we can compute the dominance frontiers by:

  ```
  // this will be the solution
  let frontiers : BasicBlock -> 𝒫(BasicBlock) = {}

  for (bb, bb_doms) in dominators:
    // strict dominators of bb
    let strict_bb_doms = bb_doms - {bb}

    // for each predecessor of bb
    for pred in cfg_preds(bb):
      // for each dominator of pred that doesn't strictly dominate bb
      for pred_dom in dominators[pred] - strict_bb_doms
        // pred_dom dominates a predecessor of bb but doesn't strictly
        // dominate bb, thus by definition frontier[pred_dom] includes bb
        frontiers[pred_dom] ∪= bb
  ```

- [demonstrate on example 2 above]

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

#### computing control dependence from dominance frontiers

- X is control dependent on Y iff Y is in the post-dominance frontier of X

    + remember that post-dominance (and post-dominance frontiers) are exactly the same as dominance (and dominance frontiers) except on a reversed CFG

    + if Y is in the post-dominance frontier of X, then that means Y has multiple successors s.t. if a specific one is taken then X must necessarily execute, and if it is not taken then X will definitely not execute---in other words, X is control dependent on Y

- algorithm:

    + step 1: compute post-dominators (i.e., flip the edges of the CFG and compute the dominators on that)

    + step 2: compute post-dominance frontiers (i.e., compute dominance frontiers but using the reversed CFG and the post-dominators)

    + step 3: each block X is control dependent on any block in its post-dominance frontier

- [demonstrate on example 2 above]

  POST-DOMINANCE FRONTIERS (and thus CONTROL DEPENDENCIES):
  ```
  A: { }
  B: { F }
  C: { B }
  D: { B, E }
  E: { B, E }
  F: { F }
  G: { }
  ```

# the math behind DFA
## preliminaries

- we've been really hand-wavy so far---how can we guarantee that the analysis is decidable (i.e., terminates)? we saw that some domains guarantee termination (e.g., signs) and some don't (e.g., intervals), and being infinite doesn't tell the whole story (e.g., constants terminates while intervals does not without widening)

- to make guarantees about computability we can't allow arbitrary abstract domains; instead we need to impose an algebraic structure to them that will allow us to prove computability

- we'll go into a bunch of definitions and theorems in abstract algebra, and then afterwards connect it back to abstract domains and computability

- these definitions are about _ordering_: given a set S, an ordering is a binary relation on that set which meets certain requirements

    + examples of well-known orderings: (ℤ, ≤), (Strings, ≤)

    + we often use the notation ⊑ for an arbitrary ordering

## definitions
### BINARY RELATION

- a binary relation on set `S ⊆ 𝒫(S × S)`

- example: `({1, 2, 3, 6}, ⊑)` where ⊑ means 'divides evenly'; the binary relation ⊑ is {(1,1), (1,2), (1,3), (1,6), (2,2), (2,6), (3,3), (3,6), (6,6)}

- instead of saying `(1,3) ∈ ⊑` we usually say `1 ⊑ 3`

- a relation may have some of the following properties:

    - reflexive: `∀a ∈ S, (a, a)`

    - transitive: `∀a,b,c ∈ S, (a, b) and (b, c) implies (a, c)`

    - symmetric: `∀a,b ∈ S, (a, b) implies (b, a)`

    - anti-symmetric: `∀a,b ∈ S, (a, b) and (b, a) implies a = b`

### PARTIAL ORDER aka POSET

- a binary relation that is _reflexive_, _transitive_, and _anti-symmetric_

    - if it were _symmetric_ instead then it would be an _equivalence relation_: (ℤ, =), "has same birthday as"

- examples:

    + reachability on a DAG (anti-symmetric because no cycles)
    + ℕ and divisibility: (ℕ, |)
    + 𝒫(S) and ⊆

- counterexample:

    + (ℤ, |) (not anti-symmetric, e.g., 2 | -2 and -2 | 2)

- we can visualize partial orders as Hasse diagrams:

    + example: 𝒫({1, 2, 3}), ⊆

    + example: ({1, 2, 3, 5, 6, 10, 15, 30}, "divides into")

- a partial order can be a _total order_, i.e., a partial order s.t. for any two elements x and y, either x ⊑ y or y ⊑ x

    + examples: (ℤ, ≤), (Strings, ≤)

    + counterexample: the partial order examples given above

### LEAST UPPER BOUND, aka JOIN, aka ⊔

- for D ⊆ S, the least x ∈ S s.t. ∀d ∈ D, d ⊑ x

- denoted `⊔D` or (if D = {x, y}) `x ⊔ y`

- examples: use posets from above

### GREATEST LOWER BOUND, aka MEET, aka ⊓

- the dual of ⊔: for D ⊆ S, the greatest x ∈ S s.t. ∀d ∈ D, x ⊑ d

- examples: taken from the above partial and total orders

- note that ⊔ and ⊓ are not guaranteed to exist

    + DAG reachability [e.g., nodes in isolated graph components]

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

    + (𝒫({1, 2, 3}), ⊆) [BOT = {}, TOP = {1,2,3}]

        - join = union
        - meet = intersection

- QUIZ: lattice or not? [draw Hasse diagrams]

  1. ({1, 2, 3}, |)             [NO]
  2. ({1, 2, 3, 6}, |)          [YES]
  3. ({1, 2, 3, 12, 18, 36}, |) [NO]
  4. (ℕ, ≤)                     [YES]

### NOETHERIAN LATTICE

- a lattice s.t. the length of any ascending chain must be finite

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

    + (𝒫(ℕ), ⊆)

        - join = union
        - meet = intersection

    + (ℕ, |) [see example from LATTICE section for details]

- counterexamples:

    + ({ S ∈ 𝒫(ℕ) | S is finite }, ⊆)

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

- we will require our abstract domains to be complete noetherian lattices with a partial order based on precision

    + examples: parity, sign, constants, reaching defs

    + recall that each abstract value represents a set of concrete values, and the abstract domain partial order reflects the subset relation between the concrete sets---a smaller concrete set of possible values means a more precise abstract value

- so when we've said "the most precise abstract value", what we mean is the lowest possible element of the lattice that still over-approximates the given concrete values

- note that traditionally DFA actually flips the lattices: the most precise element is on the top and the least precise is on the bottom, while a.i. does it like we're doing it here; it can be confusing when reading both the dfa and a.i. literature

    + the a.i. version matches the math better, but really it doesn't matter as long as we're consistent

- why is all this math useful? because we can formally define when the MFP exists and is computable

## computability
### FIXPOINTS

- recall:

    + a fixpoint of `F` is an input `x` s.t. `F(x) = x`

    + `lfp_x F = u` s.t. `x ⊑ u`, `F(u) = u`, `∀y. F(y) = y ⇒ u ⊑ y`

    + pre-fixpoint: `x ⊑ F(x)`

    + post-fixpoint: `F(x) ⊑ x`

- notice that these last three definitions only make sense for an ordered set (and now we know exactly what that means)

- note that a function may have 0—∞ fixpoints, and just because there's more than one _doesn't_ mean that there's a least one

    + f(x) = x + 2  [0 fixpoints]

    + f(x) = x² - x [2 fixpoints (0 and 1)]

    + f(x) = x² ÷ x [∞ fixpoints; no least fixpoint if x ∈ 𝐙]

- our worklist algorithm for computing the MFP is trying to compute this, but how can we be sure that it exists and is computable?

### MONOTONICITY

- function `F` is _monotone_ iff `x ⊑ y ⇒ f(x) ⊑ f(y)`, or equivalently `f(x) ⊔ f(y) ⊑ f(x ⊔ y)`

- [SKIP] PROOF (1st direction; 2nd direction left as exercise):

    1. f(x) ⊔ f(y) ⊑ f(x ⊔ y)      [given]
    2. x ⊑ y ⇒ x ⊔ y = y           [by definition]
    3. x ⊑ y ⇒ f(x) ⊔ f(y) ⊑ f(y) [by 1, 2]
    4. f(x) ⊑ f(x) ⊔ f(y)          [by definition]
    5. x ⊑ y ⇒ f(x) ⊑ f(y)        [by 3,4]

- example: `f(x) = x - 1`

- counterexample: `f(x) = x+2 if x ≤ 0, else x`

- an _extensive_ function is one for which x ⊑ F(x); note that monotone and extensive are orthogonal properties (people often confuse them)

### FIXPOINT THEOREMS

- `TARSKI`: let `L` be a complete lattice and `F:L→L` be monotone; then the set of fixpoints of `F` is a complete lattice (and thus there exists a least fixpoint)

    + so we know a least fixpoint exists, since our abstract domains are complete lattices

    + however, it doesn't tell us how to _find_ the least fixpoint

- `KLEENE`: let `L` be a complete _noetherian_ lattice and `F:L→L` be monotone and `x` be a pre-fixpoint of `F`; then ∃n ≥ 0 s.t. `Fⁿ(x)` is stationary and `lfp_x F = Fⁿ(x)`

    + so start from a solution that is less than the fixpoint, then keep applying `F` over and over; eventually we'll reach a fixpoint and it will be the least fixpoint

    + `KLEENE ITERATIONS`: ⊥ ⊑ F(⊥) ⊑ F(F(⊥)) ⊑ ... ⊑ Fⁿ(⊥) ⊑ ...

    + it turns out that this is what we're doing when we use our worklist algorithm (though in an optimized way because we only re-evaluate the parts that have changed, rather than re-evaluating everything every time)

### ABSTRACT SEMANTICS

- these theorems tell us that if (1) our abstract domains are noetherian lattices, and (2) our transfer functions are monotone, then the least fixpoint exists and is computable, and our worklist algorithm will compute it

    + note that the worklist is an optimization of kleene iterations that avoids computing transfer functions that are guaranteed to have not changed their values

    + for us, our function `F` is a system of equations based on the program being analyzed:

      ```
      IN_k = ⊔ { OUT_i | i→k ∈ CFG }
      OUT_k = F♯(IN_k)
      ```

    + here `F♯` is the abstract execution of a basic block and its argument is the initial abstract store for that basic block

### EXAMPLES

- show for the following integer abstract domains that they are noetherian lattices and the appropriate transfer functions are monotone:

#### signedness {-, 0, +}

- [show abstract domain, abstract `+`]

- is the abstract domain a noetherian lattice? YES

- abstract `+` is monotone if: `a ⊑ a'` and `b ⊑ b'` => `a + b ⊑ a' + b'`

    + case 1: `a' or b' = ⊥`; then `a or b = ⊥` and thus `a + b = ⊥` and also `a' + b' = ⊥`, thus `a + b = a' + b'`

    + case 2: `a' or b' = ⊤`; then `a' + b' = ⊤` and thus `a + b ⊑ a' + b'`

    + case 3: `a' ∈ {+,0,-}`, `b' ∈ {+,0,-}`, `a, b ̸= ⊥`; then `a = a'` and `b = b'` thus `a + b = a' + b'`

#### constant lattice

- [show abstract domain, abstract `+`]

- is the abstract domain a noetherian lattice? YES

- is the abstract `+` function monotone? [similar to proof for sign abstract `+`]

# widening redux
## context

- DFA enforces computability by requiring that abstract domains be noetherian lattices, but this limits the precision of what an analysis can compute

    - remember that the abstract domain defines what solutions an analysis can return; a noetherian lattice may not even be able to express the kind of solution that we want

    - example: the integer interval abstract domain

    - however, since the integer interval lattice is _not_ noetherian it cannot be used with DFA without some additional trickery

- we saw that there is a way around this problem that allows us to use non-noetherian abstract domains but still guarantees computability: _widening_

    - previously we looked at it intuitively

    - now let's look at the math

## definition

- recall that while complete lattices with monotone functions are guaranteed to have a fixpoint (TARSKI), the only way we're guaranteed to be able to _find_ the fixpoint is if the lattice is noetherian (i.e., no infinite ascending chains)

    + essentially, if the function is monotone then we can only stay in one place (we've found a fixpoint) or go up the lattice; if the lattice is noetherian we can only go up the lattice a finite number of times before we reach ⊤

- but what if we want to use an abstract domain that isn't noetherian, in order to get greater precision?

- what we want is a way to accelerate/guarantee convergence, i.e., if the normal fixpoint computation is working up along the abstract lattice in a chain, what we need to is leap-frog up that chain past potentially an infinite number of lattice elements

- solution: _widening_. we replace the lattice join operator with a _widening operator_, then apply the usual worklist algorithm for computing a fixpoint

    + this comes with a cost: the fixpoint we reach is not guaranteed to be the least fixpoint of the original lattice (or even a fixpoint of the original lattice at all); all we know for sure is that `lfp_orig ⊑ lfp_widened`

    + the idea is that while we aren't getting as precise an answer as we could be getting for that abstract domain, since the abstract domain itself is more precise than one that is noetherian, hopefully we're getting a more precise answer than we would be getting with that noetherian lattice (this isn't a guarantee, though)

        - example: we need to use widening for integer intervals, but we'll still often get a better solution than using the constant abstract domain

    + an additional cost is that before, we had a simple checklist for proving our analysis is computable: we have a noetherian lattice and monotone transfer functions; now we need to prove that the widening operator we choose is, in fact, a proper widening operator (there are many possible operators, and we need to prove this fact for each one we want to use)

- an operator `▽ ∈ DxD -> D` over some poset `D` is a widening operator iff:

    + `x ⊑ (x ▽ y)` and `y ⊑ (x ▽ y)`

    + for all ascending chains `x₀ ⊑ x₁ ⊑ x₂ ⊑ ...`, the following chain stabilizes in a finite number of steps:

        - `acc₀ = x₀`
        - `acc_{n+1} = accₙ ▽ x_{n+1}`

- the first condition says that the widening operator overapproximates its inputs

- the second condition says that if we apply the widening operator to an ascending chain, then the result is a finite ascending chain

    + recall that our worklist algorithm is essentially computing kleene iterations (`Fⁿ(⊥)`), which is computing an ascending chain; so if we use widening instead of join for the worklist algorithm we're guaranteed to reach a fixpoint and terminate

## example: integer intervals

- recall the integer interval abstract domain

- this is a complete lattice, but non-noetherian

- our example widening operator (one out of many possible):

    + `∀x ∈ L: ⊥ ▽ x = x ▽ ⊥ = x`

    + `[a1, b1] ▽ [a2, b2] = [a', b']` s.t.

        - if `a1 <= a2` then `a' = a1`, else `a' = -∞`
        - if `b1 >= b2` then `b' = b1`, else `b' = ∞`

    + essentially, if we're growing down jump straight to -∞ and if we're growing up jump straight to ∞

- note again that this widening operator is _not_ commutative (unlike join, which is commutative)

    - this means that the order we apply the widening operator in matters, thus the order we visit basic blocks in matters (which it didn't when we used ⊔)

    - example: two edges into a loop header, one that propagates `a -> [0, 0]` and one `a -> [1, 1]`; depending on which one propagates first we'll get either `[0, ∞)` or `(-∞, 1]`

# practice designing DFA analyses

- [go through designing a string constant analysis with the class as a warm-up, then give the prefix and regex analyses as exercises; see the pdfs in `docs/` for details]

- for each analysis give:

    + the elements of the lattice
    + which element is ⊤, which is ⊥
    + the join and meet operators and the ordering relation
    + whether the lattice is finite, infinite but noetherian, or non-noetherian
    + the alpha and gamma functions
    + the definitions of `+`
    + proof that `+` is monotone

- prefix:

    + the cases are handling various cells in the `+` table: case 1 is the leftmost column and the top row; case 2 is the middle cell; case 3 is rightmost middle cell; case 4 is the bottom middle and rightmost cells

    + for case 3 we're saying that `b <= b'` which means all the strings in `b` are contained in `b'`, and all we're doing is sticking the same prefix to both sides, so `a . b <= a . b'`

    + for case 4 we're saying that `a <= a'`, which means all strings starting with `a` are in `a'`, so no matter what `b` is it must be the case that `a . b <= a'`

- regex:

    + remember that it's only a lattice if we use normalized regexes

    + while a lattice, it isn't a _complete_ lattice (e.g., the least upper bound of the chain `01 <= 0011 <= 000111 <= ...` is the language `0{n}1{n}`, which is context-free, and there is no _least_ regex that contains it); this is a problem, but since the lattice is non-noetherian it doesn't really matter---we can't guarantee finding a fixpoint anyway

# set constraint-based analysis
## intro

- we're going to use the problem of pointers to explore a totally different method for program analysis: set constraint-based analysis

- we've had to treat pointers conservatively, and this has really hurt precision; pointers allow indirect data- and control-flow: we can't tell syntactically what is being used/defined by an instruction or where control is going (e.g., function pointers)---if we want useful, non-trivial analysis information we really need to deal with pointers somehow

- in theory we can use DFA to analyze pointer information, using our standard formula: abstract domain, abstract semantics, abstract execution; in practice, it turns out that the domain is large enough and semantics complex enough that this isn't really scalable (we'd be lucky to be able to analyze programs with just 10,000 LOC)

    + it's possible to make it more scalable (part of my phd dissertation was about how to make DFA-based pointer analysis two orders of magnitude more scalable), but (1) it isn't commonly used because it's still expensive; and (2) doing so is complex and requires set constraint-based analysis as a sub-procedure anyway

- to be clear, set constraint-based analysis isn't specifically for pointer analysis, it can be used for many different things (just like DFA); we're going to be using pointers as the motivating example because it turns out that it's probably the most common technique for doing pointer analysis in current use

- recall that DFA gives us a `flow-sensitive` analysis (one solution per program point); set constraint-based analysis is going to give us a `flow-insensitive` analysis (one solution over the entire program)

    + a flow-sensitive sign analysis: at this program point `x` is ZERO, at this other program point `x` is POS

    + a flow-insensitive sign analysis: over the entire program, `x` is TOP

- flow-insensitive analysis is clearly less precise because it can't give different answers per program point but instead must give a single answer that is sound over the entire program; the tradeoff is that it is usually much more scalable than flow-sensitive analysis

- we'll begin by talking about set constraint-based analysis in general, then show how to apply it to pointer analysis

## inclusion set constraint language

- there are a number of possible set constraint languages with various tradeoffs in expressiveness and computability (the most general set constraint language is undecidable)

- one useful point in this space is the _inclusion constraint language_:

  ```
  X ∈ Variable
  c ∈ Constructor

  T ∈ Term ::= X | c(T1,...,Tn)
  E ∈ Expr ::= T | c⁻ⁱ(X)
  S ∈ Stmt ::= E1 ⊆ E2 | S1 ∧ S2  // (E1 and E2 cannot both be projections)
  ```

- what are constructors? think of them as _uninterpreted functions_ that take sets as arguments and return sets as results

    + the analysis doesn't care what they mean---it's up to us to give them a meaningful interpretation when we're defining an analysis

    + example: type constructors (think of a type as representing a set of values)

        - nullary constructors: `int`, `string`, `bool`
        - unary constructors: `Set[·]`, `List[·]`
        - binary constructors: `·→·`, `Map[·,·]`

    + characteristics of constructors:

        - _arity_: the number of arguments it takes
        - _variance_: each argument position is either:

            + _covariant_ (the constructor is monotone wrt that position)
            + _contravariant_ (the constructor is antitone wrt that position)

- variance is answering the question "if i know `A ⊆ B`, what do i know about the relation between `c(A)` and `c(B)` (or vice versa)?

    + as an example consider a constructor `foo` with arity 2 where `foo`'s first argument is covariant and its second is contravariant, and suppose `foo(A, B) ⊆ foo(C, D)`; what can we determine about the relations between `A` and `C` and between `B` and `D`?

        - `foo`'s first argument (`A` and `C` here) is covariant, therefore `A ⊆ C` (notice that the direction of the subset is preserved)

        - `foo`'s second argument (`B` and `D` here) is contravariant, therefore `D ⊆ B` (notice that the direction of the subset is reversed)

    + we need specify variance explicitly because the constructors are essentially uninterpreted functions---what they really _mean_ is up to us when we design our analyses and so we need to explicitly specify this piece of information to allow the set constraints to be correctly solved wrt what the constructors really stand for

    + for now we'll just assume everything is covariant; we'll need contravariance later but we'll talk more about it then

- what does projection do? take the constructors denoted by the given expression, filter out the ones that don't match the given constructor, take the i'th argument of each remaining constructor, and put all of them in a set

    + we'll number positions starting from 0

    + example projection: suppose

>     X -> { a(b, C), a(D, e), f(g), h(i, j, k) }
>     C -> { l, m }
>     D -> { n, o, p }

    + then `a⁻¹(X)` = `{ e, l, m }`

- example set constraint system:

  ```
  f(X,Y) ⊆ A
  A ⊆ B
  B ⊆ C
  C ⊆ E
  g ⊆ D
  h ⊆ D
  h ⊆ f⁻¹(B)
  D ⊆ f⁻⁰(C)
  f⁻⁰(C) ⊆ E
  f⁻¹(B) ⊆ F
  ```

- what is a solution to a set constraint system?

    + let H = the set of all constructor calls

    + a solution is an assignment `σ ∈ Variable → 𝒫(H)` that maps each set variable to a set of constructor calls s.t. all constraints are satisfied

- for the above example, a valid solution would be:

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

- this is not the only possible set constraint language that yields a decidable analysis, but this one has two nice properties:

    1. it is guaranteed to have a minimal solution
    2. it is guaranteed to have cubic complexity

- analyses defined using this particular restricted set constraint language are called _inclusion-based analyses_

    + as a sidenote, another common class of analyses is _equality-based analyses_, which uses the same language except with `E1 = E2` instead of `E1 ⊆ E2`; this is even less precise than inclusion-based, but can run in near-linear time complexity

## solving inclusion constraints
### representing constraints as a graph

- it will be convenient for solving the constraints to represent them as a graph

    + a node for each set variable, projection, and constructor call
    + an edge for each constraint from the lhs to the rhs

- edges can be represented in many ways; we'll use two kinds:

    + successor edges: the edge is stored in the source node
    + predecessor edges: the edge is stored in the destination node

- the rules are:

    + any edge from a constructor call is a predecessor edge
    + any edge to a projection is a predecessor edge
    + all other edges are successor edges
    + [there cannot be any edges between projections]

- why we do it this way will become evident when we show how to solve the constraints

- example:

  ```
  f(X,Y) ⊆ A
  A ⊆ B
  B ⊆ C
  C ⊆ E
  g ⊆ D
  h ⊆ D
  h ⊆ f⁻¹(B)
  D ⊆ f⁻⁰(C)
  f⁻⁰(C) ⊆ E
  f⁻¹(B) ⊆ F
  ```

  ```
  [--> == predecessor edge]
  [==> == successor edge]

  f(X,Y) --> A ==> B ==> C
                          \\
  g --> D --> f⁻⁰(C) =======> E
       /
  h --<
       \
        f⁻¹(B) ==> F
  ```

### solving the constraint graph

- solving the constraints corresponds to computing the dynamic transitive closure of the graph

    + the predecessor edges from the constructor calls to the set variables are the solution to the constraints

    + remember that a solution maps set variables to sets of constructors, so we map each set variable to the set of constructors that have a predecessor edge to that set variable

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

- initialize worklist with all variables that have a predecessor edge (i.e., all variables that currently have something in their solution set); the worklist will only contain set variables

- perform the usual worklist algorithm (i.e., loop as long as the worklist is non-empty); when popping set variable X:

    1. propagate X's predecessor edges along all of X's successor edges; if the predecessor edges of the destination node changed, put it onto the worklist

    2. if X has any projections, process each projection node P:

        + let Y = the value of P's projection (i.e., a set of variables and/or constructor calls from projecting X)

        + for each predecessor p_i of P and successor s_i of P and each y_i ∈ Y, add edges p_i -> y_i -> s_i (using the standard rules for pred vs succ edges)

        + if a node has a new pred or succ edge added to it, add that node to the worklist

- what if we add an edge between two constructors (e.g., `c(T1,...,Tn) ⊆ c(T1',...,Tn')`)? this is where knowing the variance of each constructor argument position matters:

    + `c(T1,...,Tn) ⊆ c(T1',...,Tn')` =>

        - `Ti ⊆ Ti'` if position `i` of constructor `c` is covariant
        - `Ti' ⊆ Ti` if position `i` of constructor `c` is contravariant

    + what if the constructors don't match? technically this is an error, but in a practical implementation we usually just ignore mismatched constructors

- when the worklist is empty, we've computed a solution to the original constraints: for each set variable, look at its predecessor edges to get its solution

### implementing the constraint graph and solver

- this is just a high-level suggestion for how to implement them---the details for how to best implement them depend on the language you're using

    + feel free to implement them a different way in your solution, this is not mandatory

- define the following data structures:

    + `Constructor`: containing name, arity, and variance info for that constructor

    + `SetVariable`: containing name, list of predecessors (e.g., a set of nodes), and list of successors (e.g., another set of nodes)

    + `ConstructorCall`: containing a reference to the Constructor and a list of arguments (e.g., a vector of nodes)

    + `Projection`: containing a reference to the SetVariable the projection is being done on, a reference to the Constructor being projected, the position of the Constructor argument being projected, the list of predecessors, and the list of successors

- `SetVariables`, `ConstructorCalls`, and `Projections` are the nodes of the constraint graph, and the lists of predecessors and successors are the edges of the constraint graph

- the specific set of constructors, constructor calls, set variables, and projections will vary with the problem being solved, so you also need a way for the _user_ of the constraint solver (in our case this will be the pointer analysis) to register specific constructors, set variables, constructor calls, and projections

    + generally the easiest way to do this is to have a `register` function take all the relevant information (_except_ for edges) and return some handle to the created constructor or node (e.g., a pointer or an index into a vector)
    
    + then there is a separate `add_edge` function that takes two handles and adds an edge between them (either predecessor or successor depending on what kind of nodes they are)

## solver optimization

- cubic time complexity is still not great; we can't make the complexity go down but we can still optimize the constraint solver to make it much faster in practice (e.g., reduce the `n` in `O(n³)`)

- there are a variety of known optimizations; we won't spend time on them here but there are a number of relevant papers that describe them (including some of mine)

## defining a program analysis

- there are two steps for defining an analysis using set constraints:

    1. define the universe of discourse, i.e., the set variables and the set of constructors

    2. define constraint generation, mapping a program to a set of constraints whose solution will be the solution to the analysis

- notice that we don't need to describe how to solve the analysis, because no matter what the analysis is they're always inclusion constraints and can be solved using the method we described earlier

    + that is, the only new thing we need to do to define an analysis is the two points above; we can always use the same solver for all analyses

- this process is easiest to explain by example; the example we'll be using henceforth is pointer analysis.

# andersen-style pointer analysis
## intro

- recall that pointer analysis is computing an overapproximation of program behavior wrt pointers

    + i.e., for a given pointer access (load, store, indirect call, etc) what object/function might be accessed?

- andersen-style pointer analysis is just one form of pointer analysis, defined using the inclusion set constraint language

    + this is one of the most common kinds of pointer analysis in regular use (e.g., in compilers)

    + there are many other kinds of pointer analysis, that explore various points in the precision/performance design space

- we need an abstraction for pointer values (i.e., memory addresses)

    + memory is conceptually infinite, so we need to make a finite abstraction: we need abstract values for pointers that over-approximate memory addresses
    
    + a common choice is _static allocation site_: we label the `$alloc` instruction that creates object on the heap and use that label to stand for all objects allocated by that instruction; since there are finite allocation instructions the abstract domain is finite

    + conveniently, LIR already gives every `$alloc` instruction its own unique label, so we'll just use that

    + example: for `x = $alloc 1 [_a1]`, all addresses returned by the `$alloc` will be represented by `_a1`

    + note that a single abstract value can represent an unbounded number of concrete memory addresses (e.g., if the instruction is located inside a loop); this is why it's called a _static_ allocation site

    + there are other abstractions, but they tend to be much more expensive and hence difficult to scale in practice

- recall that an inclusion-based program analysis is defined as:

    + a set of set variables
    + a set of constructors
    + a constraint generation scheme (i.e., a way to map a program to a set of inclusion constraints)
    + then we just pass the generated constraints to the pre-existing constraint solver

- contrast this with DFA, where we defined a program analysis as:

    + abstract domain + abstract semantics + abstract execution
    + we had to define the first two, then we could use the same abstract execution engine for any DFA

## start with no structs or calls
### setup

- we'll assume for now that programs contain no struct definitions or function calls

- remember that a solution is a map from set variables to sets of constructor calls; we want to define the analysis so that the constaint solution translates to the information we're trying to compute, i.e., points-to sets (in this case)

- universe of discourse:

    + a constant (i.e., nullary constructor) and set variable for each program variable and static allocation site

        - the constant represents the program variable / allocated object

        - the set variable represents the associated points-to set

    + a `ref/2` constructor s.t. that first argument is a constant and the second argument is a set variable, connecting a program variable / allocated object with its points-to set

        - `ref(x, X)` means that `X` is the points-to set of `x`, where, e.g., both the set variable `X` and constant `x` come from the program variable `x`

### constraint generation

- we only care about pointers for pointer analysis, so for all instructions with a lhs ignore it if the type of lhs is not a pointer and for `$store` instructions ignore it if the type of the operand is not a pointer

- we can also ignore null pointers (i.e., `0` instead of a pointer variable)

    - just don't generate a constraint involving null pointers

    - this won't be sufficient for indirect calls, but we'll get to that later

- given a program variable `x`, we'll use the notation:

    + `[x]` to mean translate `x` into a set variable (creating it if it doesn't already exist) and return the set variable
    + `const(x)` to mean translate `x` into a constant (creating it if it doesn't already exist) and return the constant

- `x = $copy y`

    + `[y] ⊆ [x]`

- `x = $alloc op [id]`

    + `ref(const(id), [id]) ⊆ [x]`

    + notice that since the left-hand side is a constructor call this constraint will be represented as a predecessor edge in the constraint graph---i.e., it will be part of the solution for the `[x]` set variable

- `x = $addrof y`

    + `ref(const(y), [y]) ⊆ [x]`

    + similarly to `$alloc`, this call will be part of `[x]`'s solution

- `x = $gep y <op>`

    + `[y] ⊆ [x]`

    + note that we're being 'array-insensitive', i.e., collapsing all elements of an array into one, which is common in these kinds of analyses

- `x = $load y`

    + `ref⁻¹([y]) ⊆ [x]`

    + why? the projection is modeling pointer dereference: concretely we get the value of `y` (an address), go to that address to get the value of what `y` points to (which is also an address, since `x` is a pointer), then copy that value to `x` [draw a picture]
    
    + this constraint gets the current solution for `[y]` (which is a set of `ref` calls representing the value of `y`), projects the second argument of those `ref` calls (which are the set variables representing the value of what `y` points to, i.e., their points-to sets), then copies their values to `[x]`

- `$store x y`

    + `[y] ⊆ ref⁻¹([x])`

    + why? similar to load except the information is flowing _into_ the pointed-to variables instead of _from_ them

- we can ignore:

    + `$gfp` (because there are no structs for now)
    + `$arith`, `$cmp` (because they don't affect pointer values)
    + `$jump`, `$branch` (because the analysis is flow-insensitive)
    + `$call_*`, `$ret` (because we're not considering function calls for now)

### example 1

CODE
```
fn main() -> int {
  let a:&&int, b:int, c:&int, d:&&int, e:&&int, f:&&int, g:&&int, h:&int, i:int
  entry:
    a = $alloc 1 [_alloc]
    b = $copy 42
    c = $addrof b
    d = $gep a 42
    $store d c
    $branch b bb1 bb2

  bb1:
    e = $copy d
    $jump bb3

  bb2:
    f = $addrof c
    $jump bb3

  bb3:
    g = $copy e
    g = $copy f
    h = $load g
    i = $load h
    $ret i
}
```

CONSTRAINTS (we'll use lower case for constants, upper case for set variables)
```
ref(alloc, ALLOC) ⊆ A
ref(b, B) ⊆ C
A ⊆ D
C ⊆ ref⁻¹(D)
D ⊆ E
ref(c, C) ⊆ F
E ⊆ G
F ⊆ G
ref⁻¹(G) ⊆ H
```

GRAPH
```
ref(alloc, ALLOC) --> A ==> D ==> E ====> G
ref(c, C) ------------> F ==============//
ref(b, B) --> C --> ref⁻¹(D)
ref⁻¹(G) --> H
```

SOLUTION
```
A -> { ref(alloc, ALLOC) }
C -> { ref(b, B) }
D -> { ref(alloc, ALLOC) }
E -> { ref(alloc, ALLOC) }
F -> { ref(c, C) }
G -> { ref(alloc, ALLOC), ref(c, C) }
H -> { ref(b, B) }
ALLOC -> { ref(b, B) }
```

## adding structs [but no struct-typed variables]
### intro

- what if we have structs in the program? recall that the `$gfp` instruction is how we access the fields of a struct:

    + `x = $gfp y <fieldname>`
    + `y` is a pointer to the struct
    + `<fieldname>` is the name of the field we want to access
    + `x` is set to a pointer to the desired field

- when a struct is allocated using `$alloc` it creates a heap object that has distinct fields and we need to decide how to represent that in the analysis

- we have three choices: field-insensitive, field-based, field-sensitive; the most common choices are field-insensitive or field-sensitive, that's what we'll cover

### field-insensitive

- the simplest and easiest option: treat a struct object as if it doesn't have distinct fields, i.e., all fields are merged together into a single blob

    + treat `$gfp` like a copy

    + `x = $gfp y <field>` ==> `[y] ⊆ [x]`

- example:

>  struct foo {
>    f1: &int
>    f2: &int
>  }
>
>  fn bar(i:int) -> &int {
>    let p:&int, a:&foo, b:&&int, c:&&int, d:&int
>    entry:
>      p = $addof i
>      a = $alloc 1 [_alloc]
>      b = $gfp a f1
>      c = $gfp a f2
>      $store b p
>      d = $load c
>      $ret d
>  }

    + the pointer analysis will treat the `f1` and `f2` fields as the same thing, so `b` and `c` will have the same abstract value (i.e., the pointer analysis says that both pointers can point to both fields)

    + the analysis result will say that `d` can point to `i` even though it really can't

### field-sensitive

- the most complex and most precise option: treat each struct field independently

- we need to define a constructor for each struct type, whose name is the struct type, arity is the number of fields, and variance is all covariant

- `$gfp` instructions that are accessing a field need to turn into projections of the correct field

- this only works because we require the program to be strictly typed (and there's no inheritance); if we can have the same pointer point to structs with different numbers of fields things become more complicated

## adding direct calls
### constraint generation

- our final version of the analysis will be _interprocedural_, that is, we'll track how information flows back and forth between functions; this means we need to add constraint generation for `$call_dir`

    + we'll ignore `$call_idr` for now, but add it in next

    + we'll also ignore `$call_ext`; there's no source code to analyze so we can't do anything---we'll assume that any relevant external calls have been transformed into stubs that are called using `$call_dir` instead

- we'll use the notation `retval(func)` to mean the variable operand of the `$ret` instruction inside function `func`

- `[x =] $call_dir <function>(args...) then bb`

    + if `x` is pointer-typed: `[retval(<function>)] ⊆ [x]` (unless `<function>` returns the null pointer)

    + `∀arg ∈ args`, if arg is pointer-typed: `[arg] ⊆ [prm]` s.t. `prm` is the corresponding parameter of `<function>` (unless `arg` is the null pointer)

- these constraints account for pointer-related information being passed to the callee via the call arguments and back from the callee via the return instruction

### example 2

CODE
```
fn main() -> int {
  let a:int, b:&int, c:int, d:&int, e:&&int, f:&int, g:int
  entry:
    a = $copy 42
    b = $addrof a
    c = $copy 12
    d = $addrof c
    e = $addrof d
    f = $call foo(b, e) then exit

  exit:
    g = $load f
    $ret g
}

fn foo(p1:&int, p2:&&int) -> &int {
  let h:&int
  entry:
    h = $load p2
    $store p2 p1
    $ret h
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
ref⁻¹(P2) ⊆ H
P1 ⊆ ref⁻¹(P2)
```

GRAPH
```
ref(a,A) --> B ==> P1 --> ref⁻¹(P2) ==> H ==> F
ref(c,C) --> D
ref(d,D) --> E ==> P2
```

SOLUTION
```
 A -> {}
 B -> { ref(a,A) }
 C -> {}
 D -> { ref(a,A), ref(c,C) }
 E -> { ref(d,D) }
 F -> { ref(a,A), ref(c,C) }
 H -> { ref(a,A), ref(c,C) }
P1 -> { ref(a,A) }
P2 -> { ref(d,D) }
```

## adding indirect calls
### setup

- unlike direct calls, we can't generate constraints that link a call with its callee and the callee return value with the lhs of the call because we don't know what the callee is; instead we need to generate constraints that can somehow figure out these links dynamically during constraint solving

- we'll define a constructor `lamₓ` for each function type `X` (or really, just the ones that have a corresponding function pointer global)

    + it will have arity 1 + |P| + |R| where |P| is the number of pointer-typed parameters and |R| is the number of pointer-typed return values (which will be 0 or 1)

    + we'll call a lam constructor in two places:

        - when creating a global function pointer: position 1 is the name, position 2 is the return variable (if the function returns a pointer), the remaining positions are parameters

        - when generating a constraint for an indirect call: position 1 is a set variable, position 2 is the lhs of the call, the remaining positions are arguments

    + the first argument position is covariant; if |R| = 1 then the second argument position is covariant; all other argument positions are contravariant (we'll see why momentarily)

### example 3

- we'll start with an example to see how this will work

  CODE:
  ```
  foo: &(&int,int,&int)->&int

  fn foo(p1:&int, p2:int, p3:&int) -> &int {
    let r:&int
    entry:
      r = $copy p3
      $ret r
  }

  fn main() -> int {
    let a:&int, b:&int, c:&int, f:&(&int,int,&int)->&int
    entry:
      a = $alloc 1 [_alloc1]
      b = $alloc 1 [_alloc2]
      f = $copy foo
      c = $call_idr f(a, 42, b) then exit

    exit:
      $ret 0
  }
  ```

  CONSTRAINTS:
  ```
  define lam_{&(&int,int,&int)->&int}/4 where positions 0,1 are covariant and positions 2,3 are contravariant
  - [for convenience, let's call this lamₓ]

  lamₓ(foo, R, _P1_, _P3_) ⊆ FOO // using _X_ to mean contravariant
  ref(alloc1, ALLOC1) ⊆ A
  ref(alloc2, ALLOC2) ⊆ B
  FOO ⊆ F
  F ⊆ lamₓ(_DUMMY, C, _A_, _B_)
  ```

- notice that during solving, we eventually get `lamₓ(foo, R, _P1_, _P3_) ⊆ lamₓ(_DUMMY, C, _A_, _B_)`; then:

    + `foo ⊆ _DUMMY`, i.e., `foo` is recorded in `_DUMMY` as the function being called (we use a dummy variable because this is redundant info)
    + `R ⊆ C`, i.e., the return value of `foo` goes to the left-hand side of the indirect call
    + `A ⊆ P1`, i.e., the first pointer argument to the indirect call goes to the first pointer parameter of `foo`
    + `B ⊆ P3`, i.e., the second pointer argument to the indirect call goes to the second pointer parameter of `foo`

- by making the return value position covariant and the parameter positions contravariant, we can have the call information passing in both directions at once to correctly model function call/return semantics

### constraint generation

- for each global function pointer `<func>:&X` (e.g., `foo: &(int,int)->&int`):

    + let `N` = 1 + (number of pointer-typed parameters) + (number of pointer-typed return values); note that the latter will always be 0 or 1

    + create a constructor `lamₓ/N` if it doesn't already exist
    
        - first position covariant; if there is a pointer-type return value then second position covariant; all other positions contravariant

    + let `<func>` have pointer-typed parameters `param1`, `param2`, ... (i.e., ignore any non-pointer-typed parameters)

    + if `<func>`'s return type is a pointer:

        + if the operand of `$ret` is a variable, let `retval` be that variable
        + otherwise if the operand of `$ret` is null (i.e., 0), let `retval` be `_nil`

    + let `args` be:

        - if `<func>` has a `retval` then `(const(<func>), [retval], [param1], [param2], ...)`
        - otherwise `(const(<func>), [param1], [param2], ...)`

    + generate constraint `lamₓ(args) ⊆ [<func>]`
    
        - this says that the global function pointer points to a function of the given type

- for each indirect call instruction `[y =] $call_idr func_ptr(args...) then bb`:

    + let the pointer-typed arguments be `arg1`, `arg2`, ...

        - if any arguments are, according to the function pointer type, supposed to be pointers, but are instead `0` (representing the null pointer), then use a dummy variable `_nil` with set variable `[_nil]` instead; we'll use the same `[_nil]` set variable any time this happens

        - we can't just ignore null pointers like before because we need to have an argument for each position in the `lamₓ` constructor call

    + if the type of the function being called returns a pointer but there is no `y`, create a dummy `[y]` set variable called `_DUMMY` and act as if there is a pointer-typed `y`

    + if `y` is pointer-typed let `args` = `(_DUMMY, [y], [arg1], [arg2], ...)`, otherwise let `args` = `(_DUMMY, [arg1], [arg2], ...)`

        - we need a first argument to fill in all the constructor arguments, but we don't really need its value---we can get the same information just by looking at the points-to set of `func_ptr`

    + generate constraint `[func_ptr] ⊆ lamₓ(args)`
    
        - this says that anything `func_ptr` points to should flow into the given constructor call

- if we generate constraints this way, then eventually the `lamₓ` constructor calls containing the name, retval, and parameters of an address-taken function will flow into the `lamₓ` constructor call containing the DUMMY, lhs, and arguments of the indirect call

    + since the first position is covariant, the name of the function being called will flow into `_DUMMY`
    + since the retval position (if it exists) is covariant, `retval` will flow into `y`
    + since the remaining positions are contravariant, the arguments will flow into the parameters

# current summary and roadmap

- covered basics of DFA: abstract domain + abstract semantics + abstract execution (MOP vs MFP; worklist algorithm)

    + flow-sensitive
    + intraprocedural
    + no pointer info

- saw several examples thereof:

    + sign analysis
    + constant propagation
    + interval analysis
    + reaching definitions
    + control analysis

- looked at the math behind DFA: neotherian lattices, monotone functions, fixpoint theorems

    + make the abstract domain a noetherian lattice
    + make the abstract semantics monotone
    + then the worklist algorithm is guaranteed to terminate with the most precise answer

- then covered basics of set constraint-based analysis:

    + picking a constraint language (specifically, inclusion constraints)
    + implementing a constraint solver
    + analysis = constraint generation
    + example: andersen-style pointer analysis (flow-insensitive, field-insensitive)

- now what?

    - going back to DFA, but using pointer analysis results to get better results

        + revisit reaching defs, but using pointer info
        + reaching defs + control analysis to do program slicing
    
    - extending DFA to interprocedural analysis

        + taint analysis
        + context sensitivity

    - if time:

        - sparse analysis and SSA
        - a little abstract interpretation
        - maybe more...

# program slicing
## intro

- the point is to:

    + show why pointer info is useful now that we have it
    + introduce a different application for program analysis than optimization
    + describe some generally useful things like the PDG

- now that we have a way to compute pointer information for our analyses, let's use it on a practical application: program slicing

    + problem statement: given a specific instruction I, return the set of instructions that influence the result of I (aka a _backwards slice_)

    + could also ask for the set of instructions that I influences (aka a _forwards slice_)
    
    + the name is inspired by the idea that we're computing a "slice" of the program that only contains relevant instructions

    + slicing is a well-known and heavily-used analysis and there are many variations; we're using one of the basic definitions here but there are many more possible versions

    + slicing is usually used for things like program understanding and debugging, so we're now exploring a different use-case for program analysis

- example:

  ```
  fn main() -> int {
    let i:int, j:int, x:int = input(), y:int = 0, z:int = 1;

    i = 0;
    j = 1;

    while i < x {
        y = y + 2;
        z = z * j;
        i = i + 1;
        j = j + 2;
    }

    return y;
  }
  ```

  SLICE WRT RETURN STATEMENT:
  ```
  main() {
    let i:int, x:int = input(), y:int = 0;

    i = 0;

    while i < x {
        y = y + 2;
        i = i + 1;
    }

    return y;
  }
  ```

- slicing is built on top of several analyses that we've already studied:

    + ((pointer analysis -> reaching defs with pointer info) + control analysis) -> PDG -> slicing

    + remember that i said reaching definitions was more for follow-on analyses than for human consumption; this is the kind of thing i meant

    + notice that if we treat pointers conservatively like in assignment 1 then slicing is pretty useless; the "slice" it computes will contain many irrelevant instructions and overwhelm the user with useless info

    + also note that the PDG is a very useful data structure in its own right and can be used for things besides slicing; i'll talk more about that later

- to keep things simpler and more focused we'll stay with intraprocedural analysis for this problem; we'll move to interprocedural analysis after we're done with slicing

## reaching defs with pointer info
### intro

- we'll revisit our old friend reaching defs, but now with pointer information

    + we'll keep it intraprocedural, but we'll use pointer information to improve precision for loads, stores, and calls

- we'll assume that we have the solution of a flow-insensitive, field-insensitive pointer analysis available (i.e., it maps each program variable and heap object to its points-to set, which itself contains program variables and heap objects)

- we'll also pre-process the program we're analyzing to compute "mod/ref" information: for each function, what set of inputs to the function can be defined by a `$store` ("mod") or used by a `$load` ("ref") either directly by that function or by some function that it transitively calls

    + this mod/ref info will allow us to be less conservative when processing calls during our intraprocedural analysis

### mod/ref info

- note: we'll assume that any relevant externs have been stubbed as internal functions, so we can ignore `$call_ext` instructions

- step 1: compute the call graph of the program

    + a node for each function

    + an edge from function `A` to function `B` if `A` contains a call to `B` via `$call_{dir, idr}`
    
        - use the points-to solution to resolve targets of `$call_idr`

    + this is an very common and useful data structure that a lot of program analysis tools use

- step 2: compute the transitive closure of the call graph

    + augment the call graph so that there is an edge from `A` to `B` if `A` can directly or transitively reach `B` in the call graph

    + you can use a worklist algorithm where the worklist contains all functions that have new incoming edges since they were last processed (initialized with all functions)

- step 3: compute initial mod/ref info

    + for each function `<func>`, compute (1) the set of globals and pointed-to objects that can be defined in `<func>` (`Mods(<func>)`) and (2) the set of globals and pointed-to objects that can be used in `<func>` (`Refs(<func>)`)

- step 4: propagate the mod/ref info using the transitively closed call graph

    + for `<func2>` s.t. `<func> --> <func2>` in the closed graph: `Refs(<func>) = Refs(<func>) ∪ Refs(<func2>)`; `Mods(<func>) = Mods(<func>) ∪ Mods(<func2>)`

- what does this tell us? given a function `<func>`, `Refs(<func>)` is the set of objects that can be used in `<func>` or any function that `<func>` may call, and `Mods(<func>)` is the set of objects that can be defined in `<func>` or any function that `<func>` may call

    + this info will help us be more precise when processing calls during the reaching defs analysis

### the improved analysis

- no more need for "fake variables"; that's handled by the pointer analysis (i.e., the static allocation sites) and we'll just use its results

- all the instructions stay the same except for `$load`, `$store`, and `$call_{dir, idr}`

    + again, we assume relevant extern functions have been stubbed so we can ignore `$call_ext`

- `x = $load y`:

    + USE = `{ y } ∪ points-to[y]`
    + rest is the same

- `$store x <op>`:

    + DEF = `points-to[x]`
    + rest is the same

- `[x =] $call_{dir, idr} id/<fp>(<arg_op>...)`:

    + CALLEES = `{ id }` (if `$call_dir`) OR `points-to[fp]` (if `$call_idr`)
    + REACHABLE = globals + all objects reachable from the arguments or globals per the points-to solution

    + WDEF = `(⋃_{callee ∈ CALLEES} Mods(callee)) ∩ REACHABLE`
    + USE = `{ <fp> } ∪ { <arg_op> | <arg_op> is a variable } ∪ ((⋃_{callee \in CALLEES} Refs(callee)) ∩ REACHABLE)`
    + rest is the same

## control dependence reminder

- remember that block `X` is control dependent on block `Y` iff `Y` is in the post-dominance frontier of `X`

- given a CFG:

    + reverse the edges
    + now the exit block is the entry block
    + compute dominance frontiers (just like assignment 2)
    + `X` c.d. on `Y` iff `Y` ∈ dom_frontier(`X`)

## pdg

- the PDG is a data structure that embodies the reaching defs (aka _data dependencies_) and control dependence information

- a node for for each instruction

- a _data dependence_ edge from `A` to `B` if the definition at `A` reaches a use at `B`

    + we get this directly from the reaching defs analysis

- a _control dependence_ edge from `A` to `B` if `B` is control dependent on `A`

    + we expand the basic block control dependence info to individual instructions: if basic block `X` is control dependent on block `Y`, then all instructions in `X` have a control dependence edge from the terminator instruction in `Y`

- it will be useful to have bi-directional edges so that we can go forwards or backwards in the PDG

- example

  SOURCE:
  ```
  fn main() -> int {
    let i:int, j:int, x:int = input(), y:int = 0, z:int = 1;

    i = 0;
    j = 1;

    while i < x {
        y = y + 2;
        z = z * j;
        i = i + 1;
        j = j + 2;
    }

    return y;
  }
  ```

  LIR:
  ```
  fn main() -> int {
  let i:int, j:int, x:int, y:int, z:int, tmp:int

  entry:
    x = $call_ext input()
    y = $copy 0
    z = $copy 1
    i = $copy 0
    j = $copy 1
    $jump loop_hdr

  loop_hdr:
    tmp = $cmp lt i x
    $branch tmp loop_body exit

  loop_body:
    y = $arith add y 2
    z = $arith mul z j
    i = $arith add i 1
    j = $arith add j 2
    $jump loop_hdr

  exit:
    $ret y
  }
  ```

  PDG:
  ```
  [show reaching defs and control dependencies graphically]
  ```

## slicing

- recall: a _backwards slice_ of a function for instruction `X` consists of all instructions that affect the behavior of `X`

- if `X` is data dependent on `Y` then clearly `Y` affects `X`'s behavior...and so do the instructions that `Y` is data dependent on, and so on

    + this is the transitive closure of the data dependence edges in the PDG (going backwards from `X`)

- if `Y` controls how many times `X` is executed then clearly `Y` affects `X`'s behavior...and so do all the instructions that control whether `Y` executes or not, and so on

    + this is the transitive closure of the control dependence edges in the PDG (going backwards from `X`)

- if you think about it, if `X` is data dependent on `Y` and `Y` is control dependent on `Z` then `Z` also affects `X`'s behavior; and if `X` is control dependent on `Y` and `Y` is data dependent on `Z` then same thing

- so to compute a slice wrt `X`: compute the transitive closure of all data and control dependence edges from X going _backwards_ in the PDG

    + we could also compute a _forwards slice_ by doing the same thing except going forwards in the PDG; this kind of slice tells us what instructions the execution of `X` can directly or indirectly affect

- for human consumption, rather than a simple set of instructions it's useful to present the slice as a new function that only contains the relevant instructions

- example: backwards slice from return instruction for the PDG example

  SLICED:
  ```
  fn main() -> int {
  let i:int, x:int, y:int, tmp:int

  entry:
    x = $call_ext input()
    y = $copy 0
    i = $copy 0

  loop_hdr:
    tmp = $cmp lt i x
    $branch tmp loop_body exit

  loop_body:
    y = $arith add y 2
    i = $arith add i 1

  exit:
    $ret y
  }
  ```

- notice that we're missing some `$jump` instructions; that's because they are unconditional and so there is no control dependence on them

# taint analysis (interprocedural dfa; icfg; context sensitivity)
## intro

- we've seen applications of program analysis for optimization and debugging, and we've seen how to overcome the difficulty of pointers; now let's explore how to deal with function calls and interprocedural analysis at the same time as we expand our applications to include security

- we'll look at _taint analysis_: the goal of taint analysis is to track "tainted" information (e.g., input from users) as it propagates through a program and to ensure that it doesn't influence sensitive operations

    + example: sql injection

    + we can also use taint analysis for the inverse: track sensitive information (e.g., from a user's private data) to ensure that it doesn't influence information output from the program (e.g., that a browser addon doesn't read a user's credit card info and send it out over the network)

- to make this analysis useful, it really needs to be interprocedural---that is, it needs to track information across function call boundaries

    + andersen-style pointer analysis was interprocedural, but not in a DFA setting

- when dealing with the intraprocedural CFG we had two choices: flow-sensitive vs flow-insensitive

    + flow-sensitive: respect the flow of control; a solution per program point; a variable can have a difference abstract value at different places in the program

    + flow-insensitive: disregard the flow of control and assume any statement can execute at any time; one solution for the entire program; a variable must have one abstract value that over-approximates all possible concrete values it might have anywhere

- when dealing with interprocedural analysis we have a similar choice: context-insensitive vs context-sensitive

    + concretely during program execution, every function call results in a unique function instance (typically using stack frames); conceptually, this results in an infinite domain of possible function instances

    + for program analysis to be decidable, we need to abstract this infinite domain into something tractable while still over-approximating the conrete domain

    + context-insensitive: we'll abstract all function instances of the same function into a single abstract function instance that over-approximates all of them; that is, the information for every call to the same function is merged together

    + context-sensitive: we'll partition the set of function instances for the same function into a set of different abstract function instances, treating each one as distinct; the different ways that we partition the set result in different context-sensitivity schemes

        - unlike flow-sensitivity (for which there is just one definition), there are many different versions of context-sensitivity

- we'll start with intraprocedural taint analysis ignoring the possibility of function calls, then we'll look at interprocedural context-insensitive taint analysis, and then explore a couple of different context-sensitivity strategies

## intraprocedural taint analysis
### intro

- we'll suppose that there are a set of functions that create tainted information, called _sources_, and a set of functions that consume potentially tainted information, called _sinks_

    + what these functions are will depend on the specific program we're analyzing; for our purposes we'll say any external function starting with `src` is a source and any external function starting with `snk` is a sink (e.g., `src123` is a source and `snk_blah` is a sink)

    + we'll assume only external functions can be sources and sinks just to keep things simple; we can easily extend it to allow for internal sources and sinks if we wanted to but it adds complexity without any pedagogical benefit

- a taint analysis solution is a map from each sink to the set of sources that _reach_ that sink; that is, there is some path along which information derived from or influenced by a source can be passed to the sink

    + we'll only care about which sources and sinks are involved, not which specific _objects_ or _call instructions_ were involved; e.g., if there are two different calls to the same source `src` and only one of them reaches a call to the sink `snk` (perhaps transmitted through some intermediate variable `x`), the solution will just say `snk --> { src }`

    + this is like reaching defs, where the analysis tracked for each variable what defs reached each program point but the final solution just mapped uses to the reaching defs for those uses; here our analysis will actually track for each variable what sources they are tainted by but the final solution will just record what sources reached what sinks

    + a more elaborate analysis can also take into account _sanitizers_ that kill taint, but we won't worry about that possibility here

- we'll take the result of an interprocedural, flow-insensitive pointer analysis as input (e.g., from an Andersen-style pointer analysis)

- an object (variable or heap object) becomes _tainted_ if it is (1) assigned the result of a call to a source; (2) reachable from a pointer returned from a source; or (3) reachable from a pointer passed to a call to the source

- a tainted object _reaches_ a a sink if (1) it is an argument to a call to the sink; or (2) it is reachable from a pointer passed as an argument to a call to the sink

- example

  ```
  extern src1: () -> int
  extern src2: (&int) -> _
  extern snk1: (int) -> _
  extern snk2: (&int) -> _
  
  fn main() -> int {
    let x:int, y:int, z:&int

    entry:
      x = $call_ext src1()
      z = $addrof y
      $call_ext src2(z)
      $branch x bb1 bb2

    bb1:
      y = $copy x
      $call_ext snk1(y)
      $jump exit

    bb2:
      $call_ext snk2(z)
      $jump exit

    exit:
      $ret 0
  }
  ```

  SOLUTION
  ```
  snk1 --> { src1 }
  snk2 --> { src2 }
  ```

### abstract domain

- an abstract value will be a set of sources, signifying which sources may have contributed to the concrete value

    + this is a second-order analysis like reaching defs; we don't care what the value is just where it came from

    + ⊤ = set of all sources

    + ⊥ = empty set

    + ⊔ = set union

- this domain is finite, hence necessarily complete and noetherian

- the abstract store will map variables to abstract values as usual

### abstract semantics

- helpers:

    + let `taint(op)` = `store[op]` if `op` is a variable, else `{}`

    + let `reachable(v...)` be the set of pointed-to objects reachable from the given arguments according to the points-to solution

- `x = $call_ext <source>(args...)`

    + `store[x] = {<source>}`
    + ∀v ∈ reachable(args), `store[v] = store[v] ∪ {<source>}`

    + notice that even if `x` is a pointer we don't taint the objects it points to; why not? because the things it points to can't have come from the external function (since the pointer analysis ignores external calls) therefore the source can't have tainted them; it's sufficient that anything accessed _through_ the pointer returned from the source will be tainted with that source

    + also note that we're assuming the external call didn't change the pointer analysis solution---again, if it did we would have stubbed it as an internal function so the pointer analysis could analyze it

- `x = $call_ext <non-source>(args...)`

    + `store[x] = {}`
    + we're replacing `x`'s value with something non-tainted (again, assuming that if the external function propagated taint we would have stubbed it as an internal function)

- `x = $call <sink>(args...)`

    + this is a subset of the instruction above, during the analysis treat it like that
    + we only need to do this once, after the analysis is finished (just like the reaching defs solution)
    + ∀v ∈ (args ∪ reachable(args)), `soln[<sink>] = soln[<sink>] ∪ store[v]`

- `x = $copy op`

    + `store[x] = taint(op)`

- `x = $arith <aop> op1 op2`

    + `store[x] = taint(op1) ⊔ taint(op2)`

- `x = $cmp <rop> op1 op2`

    + `store[x] = taint(op1) ⊔ taint(op2)`

- `x = $addrof y`

    + `store[x] = {}`

- `x = $alloc op [id]`

    + `store[x] = {}`
    + notice that `op` could have been tainted, but that doesn't taint `x` because `op`'s value can't influence `x`'s value (just the number of allocated objects)

- `x = $gep y op`

    + `store[x] = taint(y) ⊔ taint(op)`
    + in this case we take `op`'s taint into account because it can influence what `x` points to

- `x = $gfp y <field>`

    + `store[x] = taint(y)`

- `x = $load y`

    + `store[x] = (⨆_{v ∈ ptsto(y)} taint(v)) ⊔ taint(y)`

- `$store x op`

    + ∀v ∈ ptsto(x), `store[v] = store[v] ⊔ (taint(x) ⊔ taint(op))`

- `$jump` and `$branch`: propagate abstract store as normal

### example

- [give students points-to solution, have them come up with final abstract store and solution]

  ```
  extern src1: (&int) -> &int
  extern src2: () -> int
  extern snk1: (&int, &int) -> _
  extern snk2: (int) -> _
  extern snk3: (&int, &int) -> _

  fn main() -> int {
    let a:&int, b:&int, c:int, d:&int, e:&int, f:&int, g:int
    entry:
      a = $alloc 1 [_a1]
      b = $alloc 1 [_a2]
      b = $call_ext src1(a)
      c = $call_ext src2()
      d = $addrof c
      e = $gep a c
      f = $alloc 1 [_a3]
      $store f c
      g = $load e
      $call_ext snk1(a, d)
      $call_ext snk2(g)
      $call_ext snk3(b, f)
      $ret 0
  }
  ```

  POINTS-TO
  ```
  a --> { _a1 }
  b --> { _a2 }
  d --> { c }
  e --> { _a1 }
  f --> { _a3 }
  ```

  FINAL ABSTRACT STORE
  ```
  a --> {}
  b --> { src1 }
  c --> { src2 }
  d --> {}
  e --> { src2 }
  f --> {}
  g --> { src1, src2 }
  _a1 --> { src1 }
  _a2 --> {}
  _a3 --> { src2 }
  ```

  SOLUTION
  ```
  snk1 --> { src1, src2 }
  snk2 --> { src1, src2 }
  snk3 --> { src1, src2 }
  ```

## interprocedural analysis: icfg

- we need to revisit our old program representation of the CFG: now that we're propagating information to calls and back, we need something that shows the connections between functions

- Interprocedural Control-Flow Graph (ICFG): take the individual function CFGs and add edges

    + from a `$call_{dir, idr}` instruction to the entry nodes of all callee functions (note that we need pointer info to handle `$call_idr`)
    + from a `$ret` instruction back to the callsites for that function

- unlike intraprocedural analyses where we picked which function to analyze, for interprocedural analyses we always start at `main`...which functions we end up analyzing will depend on which ones are reachable from `main` via function calls

- note that we've already set up our CFG definition to make this easy by requiring direct and indirect calls to be terminals for basic blocks, so all we need to do is add the interprocedural edges (at least conceptually---you don't have to have an explicit representation of these edges as long as you can easily look up the callees for call instructions and the callsites for return instructions)

    - many definitions of CFGs allow calls in the middle of basic blocks, which is fine for intraprocedural analysis (the most common type of analysis done in compilers) but makes interprocedural analysis difficult

    - note that `$call_ext` isn't included in the ICFG, since we don't have any source code for the callee functions

- example:

  ```
  extern input: () -> int

  fn main() -> int {
    let x:int, y:int, z:int
    entry:
      x = $call_ext input()
      $branch x call_f1 call_f2

    call_f1:
      y = $call_dir func1(x) then ret_f1

    ret_f1:
      z = $arith add z 2
      $jump exit

    call_f2:
      y = $call_dir func2(x) then ret_f2

    ret_f2:
      z = $arith mul z 4
      $jump exit

    exit:
      $ret z
  }

  def func1(p1:int) -> int {
    let a:int
    entry:
      a = $call_dir func3(p1) then exit

    exit:
      a = $arith add a 1
      $ret a
  }

  def func2(p2:int) -> int {
    let b:int
    entry:
      b = $call_dir func3(p2) then exit

    exit:
      b = $arith mul b 2
      $ret b
  }

  def func3(p3:int) -> int {
    entry:
      $ret p3
  }
  ```

## context-insensitive interprocedural taint analysis
### prep

- we need to remember the connections between call instructions and callees (e.g., when we return from a callee what call instructions might we be returning to?) and also what information got returned from each callee (whose use we'll see soon)

    + we'll have a map `call_edges` : `callee function -> { set of call instructions (i.e., function + basic block) }`

    + we'll have a map `call_returned` : `function -> returned abstract store` that remembers for each function the abstract store it returns to its callers

- since we're now analyzing multiple functions we need to change `bb_in` from `basic block -> abstract store` to `function -> basic block -> abstract store` (remember that basic block labels can be repeated across functions, and in fact at least `entry` definitely will be)

    + again, we'll always start the analysis from the entry block of `main`

- we also need to change the worklist from a list of `basic block`s to a list of `(function, basic block)` pairs (or else have each basic block have a reference to its parent function)

- the worklist loop will still work the same (including propagating the abstract store to the target basic block for call terminals), with the addition of _also_ propagating abstract stores from calls to callees

### helpers

- intuition for handling calls:

    + what are we actually passing to a callee? the value of the arguments PLUS anything reachable from the arguments via the points-to solution

        - that is, we don't pass _everything_ in the caller's abstract store, just the things that the callee can actually access

    + what are we returning? the return value PLUS anything reachable from the return value PLUS anything reachable from the parameters

        - again we don't pass _everything_ in the callee's abstract store, just the things the caller can actually access

- we'll use several helper functions

    + `get_callee_store` : `(ptsto info, abstract store, callee function, args) -> abstract store` 
    
        - given the current abstract store at a call instruction, the callee function, and the call arguments, computes the abstract store that will be passed to the callee

        - what is that callee store? it maps each callee parameter to the abstract value of its corresponding argument AND copies each element of the current abstract store reachable from an argument via pointer derefences

        - example (sign analysis with pointer info):

>         // ptsto = [b -> {c}]
>         // store = [a -> Pos, c -> Neg]
>         x = $call_dir foo(a, 0, b) then bb
>
>         // callee store = [p1 -> Pos, p2 -> Zero, c -> Neg]
>         fn foo(p1:int, p2:int, p3:&int) -> int { ... }

        - one tricky corner case: the reachable elements may include the callee parameters (e.g., a recursive call whose argument is a pointer that reaches a parameter); be sure to use _weak updates_ to create the callee store to avoid unsoundness

    + `get_returned_store` : `(ptsto info, abstract store, current function, return operand(?)) -> abstract store`

        - given the current abstract store at a return instruction, the current function containing that return instruction, and the returned operand (if one exists), computes the abstract store that will be passed back to the call instruction

        - what is that returned store? this is a little tricky because we could be returning to multiple different call instructions and its slightly different depending on which call we're returning to based on the left-hand side of the call...we'll create a generic returned store and then specialize it later

            + it contains all variables reachable from the current function parameters via pointer deferences AND all variables reachable from the return instruction's variable operand (if there was one) AND the value of the returned operand (if there was one)

            + problem: what do we map to the value of the returned operand? it should be the left-hand side of the original call to this function, but there could be many such calls...for now use a placeholder fake variable (distinct from any variable in the program)

        - example (sign analysis with pointer info):

>         // returned store = [c -> Top, _placeholder -> Zero]
>         x = $call_dir foo(a, 0, b) then bb
>
>         // ptsto = [p3 -> c]
>         // current store = [p1 -> Neg, p2 -> Top, c -> Top, y -> Zero]
>         fn foo(p1:int, p2:int, p3:&int) -> int { ... $ret y }

    + `get_caller_store` : `(abstract store, optionally call lhs) -> abstract store`

        - given the returned abstract store and a specific call instruction's left-hand side, returns the abstract store returned to that specific call (i.e., with the placeholder value, if present, replaced by the left-hand side of the call, if present)

        - example 1 (sign analysis with pointer info):

>         // returned store = [c -> Top, _placeholder -> Zero]
>         // caller store = [c -> Top, x -> Zero]
>         x = $call_dir foo(a, 0, b) then bb

        - example 2 (sign analysis with pointer info):

>         // returned store = [c -> Top, _placeholder -> Zero]
>         // caller store = [c -> Top]
>         $call_dir foo(a, 0, b) then bb

### adding call and return instructions

- `[x =] $call_dir <func>(args...) then bb`

    + `store[x] = ⊥` (the actual value of `x` will be computed by the callee's `$ret`)
    + propagate `store` to `bb` as normal
    + notice that we're propagating the entire store to `bb` including the parts that we're also passing to the callee; that's because the callee can only make weak updates to those values (via a pointer) and so we don't lose any precision by also propagating them locally

    + now we need to handle the actual call, but there's a tricky corner case: what if we propagate info to the callee _but_ (1) the callee has already been analyzed from another call and so has pre-existing abstract stores; and (2) the current info we're propagating isn't adding any new info to that pre-existing info? then the callee analysis may never reach the `$ret` instruction and thus it's returned store may never get propagated back to _this_ call

    + to handle that tricky case we check if the callee already has a `returned store` and if so we preemptively propagate it here
    + if `call_returned[<func>]` = `ret_store` then
        - let `caller_store` = `get_caller_store(ret_store, x)`
        - propagate `caller_store` to `bb`

    + but we still need to propagate our current info to the callee in case it _is_ new information
    + let `callee_store` = `get_callee_store(ptsto, store, <func>, args)`
    + propagate `callee_store` to `(<func>, entry)`

- `x = $call_idr fp(args...) then bb`

    + mostly like `$call_dir`, but what if there are multiple callees (i.e., `fp` has a points-to set greater than 1)? we need the analysis to behave as if each callee is being called independently "in parallel"

    + handle `ret_store` and `callee_store` for each callee separately

    + note that `x` will be strongly updated in that its old value will be replaced, but its new value must be the _join_ of the return values from each callee; we'll handle that when we process a `$ret`

- `$ret op?`

    + if we're in `main` then there is no caller and we can skip this instruction

    + let `ret_store` = `get_returned_store(ptsto, store, <current function>, op?)`

    + set `call_returned[<current function>]` = `ret_store`

    + `∀(func, bb) ∈ call_edges[<current function>]` where the terminal of `bb` is `[x =] $call_{dir, idr} ... then next_bb`:

        - `caller_store` = `get_caller_store(ret_store, x)`
        - propagate `caller_store` to `(func, next_bb)`

    + notice that because propagation uses ⊔ and because we set `x` to ⊥ when handling the call instruction, the effect of this process is that the value of `x` for `next_bb` will be the join of the return values of each callee

### example

- [ask students to go over it in their head first, figuring out the solution without going through it step by step; then go over it together walking through it step by step]

CFG
```
extern src1: () -> int
extern src2: (&int) -> _
extern src3: () -> int
extern src4: () -> int
extern snk: (int) -> _

fn main() -> int {
  let a:int, b:&int, c:&int, d:&int, e:int, f:int, g:&int, h:&int, i:int, j:int

  entry:
    a = $call src1()
    b = $addrof a
    $branch a bb2 bb4

  bb2:
    c = $alloc 1 [_a1]
    $call_ext src2(c)
    d = $call_dir foo(c) then bb3

  bb3:
    e = $load d
    $jump exit

  bb4:
    f = $call src3()
    g = $addrof f
    h = $call foo(g)
    j = $load h
    $jump exit

  exit:
    $call snk(j)
    $ret 42
}

fn foo(p:&int) -> &int {
  let q:int, r:int, s:int, t:&int

  entry:
    q = $call src4()
    r = $load p
    s = $arith add q r
    t = $addrof s
    $ret t
}
```

POINTS-TO
```
b --> { a }
c --> { _a1 }
d --> { s }
g --> { f }
h --> { s }
p --> { _a1, f }
t --> { s }
```

SOLUTION
```
snk --> { src2, src3, src4 }
```

## adding context-sensitivity
### intro

- the context-insensitive analysis does not follow the concrete semantics for how function calls concretely work---it essentially transforms calls and returns into gotos, so that values can flow into a callee from one callsite and return at another, losing precision

    + the "context", in this case, is the execution context in which calls to the same function are made (e.g., callsite, function stack, arguments, etc)

- example:

  ```
  fn main() -> int {
    let taint1:int, taint2:int, a:int, b:int
    entry:
      taint1 = $call_ext src1()
      a = $call_dir foo(taint1) then bb1

    bb1:
      $call_ext snk1(a)
      taint2 = $call_ext src2()
      b = $call_dir foo(taint2) then exit

    exit:
      $call_ext snk2(b)
      $ret 0
  }

  fn foo(p:int) -> int {
    let taint3:int, taint4:int, c:int
    entry:
      $branch p bb1 bb3

    bb1:
      taint3 = $call_ext src3()
      c = $call_dir bar(p, taint3) then bb2

    bb2:
      $call_ext snk3(c)
      $jump exit

    bb3:
      taint4 = $call src4()
      c = $call_dir bar(p, taint4) then bb4

    bb4:
      $call_ext snk4(c)
      $jump exit

    exit:
      $ret c
  }

  fn bar(q:int, r:int) -> int {
    entry:
      q = $arith add q r
      $ret q
  }
  ```

  SOLUTION (CI)
  ```
  snk1 --> { src1, src2, src3, src4 }
  snk2 --> { src1, src2, src3, src4 }
  snk3 --> { src1, src2, src3, src4 }
  snk4 --> { src1, src2, src3, src4 }
  ```

- the arguments from one callsite propagate back to the other callsites, polluting the precision of the analysis

- to make the analysis more precise we can make it _context sensitive_: it can try to distinguish different contexts for the same callee function

    + the basic idea is to give each function context a unique id `cid`; then instead of `bb_in` : `function -> basic block -> abstract store` we have `bb_in` : `function + cid -> basic block -> abstract store`

        - in other words, we compute an entirely separate solution for each context of each function, and make sure to only propagate information between matching caller/callee contexts

    + we haven't said what a "context" is exactly; there are a number of different schemes and we'll go over two of the most common

    + as always, there is a cost: more precision means a more expensive, less scalable analysis

### call-string context-sensitivity

- one way to distinguish different contexts for the same callee function is to abstract the runtime function stack; essentially, mimic the concrete semantics of function calls

    + maintain a stack of callsites; this will be the context

    + when making a call, push the callsite onto the stack

    + when returning from a call, pop the top callsite off that stack and use that as the return point (rather than all possible callsites)

- to change the analysis to take advantage of context sensitivity:

    + the worklist now contains `(function + context, basic block)` pairs

    + instead of `bb_in` mapping from `basic block -> abstract store` now it maps from `(function + context, basic block) -> abstract store`

    + the context only changes when calling into or returning from some function, so analysis inside a single function works the same as before

- example: [use the intro example]

  SOLUTION (CALLSTRING, full callstack)
  ```
  snk1 --> { src1, src3, src4 }
  snk2 --> { src2, src3, src4 }
  snk3 --> { src1, src2, src3 }
  snk4 --> { src1, src2, src4 }
  ```

- while conceptually simple, there is an issue we need to address: the domain of possible callstacks ranges from exponential to infinite (depending on if there are any recursive cycles in the call graph)

    + we can see the exponential problem in the example we just gave; for the infinite problem, see the example below

- recursive example:

  ```
  fn foo(p:int) -> int {
    let a:int
    entry:
      $branch p bb1 exit

    bb1:
      p = $arith sub p 1
      a = $call_dir bar(p) then exit

    exit:
      $call_ext snk(a)
      $ret a
  }

  function bar(q:int) -> int {
    let b:int, c:int, d:int
    entry:
      b = $call_ext src()
      c = $arith add b q
      d = $call_dir foo(c) then exit

    exit:
      $ret d
  }
  ```

- to fix this, we need to modify our scheme to something called `k-limited call-string` context sensitivity

    + for some fixed constant `k`, we only use the top `k` elements of the callstack to determine what context we're in

- this k-limiting raises an issue: what context are we returning to? previously we had the entire callstack, but now we only have the top `k` entries
    
    + consider the 3-limited context `abc` that makes a call at callsite `d`, yielding the callee context `bcd`; when the callee returns, how does it know that it should return to the context `abc`?

    + we already have most of a solution in `call_edges`; extend it to from `callee function -> { set of call instructions }` to `(callee function + context) -> { set of (context + call instruction) pairs }`

    + update `call_edges` as before when processing a call instruction, just include the callee and caller contexts in the saved information

- another issue: what if the callee was already analyzed under a given callee context, but a different caller context? 
    
    + consider again the 3-limited context `abc` that makes a call at callsite `d` yielding the callee context `bcd`, and suppose that previously there was a context `xbc` that makes a call at `d`, yielding the same callee context
        
    + again we already have most of a solution in `call_returned` : `function -> returned abstract store`; extend it to `(function + context) -> returned abstract store`

    + update `call_returned` as before when processing a `$ret` instruction, just include the current context in the saved information

    + consult `call_returned` as before when processing a call instruction, but using context information

- [revisit intro example with k == 1, recursive example with k == 2]

  INTRO SOLUTION (CALLSTRING, k == 1)
  ```
  sink1 --> { src1, src2, src3, src4 }
  sink2 --> { src1, src2, src3, src4 }
  sink3 --> { src1, src2, src3 }
  sink4 --> { src1, src2, src4 }
  ```

  RECURSIVE EXAMPLE SOLUTION (k == 2)
  ```
  sink --> { src }
  ```

- an alternative, common way to handle recursive cycles is to treat all the functions within a cycle context-insensitively (i.e., transform recursive call/return edges into gotos)

    + we would usually do k-limiting anyway to combat exponential callstrings, but this can still help performance (but hurt precision)
    
    + we won't be doing this for our own implementations

- implementation tip: it will make your lives a bit easier if you implement the analysis s.t. each 'context' is represented with a unique integer id

    + this will make your lives easier because you can easily change the context-sensitivity strategy for your analysis just by changing how you derive the integer id

        - context-insensitive: id is the function name

        - call-string context-sensitive: id is derived from the k-limited callstack

        - other kinds of context-sensitivity (which we'll get to shortly): id is derived using the appropriate definition of context

    + the key is that the only difference between all of these schemes is how the context is defined; otherwise they all work exactly the same way

### functional context-sensitivity (aka partial transfer function)

- another way to distinguish different contexts for the same callee function is by remembering that the abstract transfer functions are, in fact, functions---and hence if we give them the same input they will always yield the same output

    + so we memoize: for each abstract input to a function, if we haven't seen it before then we analyze the function with that input and then remember the result; if we have seen it before then we just return the corresponding result without any further analysis

    + thus, a 'context' is an abstract store describing the inputs to a given function

- we'll use the exact same data structures as for the call-string strategy except using abstract stores as contexts instead of call-strings; all the things like `call_edges`, `call_returned`, the worklist, `bb_in`, etc stay the same

- example:

  ```
  fn foo(i:int) -> int {
    entry:
      $branch i bb1 bb4

    bb1:
      taint1 = $call_ext src1()
      a = $call_dir bar(taint1) then bb2

    bb2:
      $call_ext snk1(a)
      taint2 = $call_ext src2()
      b = $call_dir bar(taint2) then bb3

    bb3:
      $call_ext snk2(b)
      $jump bb7

    bb4:
      taint1 = $call_ext src1()
      a = $call_dir bar(taint1) then bb5

    bb5:
      $call_ext snk1(a)
      taint2 = $call_ext src2()
      b = $call_dir bar(taint2) then bb6

    bb6:
      $call_ext snk2(b)
      $jump bb7

    bb7:
      c = $arith add a b
      d = $call_dir bar(c) then exit

    exit:
      $call_ext snk3(d)
      $ret 0
  }

  fn bar(p:int) -> int {
    entry:
      r = $call_dir baz(p) then exit

    exit:
      $ret r
  }

  fn baz(q:int) -> int {
    entry:
      $ret q
  }
  ```

  SOLUTION:
  ```
  snk1 --> { src1 }
  snk2 --> { src2 }
  snk3 --> { src1, src2 }
  ```

- however, we still have an issue to address: just like with call-string, the number of possible contexts could be infinite (consider, e.g., constant propagation)

    + this only happens if our abstract domain is infinite, but if it is then we have to have some ad-hoc threshold beyond which we just merge contexts together

    + this won't happen for our assignment because our abstract domain for taint analysis is finite

### which scheme is better?

- call-string: what we're doing with this scheme is essentially statically expanding the ICFG to have a separate copy of each function for each possible callstack that reaches it

    + we don't have to actually create a separate copy for each context because we keep the different contexts separate for a given function by mapping `(function + context, basic block) --> abstract store`

    + the drawback of this scheme is that because we're separating contexts statically, we always keep different contexts separate even if it doesn't gain any additional precision; that is, even if merging two contexts wouldn't make the analysis any less precise, we still track them separately

- functional: what we're doing with this scheme is dynamically expanding the ICFG based on what abstract inputs we actually see during the analysis

    + unlike call-string we don't separate out different contexts for the same function unless it will (probably) benefit us (it's possible we'll still get the same output for different inputs, but less likely)

    + however, the number of possible contexts is a function of both the size of the abstract domain we're working with _and_ the details of the program we're analyzing; this means it's unpredictable and could potentially get extremely expensive

- so which is better? it depends

### other kinds of context-sensitivity
#### summary-based (aka transfer function)

- the partial transfer function approach (aka functional) memoized the callee context based on its abstract inputs, so if we saw the same abstract inputs we could just retrieve the previous result

    + the partial version re-analyzes the function for each new input, and only for inputs that actually happen during the analysis

- the full transfer function approach (aka summary-based) instead directly replaces a function with an abstract version that takes the function's input abtract values and returns the function's abstract result

    + essentially we transform the function body into a function on abstract values

    + whenever the function is called, the analysis applies this transfer function to the inputs to get the result; since this happens for every call to the same function, each call is treated independently

    + the full version _replaces_ the function entirely with this new transfer function

- example:

  ```
  fn main() -> int {
    let a:int, b:int, c:int, d:int, e:int
    entry:
      a = $copy -42
      b = $call abs(a)
      c = $copy 0
      d = $call abs(c)
      e = $arith add b d
      $ret e
  }

  fn abs(p:int) -> int {
    let t:int
    entry:
      t = $cmp lt p 0
      $branch t bb1 exit

    bb1:
      p = $arith sub 0 p
      $jump exit

    exit:
      $ret p
  }
  ```

- suppose we're doing sign analysis on the above program, which computes the sum of the absolute values of 0 and -42

    + a context-insensitive analysis would say that both calls to foo would return ⊤ (because it would either be Zero or Pos, and Zero ⊔ Pos = ⊤), so `e` is ⊤

    + we can replace all calls to `abs` with the transfer function: `abs(x) = (x == Neg) ? Pos : x`

    + now `b` is Pos and `d` is Zero, so `e` is Pos

- if we can compute a relatively simple summary for a function, this approach can work great

- however, for complex analyses (e.g., flow-sensitive pointer analysis) the summary can be at least as complicated as the function itself, and often expensive to compute at all

#### object sensitivity

- so far we've talked about abstracting function instances based on the stack (call-string) or inputs (partial or full transfer functions); researchers noticed that specifically for object-oriented languages neither approach was a great fit

- quick review of how methods work:

  OOP:
  ```
  Object* obj = new Object();
  obj->method(arg1, arg2);
  ```

  LOWERED VERSION (static dispatch, e.g., C++ non-virtual method):
  ```
  Object* obj = new Object();
  name_mangled_Object_method(obj, arg1, arg2);
  ```

- `obj` is a pointer to the _receiver object_, i.e., a pointer to the allocated object in memory on which the method is being called

- when doing a static analysis, the receiver object will be some abstract object (e.g., a static allocation site); object sensitivity uses this abstract object as the context for a context-sensitive analysis

    + it doesn't track the actual callsite, like call-string context sensitivity

    + it doesn't track the method arguments, like full or partial transfer functions

- [can't show an example using our IR, since it isn't object-oriented]

    + basically, for a method call `obj->method(...)` the context for analyzing `method` would be the abstract object pointed to by `obj`, as computed by some pointer analysis

#### CFL reachability

- this is a different approach to program analysis than either DFA or set constraint-based

- suppose that we take a program and compute its ICFG, then label each call edge to a function `foo` at program point `p` as `(_p` and each return edge from `foo` to program point `p` as `)_p`

- example: [use example from intro]

- context sensitivity is trying to emulate the concrete behavior of function calls: at a callsite we go to the callee function, execute it, and return the results back to the _same_ callsite---we shouldn't mix results from different calls together

    + that is, a _valid path_ for the analysis to take is one that respects the concrete flow of control for a function call

- notice that in the labeled ICFG, valid paths correspond to properly nested parentheses

    + [trace some valid paths in the ICFG and show the resulting strings from the labels]

- properly nested parentheses is a context-free language, i.e., it can be described using a context-free grammar or a pushdown automaton

    + notice the potential for confusion: we're talking about _context sensitivity_ using a _context free_ language; they're talking about different kinds of context

- so the CFL reachability approach to context sensitivity is to only allow the analysis to follow ICFG paths that are part of the well-matched parentheses language

    + CFL reachability == "context free language" reachability == from node X in the ICFG we can reach node Y using only valid paths according to some grammar

- we can use CFL reachability for other things besides context sensitivity as well (e.g., field sensitivity)

### context-sensitive heap models

- when doing pointer analysis we used a common heap model: an abstract object per static allocation site

    + we can make our pointer analysis context-sensitive using any of the methods outlined above (though it's not trivial and takes some thought and effort to make it work)

    + we could still use the same heap model as before, but there is an opportunity to do better

- example:

  ```
  fn main() -> int {
    let tainted:int, untainted:int, x:&int, y:&int
    entry:
      tainted = $call src()
      untainted = 42
      x = $call_dir foo() then bb1

    bb1:
      y = $call_dir foo() then bb2

    bb2:
      $store x tainted
      $store y untainted
      $call snk1(x)
      $call snk2(y)
  }

  fn foo() -> &int {
    let p:&int
    entry:
      p = $alloc 1 [_a1]
      $ret p
  }
  ```

- note that both `x` and `y` point to the same abstract object; it doesn't matter if we treat `foo` context-sensitively, we'll still get an imprecise result

- context-sensitive heap model: an abstract object per static allocation site _per context_

- example revisited: now `x` points to `_a1_context1` and `y` points-to `_a1_context2`

- as always, there's a cost: a context-sensitive heap model is more precise, but can be much more expensive

# sparse analysis and SSA
## efficiency problem with standard DFA

- the DFA algorithm we've been using has an efficiency problem: information is propagated indiscriminately throughout the CFG, delaying convergence

- example (constant propagation):

  ```
  fn foo(p:int) -> int {
  let x:int, y:int, z:int
  entry:
    x = $copy 4
    y = $copy 1
    $branch p if_true if_false

  if_true:
    y = $copy 3
    z = $copy x
    $jump exit

  if_false:
    z = $copy 3
    $jump exit

  exit:
    a = $arith add y z
    $ret a
  }
  ```

- notice that the `x` info is propagated to `if_false` and `exit` and the `y` info is propagated to `if_true` and `if_false`, none of which actually use this info

- this problem doesn't affect precision but does impact performance; it would be better to propagate information only where it is actually needed and nowhere else

    + for large programs this can mean the difference between taking seconds and taking hours, or between taking hours and taking days

## def-use chains and sparse analysis

- how do we know where information is needed? we already know how to figure this out: reaching definitions

- the reaching definitions solution allows us to directly connect where information is _produced_ (defs) to where it is _needed_ (uses)

    + this is basically the PDG with only data dependence edges, no control dependence edges

- replace the CFG with a "def-use graph":

    + nodes are statements (not basic blocks)

    + there is an edge `A-->B` if a def at instruction `A` reaches a use at instruction `B`

- run the standard DFA worklist algorithm, but on the def-use graph instead of the CFG

    + there is no single 'entry' point; the easiest thing to do is just initialize the worklist with all statements

- example: [reuse example from above]

## factoring def-use chains: SSA form

- there's still a problem with the def-use graph: the number of def-use edges can be quadratic in the number of statements

- example (using shorthand to remove irrelevant details):

  ```
  if (_)      x = _
  else if (_) x = _
  else        x = _

  if (_)      _ = x
  else if (_) _ = x
  else        _ = x
  ```

- [draw def-use graph]

- the solution is to _factor_ the def-use edges, which will give us _SSA form_

- a program is in _Static Single Assignment_ (SSA) form if every variable is defined with exactly one instruction and every use of a variable has exactly one reaching definition

- example 1: renaming variables to transform into SSA

  ```
  x = 4
  y = x
  x = 6
  y = x
  ```

  ```
  x1 = 4
  y1 = x1
  x2 = 6
  y2 = x2
  ```

- why _static_ single assignment? there is only a single textual assignment, but it can happen dynamically an arbitrary number of times

  ```
  while (_) x = x + 1
  ```

- there's still one complexity: what if multiple defs reach a single use?

  ```
  if (_)      x1 = _
  else if (_) x2 = _
  else        x3 = _

  if (_)      _ = x?
  else if (_) _ = x?
  else        _ = x?
  ```

- this is where _phi functions_ come in: a phi function is a choice operator that multiplexes multiple defs together

    + `x1 = phi(x2, x3, ...)` _means_ that `x1` is assigned the value of whichever reaching def (one of `x2, x3, ...`) is appropriate

  ```
  if (_)      x1 = _
  else if (_) x2 = _
  else        x3 = _
  x4 = phi(x1, x2, x3)
  if (_)      _ = x4
  else if (_) _ = x4
  else        _ = x4
  ```

- [draw the new def-use graph]

    + note that the number of edges has gone from quadratic to linear

- SSA form renders some analyses trivial (e.g., reaching defs) and others much easier and more efficient (e.g., sign analysis, constant propagation, slicing, taint analysis)

- to compute SSA form, we need to (1) insert phi functions, and (2) rename variables

## computing SSA form
### intro

- there are two parts to computing SSA:

    + where should phi functions be placed?

    + how to rename variables?

### placing phi functions

- where should we put these new phi functions?

- naive answer: at every join point in the CFG, put a phi function for every variable

    + this is way too many, most of them are unneeded

- better answer: for each variable `x`, if blocks `A` and `B` both define `x` and there are non-intersecting paths from `A` and `B` to block `Z`, then put a phi function for `x` at the top of block `Z`

    + this just says to find the earliest point in the CFG where multiple defs converge to one point, and put the phi function there

    + there are refinements that yield even fewer phi functions (e.g., require that variable `x` must be used at some point after `Z`), but this answer is a pretty good one

- fortunately, we already know how to compute this information: dominance frontiers

    + the dominance frontier of block `X` is the set of all blocks `Y` s.t. `X` dominates a predecessor of `Y` but does not strictly dominate `Y`

    + intuitively, the dominance frontier is the set of blocks that are _almost_ dominated by `X`, but just outside its control; or, it's the set of blocks that are the earliest point where some competing control-flow path may reach that block

- the dominance frontier of a variable definition is the set of blocks where we need to add phi functions for that variable

    + remember that we used _post-dominance_ for computing control dependence; now we're using _dominance_: same algorithm, just different direction of the CFG edges

    + note that when we add a phi function that this is a new definition of the variable, which itself may require more phi functions to be added

    + iterated dominance frontier: `DF⁺(S)` = the limit of the sequence:

        - `DF₁(S)` = `DF(S)`
        - `DFᵢ₊₁(S)` = `DF(S ∪ DFᵢ(S))`

    + let `S` be the set of definitions for variable `x`; then `DF⁺(S)` is exactly the set of blocks that require phi functions for `x`

### renaming variables

- after phi functions have been placed, we need to rename all the variables

- simple method:

    + compute reaching defs using the original variables names (but with phi functions in place); because of how we placed phi functions only one def will ever reach any use

    + for each definition, give the defined variable a unique name

    + for each use, replace that use with the unique name of the single reaching def

- this is easy but not as efficient as it could be; there's a more complicated but efficient version found in cytron et al, "efficiently computing static single assignment form and the control dependence graph"

### example

SOURCE
```
fn main() -> int {
  let a:int = input(), b:int = 2, c:int;
  if a > 0 {
    while a < 100 {
      a = a * 2;
    }
    c = a + b;
  }
  else {
    c = a + b;
  }
  return c + a;
}  
```

LIR
```
fn main() -> int {
let a:int, b:int, c:int, t1:int, t2:int, t3:int
entry:
  a = $call_ext input()
  b = $copy 2
  t1 = $cmp gt a 0
  $branch t1 else loop_hdr

loop_hdr:
  t2 = $cmp lt a 100
  $branch t2 loop_body loop_exit

loop_body:
  a = $arith mul a 2
  $jump loop_hdr

loop_exit:
  c = $arith add a b
  $jump exit

else:
  c = $arith add a b
  $jump exit

exit:
  t3 = $arith add c a
  $ret t3
}
```

DOM FRONTIERS
```
entry -> {}
loop_hdr -> { exit }
loop_body -> { loop_hdr }
loop_exit -> { exit }
else -> { exit }
exit -> {}
```

ITERATED DOM FRONTIERS (a)
```
DF₁({entry, loop_body}) = { loop_hdr }
DF₂({entry, loop_body, loop_hdr}) = { loop_hdr, exit }
DF₃({entry, loop_body, loop_hdr, exit}) = { loop_hdr, exit }
```

ITERATED DOM FRONTIERS (c)
```
DF₁({else, loop_exit}) = { exit }
DF₂({else, loop_exit, exit}) = { exit }
```

SSA
```
fn main() -> int {
let a1:int, a2:int, a3:int, a4:int, b:int, c1:int, c2:int, c3:int, t1:int, t2:int, t3:int
entry:
  a1 = $call_ext input()
  b = $copy 2
  t1 = $cmp gt a1 0
  $branch t1 else loop_hdr

loop_hdr:
  a2 = phi(a1, a3)
  t2 = $cmp lt a2 100
  $branch t2 loop_body loop_exit

loop_body:
  a3 = $arith mul a2 2
  $jump loop_hdr

loop_exit:
  c1 = $arith add a2 b
  $jump exit

else:
  c2 = $arith add a1 b
  $jump exit

exit:
  a4 = phi(a1, a2)
  c3 = phi(c1, c2)
  t3 = $arith add c3 a4
  $ret t3
}
```

## revisiting sparse analysis

- if the program is in SSA form and we're doing a first-order analysis (i.e., computing an abstraction of program variable values), then we can modify our worklist algorithm to operate on variables instead of basic blocks or statements

    + since there is a single assignment for each variable, we don't need separate abstract stores for each block: just a single, global abstract store for the whole program

    + there is no propagation of abstract stores or anything like that; in fact, we're doing effectively a flow-insensitive analysis, but because of being in SSA form we're getting a flow-sensitive solution

- example: constant propagation

  ```
  initialize store and worklist to empty

  for each variable x:
    if x is assigned a constant k:
      store[x] = k
      add x to worklist

  while worklist is not empty:
    pop x from worklist
    for each variable y defined using x:
      compute value for y using value of x
      store[y] = updated value
      if y's value changed: add y to worklist
  ```

- [demonstrate using previous example for computing SSA]

## dealing with pointers

- all of the examples i've given have contained _no_ pointers; what if they did have pointers? 

- there are basically two options

- option 1 (the option compilers use, like gcc and llvm): don't allow address-taken variables; any source-level address-taken variable is converted into a pointer instead, and we do SSA only for the program variables, not for the address-taken objects

    + example:

BEFORE
```
      x = $copy 0
      y = $addrof x
      z = x
```

AFTER
```
      x = $alloc 1 [id] // on stack, not on heap
      $store x 0
      y = x
      z = $load x
```

- people generally just call this SSA, but in terms of analysis i like to call it "partial SSA" because only top-level variables are really in SSA form; anything on the heap is not in SSA form

- option 2 (can be useful, but not the common choice): do a pointer analysis and then treat every store as a def and every load as a use of the appropriate address-taken objects and compute SSA as normal

    + the points-to sets can be large, and the pointer analysis is a "may point-to" analysis, so there can be many spurious def-use edges in this version

    + still useful: this is one of the techniques that i used to scale flow-sensitive pointer analysis to make it 100x more scalable

# MORE TOPICS

- type inference using equality-based analysis: borrow from my 162 notes to introduce a small functional language, a type system (which can be thought of as a kind of analysis), and hindley-milner type inference

    + this may be a lot of extra material not directly related to program analysis, perhaps too much

- logic-based analyses (DOOP-style); is there much to show?

    + andersen-style pointer analysis

    + making andersen-style context-sensitive

    + other analyses?

- abstract interpretation: grab my old notes from previous iterations of this course to introduce operational semantics, galois connections, etc

    + this may be too much, i'd likely have to prune it a lot
