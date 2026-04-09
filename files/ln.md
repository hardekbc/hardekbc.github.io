# RETROSPECTIVE

- cs40 won't be taught again, so these notes are more for applying to cs42 in the future

- i think it's a mistake to rely entirely on lectures; what i should have done is assigned reading, then reviewed important points in lecture (and given examples and exercises)

- think about whether it would be better to have weekly or semi-weekly assignments, in terms of how much material we can cover and interaction with quizzes

- think about whether a series of in-class quizzes is the right thing to do

- assignment 1 

    - Q1(c) could be read as an imperative or a proposition (maybe add !)
    - Q3(c) is missing parentheses to disambiguate between the ∨ and ∧ 

- can/should i make the assignments completely online with gradescope?

# SECOND LECTURE ANNOUNCEMENTS

- i miscounted, there are 9 weekly assignments not 8

- for the 5 weekly quizzes

    - they aren't supposed to be a surprise; my initial plan is Th of week 2, 4, 6, 8, 10
    - this may change if the pace isn't what i expect
    - i will drop your lowest quiz grade

- first assignment going out today, due next Tuesday; first quiz next Thursday

    - give them the option, either:
        - give 24 hours for late assignments, post solutions Th morning
        - don't give any late credit, post solutions immediately after it is due

    - assignment must be done using LaTeX
        - more information in syllabus
        - TAs will give a quick tutorial in section

    - DECISION: the class was split, i made the executive decision to not allow late submissions so that i can immediately give the solution

# intro to course

- introduce myself, TAs / ULAs

- course logistics (full information will be in the course syllabus; this is an overview)

    - we'll have a course slack

        - all communication should go through slack
        - be sure to stay on top of notifications; set preferences accordingly

    - office hours TBA; mix of days/times, in-person and via zoom

        - office hours are meant to help you---please take advantage!

    - discussion sections

        - no new material
        - examples, exercises, chance to work on assignments if time

    - assessment

        - 8 weekly assignments: 30%
        - 5 in-class quizzes: 30%
        - final: 40%

        - quizzes will be roughly one every two weeks, but exact times will depend on the pace of the lectures
            - make-up quizzes only with note from doctor or the office of the dean of students

        - assignments are a chance to practice and get help (quizzes/final are for evaluating mastery)
            - one day late = 20% penalty; no credit after that
            - we'll be releasing solutions

    - we have a textbook for this course (given in the syllabus)

        - not available in the university bookstore; have to acquire elsewhere
        - i don't teach from it, but it's useful as a study aide (covers the same material)

    - please ask questions and correct my mistakes!

        - questions are good: they let me know when i need to do a better job of explaining things and slow the pace of the lecture if it's going too fast

        - i'm only human, and sometimes when i'm writing things down my wires get crossed and i make mistakes...i'd much prefer that they're caught right away instead of going into people's notes and causing confusion later

- how to succeed in this course

    - the material builds on itself over the quarter
    - don't be left behind! avoid procastinating
    - the material is abstract and can be confusing
        - everyone can master it, but it takes work
        - the best way to learn: practice!

    - attend lectures, sections (remember that you need to be present for quizzes)
    - keep up with assignments (master material before quizzes happen)
    - ask for help when necessary
        - office hours
        - slack

- QUESTIONS?

# intro to material

- this course is about two things

    - logic and reasoning (i.e., proofs)
    - fundamental mathematical concepts and tools used in many fields of CS
    - the material is mathematical in nature, but extremely applicable to CS

- motivate why we care about logic and proof

    - we discover things via intuition, then prove our intuition is correct

    - f(n) = n² + n + 41; is the result always a prime?
        f(1) = 43, yes
        f(2) = 47, yes
        f(30) = 971, yes
        at f(40), the answer is no (1681 = 41²)

    - ∫_(0..∞) (sin(t) / t) ⬝ (sin(t / 101) / (t / 101))dt = π/2
        add product (sin(t / 201) / (t / 201)), still π/2
        ∀n ∈ ℕ, ∫_(0..∞) (Π_(m = 1..n), sin(t / 100n+1) / (t / 100n+1))dt ?= π/2
        no, pattern breaks at n = 7.4 × 10⁴³

    - f(n) = n/2 if n is even, 3n+1 if n is odd
        f(10) = 5 → 16 → 8 → 4 → 2 → 1 (→ 4 → 2 → 1...)
        f(24) = 24 → 12 → 6 → 3 → 10 → (same as above)
        ∀n ∈ ℕ, f(n) converges to 1?
        the is the collatz conjecture, and no one knows the answer
            - we've tried n past 10¹⁸ and it's always worked so far...

    - like testing vs verification for programs

