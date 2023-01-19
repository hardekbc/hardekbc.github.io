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

- example: signedness analysis (i.e., 0/positive/negative)

    + there are infinite possible concrete integers (assuming arbitrary precision integers); we'll approximate them using sets of integers corresponding to signedness

    + abstract values:

        + 0: {0}
        + pos: {n | n > 0}
        + neg: {n | n < 0}
        + bottom (undefined): {}
        + top (any value): N

        + notice that these form a partial order based on subset, corresponding to the precision of the abstract value

        + also notice that we could have included abstract values for "non-negative" and "non-positive"; defining the set of abstract values is part of analysis design

    + abstract semantics for addition:

        + 0 + 0 = 0
        + 0 + pos = pos
        + 0 + neg = neg
        + pos + pos = pos
        + pos + neg = top
        + neg + neg = neg
        + bottom + X = X
        + top + X = X (where X is not bottom)

    + example program (just doing things by eye, no specific algorithm):

    ```
    [1] x := input(0, 100) <-- x is top
    [2] if (x > 0) {       <-- x is pos
    [3]   y := x * x;      <-- y is pos
    [4]   z := 1/y;        <-- z is top (because could be 0)
    [5] }
    [6] else {             <-- x is 0
    [7]   y := x - x;      <-- y is 0
    [8]   z := 1/y         <-- division by 0
    [9] }
    ```

- now let's get a little bit more in-depth (though still high-level)

## control-flow graph (CFG)