- what do we want to prove in computer science?

    - algorithmic correctness (e.g., sorting algorithms, security protocols, distributed computing protocols, ...)
    - program correctness (or other properties, like security)
    - program equivalence (e.g., optimizations)
    - can a computational problem be solved or not?
    - can a solvable problem be solved efficiently or not?
    - etc...

- what is a proof?

    - a convincing argument
    - what this means is subjective and depends on the audience
        - legal proof vs scientific proof vs mathematical proof
        - mathematical ==> based on logic

- what is a formal proof? (as opposed to non-formal but rigorous proof)

    - formalize statements symbolically in logic
    - use logical rules of deduction for each proof step

- why formalize proofs when mathematicians usually don't?

    - in concept they should be able to formalize their proofs
    - formalization brings logic into the realm of computing
        - SAT solvers, SMT solvers, proof assistants, logic programming, databases, ...
    - mathematicians actually are formalizing things using proof assistants

- what else besides logic and proofs?

    - learn useful mathematical objects (set, function, relation)
    - learn useful tools like combinatorics, modular arithmetic
    - these are ideas and vocabulary that you are expected to know as a computer scientist
    - these ideas underly many important algorithms and data structures

- we'll combine these learning goals by doing proofs about these objects and tools

- do all computer scientists use the objects and tools we cover here?

    - YES (though not always formally)
    - sets show up everywhere as a basic container type when we don't care about multiplicity or ordering
    - graphs are a binary relation, hypergraphs are an n-ary relation
        - graphs are used to describe computer networks, social networks, programs (CFG), and many other things
        - graph reachability is the reflexive transitive closure of a binary relation
    - logic programming, databases are relational
    - functional programming is based on functions
    - ...

- do all computer scientists formalize and prove things all day?

    - NO
    - some definitely do, but not all
    - some focus on building systems and tools, not on proving things about them
    - even if people aren't doing formal proofs, they still need to reason about what they're doing
        - is my code doing what it's supposed to?
        - is my code using efficient data structures and algorithms for this problem?
        - informal reasoning is based on formal reasoning, we just don't write down all the definitions and steps
        - evaluating an argument requires understanding of logic and proof techniques

# propositional logic

- we'll start with propositional logic

    - what is "logic"? (method for making arguments about what is true)
    - what is a "proposition"? (a statement with a truth value)
    - [explain them]

- examples of logical arguments

    1. It will either rain or snow tomorrow.
       It’s too warm for snow.
       Therefore, it will rain.

    2. If today is Sunday, then I don’t have to go to work today.
       Today is Sunday.
       Therefore, I don’t have to go to work today.

    3. I will go to work either tomorrow or today.
       I’m going to stay home today.
       Therefore, I will go to work tomorrow.

- a logical argument asserts some _premises_ (things we assume to be true for the sake of the argument) and then a _conclusion_

    - an argument is _valid_ if, assuming that the premises are true, the conclusion must necessarily be true as well
    - valid doesn't mean _correct_, it means that _if_ the premises are true then so is the conclusion
    - each example argument has 2 _premises_ and 1 _conclusion_, and is valid

- example of _invalid_ argument

    Either the butler is guilty or the maid is guilty.
    Either the maid is guilty or the cook is guilty.
    Therefore, either the butler is guilty or the cook is guilty.

- symbolic propositions

    - an important aspect of these arguments is that the _content_ of the argument doesn't matter, only the _form_
    - [redo examples using propositional variables]

- [LECTURE 1 STOPPED HERE]

- NOTE FOR STUDENTS:
    - we're learning a new language, so we're going to be introducing a lot of new terms and definitions
    - be sure to write them all down
    - you'll become familiar with them by using them over and over (that's what the assignments are for)

- last time we showed that the contents of an argument aren't really relevant to determining whether it is a _valid_ argument (recall the definition of validity)

    - we can abstract from the contents by using propositional variables
    - [show previous examples but using variables]

- we still have some english in there, but english is ambiguous and imprecise; we will replace that english using symbols for various logical connectives (i.e., ways to combine propositions into larger propositions)

    - ∧ conjunction ("and"), operands called "conjuncts"
    - ∨ disjunction ("or"), operands called "disjuncts"
    - → implication ("implies", "if..then"), left of → is the "antecedent", right is the "consequent"
    - ¬ negation ("not")
    - these are the standard ones, there are more

- EXAMPLE: show previous examples completely symbolically

- it is an important skill to be able to translate informal arguments into formal symbols and vice-versa

- EXERCISE 1

    - [translate from english to symbols]

        1. Joe is going to leave home and not come back.
        2. Either Bill is at work and Jane isn’t, or Jane is at work and Bill isn’t.
        3. I will go to the game if you give me a ticket or money to buy a ticket.

        1. L = joe leaving home; B = joe coming back ==> L ∧ ¬B
        2. B = bill at work; J = jane at work ==> (B ∧ ¬J) ∨ (¬B ∧ J)
        3. G = go to game; T = ticket; M = money ==> (T ∨ M) → G

    - [translate from symbols to english]

        1. (¬S ∧ L) ∨ S, where S stands for “John is smart” and L stands for “John is lucky.”
        2. ¬S ∧ (L ∨ S), where S and L have the same meanings as before.
        3. (R ∨ S) → C, where R = it's raining, S = it's snowing, C = game is canceled
        
        1. Either John isn’t smart and he is lucky, or he’s smart.
        2. John isn’t smart, and either he’s lucky or he’s smart.
        3. if it is raining or snowing, then the game is canceled

- in logic we have to be precise about exactly what things mean; in particular we want to be very clear about what the logical connectives mean

    - does "P or Q" mean that exactly one is true, or at least one?
    - is "if P then Q" true or false when P is false but Q is true?

    - we define what the symbols mean using _truth tables_
        - [give truth tables for ∧ ∨ → ¬]
        - note that ∨ means _at least one_ is true, i.e., "inclusive or" (vs "exclusive or")
        - note that → is true whenever the antecedent is false

    - note that there are actually 16 possible binary connectives
        - four possible combinations of truth values
        - two possible truth value outcomes
        - 4² = 16

    - we don't have symbols for all of them because we don't need them

        - ∧ ∨ ¬ is actually sufficient to express all possibilities
            - e.g., A → B === ¬A ∨ B
            - [show via truth table]

        - we have a few other common ones like ↔ and ⊕, but they are just a convenience
            - A ↔ B ("A iff B") === (A → B) ∧ (B → A) === (¬A ∨ B) ∧ (¬B ∨ A)
            - A ⊕ B ("A xor B") === (A ∧ ¬B) ∨ (¬A ∧ B) [show via truth table]

- EXERCISE 2: write out a truth table for the given expressions

    1. (¬S ∧ L) ∨ S
    2. ¬(B ∧ C)

- we can use truth tables to determine validity (recall the definition of valid)

    - example (valid):

      P ∨ Q
      ¬Q
      -----
      P

    - example (invalid):

      P → Q
      Q
      -----
      P

- EXERCISE 3: determine validity of the following arguments [SKIPPED]

    1. (¬S ∧ L) ∨ S
       S
       ------------ (invalid)
       ¬L 

    2. ¬(B ∧ C) 
       L ∨ C
       -------- (valid)
       L ∨ ¬B

- important: we can study logical reasoning symbolically without caring about the content of what we are arguing about

- summary of what we've done so far

    - take prose expressions and arguments and represent them symbolically and precisely
    - reason about their truth values using truth tables

- CS CONNECTION: 
    - let T be represented by 1 and F by 0
    - we can see that combinational hardware circuits are computing propositional logic
    - learn more about this in CS 64 and CS 154

- some more vocabulary:

    - [in addition to proposition, premise, conclusion, validity]

    - tautology = an expression that evaluated to T no matter what the values of the variables are
        - example: A ∨ ¬A

    - contradiction = an expression that evaluated to F no matter what the values of the variables are
        - example: A ∧ ¬A

    - satisfiable = an expression that can evaluate to T for at least some variable values
        - an expression is either satisfiable or a contradiction

- EXERCISE 4: determine whether tautology, satisfiable, or contradiction [SKIPPED]

    - P ∨ (Q ∨ ¬P)  [tautology]
    - P ∧ ¬(Q ∨ ¬Q) [contradiction]
    - P ∨ ¬(Q ∨ ¬Q) [satisfiable]

- we can manipulate logical expressions similar to algebraic expressions; here are some laws (verifiable via truth table)

    - commutative
        - A ∧ B === B ∧ A
        - A ∨ B === B ∨ A

    - associative
        - (A ∧ B) ∧ C === A ∧ (B ∧ C)
        - (A ∨ B) ∨ C === A ∨ (B ∨ C)

    - idempotent
        - A ∧ A === A
        - A ∨ A === A

    - identity
        - False ∧ A === False
        - True ∨ A === True

    - tautology, contradiction
        - A ∧ True === A   (where True is any tautology)
        - A ∨ False === A  (where False is any contradiction)

    - distributive
        - A ∧ (B ∨ C) === (A ∧ B) ∨ (A ∧ C)
        - A ∨ (B ∧ C) === (A ∨ B) ∧ (A ∨ C)

    - absorption
        - A ∨ (A ∧ B) === A
        - A ∧ (A ∨ B) === A

    - double negation
        - ¬¬A === A

    - de morgan's
        - ¬(A ∧ B) === ¬A ∨ ¬B
        - ¬(A ∨ B) === ¬A ∧ ¬B

    - contrapositive
        - A → B === ¬B → ¬A