- DFA was originally defined for flowchart-style programs with explicit and obvious control-flow. thus, it is based on the idea of a 'control-flow graph' (CFG, which is confusing if you're also talking about grammars). a CFG is just a representation of a program's possible execution paths.

- [define CFG using examples; talk about basic blocks---a block will always begin with a label that can be jumped to and end in a terminator instruction (ret, jump, branch)]

- EXAMPLE 1

  ```
  int x = input(), y = 0, z = 0;
  if (x) { x = 3; y = 2; } else { x = 2; y = 3; }
  z = x+y;
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

- EXAMPLE 2

  ```
  int x = input(), y = input(), z = input();
  while (z) {
    x = x+1;
    y = y+2;
    z = x/y;
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

- EXAMPLE 3

  ```
  int x = input(), y = input(), z = input();
  while (z) {
    while (y <= x) {
      y = y+2;
      z = x/y;
    }
    x = x+1;
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

- the usual assumption is that for each function there is a single entry basic block and a single exit basic block

    + if this isn't true (e.g., there are multiple return instructions) it's easy to massage the CFG to make it true (e.g., replace all the return instructions with jumps to a new basic block that contains a single return instruction).

- show IR example: analysis-impl/convert/testdata/test1.c, test1.nossa.ir; maybe show C program first and ask students to draw CFG, then show IR version

- notice that call instructions are not terminator instructions (they can appear in the middle of a basic block)

    + remember we're focusing for now on _intraprocedural analysis_, i.e., analyzing each function independently (and hence we make conservative assumptions about what calls can do); later on we'll look at _interprocedural analysis_ where the analysis crosses function boundaries.

    + while a call instruction does jump control flow, that control flow will always jump right back and so the central invariant of a basic block remains true: all instructions will be executed

## abstract domains

- naive approach to analysis: execute program on all possible inputs---obviously not tractable

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

- now that we're operating on abstract values instead of concrete values, we need to know how concrete operators map to abstract operators. when we see, e.g., `x + y` in a program, we're no longer operating on integers but on partitions of integers. the abstract versions of concrete operations are often called 'abstract transfer functions'.

- give abstract transfer functions for both example domains used above. do so for some other the arithmetic and comparison operators (with class help).

- parity

    + addition

    ```
        B | E | O | T
      +--------------
    B | B   B   B   B
    E | B   E   O   T
    O | B   O   E   T
    T | B   T   T   T
    ```

    + less-than (assume result of comparison is either 0 [false] or 1 [true])

    ```
        B | E | O | T
      +--------------
    B | B   B   B   B
    E | B   T   T   T
    O | B   T   T   T
    T | B   T   T   T
    ```

- signedness

    + addition

    ```
        B | + | 0 | - | T
      +------------------
    B | B   B   B   B   B
    + | B   +   +   T   T
    0 | B   +   0   -   T
    - | B   T   -   -   T
    T | B   T   T   T   T
    ```

    + less-than (assume result of comparison is either 0 [false] or 1 [true])

    ```
        B | + | 0 | - | T
      +------------------
    B | B   B   B   B   B
    + | B   T   0   0   T
    0 | B   +   0   0   T
    - | B   +   +   T   T
    T | B   T   T   T   T
    ```

- for both of these examples we can define the abstract transfer functions using tables because the abstract domains are finite; this isn't always the case (but again, to guarantee that infinite abstract domains still result in decidable analyses requires some math we'll get into later).

- we could combine these abstract domains if we want: an abstract value would be a pair `(parity, sign)`, with the obvious abstract semantics. this is generally true of all abstract domains, that they can be combined to get more precise domains.

- note that again we're being hand-wavy, we can't just make these transfer functions be anything we want and still ensure the analysis works---soon we'll need to formalize this notion of transfer function and give some restrictions to make sure they'll work properly.

## abstract execution (MOP vs MFP)

- given a CFG, abstract domains, and abstract transfer functions, how do we actually analyze a given program?

- DFA is inherently _flow-sensitive_: the goal is to compute an abstract value for each variable at each program point (i.e., each node of the CFG).

    + contrast with path-sensitive and flow-insensitive

- method 1: MOP. trace through each execution path in turn, then for each program point find the most precise abstract value that contains all the abstract values computed at the program point.

    + at best exponential time complexity, at worst non-termination

    + gives the best possible precision; serves as the "gold standard" for judging other methods

- method 2: MFP. treat the transfer functions as a system of equations and compute a fixpoint (more particularly, the most precise fixpoint).

    + fixpoint review:

        - define fixpoint of a function: an input that yields itself.

        - functions can have zero fixpoints, a single fixpoint, multiple but finite fixpoints, or infinite fixpoints.

        - example: f(x) = x²: fixpoints are 0, 1; least fixpoint is 0

    + so for MFP, we want to assign abstract values to each variable at each program point s.t. if we execute the abstract semantics on the program, we get the results we started with.

        - and specifically, we want the most precise abstract values for each variable that makes this true, i.e., the 'least fixpoint'

    + polynomial time complexity

    + [give the worklist algorithm for computing a fixpoint]

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

    + remember that fixpoints don't necessarily exist (and if they do there isn't necessarily a least fixpoint); later we'll worry about how to guarantee a least fixpoint exists and that we can find it (you guessed it: we need math)

- EXAMPLE: [do parity analysis; have students do signedness analysis as exercise]

  ```
    int x = input(), y = -2, z = 0;
    while (x < 10) {
      x = x + 1;
      y = y - 2;
      z = z + 1;
    }
    z = y + z;
    return z
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

- to get an intuitive understanding of the difference between MOP and MFP, consider the following example:

  ```
  int x = 0, y = input(), z = input();
  if (y) { x = 1; } else { x = 2; }
  if (z) { x = x + x; } else { x = x - x; }
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

    + [show MOP paths (exponential) then MFP paths (linear), then show that using parity domain MOP is more precise than MFP]

- an analysis is _distributive_ if, for transfer function F, merge(F(x), F(y)) = F(merge(x, y))

    + in other words, it doesn't matter if we merge abstract values before applying the transfer function or afterwards.

    + for distributive analyses, MOP = MFP; for non-distributive domains, MOP <= MFP

- this concludes the high-level overview of how DFA works; next we'll define some specific analyses in detail.

# defining the full signedness analysis
## abstract domain

- the signedness domain we've been using (BOT, TOP, 0, POS, NEG)

## abstract transfer functions

- `lhs = $arith <operation> op1 op2`

    + lhs, op1, op2 must be integer types (either constants or integer variables)
    + translate op1, op2 to abstract values (using alpha or abstract store)
    + apply abstract transfer function
    + update store to map lhs to result

- `lhs = $cmp <relation> op1 op2`

    + same as $arith

- `lhs = $phi(ops...)`

    + if lhs isn't an integer type, ignore instruction
    + merge abstract values of all arguments to the phi function
    + update store to map lhs to result

- `lhs = $copy op`

    + if lhs isn't an integer type, ignore instruction
    + update store to map lhs to abstract value of op

- `lhs = $load src_ptr`

    + if lhs isn't an integer type, ignore instruction
    + update store to map lhs to TOP

- `$store $dst_ptr op`

    + if op isn't an integer, ignore instruction
    + update store to map all integer-typed address-taken variables to TOP

- `lhs = $select op1 op2 op3`

    + if lhs isn't an integer type, ignore instruction
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

- we can ignore: `$ret` (because it doesn't do anything in an intraprocedural analysis) and `$addrof`, `$alloc`, `$gep` (because they only operate on pointers and we're ignoring pointers)

## MFP

- if you're using my C++ infrastructure, you'll want to use VarPtr_t to reference variables and BbPtr_t to reference basic blocks.

- use the standard worklist algorithm:

    + initialize empty abstract store for beginning of entry block (if looking up a variable that doesn't currently exist in the store, assume the value is BOT)

    + initialize worklist with entry basic block

    + merging two stores: merge the values for each variable in the two stores (again, if a variable doesn't exist assume its value is BOT)

- note that for the initial abstract stores of entry blocks for functions with integer parameters, those parameters should have the value TOP (we ignore non-integer parameters).

# second-order DFA analyses
???

# defining the full reaching defs analysis
???

# the math behind DFA
???