- [LECTURE 2 STOPPED HERE; SKIPPED THE EXAMPLES AND EXERCISE BELOW]

- EXAMPLES

    - ¬(P ∨ ¬Q)
      ¬P ∧ ¬¬Q   [by de morgan]
      ¬P ∧ Q     [by double negation] 

    - ¬(Q ∧ ¬P) ∨ P
      (¬Q ∨ ¬¬P) ∨ P [by de morgan]
      (¬Q ∨ P) ∨ P   [by double negation]
      ¬Q ∨ (P ∨ P)   [by associativity]
      ¬Q ∨ P         [by identity]

- EXERCISE 5: simplify the following expression as much as possible [USED FOR ASSIGN1]

    - ¬(P ∨ (Q ∧ ¬R)) ∧ Q

    SOLUTION
      (¬P ∧ ¬(Q ∧ ¬R)) ∧ Q      [by de morgan]
      (¬P ∧ (¬Q ∨ ¬¬R)) ∧ Q     [by de morgan]
      (¬P ∧ (¬Q ∨ R)) ∧ Q       [by double negation]
      ¬P ∧ ((¬Q ∨ R) ∧ Q)       [by associativity]
      ¬P ∧ (Q ∧ (¬Q ∨ R))       [by commutativity]
      ¬P ∧ ((Q ∧ ¬Q) ∨ (Q ∧ R)) [by distributivity]
      ¬P ∧ (Q ∧ R)              [by contradiction]

# sets and set operations

- before we continue with logic, we'll take a brief detour to talk about _sets_

    - sets underly many mathematical concepts we'll be discussing in this course
    - including predicate logic, which makes propositional logic more powerful

- DEFINITION: a set is an unordered collection of objects without repetition

    - {1, 2, 3} = {3, 2, 1}
    - {blue, apple, willow} = {apple, willow, blue, willow}

- sets can contain other sets: {1, {1}, {{1}}}

- sets can be finite or infinite (we'll talk a lot more about infinite sets later in the course)

    - {0, 1, 2, ...}       = the set of natural numbers
    - {..., -1, 0, 1, ...} = the set of integers

- if an object is contained within a set, we say that it is an _element of_ that set

    - 1 ∈ {1, 2, 3}
    - 4 ̸∈ {1, 2, 3}

- there are several sets that are so common in mathematics that they have their own symbols

    - the empty set:   Φ or {}
    - natural numbers: ℕ (also ℕ⁺)
    - integers:        ℤ (also ℤ⁺, ℤ⁻) [from "Zahlen", german for "numbers"]
    - rationals:       ℚ (ditto)      [from "quotient"]
    - reals:           ℝ (ditto)
    - complex:         ℂ (ditto)

- two sets are _disjoint_ if they have no elements in common

    - {1, 2} and {3, 4} are disjoint
    - {1, 2} and {2, 3} are not disjoint

- we can compare two sets using the _subset_ relation

    - if all elements in A are also in B, then A ⊆ B
    - if A ⊆ B and B ⊆ A then A = B

    - {1, 2} ⊆ {1, 2, 3}
    - {1, 2} ⊆ {1, 2}
    - {} ⊆ X for any set X

    - ⊆ is like ≤ except that for some pairs of sets, neither set is a subset of the other
    - {1, 2} ̸⊆ {1, 3} and {1, 3} ̸⊆ {1, 2}

- notation convention

    - a common convention is to make variables representing sets upper-case letters and variables representing elements of sets lower-case letters
    - it's just a convention, not a rule
    - obviously doesn't completely work when we have sets that contain other sets

- notice that a ∈ A and A ⊆ B are propositions, they are either true or false

- how can we define our own sets? there are two basic ways:

    - _enumeration_: list all of the elements
        
        - what if the set is infinite (or just really big)? 
        - then list enough elements to establish a pattern, e.g., {1, 3, 5, 7, ...}
        - note that this can still be ambiguous

    - _set comprehension_: use propositions to describe a membership test

        - {x | x is a positive odd integer}
        - "the set of elements x _such that_ x is a positive odd integer"
        - more symbolically: {x | x ∈ ℤ⁺ ∧ x is odd}
            - we'll see how to define `odd` logically later, we'll need predicate logic for that
        - note that `x` isn't special, we could use any variable

- by default a set can contain _any_ kind of object; if we want to restrict ourselves to only certain kinds of objects then we need to define a _universe of discourse_

- DEFINITION: a universe of discourse is the set containing all objects that are relevant to whatever we're doing

    - notation: often we use `U` to represent a generic universe of discourse

- let A = {x | x² < 9}; what are the elements of A?

    - if U = ℕ then A = {0, 1, 2}
    - if U = ℤ then A = {-2, -1, 0, 1, 2}
    - if U = ℝ then A = (-3..3)

- we can specify the universe of discourse directly in a set comprehension

    - {x ∈ ℕ | x² < 9}
    - {x ∈ ℤ | x² < 9}
    - {x ∈ ℝ | x² < 9}

- EXERCISE 6 (HTPI S1.3 exercise 7): list the elements of the following sets

    - {x ∈ ℝ | 2x² + x - 1 = 0}   [-1, 0.5]
    - {x ∈ ℝ⁺ | 2x² + x - 1 = 0}  [0.5]
    - {x ∈ ℤ | 2x² + x - 1 = 0}   [-1]
    - {x ∈ ℕ | 2x² + x - 1 = 0}   []

- we can apply various operations on sets to get new sets

    - intersection: A ∩ B = all elements that are in both A and in B
        - x ∈ A ∩ B ↔ x ∈ A ∧ x ∈ B
        - {1, 2, 3} ∩ {1, 2, 4} = {1, 2}
        - {1, 2} ∩ {a, b} = {} (note that A and B are disjoint iff A ∩ B = {})
        - {x ∈ ℤ | x² < 9} ∩ ℕ = {0, 1, 2}
        - if X = {A, B, ...} whose elements are sets, ⋂ X = A ∩ B ∩ ...

    - union: A ∪ B = all elements that are in either A or B
        - x ∈ A ∪ B ↔ x ∈ A ∨ x ∈ B
        - {1, 2, 3} ∪ {1, 2, 5} = {1, 2, 3, 4}
        - {1, 2} ∪ {a, b} = {1, 2, a, b}
        - {x ∈ ℤ | x² < 9} ∪ ℕ = {-2, -1, 0, 1, 2, 3, ...}
        - if X = {A, B, ...} whose elements are sets, ⋃ X = A ∪ B ∪ ...

    - complement: ¬A = all elements in U that aren't in A
        - sometimes written as an overline instead of ¬ 
        - requires a universe of discourse
        - x ∈ ¬A ↔ ¬ x ∈ A
        - U = ℕ: ¬{1, 2, 3} = {x | x = 0 ∨ x > 3}
        - U = ℕ: ¬{} = ℕ 
        - U = ℕ: ¬ℕ = {}

    - set difference: A \ B = all elements in A that aren't in B
        - x ∈ A \ B ↔ x ∈ A ∧ ¬ x ∈ B
        - {1, 2, 3} \ {1, 2, 4} = {3}
        - {1, 2} \ {a, b} = {1, 2}
        - {1, 2} \ {1, 2, 3} = {}

    - powerset: 𝒫(A) = the set of all subsets of A
        - X ∈ 𝒫(A) ↔ X ⊆ A
        - 𝒫({1, 2}) = { {}, {1}, {2}, {1, 2} }
        - 𝒫({}) = {}
        - {} ∈ 𝒫(A) for all sets A

- EXERCISE 7 (HTPI S1.4 example 1.4.2): let A = {1, 2, 3, 4, 5} and B = {2, 4, 6, 8, 10}; list the elements of the following sets:

    - A ∩ B               = {2, 4}
    - A ∪ B               = {1, 2, 3, 4, 5, 6, 8, 10}
    - A \ B               = {1, 3, 5}
    - (A ∪ B) \ (A ∩ B)   = {1, 3, 5, 6, 8, 10}
    - (A \ B) ∪ (B \ A)   = {1, 3, 5, 6, 8, 10}

- given the connection between set operations and propositional logic, it shouldn't be surprising that they share many laws

    - U is like True
    - {} is like False
    - ∩ is like ∧ 
    - ∪ is like ∨ 
    - ¬ is like ¬ 

    - any propositional law using only True, False, and these connectives applies also to sets

# predicate logic

- propositional logic is useful, but limited: it cannot relate different propositions that are about the same thing

    - A = `x > 10`; B = `x > 20`
    - if we prove B, that doesn't help us prove A

- predicate logic extends propositional logic to give us more expressive power

- a _predicate_ is a function from an object to a proposition

    - `P(x) = x > 10`   <-- predicate
    - P(0) = 0 > 10     <-- proposition
    - P(20) = 20 > 10   <-- proposition

    - `Q(x) = x has been to the moon` <-- predicate
    - Q(buzz aldrin)                  <-- proposition
    - Q(ben hardekopf)                <-- proposition

- notice that the body of a predicate is a proposition _except_ that it contains a variable standing for some argument

- a predicate should have some universe of discourse U that says what kinds of objects can be given as arguments

    - a predicate may be over integers, or sets, or people, or colors, or any other universe of discourse
    - e.g., P's universe of discourse is ℕ, and Q's is people

- a predicate can't have a truth table because it isn't a proposition (it has no truth value by itself)

    - e.g., what is the truth value of `x > 10`? it depends on the value of x
    - instead it has a _truth set_
    - the set of objects that, given as an argument, yield a true proposition

- EXAMPLE

    - truth set of P = {11, 12, 13, ...}
    - truth set of Q = {buzz aldrin, neil armstrong, ...}

- sometimes we want to talk about _how many_ objects in U make a predicate true; we can do so using _quantifiers_

    - `∀x, P(x)` means "for all objects o ∈ U, P(o) is true"
    - `∃x, P(x)` means "there exists at least one object o ∈ U s.t. P(o) is true"

    - U can be implicit, but we can also make it explicit similar to set comprehensions
        - ∀x ∈ ℕ, P(x)
        - ∃x ∈ ℕ, P(x)

    - a predicate wrapped in a quantifier is a proposition (i.e., it has a truth value)
    - note that the quantifier extends as far as possible: `∀x, P(x) ∧ Q(x)` = `∀x, (P(x) ∧ Q(x))`

- EXAMPLE

    - ∀x ∈ ℕ, x ≥ 0    [true]
    - ∀x ∈ ℤ, x ≥ 0    [false]
    - ∃x ∈ ℕ, x = 2    [true]
    - ∃x ∈ ℕ, x < 0    [false]

- EXERCISE (HTPI example 2.1.2): translate the following propositions into symbolic forms using quantifiers

    1. Someone didn't do the homework. 
        - soln: `∃x, ¬H(x)`
    2. Everything in that store is either overpriced or poorly made.
        - soln: `∀x ∈ StoreItems, Overpriced(x) ∨ Poorly-made(x)`
        -   or: `∀x, InStore(x) → Overpriced(x) ∨ Poorly-made(x)`
    3. Nobody's perfect.
        - soln: `¬∃x, Perfect(x)`
    4. Susan likes everyone who dislikes Joe.
        - soln: `∀x, ¬Likes(x, Joe) → Likes(Susan, x)`

- another view of quantifiers

    - we can think of ∀ as a series of conjunctions over all objects in U
    - we can think if ∃ as a series of disjunctions over all objects in U

    - `∀x ∈ {1, 2, 3}, P(x)` == P(1) ∧ P(2) ∧ P(3)
    - `∃x ∈ {1, 2, 3}, P(x)` == P(1) ∨  P(2) ∨ P(3)

    - if U is infinite then we can't actually write out the conjunctions or disjunctions, that's why we need the quantifiers

- we can have multiple quantifiers in a row, either the same or mixed

    - ∀x ∈ ℕ, ∀y ∈ ℕ, x > y → (x - 1) ≥ y
    - ∃x ∈ ℕ, ∃y ∈ ℕ, x ≤ y
    - ∀x ∈ N, ∃y ∈ ℕ, x + 1 = y

- for multiple of the same quantifier, order doesn't matter and we often collapse them into a single quantifier

    - `∀x ∈ ℕ, y ∈ ℕ, x > y → (x - 1) ≥ y` = `∀y ∈ ℕ, x ∈ ℕ, x > y → (x - 1) ≥ y`
    - `∃x ∈ ℕ, y ∈ ℕ, x ≤ y` = `∃x ∈ ℕ, y ∈ ℕ, x ≤ y`

- if the quantifiers are mixed, order _does_ matter

    - ∀x ∈ N, ∃y ∈ ℕ, x + 1 = y  [true]
    - ∃y ∈ ℕ, ∀x ∈ N, x + 1 = y  [false]

- CALL-BACKS

    - earlier we said propositional can't express that `P = x > 10` and `Q = x > 20` are related
        - `∀m,n,x ∈ ℤ, m > n ∧ x > m → x > n`

    - earlier we wanted to say "x is odd" symbolically but couldn't
        - `Odd(x) = ∃k ∈ ℕ, 2k + 1 = x`

- quantifier laws

    - we can push negation through a quantifier in a way similar to de morgan's law
        - pushing negation through ∀ flips to ∃ and through ∃ flips to ∀
        - `¬ (∀x ∈ N, ∃y ∈ ℕ, x + 1 = y)` = `∃x ∈ ℕ, ∀y ∈ ℕ, ¬ (x + 1 = y)` [or: `x + 1 ≠ y`]

    - ∀ distributes over conjunction (which makes sense because it is a series of conjunctions)
        - `∀x, P(x) ∧ Q(x)` = `(∀x, P(x)) ∧ (∀x, Q(x))`

    - ∃ distributes over disjunction (which makes sense because it is a series of disjunctions)
        - `∃x, P(x) ∨ Q(x)` = `(∃x, P(x)) ∨ (∃x, Q(x))`

- EXERCISE (HTPI example 2.2.1): negate each statement

    - A ⊆ B
        - `∀x, x ∈ A → x ∈ B`
        - soln: `∃ x, x ∈ A ∧ x ̸∈ B`
    - Everyone has a relative they don't like.
        - `∀x, ∃y, R(x,y) ∧ ¬L(x,y)`
        - soln: `∃x, ∀y, R(x,y) → L(x,y)`

- NOTE: HMWK FROM HTPI

    - S2.1 examples 2.1.4
    - S2.1 exercises 2, 3, (8,9,10)
    - S2.2 examples 2.2.3
    - S2.2 exercises (8,10), (11,12,14)

# proof methods
## intro

- we've looked at propositional and predicate logic and how to precisely specify definitions and properties

- we've also seen how to prove statements and arguments for propositional logic using truth tables

- the truth table-based approach doesn't scale or generalize

    - as we add more variables, tables become exponentially larger
    - tables don't work for predicate logic at all (infinite sets)

- we're going to look now at how to prove logical statements without regard to truth tables or any other external means

    - we'll rely on laws of reasoning that allow us to manipulate statements symbolically

    - remember that sometimes it's appropriate to formulate and prove something purely symbolically, and other times it's appropriate to formulate and prove them non-formally but still rigorously

    - regardless of which approach we use, we're still following the same laws of reasoning that we'll talk about now

    - i'll be presenting the rules themselves symbolically because it's easier to list and categorize them that way, but the proofs will mainly be non-formal (but still rigorous, i.e., following the rules)

- an important thing to keep in mind:

    - there is no mechanical process by which we can take a statement and create a proof for that statement (we talked about this fact last class)

    - non-trivial proofs will take some thought and creativity to figure out the right approach; often it requires some trial-and-error, hitting dead ends and backtracking to try a different approach

    - i'll be giving you the tools you need, but learning how to apply those tools the right way takes practice

- what are we trying to prove, and what is a proof exactly?

    - a theorem is a list of assumptions (the "hypotheses" or "givens") and a conclusion (or "goal")
    
        - these are all propositions (including quantified propositions)

    - a theorem is _true_ if, whenever all the givens are true, so is the goal

    - a proof is a valid deductive argument that shows how the goal must necessarily follow from the givens

- EXAMPLE (HTPI example 3.1.1)

    Let `x` and `y` be integers. Suppose `x > 3` and `y < 2`. Then `x² - 2y > 5`.

    Symbolically:
    `∀x,y ∈ ℤ, x > 3 ∧ y < 2 → x² - 2y > 5`

    Given:
    - x,y ∈ ℤ 
    - x > 3
    - y < 2
    
    ⊢ x² - 2y > 5

    Proof:
    - x² - 2y is smallest when x² is smallest and 2y is largest
    - x² is smallest when x is smallest (for positive x)
    - the smallest value of x is 4
    - 2y is largest when y is largest
    - the largest value of y is 1
    - 4² - 2(1) = 16 - 2 = 14 > 5
    - all larger values of x and smaller values of y must increase x² - 2y
    - ∴ x² - 2y > 5

- notice in the example proof that i didn't justify the statements that `x² - 2y` is smallest when `x²` is smallest and `2y` is largest, or that `x²` is smallest when `x` is smallest, or that `2y` is largest when `y` is largest

    - i took them as known algebraic facts that i don't have to justify

    - if i were doing the proof from scratch then i would need to actually prove those statements

    - so when is justification necessary and when is it not?

        - it depends on your audience and the context you're doing the proof in
        - it's a judgement call you need to make
        - you also have to be careful, e.g., if we allow `x` to be negative then the statement about `x²` is actually wrong

- we can also prove that a theorem is false by providing a _counterexample_, i.e., an example where the assumptions are all true but the conclusion is false

    Let `x` and `y` be integers. Suppose `x > 3`. Then `x² - 2y > 5`.

    Symbolically:
    `∀x,y ∈ ℤ, x > 3 → x² - 2y > 5`

    COUNTER-EXAMPLE: x = 4 and y = 8

## conjunction as goal

- TODO: (HTPI 3.4)

## conjunction as given

- TODO: (HTPI 3.4)

## disjunction as goal

- TODO: (HTPI 3.5)

## disjunction as given

- TODO: (HTPI 3.5)

## implication as goal

- to prove a goal P → Q, assume P is true and then prove Q

    - assuming P is true means adding it to your list of givens

    - Givens:
      - ⋮
      ⊢ P → Q

      === APPLY RULE ===

      Givens:
      - ⋮
      - P
      ⊢ Q

    - this rule doesn't tell you how to complete the proof, it only gives you one step along the way...the next steps would be determined by the form of Q and/or the form of the new given P

- why does this rule work?

    - remember that P → Q is automatically true whenever P is false, so we don't care about that case

    - the only time P → Q can be false is if P is true but Q is false

    - so if we assume P is true and can prove that Q is also true, then P → Q must be true

- EXAMPLE (HTPI example 3.1.2)

    Let `a` and `b` be real numbers. Prove that if `0 < a < b` then `a² < b²`.

    Symbolically:
    ∀a,b ∈ ℝ, 0 < a ∧ a < b → a² < b²

    Givens:
    - a,b ∈ ℝ 
    
    ⊢ 0 < a < b → a² < b²

    [APPLY RULE]

    Givens:
    - a,b ∈ ℝ 
    - 0 < a < b
    
    ⊢ a² < b²

    the form of the goal suggests multiplying `0 < a < b` by `a` and (separately) by `b`. since a and b must be positive, we can do the multiplications without swapping the inequality.

    Givens:
    - a,b ∈ ℝ 
    - 0 < a < b
    - 0 < a² < ab
    - 0 < ab < b²
    
    ⊢ a² < b²

    Since a² < ab and ab < b², a² < b².

    === IN PROSE FORM ===

    suppose `0 < a < b`. multiplying `0 < a < b` by `a` (which is positive) yields `0 < a² < ab`. multiplying `0 < a < b` by `b` (which is also positive) yields `0 < ab < b²`. therefore `a² < ab < b²`, so `a² < b²`.

- notice about the the prose proof:

    - it follows the same rule ("suppose `0 < a < b`..."), but you have to be aware of what is going on and why it works...it doesn't spell it out explicitly

    - it doesn't explain why it does what it does, there is no description of the though process behind the proof; only the final argument

    - the purpose of a proof is to provide an argument, not explain how that argument was arrived at; maybe the prover tried a number of different things before hitting on the one that worked, but those other tries aren't included in the final proof

- for your proofs, they should usually include more "scratch work" and explanation than you would see in a math paper or textbook proof

    - this allows us to follow what you were thinking and award partial credit if applicable

- also notice that we can use the propositional laws to rewrite goals before proving them

    - ⊢ P → Q ≡ ¬Q → ¬P
    - then apply our rule

- EXAMPLE (HTPI example 3.1.3)

    Suppose `a`, `b`, and `c` are real numbers and `a > b`. Prove that if `ac ≤ bc` then `c ≤ 0`.

    Symbolically:
        `∀a,b,c ∈ ℝ, a > b → ac ≤ bc → c ≤ 0`

    Givens:
    - a,b,c ∈ ℝ 
    ⊢ a > b → ac ≤ bc → c ≤ 0

    [APPLY RULE]

    Givens:
    - a,b,c ∈ ℝ 
    - a > b
    ⊢ ac ≤ bc → c ≤ 0

    [CONTRAPOSITIVE OF GOAL]

    Givens:
    - a,b,c ∈ ℝ 
    - a > b
    ⊢ c > 0 → ac > bc

    [APPLY RULE]

    Givens:
    - a,b,c ∈ ℝ 
    - a > b
    - c > 0
    ⊢ ac > bc

    since `c` is positive, we can multiple `a > b` by `c` without swapping the inequality, yielding the goal

    === PROSE PROOF ===

    We prove the contrapositive. suppose c > 0. then we can multiply `a > b` by `c` and conclude that `ac > bc`. therefore if `ac ≤ bc` then `c ≤ 0`.

- how did we know to use the contrapositive instead of proving the theorem directly? we tried both ways, and the second one works.

## implication as given

- TODO: (HTPI 3.2?)

## biconditionals

- TODO: (HTPI 3.4)

## negation as goal

- TODO: (HTPI 3.2)

## negation as given

- TODO: (HTPI 3.2)

## ∀ as goal

- TODO: (HTPI 3.3)

## ∀ as given

- TODO: (HTPI 3.3)

## ∃ as goal

- TODO: (HTPI 3.3)

## ∃ as given

- TODO: (HTPI 3.3)

# relations

- TODO: (HTPI 4)

# functions

- TODO: (HTPI 5)

# induction

- TODO: (HTPI 6)

# number theory

- TODO: (HTPI 7)

# infinite sets

- TODO: (HTPI 8)

# combinatorics

- TODO: (LaP 20)
