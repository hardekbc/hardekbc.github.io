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

- sets can contain other sets: {1, {1}, { {1} } }

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

- NOTE: STOPPED HERE LAST LECTURE

- what are we trying to prove, and what is a proof exactly?

    - a theorem is a list of assumptions (the "hypotheses" or "givens") and a conclusion (or "goal")
    
        - these are all propositions (including quantified propositions)

    - a theorem is _true_ if, whenever all the givens are true, so is the goal

    - a proof is a valid deductive argument that shows how the goal must necessarily follow from the givens

    - sometimes a theorem is called a "lemma" or "corollary"; these are all really the same thing, just with different connotations

        - "lemma" implies the theorem isn't interesting for its own sake, only as a helper for proving some other theorem

        - "corollary" implies that the prove is a trivial application of an already-proved theorem

        - when in doubt, just use "theorem"

- EXAMPLE (adapted from HTPI example 3.1.1)

```
Let x and y be integers. Suppose x ≥ 3 and y ≤ 2. Then x² - 2y ≥ 5.

Symbolically:
∀x,y ∈ ℤ, x ≥ 3 ∧ y ≤ 2 → x² - 2y ≥ 5

Given:
- x ∈ ℤ
- y ∈ ℤ 
- x ≥ 3
- y ≤ 2
⊢ x² - 2y ≥ 5

Proof:
- x² - 2y is smallest when x² is smallest and 2y is largest
- x² is smallest when x is smallest (for positive x)
- the smallest value of x is 3
- 2y is largest when y is largest
- the largest value of y is 2
- 3² - 2(2) = 9 - 4 = 5, which is ≥ 5
- all larger values of x and smaller values of y must increase x² - 2y
- QED
```

- notice that the theorem is given without using logical symbols; we needed to translate it (in our heads, if not explicitly) into logic to see exactly what we're proving

    - this is common in non-formal proofs; the reader is expected to understand what the underlying logic is without it being made explicit

    - in this class we will constantly refer to the logical formulation of a statement because it makes it much more clear how to approach proving it (at least until the ideas become familiar enough that it isn't necessary anymore)

- also notice in the example proof that i didn't justify the statements that `x² - 2y` is smallest when `x²` is smallest and `2y` is largest, or that `x²` is smallest when `x` is smallest, or that `2y` is largest when `y` is largest

    - i took them as known algebraic facts that i don't have to justify

    - so when is justification necessary and when is it not?

        - it depends on your audience and the context you're doing the proof in
        - it's a judgement call you need to make
        - you also have to be careful, e.g., if we allow `x` to be negative then the statement about `x²` is actually wrong

- we can prove that a theorem is false by providing a _counterexample_, i.e., an example where the assumptions are all true but the conclusion is false

```
Let x and y be integers. Suppose x ≥ 3 and y ≤ 2. Then x² - 2y > 5.

Symbolically:
`∀x,y ∈ ℤ, x ≥ 3 ∧ y ≤ 2 → x² - 2y > 5`

COUNTER-EXAMPLE: x = 3 and y = 2
```

- i'll be giving you a bunch of rules for how to prove things that use different logical connectives, both as goals (things to prove) and as givens (things we can assume are true)

- for each one i'll give example proofs that demonstrate how to apply these rules

- the end goal is for you to be able to write your own proofs; this only comes with practice

## implication
### as goal

- to prove a goal P → Q, assume P is true and then prove Q

    - assuming P is true means adding it to your list of givens

```
Givens:
  ⋮
⊢ P → Q

[APPLY → GOAL RULE]

Givens:
  ⋮
  P
⊢ Q
```

- why does this rule work?

    - remember that P → Q is automatically true whenever P is false, so we don't care about that case

    - the only time P → Q can be false is if P is true but Q is false

    - so if we assume P is true and can prove that Q is also true, then P → Q must be true

- EXAMPLE (propositional)

```
Prove that P → (P → P).

Givens: none
⊢ P → (P → P)

[APPLY → GOAL RULE]

Givens:
- P
⊢ P → P

[APPLY → GOAL RULE]

Givens:
- P
⊢ P

QED
```

- EXAMPLE (HTPI example 3.1.2)

```
Let a and b be real numbers. Prove that if 0 < a < b then a² < b².

Symbolically:
∀a,b ∈ ℝ, 0 < a < b → a² < b²

Givens:
- a,b ∈ ℝ 
⊢ 0 < a < b → a² < b²

[APPLY → GOAL RULE]

Givens:
- a,b ∈ ℝ 
- 0 < a < b
⊢ a² < b²

[the form of the goal suggests multiplying `0 < a < b` by `a` and (separately) 
by `b`. since a and b must be positive, we can do the multiplications without
swapping the inequality.]

Givens:
- a,b ∈ ℝ 
- 0 < a < b
- 0 < a² < ab
- 0 < ab < b²
⊢ a² < b²

Since a² < ab and ab < b², a² < b². QED

=== PROSE PROOF ===

suppose 0 < a < b. multiplying 0 < a < b by a (which is positive) yields 
0 < a² < ab. multiplying 0 < a < b by b (which is also positive) yields 
0 < ab < b². therefore a² < ab < b², so a² < b².
```

- notice about the the prose proof:

    - it follows the same rule ("suppose `0 < a < b`..."), but you have to be aware of what is going on and why it works...it doesn't spell it out explicitly

    - it doesn't explain why it does what it does, there is no description of the thought process behind the proof; only the final argument

    - the prose proof is much shorter and less detailed than the scratch proof

    - the purpose of a proof is to provide an argument, not explain how that argument was arrived at; maybe the prover tried a number of different things before hitting on the one that worked, but those other tries aren't included in the final proof

- for your proofs, they should usually include more "scratch work" and explanation than you would see in a math paper or textbook proof

    - this allows us to follow what you were thinking and award partial credit if applicable

    - you don't necessarily need to always translate into symbolic form and show exactly which rules you're applying at each step (but again it can help, especially when you're getting started)

        - i'm showing how the givens and goals evolve at each step because i want to make it very easy to follow what's happening

        - if we were using a proof assistant like lean, it would take care of this for us automatically

- also notice that we can use the propositional laws to rewrite goals before proving them

    - ⊢ P → Q ≡ ⊢ ¬Q → ¬P
    - then apply our rule

- EXAMPLE (HTPI example 3.1.3)

```
Suppose a, b, and c are real numbers and a > b. Prove that if ac ≤ bc then c ≤ 0.

Symbolically:
∀a,b,c ∈ ℝ, a > b → ac ≤ bc → c ≤ 0

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

[since c is positive, we can multiple a > b by c without swapping the inequality,
yielding the goal]

Givens:
- a,b,c ∈ ℝ 
- a > b
- c > 0
- ac > bc
⊢ ac > bc

QED

=== PROSE PROOF ===

We prove the contrapositive. suppose c > 0. then we can multiply a > b by c 
and conclude that ac > bc. therefore if ac ≤ bc then c ≤ 0. QED
```

- how did we know to use the contrapositive instead of proving the theorem directly? we tried both ways, and the second one works.

### as given

- to use a given P → Q, you can use a given P to derive a new given Q

    - if there is no given P, you may have to prove it first in order to make it a given

    - as with the → goal you can apply logical laws to givens: if you have a given ¬Q, then you can rewrite P → Q as ¬Q → ¬P and then use ¬Q to conclude ¬P

```
Givens:
- P → Q
- P
⊢ …

[APPLY → GIVEN RULE]

Givens:
- P → Q
- P
- Q
⊢ …
```

- why does this rule work? because we know that P → Q can only be true if, whenever P is true, Q must also be true. since we know that P → Q is true and P is true (because they are givens), Q must also be true

- EXAMPLE

```
suppose A → B and B → C and A. Prove C.

Givens:
- A → B
- B → C
- A
⊢ C

[APPLY → GIVEN RULE]

Givens:
- A → B
- B → C
- A
- B
⊢ C

[APPLY → GIVEN RULE]

Givens:
- A → B
- B → C
- A
- B
- C
⊢ C

QED

=== PROSE PROOF ===

since A → B and A, then B. since B → C and B, then C. QED
```

## negation
### as goal (v1)

- to prove a goal ⊢ ¬P, first try to use logical laws to reexpress the goal as something positive

    - negatives can be hard to work with; if we can get rid of the negative the result will often be easier

- EXAMPLE (HTPI example 3.2.1)

```
Suppose A ∩ C ⊆ B and a ∈ C. Prove that a ̸∈ A \ B.

Symbolically:
A ∩ C ⊆ B ∧ a ∈ C → a ̸∈ A \ B

Givens:
- A ∩ C ⊆ B
- a ∈ C
⊢ a ̸∈ A \ B

[REWRITE GOAL]

Givens:
- A ∩ C ⊆ B
- a ∈ C
⊢ ¬ (a ∈ A ∧ a ̸∈ B)
=> ⊢ a ̸∈ A ∨ a ∈ B
=> ⊢ a ∈ A → a ∈ B

[APPLY → GOAL RULE]

Givens:
- A ∩ C ⊆ B
- a ∈ C
- a ∈ A
⊢ a ∈ B

[since a ∈ A and a ∈ C, a ∈ A ∩ C by definition of ∩]

Givens:
- A ∩ C ⊆ B
- a ∈ C
- a ∈ A
- a ∈ A ∩ C
⊢ a ∈ B

[definition of ⊆]

Givens:
- A ∩ C ⊆ B
- a ∈ C
- a ∈ A
- a ∈ A ∩ C
- a ∈ B
⊢ a ∈ B

QED

=== PROSE PROOF ===

suppose a ∈ A. then since a ∈ C, a ∈ A ∩ C. but then since A ∩ C ⊆ B, it follows
that a ∈ B. thus, it cannot be the case that a ∈ A but a ̸∈ B, so a ̸∈ A \ B.
```

### as goal (v2)

- if we cannot remove the negation, the solution is usually to do proof by contradiction

- proof by contradiction: if trying to prove P, assume ¬P and derive a contradiction; thus P

    - note that if our goal is ¬P, then we assume P (i.e., ¬¬P)

    - what is a contradiction in proof terms? proving that some proposition Q is true and proving that Q is false at the same time

    - note that the contradiction doesn't have to be about P itself, it may involve something that we derive from P

- why does this rule work?

    - we know that a proposition is either true or false

    - if it being false yields a contradiction, then it cannot be false

    - therefore it must be true

```
Givens:
  ⋮
⊢ ¬P

[APPLY PROOF BY CONTRADICTION]

Givens:
  ⋮
  P
⊢ False
```

- EXAMPLE (HTPI example 3.2.2)

```
let x, y be integers, x² + y = 13, and y ≠ 4. prove x ≠ 3.

Symbolically:
∀x,y ∈ ℤ, x² + y = 13 ∧ y ≠ 4 → x ≠ 3

Givens:
- x ∈ ℤ 
- y ∈ ℤ 
- x² + y = 13
- y ≠ 4
⊢ x ≠ 3

[we can't remove the negation in the goal, so use proof by contradiction]

Givens:
- x ∈ ℤ 
- y ∈ ℤ 
- x² + y = 13
- y ≠ 4
- x = 3
⊢ False

[we need to derive a contradiction...first substitute 3 for x]

Givens:
- x ∈ ℤ 
- y ∈ ℤ 
- 3² + y = 13
- y ≠ 4
- x = 3
⊢ False

[then solve for y]

Givens:
- x ∈ ℤ 
- y ∈ ℤ 
- y = 4
- y ≠ 4
- x = 3
⊢ False

QED

=== PROSE PROOF ===

we will prove by contradiction that x ≠ 3. suppose x = 3. then substituting for 
x in x² + y = 13 yields 9 + y = 13, which means y = 4. but this contradicts our
assumption that y ≠ 4. hence x ≠ 3.
```

- proof by contradiction lets us remove a negated goal by making it a non-negated given, but it leaves us with a very vague goal: derive _some_ contradiction

    - it's up to us to figure out what contradiction that might be

### as given

- as with goals, it is often useful to re-express a negated given in a positive form if possible

- if doing a proof by contradiction, it's a good idea to look carefully at the list of givens and try to find something of the form ¬Q (for some Q), and then try to derive Q as a contradiction

    - if there is no ¬Q, maybe there is a way to derive ¬Q (e.g., if we have the given P → ¬Q, then we could prove P to get ¬Q)

### more on proof by contradiction

- we introduced proof by contradiction as a way to prove a negated goal, but we can actually use it for any goal

    - this is part of the "creativity" requirement for proving things---there isn't any obvious sign that proof by contradiction would be useful, you just have to figure it out by trial and error or intuition

- EXAMPLE (HTPI example 3.2.3)

```
suppose A \ B ⊆ C and x is anything. prove that if x ∈ A \ C then x ∈ B.

Symbolic:
A \ B ⊆ C → ∀x, x ∈ A \ C → x ∈ B

Givens:
- A \ B ⊆ C
- object x
⊢ x ∈ A \ C → x ∈ B

[APPLY → GOAL RULE]

Givens:
- A \ B ⊆ C
- object x
- x ∈ A \ C
⊢ x ∈ B

[the form of the goal doesn't use any logical connectives, so it isn't clear 
what to do...so we try proof by contradiction]

Givens:
- A \ B ⊆ C
- object x
- x ∈ A \ C
- x ̸∈ B
⊢ False

[REWRITE GIVEN]

Givens:
- A \ B ⊆ C
- object x
- x ∈ A
- x ̸∈ C
- x ̸∈ B
⊢ False

[x ∈ A and x ̸∈ B means x ∈ A \ B]

Givens:
- A \ B ⊆ C
- object x
- x ∈ A
- x ̸∈ C
- x ̸∈ B
- x ∈ A \ B
⊢ False

[then by the first given, x ∈ C...which contradicts x ̸∈ C]

Givens:
- A \ B ⊆ C
- object x
- x ∈ A
- x ̸∈ C
- x ̸∈ B
- x ∈ A \ B
- x ∈ C
⊢ False

QED

=== PROSE PROOF ===

suppose x ∈ A \ C. this means x ∈ A and x ̸∈ C. we will prove x ∈ B by
contradiction. suppose x ̸∈ B. then x ∈ A \ B, and since A \ B ⊆ C then 
x ∈ C. but this contradicts the fact x ̸∈ C. therefore x ⁠∈ B.
```

## STUDENT EXERCISES

- EXERCISE (HTPI 3.2 exercise 4)

```
Suppose that x ∈ A and A \ B is disjoint from C. Prove that if x ∈ C then x ∈ B.

[SOLN]

Givens:
- x ∈ A
- A \ B ∩ C = ∅
⊢ x ∈ C → x ∈ B

We will prove the contrapositive, i.e., x ̸∈ B → x ̸∈ C. Suppose x ̸∈ B. Since
x ∈ A, x ∈ A \ B. But A \ B ∩ C = ∅, therefore x ̸∈ C.
```

- EXERCISE (HTPI 3.2 exercise 5)

```
Prove that it cannot be the case that x ∈ A \ B and x ∈ B \ C.

[SOLN]

Givens: none
⊢ ¬ (x ∈ A \ B ∧ x ∈ B \ C)

We will prove by contradiction. Suppose x ∈ A \ B and x ∈ B \ C. Then x ∈ A and
x ̸∈ B and x ∈ B and x ̸∈ C. But then x ̸∈ B and x ∈ B, which is a contradiction.
Therefore it cannot be that x ∈ A \ B and x ∈ B \ C.
```

- EXERCISE (HTPI 3.2 exercise 10)

```
Suppose that x and y are real numbers. Prove that if x ≠ 0, then if y =
(3x² + 2y)/(x² + 2) then y = 3.

[SOLN]

Givens:
- x, y ∈ ℝ 
⊢ x ≠ 0 → y = (3x² + 2y)/(x² + 2) → y = 3

Suppose x ≠ 0 and y = (3x² + 2y)/(x² + 2). Then y(x² + 2) = 3x² + 2y, so
yx² + 2y = 3x² + 2y. We can cancel 2y from both sides. Since x ≠ 0, we can
divide both sides by x². Therefore y = 3.
```

## ∀
### as goal

- to prove a goal `∀x, P(x)`, assume that there is an _arbitrary_ value `x` (i.e., that you don't know anything about) and prove `P(x)`

    - note that you can't re-use a variable name that is already being used to refer to something in the proof...it must be a _fresh_ name

    - notice that the proof example in the intro used the `∀ GOAL` rule

```
Givens:
  ⋮
⊢ ∀x ∈ S, P(x)

[APPLY ∀ GOAL RULE]

Givens:
  ⋮
- x ∈ S
⊢ P(x)
```

- why does this rule work? because if we can prove `P(x)` for an arbitrary `x` then it doesn't matter which `x` we pick, `P(x)` must be true

- EXAMPLE (HTPI example 3.3.2)

```
prove that if A ∩ B = A then A ⊆ B.

Symbolically:
A ∩ B = A → A ⊆ B

Givens: none
⊢ A ∩ B = A → A ⊆ B

[APPLY → GOAL RULE]

Givens:
- A ∩ B = A
⊢ A ⊆ B

[REWRITE GOAL]

Givens:
- A ∩ B = A
⊢ ∀x, x ∈ A → x ∈ B

[APPLY ∀ GOAL RULE with `a`]

Givens:
- A ∩ B = A
- object a
⊢ a ∈ A → a ∈ B

[APPLY → GOAL RULE]

Givens:
- A ∩ B = A
- object a
- a ∈ A
⊢ a ∈ B

[since A ∩ B = A, we can substitute A ∩ B for A anywhere in the proof]

Givens:
- A ∩ B = A
- object a
- a ∈ A ∩ B
⊢ a ∈ B

[REWRITE LAST GIVEN]

Givens:
- A ∩ B = A
- object a
- a ∈ A ∧ a ∈ B
⊢ a ∈ B

[APPLY ∧ GIVEN RULE]

Givens:
- A ∩ B = A
- object a
- a ∈ A
- a ∈ B
⊢ a ∈ B

QED

=== PROSE PROOF ===

suppose A ∩ B = A and x ∈ A. then since A ∩ B = A, x ∈ A ∩ B, so x ∈ B. since
x was an arbitrary element of A, A ⊆ B.
```

- notice that the prose proof didn't explicitly say "let x be an arbitrary element of A"; this is such a common and expected thing to do that people often don't feel it's worth calling out

- also notice that the prose proof didn't say things like "every element of `A \ C`" or "all elements of `A \ C`", it just talked about `x` (while not making any assumptions about _which_ element of `A \ C` `x` stands for)

### as given

- to use `∀x, P(x)` as a given, select a value for x and plug it in

```
Givens:
- ∀x, P(x)
- object a
⊢ …

[APPLY ∀ GIVEN RULE]

Givens:
- P(a)
- object a
⊢ …
```

- why does this rule work? we know P(x) is true for all x, so it must be true for any x

- EXAMPLE

```
suppose A ⊆ B and a ∈ A. prove a ∈ B.

Givens:
- A ⊆ B
- a ∈ A
⊢ a ∈ B

[REWRITE GIVEN]

Givens:
- ∀x, x ∈ A → x ∈ B
- a ∈ A
⊢ a ∈ B

[APPLY ∀ GIVEN RULE USING a]

Givens:
- a ∈ A → a ∈ B
- a ∈ A
⊢ a ∈ B

[APPLY → GIVEN RULE]

Givens:
- a ∈ A → a ∈ B
- a ∈ A
- a ∈ B
⊢ a ∈ B

QED
```

- we used this rule implicitly in some of our earlier proofs

## ∃
### as goal

- to prove a goal `∃x, P(x)`, pick a specific value for x that makes P(x) true

    - note that unlike ∀, where we deliberately did not specify a value for x, for ∃ we give a specific value

    - it may require some thought and creativity to figure out what value to choose for x...sometimes it's a value that's already in the givens, but sometimes it's not

    - a possible strategy: ask yourself, "if P(x) is true what must x be?" and work backwards

```
Givens:
  ⋮
⊢ ∃x, P(x)

[APPLY ∃ GOAL RULE WITH VALUE 'a']

Givens:
  ⋮
⊢ P(a)
```

- why does this rule work? because ∃ means there is at least one value for x that makes P(x) true...so to prove it all we need to do is fine one such value

- EXAMPLE (HTPI example 3.3.3)

```
prove that for every real number x, if x > 0 then there is a real number y such
that y(y + 1) = x.

Symbolically:
∀x ∈ ℝ, x > 0 → ∃y ∈ ℝ, y(y + 1) = x

Givens: none
⊢ ∀x ∈ ℝ, x > 0 → ∃y ∈ ℝ, y(y + 1) = x

[APPLY ∀ GOAL and → GOAL RULES]

Givens:
- x ∈ ℝ 
- x > 0
⊢ ∃y ∈ ℝ, y(y + 1) = x

[use the quadratic formula to solve for y: y(y + 1) = x ==> y² + y - x = 0 ==> 
y = (-1 +/- sqrt(1 + 4x))/2. note that the sqrt is defined because x > 0. also
note that we have two possible solutions...we only need one for the proof.]

[APPLY ∃ GOAL RULE USING (-1 + sqrt(1 + 4x))/2]

Givens:
- x ∈ ℝ 
- x > 0
- y = (-1 + sqrt(1 + 4x))/2
⊢ y(y + 1) = x

[do algebra]

Givens:
- x ∈ ℝ 
- x > 0
- y = (-1 + sqrt(1 + 4x))/2
⊢ x = x

QED

=== PROSE PROOF ===

suppose x ∈ ℝ and x > 0. let y = (-1 + sqrt(1 + 4x))/2. then y(y + 1) = ... = x.
```

### as given

- to use a given `∃x, P(x)`, introduce a fresh new variable to stand for some arbitrary value of x and plug it in

```
Givens:
- ∃x, P(x)
⊢ …

[APPLY ∃ GIVEN RULE]

Givens:
- object a
- P(a)
⊢ …
```

- why does this rule work? 

    - we know that there is some value for x such that P(x), but we don't know what it is

    - so we make a variable that stands for that unknown value of x and plug it in

    - since it's a fresh arbitrary variable, the only thing we know about it is P(x)

- EXAMPLE

```
suppose (∃x, x ∈ A) and A ⊆ B. prove ∃x, x ∈ B.

Givens:
- ∃x, x ∈ A
- A ⊆ B
⊢ ∃x, x ∈ B

[APPLY ∃ GIVEN RULE]

Givens:
- object a
- a ∈ A
- A ⊆ B
⊢ ∃x, x ∈ B

[APPLY ∃ GOAL RULE USING a]

Givens:
- object a
- a ∈ A
- A ⊆ B
⊢ a ∈ B

[since a ∈ A and A ⊆ B, a ∈ B]

Givens:
- object a
- a ∈ A
- A ⊆ B
- a ∈ B
⊢ a ∈ B

QED
```

## more on ∀ vs ∃ 

- note the symmetry between proof methods for ∀ and ∃ 

    - to prove goal ∀ introduce a fresh arbitrary variable
    - to use a given ∃ introduce a fresh arbitrary variable

    - to use a given ∀ select a specific value to plug in
    - to prove a goal ∃ select a specific value to plug in

- it's important not to get them confused

    - the best way to do that is to understand the intuition behind why each proof method works in the appropriate scenario and why it fails in the wrong scenario

## conjunction 
### as goal

- to prove a goal P ∧ Q, separately prove P true and Q true

```
Givens:
  ⋮
⊢ P ∧ Q

[APPLY ∧ GOAL RULE]

Givens:
  ⋮
⊢ P
⊢ Q
```

- why does this work? because P ∧ Q is true iff both P and Q are true

### as given

- to use a given P ∧ Q, treat it as two separate givens (one for P and one for Q)

```
Givens:
- P ∧ Q
⊢ …

[APPLY ∧ GIVEN RULE]

Givens:
- P
- Q
⊢ …
```

- why does this work? because the only way P ∧ Q could be true is if both P and Q are true

### EXAMPLE

```
Prove that A ∧ B → B ∧ A.

Givens: none
⊢ A ∧ B → B ∧ A

[APPLY → GOAL RULE]

Givens:
- A ∧ B
⊢ B ∧ A

[APPLY ∧ GIVEN RULE]

Givens:
- A
- B
⊢ B ∧ A

[APPLY ∧ GOAL RULE]

Givens:
- A
- B
⊢ B
⊢ A

QED

=== PROSE PROOF ===

suppose we have A ∧ B. then A is true and B is true. therefore, B ∧ A is true.
```

- notice that the proof example in the intro used the `∧ GIVEN` rule

## biconditional

- a biconditional P ↔ Q is the conjunction of two implications: P → Q ∧ Q → P

- if a given or goal is a biconditional, rewrite it as a conjunction of implications and proceed using the ∧ and → rules

```
Givens:
- A ↔ B
⊢ C ↔ D

[REWRITE BOTH GIVEN AND GOAL]

Givens:
- (A → B) ∧ (B → C)
⊢ (C → D) ∧ (D → C)
```

- EXAMPLE (HTPI example 3.4.3)

    - we will use the following facts:

        - Even(x) = ∃k ∈ ℤ, x = 2k
        - Odd(x) = ∃k ∈ ℤ, x = 2k + 1
        - every integer is either even or odd, but not both (we don't have the tools yet to prove this fact, but we will later)

```
suppose x is an integer. prove that x is even iff x² is even.

Symbolically:
∀x ∈ ℤ, Even(x) ↔ Even(x²)

Givens:
- x ∈ ℤ 
⊢ Even(x) ↔ Even(x²)

[REWRITE GOAL]

Givens:
- x ∈ ℤ 
⊢ (Even(x) → Even(x²)) ∧ (Even(x²) → Even(x))

[APPLY ∧ GOAL RULE]

Givens:
- x ∈ ℤ 
⊢ Even(x) → Even(x²)
⊢ Even(x²) → Even(x)

[PROVE FIRST GOAL]

Givens:
- x ∈ ℤ 
⊢ Even(x) → Even(x²)

[APPLY → GOAL RULE]

Givens:
- x ∈ ℤ 
- Even(x)
⊢ Even(x²)

[REWRITE GOAL AND GIVEN]

Givens:
- x ∈ ℤ 
- ∃k ∈ ℤ, x = 2k
⊢ ∃k ∈ ℤ, x² = 2k

[APPLY ∃ GIVEN RULE]

Givens:
- x ∈ ℤ 
- k ∈ ℤ 
- x = 2k
⊢ ∃k ∈ ℤ, x² = 2k

[note that the k in the goal is different from the k in the givens.]
[if x = 2k, then x² = (2k)² = 4k² = 2(2k²)]
[APPLY ∃ GOAL RULE USING 2k²]

Givens:
- x ∈ ℤ 
- k ∈ ℤ 
- x = 2k
⊢ x² = 2(2k²)

[SUBSTITUTE 2k FOR x AND SIMPLIFY]

Givens:
- x ∈ ℤ 
- k ∈ ℤ 
- x = 2k
⊢ 4k² = 4k²

[PROVE SECOND GOAL]

Givens:
- x ∈ ℤ 
⊢ Even(x²) → Even(x)

[CONTRAPOSTIVE OF GOAL]

Givens:
- x ∈ ℤ 
⊢ ¬Even(x) → ¬Even(x²)

[USE FACT THAT ¬EVEN MUST BE ODD]

Givens:
- x ∈ ℤ 
⊢ Odd(x) → Odd(x²)

[APPLY → GOAL RULE AND REWRITE GOAL AND GIVEN]

Givens:
- x ∈ ℤ 
- ∃k ∈ ℤ, x = 2k + 1
⊢ ∃k ∈ ℤ, x² = 2k + 1

[APPLY ∃ GIVEN RULE]

Givens:
- x ∈ ℤ 
- k ∈ ℤ 
- x = 2k + 1
⊢ ∃k ∈ ℤ, x² = 2k + 1

[x² = (2k + 1)² = 4k² + 4k + 1 = 2(2k² + 2k) + 1]
[APPLY ∃ GOAL RULE USING 2k² + 2k]

Givens:
- x ∈ ℤ 
- k ∈ ℤ 
- x = 2k + 1
⊢ x² = 2(2k² + 2k) + 1

[PLUG IN 2k + 1 FOR X AND SIMPLIFY]

Givens:
- x ∈ ℤ 
- k ∈ ℤ 
- x = 2k + 1
⊢ 2(2k² + 2k) + 1 = 2(2k² + 2k) + 1

QED

=== PROSE PROOF ===

Proof.

(→) suppose x is even. then for some integer k, x = 2k. therefore, x² = 4k² = 
2(2k²). since 2k² is an integer, x² is even.

(←) we prove the contrapositive. suppose x is odd. then for some integer k, 
x = 2k + 1. therefore, x² = 4k² + 4k + 1 = 2(2k² + 2k) + 1. since 2k² + 2k is
an integer, x² is odd.
```

## disjunction
### as goal

- to prove a goal `P ∨ Q`, simply choose one or the other to prove

```
Givens:
  ⋮
⊢ P ∨ Q

[APPLY ∨ GOAL RULE]

Givens:
  ⋮
⊢ P
```

- why does this rule work? because we only need to prove one of them for the disjunction to be true

- EXAMPLE

```
prove a ∈ A → a ∈ A ∪ B

Givens: none
⊢ a ∈ A → a ∈ A ∪ B

[APPLY → GOAL RULE]

Givens:
- a ∈ A
⊢ a ∈ A ∪ B

[REWRITE GOAL]

Givens:
- a ∈ A
⊢ a ∈ A ∨ a ∈ B

[APPLY ∨ GOAL RULE]

Givens:
- a ∈ A
⊢ a ∈ A

QED
```

- it can be useful to go even further and assume that whichever one you are not proving is false

```
Givens:
  ⋮
⊢ P ∨ Q

[APPLY ∨ GOAL RULE]

Givens:
  ⋮
- ¬Q
⊢ P
```

- why does this rule work? if Q is true then P ∨ Q is automatically true, so the only thing we really need to prove is that P is true when ¬Q

- EXAMPLE (HTPI example 3.5.4)

```
prove that for every real number x, if x² ≥ x then either x ≤ 0 or x ≥ 1.

Symbolically:
∀x ∈ ℝ, x² ≥ x → (x ≤ 0 ∨ x ≥ 1)

Givens: none
⊢ ∀x ∈ ℝ, x² ≥ x → (x ≤ 0 ∨ x ≥ 1)

[APPLY ∀ GOAL and → GOAL RULES]

Givens:
- x ∈ ℝ 
- x² ≥ x
⊢ x ≤ 0 ∨ x ≥ 1

[APPLY ALT ∨ GOAL RULE]

Givens:
- x ∈ ℝ 
- x² ≥ x
- x > 0
⊢ x ≥ 1

[since x > 0 we can divide the inequality by x]

Givens:
- x ∈ ℝ 
- x² ≥ x
- x > 0
- x ≥ 1
⊢ x ≥ 1

QED

=== PROSE PROOF ===

suppose x² ≥ x. if x ≤ 0 then clearly x ≤ 0 or x ≥ 1. now suppose x > 0. then we
can divide both sides of the inequality x² ≥ x by x, yielding x ≥ 1.
```

### as a given (v1)

- an easy way to use a given P ∨ Q is if you can derive ¬P, in which case you can immediately conclude Q (or vice-versa)

```
Givens:
- P ∨ Q
- ¬P
⊢ …

[APPLY ∨ GIVEN RULE 1]

Givens:
- P ∨ Q
- ¬P
- Q
⊢ …
```

- why does this rule work? because at least one must be true, so if P is false then Q must be true (and vice-versa)

- also notice that P ∨ Q is the same thing as ¬P → Q, so really this is just the → goal rule in disguise

    - of course, P ∨ Q = Q ∨ P = ¬Q → P, so it works the other way around as well

### as given (v2)

- to use a given `P ∨ Q`, use "proof by cases"

- consider the case where P is true and prove the goal, then separately consider the case where Q is true and prove the same goal

```
Givens:
- P ∨ Q
⊢ R

[APPLY ∨ GIVEN RULE]

Givens:
- P
⊢ R

Givens:
- Q
⊢ R
```

- why does this rule work? because we know at least one of P or Q must be true (but we don't know which), so if we prove the goal using P and prove the goal using Q, then the goal must be true no matter which one is true

- note that the cases must be _exhaustive_, i.e., they must cover all possibilities

- EXAMPLE (HTPI example 3.5.1)

```
prove that if A ⊆ C and B ⊆ C then A ∪ B ⊆ C.

Symbolic:
A ⊆ C ∧ B ⊆ C → A ∪ B ⊆ C

Givens: none
⊢ A ⊆ C ∧ B ⊆ C → A ∪ B ⊆ C

[APPLY → GOAL and ∧ GIVEN RULES]

Givens:
- A ⊆ C
- B ⊆ C
⊢ A ∪ B ⊆ C

[REWRITE GOAL AND GIVENS]

Givens:
- ∀x, x ∈ A → x ∈ C
- ∀x, x ∈ B → x ∈ C
⊢ ∀x, x ∈ A ∪ B → x ∈ C

[APPLY ∀ GOAL RULE]

Givens:
- ∀x, x ∈ A → x ∈ C
- ∀x, x ∈ B → x ∈ C
- object a
⊢ a ∈ A ∪ B → a ∈ C

[APPLY ∀ GIVEN RULE (twice)]

Givens:
- a ∈ A → a ∈ C
- a ∈ B → a ∈ C
- object a
⊢ a ∈ A ∪ B → a ∈ C

[APPLY → GOAL RULE]

Givens:
- a ∈ A → a ∈ C
- a ∈ B → a ∈ C
- object a
- a ∈ A ∪ B
⊢ a ∈ C

[REWRITE GIVEN]

Givens:
- a ∈ A → a ∈ C
- a ∈ B → a ∈ C
- object a
- a ∈ A ∨ a ∈ B
⊢ a ∈ C

[PROOF BY CASES]

[CASE 1]

Givens:
- a ∈ A → a ∈ C
- a ∈ B → a ∈ C
- object a
- a ∈ A
⊢ a ∈ C

[APPLY → GIVEN RULE]

Givens:
- a ∈ A → a ∈ C
- a ∈ B → a ∈ C
- object a
- a ∈ A
- a ∈ C
⊢ a ∈ C

QED FOR CASE 1

[CASE 2]

Givens:
- a ∈ A → a ∈ C
- a ∈ B → a ∈ C
- object a
- a ∈ B
⊢ a ∈ C

[APPLY → GIVEN RULE]

Givens:
- a ∈ A → a ∈ C
- a ∈ B → a ∈ C
- object a
- a ∈ A
- a ∈ C
⊢ a ∈ C

QED FOR CASE 2

=== PROSE PROOF ===

suppose A ⊆ C and B ⊆ C, and let x ∈ A ∪ B. then either x ∈ A or x ∈ B.

case 1: x ∈ A. then since A ⊆ C, x ∈ C.
case 2: x ∈ B. then since B ⊆ C, x ∈ C.

x was an arbitrary element of A ∪ B, therefore A ∪ B ⊆ C.
```

## more on proof by cases

- proof by cases doesn't require an explicit ∨ in the givens; any proof can be broken into cases as long as the cases exhaust all possibilities

- EXAMPLE (HTPI example 3.5.3)

```
prove that for every integer x, the remainder of x² / 4 is either 0 or 1.

Symbolically:
∀x ∈ ℤ, x² % 4 = 0 ∨ x² % 4 = 1

Givens:
- x ∈ ℤ 
⊢ x² % 4 = 0 ∨ x² % 4 = 1

[it isn't clear how to proceed...let's write out some examples]

x  | x² | ⌊x² / 4⌋ | x² % 4
---+----+---------+-------
1  | 1  | 0       | 1
2  | 4  | 1       | 0
3  | 9  | 2       | 1
4  | 16 | 4       | 0
5  | 25 | 6       | 1
6  | 36 | 9       | 0

[our intuition is that the remainder is 0 when x is even and 1 when x is odd;
let's see if we can prove it using proof by cases]

[CASE 1: x is even]

Givens:
- x ∈ ℤ 
- ∃k ∈ ℤ, x = 2k
⊢ x² % 4 = 0 ∨ x² % 4 = 1

[APPLY ∨ GOAL RULE]

Givens:
- x ∈ ℤ 
- ∃k ∈ ℤ, x = 2k
⊢ x² % 4 = 0

[APPLY ∃ GIVEN RULE]

Givens:
- x ∈ ℤ 
- k ∈ ℤ 
- x = 2k
⊢ x² % 4 = 0

[substitute 2k for x and simplify]

Givens:
- x ∈ ℤ 
- k ∈ ℤ 
- x = 2k
⊢ 4k² % 4 = 0

[CASE 2: x is odd]

Givens:
- x ∈ ℤ 
- ∃k ∈ ℤ, x = 2k + 1
⊢ x² % 4 = 0 ∨ x² % 4 = 1

[SAME SERIES OF STEPS]

Givens:
- x ∈ ℤ
- k ∈ ℤ 
- x = 2k + 1
⊢ (4k² + 4k + 1) % 4 = 1

QED

=== PROSE PROOF ===

suppose x is an integer. we consider two cases.

case 1: x is even. then x = 2k for some integer k, so x² = 4k². the remainder 
of 4k² when divided by 4 must be 0.

case 2: x is odd. then x = 2k+1 for some integer k, so x² = 4k² + 4k + 1. the
remainder of 4k² + 4k + 1 must be 1.

x must be even or odd, so these cases are exhaustive. therefore x² % 4 is either
0 or 1.
```

## STUDENT EXERCISES

- DEF: "x divides y" (x | y) if ∃k ∈ ℤ, y = kx

- EXERCISE (HTPI thm 3.3.7)

```
for all integers a, b, and c, if a|b and b|c then a|c

SOLN

⊢ ∀a,b,c ∈ ℤ, a|b ∧ b|c → a | c

givens:
- a,b,c ∈ ℤ 
- a|b ==> ∃k ∈ ℤ, b = ka
- b|c ==> ∃k ∈ ℤ, c = kb
⊢ a|c ==> ∃k ∈ ℤ, c = ka

let a,b,c be arbitrary integers and suppose a|b and b|c. since a|b, there is some
integer k s.t. b = ka. since b|c there is some integer j s.t. c = jb. since 
b = ka, c = jka. jk is an integer, therefore a|c.
```

- EXERCISE (HTPI thm 3.4.7)

```
for every integer n, 6|n iff 2|n and 3|n.

SOLN

⊢ ∀n ∈ ℤ, 6|n ↔ 2|n ∧ 3|n

givens:
- n ∈ ℤ 
⊢ 6|n → 2|n ∧ 3|n
⊢ 2|n ∧ 3|n → 6|n

(->) suppose 6|n. then there exists some integer k s.t. n = 6k = 2(3k) = 3(2k).
2k and 3k are integers, therefore 2|n and 3|n.

(<-) suppose 2|n and 3|n. then there exists integers k,j s.t. n = 2j and n = 3k.
then j-k is an integer and 6(j - k) = 6j - 6k = 3(2j) - 2(3k) = 3n - 2n = n, 
therefore 6|n.
```

- EXERCISE (HTPI thm 3.5.5)

[remember Even(x) = ∃k ∈ ℤ, x = 2k, Odd(x) = ∃k ∈ ℤ, x = 2k + 1, and a number must be either Even or Odd (but not both)]

```
suppose m and n are integers. if mn is even, then either m is even or n is even.

⊢ ∀m,n ∈ ℤ, Even(mn) → Even(m) ∨ Even(n)

givens:
- m,n ∈ ℤ 
- Even(mn) ==> ∃k ∈ ℤ, mn = 2k
⊢ Even(m) ∨ Even(n) ==> (∃k ∈ ℤ, m = 2k) ∨ (∃k ∈ ℤ, n = 2k)

givens:
- m,n ∈ ℤ 
- Even(mn) ==> ∃k ∈ ℤ, mn = 2k
- Even(m) ∨ Odd(m)
⊢ Even(m) ∨ Even(n) ==> (∃k ∈ ℤ, m = 2k) ∨ (∃k ∈ ℤ, n = 2k)

suppose mn is even. we proceed by cases:

case 1: m is even. then there is nothing more to prove.

case 2: m is odd. then there is some integer k s.t. m = 2k + 1. since mn is even,
there is some integer j s.t. mn = 2j. so (2k + 1)n = 2j, and by rewriting we get
that n = 2j - 2kn = 2(j - kn). since j - kn is an integer, n is even.
```

# relations
## cartesian product

- we will now build on top of sets to create other useful mathematical objects, specifically _relations_

- first we introduce pairs and tuples

    - `(a, b)` is a 2-tuple (i.e., pair) whose first element is `a` and whose second element is `b`

    - `(a, b, c, d)` is a 4-tuple

    - order and repetition matter, unlike sets

        - `(a, b)` is different from `(b, a)` and from `(a, a, b)`

        - `{a, b}` and `(a, b)` are two entirely different things

    - tuples are like structs in C/C++, they group data together

- examples of tuples

    - coordinates: the coordinate of a point on a 2D grid is a pair; the coordinate of a point on a 3D cube is a triple; etc

    - truth sets for multvariate predicates: the elements of the truth set of `P(x, y)` are pairs `(a, b)` such that setting `x = a` and `y = b` make the predicate true

        - let `P(x, y)` = `x` and `y` are integers and `x < y`; its truth set = `{(-1, 0), (42, 100), ...}`

- what does this have to do with sets? we can use sets to describe the domain of a tuple, i.e., what objects can be used for which elements

- DEF: the _cartesian product_ of two sets A and B is denoted A × B, and its elements are all pairs whose first element is from A and whose second element is from B

    - i.e., `A × B` = `{(a, b) | a ∈ A ∧ b ∈ B}`

    - `A = {1, 2}`, `B = {a, b}`, `A × B = {(1,a), (1,b), (2,a), (2,b)}`

    - to get `n`-ary tuples just use `n` cartesian products, e.g., A × B × C

- here are some theorems about cartesian product:

    1. `A × (B ∩ C)` = `(A × B) ∩ (A × C)`
    2. `A × (B ∪ C)` = `(A × B) ∪ (A × C)`
    3. `(A × B) ∩ (C × D)` = `(A ∩ C) × (B ∩ B)`
    4. `(A × B) ∪ (C × D)` ⊆ `(A ∪ C) × (B ∪ D)`
    5. `A × ∅` = `∅ × A` = `∅`

- PROOF OF THEOREM 1

```
let (a, d) ∈ A × (B ∩ C). then a ∈ A, d ∈ B, and d ∈ C. since a ∈ A and d ∈ B,
(a, d) ∈ A × B. since a ∈ A and d ∈ C, (a, d) ∈ A × C. therefore (a, d) ∈ 
(A × B) ∩ (A × C).
```

- we may ask you to do proofs for the other theorems in assignments, quizzes, or the final

- we know that order matters, so that in general A × B ≠ B × A...are there any circumstances when A × B = B × A?

- CONJECTURE: A × B = B × A ↔ A = B

- INCORRECT PROOF

```
the first coordinates of A × B comes from A, and the first coordinates of B × A 
comes from B. but if A × B = B × A, then the first coordinates for both sides
must be the same, so A = B.
```

- the reasoning may look plausible, but it doesn't follow the rules for reasoning that we've established for doing proofs

- this is a good example of why following the proper rules of inference is important; let's try again using those rules

- MORE FORMAL VERSION OF INCORRECT PROOF

```
(→) suppose A × B = B × A. let x be arbitrary. to prove A = B, we must prove that
x ∈ A ↔ x ∈ B. first we prove the → direction: suppose x ∈ A. then we need to find
some y ∈ B such that (x, y) ∈ A × B, which since A × B = B × A would mean that 
(y, x) ∈ A × B, which would mean that x ∈ B. 

BUT: how do we find y? we have no information about B other than A × B = B × A.
if we assume there is a y ∈ B then we're making an assumption that isn't justified
by our givens. in fact, B could be the empty set! then it's impossible to choose
y ∈ B, and the proof fails.
```

- in fact, the conjecture is wrong: what if A or B is ∅?

- CORRECTED CONJECTURE: A × B = B × A ↔ A = B ∨ A = ∅ ∨ B = ∅

- this is a good example of how mathematics usually works:

    - we come up with a reasonable conjecture
    - we try to prove it correct, but fail
    - we tweak the conjecture to correct the failure
    - we try to prove it again

- EXERCISE: prove the corrected conjecture

```SOLN
(→) suppose A × B = B × A. if either A = ∅ or B = ∅ then both A × B and B × A are
∅ and the proof is done. otherwise, A ≠ ∅ and B ≠ ∅. then we can choose arbitrary
x ∈ A and y ∈ B. then (x, y) ∈ A × B and, since A × B = B × A, (x, y) ∈ B × A.
then x ∈ B. since x was arbitrary, A = B.

(←) we proceed by cases:
case 1: A = ∅. then A × B = ∅ × B = B × ∅ = B × A
case 2: B = ∅. similar to case 1
case 3. A = B. then A × B = A × A = B × A
```

## relations

- DEF: a set R ⊆ A × B is a _relation_ from A to B

    - intuitively we can think of A × B as the domain of the truth set for some binary predicate, e.g., the predicate P(x, y) defines a relation according to the truth set of P(x, y)

    - however, our definition doesn't depend on the idea of truth sets: any set meeting the definition is a relation

    - specifically this is a _binary relation_; relations can also be n-ary, e.g., R ⊆ A × B × C × D

- EXAMPLES

    - A = {1, 2, 3}, B = {a, b, c}, R ⊆ A × B = {(1,a), (2,b), (3,c)}
    - {(x, y) ∈ ℝ × ℝ | x > y}
    - A = {1, 2}, B = 𝒫(A), R ⊆ A × B = {(x, y) ∈ A × B | x ∈ y}
        - R = {(1, {1}), (1, {1,2}), (2, {2}), (2, {1, 2})}
    - {(s, d) ∈ Students × Dorms | s lives in d}
    - {(s, c) ∈ Students × Courses | s is enrolled in c}
    - {(c, p) ∈ Courses × Professors | c is taught by p}

- notation: for `(x,y) ∈ R`, we can also write `x R y`

    - EXAMPLE: instead of `(1,a) ∈ R` we can write `1 R a`
    - EXAMPLE: instead of `(1,3) ∈ ≤` we write `1 ≤ 3`

- some important definitions; in each case R is a relation from A to B

    - the _domain_ of R is `dom(R) = {a ∈ A | ∃b ∈ B, (a,b) ∈ R}`
    - the _range_ of R is `ran(R) = {b ∈ B | ∃a ∈ A, (a,b) ∈ R}`
    - the _inverse_ of R is `R⁻¹ = {(b, a) ∈ B × A | (a, b) ∈ R}`
    - suppose S ⊆ B × C, then the _composition_ of S and R is
        `S ∘ R = {(a, c) ∈ A × C | ∃b ∈ B, (a,b) ∈ R ∧ (b,c) ∈ S}`

- for composition:

    - notice that S ⊆ B × C, R ⊆ A × B, and S ∘ R ⊆ A × C, i.e., we compute the composition from right to left

    - this is exactly the database `join` operation...database tables are relations, and joins, projections, etc are operations on relations

- EXAMPLES: what are the domains of the following relations?

    - {(x, y) ∈ ℝ × ℝ | x > y}?
        - SOLN: ℝ 

    - {(s, d) ∈ Students × Dorms | s lives in d}?
        - SOLN: {s ∈ Students | ∃d ∈ Dorms, s lives in d}
        - NOT Students!

- EXAMPLES: what are the ranges of the following relations?

    - A = {1, 2}, B = 𝒫(A), R ⊆ A × B = {(x, y) ∈ A × B | x ∈ y}?
        - { {1}, {2}, {1,2} }
        - NOT 𝒫(A)!

    - {(c, p) ∈ Courses × Professors | c is taught by p}?
        - {p ∈ Professors | ∃c ∈ Courses, p teaches c }
        - NOT Professors!

- EXAMPLES: what is the composition of T = {(c, p) ∈ Courses × Professors | c is taught by p} and E = {(s, c) ∈ Students × Courses | s is enrolled in c}?

    - note that E ⊆ Students × Courses and T ⊆ Courses × Professors, so T ∘ E ⊆ Students × Professors

    - {(s, p) ∈ Students × Professors | s is enrolled in a course taught by p}

    - the fact that E ∘ T seems backward can trip you up, you just need to remember the proper order (why it's "backwards" will make more sense later)

- EXERCISE

    - let L = {(s, d) ∈ Students × Dorms | s lives in d}
          E = {(s, c) ∈ Students × Courses | s is enrolled in c}
          T = {(c, p) ∈ Courses × Professors | c is taught by p}

    - describe the following relations using set comprehensions, where the predicate is in English

        1. E⁻¹
        2. E ∘ L⁻¹
        3. E⁻¹ ∘ E
        4. E ∘ E⁻¹
        5. T ∘ (E ∘ L⁻¹)
        6. (T ∘ E) ∘ L⁻¹

    - SOLUTIONS

        - {(c,s) ∈ Courses × Students | s is enrolled in c}
        - {(d,c) ∈ Dorms × Courses | some student living in d is enrolled in c}
        - {(s1,s2) ∈ Students × Students | there is some course that s1 and s2 are both enrolled in}
        - {(c1,c2) ∈ Courses × Courses | there is some student enrolled in both c1 and c2}
        - {(d,p) ∈ Dorms × Professors | some student lives in d is enrolled in a course taught by p}
        - same as above

    - notice that (3) and (4) are different, showing that composition is _not_ commutative, but (5) and (6) are the same, indicating that composition _might_ be associative (we have an example, not a proof)

- theorems about domain, range, inverse, and composition; let R ⊆ A × B, S ⊆ B × C, and T ⊆ C × D

    1. (R⁻¹)⁻¹ = R
    2. dom(R⁻¹) = ran(R)
    3. ran(R⁻¹) = dom(R)
    4. T ∘ (S ∘ R) = (T ∘ S) ∘ R
    5. (S ∘ R)⁻¹ = R⁻¹ ∘ S⁻¹

- proof of theorem (4)

```
clearly T ∘ (S ∘ R) and (T ∘ S) ∘ R are both relations from A to D. let (a,d) be 
an arbitrary element of A × D.

direction 1: suppose (a,d) ∈ T ∘ (S ∘ R). by definition, there is some c ∈ C s.t.
(a,c) ∈ S ∘ R and (c,d) ∈ T. since (a,c) ∈ S ∘ R, again by definition there is
some b s.t. (a,b) ∈ R and (b,c) ∈ S. since (b,c) ∈ S and (c,d) ∈ T, (b,d) ∈ T ∘ S.
since (a,b) ∈ R and (b,d) ∈ T ∘ S, (a,d) ∈ (T ∘ S) ∘ R.

direction 2: suppose (a,d) ∈ (T ∘ S) ∘ R. a similar argument shows that (a,d) ∈ 
T ∘ (S ∘ R).

therefore, T ∘ (S ∘ R) = (T ∘ S) ∘ R.
```

- note the hand-waving going on for direction 2 of the proof...this is common, but somewhat dangerous

## binary relations and graphs

- a binary relation R ⊆ A × A can also be called "a relation on A", and can also be written as A²

- EXAMPLES

    - A = {1,2}, B = 𝒫(A), and S = {(x,y) ∈ B² | x ⊆ y}
        - S is a relation on B

    - for some set A, I = {(x, y) ∈ A² | x = y}
        - I is a relation on A

    - for some positive real number r, D_r = {(x,y) ∈ ℝ² | |x-y| < r}
        - D_r is a relation on ℝ 

- for a relation R on some set A, another way to represent it is as a _graph_

    - nodes (or vertices): the elements of A
    - edge from x to y: represents xRy

- EXAMPLE: the graph for S from the examples above

- graphs are huge in computer science, they are used everywhere

    - modeling digital networks, social networks, and many other things
    - there are tons of algorithms solving graph-related problems
    - you'll see a lot more of them in 130A/B

- some definitions about binary relations R ⊆ A × A

    - R is _reflexive_ if ∀x ∈ A, xRx
    - R is _symmetric_ if ∀x,y ∈ A, xRy → yRx
    - R is _transitive_ if ∀x,y,z ∈ A, xRy ∧ yRz → xRz

- EXAMPLE: the example graph for S above is reflexive and transitive, but not symmetric

- EXERCISES: identify whether the following relations are reflexive, symmetric, and/or transitive

    1. = on integers [RST]
    2. < on integers [T]
    3. ⊆ on sets     [RT]
    4. {(1,2), (1,3), (2,1), (2,2), (3,1), (3,3)} [S]

- theorems about binary relations

    1. R is symmetric iff R = R⁻¹
    2. R is transitive iff R ∘ R ⊆ R

- proof of theorem (1)

```
(→) suppose R ⊆ A × A is symmetric. let x,y be arbitrary elements of A and xRy.
then since R is symmetric, yRx. since yRx, xR⁻¹y. because x and y were arbitrary, 
it follows that R ⊆ R⁻¹. now suppose xR⁻¹y. by definition of the inverse, yRx. 
since R is symmetric, xRy. because x and y were arbitrary, it follows that 
R⁻¹ ⊆ R. therefore R = R⁻¹.

(←) suppose R = R⁻¹, and let x,y be arbitrary elements of A. suppose xRy. since
R = R⁻¹, xR⁻¹y. by definition of the inverse, yRx. therefore, R is symmetric.
```

## equivalence relations

- binary relations that are reflexive, symmetric, and transitive are called _equivalence relations_

    - EXAMPLES: equals, has-the-same-birthday
    - [explain how each is RST]

- equivalence relations group objects based on some property, so that all objects with the same property are related to each other

    - the objects that are all related to each other are called an _equivalence class_

    - equals groups things that are equal, so there is only one object in each equivalence class

    - has-same-birthday groups people with the same birthday into the same equivalence class

- equivalence classes _partition_ a set into disjoint subsets

    - each partition is an equivalence class
    - all objects must be in some partition
    - no object can be in more than one partition
    - [draw example with has-same-birthday]

- notation: [x]_R is the equivalence class from R that contains x

    - EXAMPLE: [bob]_has-same-birthday = {x | x has the same birthday as bob}
    - we can omit the _R if it is obvious from context, e.g., [bob]

- equivalence relations are very useful, because if we only care about the equivalence relation property then we can pick one element from each equivalence class as a representative and forget about the rest

    - the elements in a given equivalence class may be different from each other in many other ways, but in terms of the equivalence relation they are identical

- defining things more formally:

    - let A be a set and E be some equivalence relation on A

    - the set of equivalence classes A/E ("A modulo E") is
        - `{X ⊆ A | ∃x ∈ A, X = {y ∈ A | xEy}}`
        - note that A/E is a set of subsets of A, i.e., A/E ⊆ 𝒫(A)

    - P ⊆ 𝒫(A) is a partition of A if:
        - ⋃P = A
        - P is _pairwise disjoint_ (every pair of distinct elements in P are disjoint)
        - ∀X ∈ P, X ≠ ∅

    - then C is a partition of A
    - also any partition of A defines an equivalence relation

- EXAMPLE: S = {(x,y) ∈ ℝ² | x - y ∈ ℤ}

    - for any x ∈ ℝ, x - x = 0 ∈ ℤ ==> reflexive
    - suppose (x,y) ∈ S; then x-y ∈ ℤ. but then -(x-y) = y-x ∈ ℤ ==> symmetric
    - suppose (x,y) ∈ S and (y,z) ∈ S; then x-y ∈ ℤ and y-z ∈ ℤ. x-y + y-z = x-z ∈ ℤ ==> transitive
    - therefore S is an equivalence relation 
    - [r ∈ ℝ] = {..., r-2, r-1, r, r+1, r+2, ...}

- theorems about equivalence relations; let R be an equivalence relation on A:

    1. ∀x ∈ A, x ∈ [x]
        - easy to prove using reflexivity of R

    2. ∀x,y ∈ A, y ∈ [x] iff [y] = [x]
        - easy to prove using symmetry and transitivity of R

    3. A/R is a partition of A

    4. let P be a parition of A; then there is an equivalence relation R on A such that A/R = P

- proof of theorem (3)

```
we must prove the following:

1. ⋃ A/R = A. since every equivalence class in A/R is a subset of A, ⋃ A/R ⊆ A.
now we must prove that A ⊆ A/R. suppose x ∈ A. then since (by theorem 1) x ∈ [x],
and since [x] ∈ A/R, x ∈ ⋃ A/R and thus A ⊆ ⋃ A/R. since ⋃ A/R ⊆ A and A ⊆ ⋃ A/R,
⋃ A/R = A.

2. A/R is pairwise disjoint. suppose X, Y ∈ A/R and X ∩ Y ≠ ∅. X and Y are
equivalence classes (by definition), so for some x,y ∈ A, X = [x] and Y = [y].
since X ∩ Y ≠ ∅, we can choose some z ∈ X ∩ Y = [x] ∩ [y]. thus, z ∈ [x]
and z ∈ [y]. by theorem 2 [z] = [x] = [y], thus X = Y. therefore if X ≠ Y then
X ∩ Y = ∅, so A/R is pairwise disjoint.

3. ∀X ∈ A/R, X ≠ ∅. suppose X ∈ A/R. then there is some x s.t. X = [x]. by
theorem 2, x ∈ [x] and so x ∈ X. therefore X ≠ ∅.
```

## intro to modular arithmetic [WAIT UNTIL NUMBER THEORY? CHECK EXERCISES]

- we can define a very useful family of equivalence relations as follows

- DEF: suppose m ∈ ℤ⁺. for any x,y ∈ ℤ, we say that "x is congruent to y modulo m" if ∃k ∈ ℤ, x - y = km. in other words, m divides (x - y).

    - we will use the notation `x ≡ y (mod m)`

    - EXAMPLE: 12 ≡ 27 (mod 5), because 12 - 27 = -15 and 5 divides -3.

- for any m ∈ ℤ⁺, define the relation {(x, y) ∈ ℤ × ℤ | x ≡ y (mod m)}

    - we will call this relation `≡ₘ`
    - i.e., `x ≡ₘ y` = `x ≡ y (mod m)`

- theorem: for every m ∈ ℤ⁺, ≡ₘ is an equivalence relation (i.e., ≡ₘ is reflexive, symmetric, and transitive)

    - proof left as an exercise

- these relations give us _modular arithmetic_, which is heavily used in many places (e.g., crypto)

- what is it doing? suppose we are using ≡₄

    - 0, 1,  2,  3
    - 4, 5,  6,  7
    - 8, 9, 10, 11
    - ...

    - each column is an equivalence class

    - addition "wraps around", e.g., 6 + 7 = 2 + 3 = 1

- in fact, when you use unsigned integers in C/C++, you are operating on ≡_(2^64)

- we'll talk more about modular arithmetic later in the course

# functions - TODO: (HTPI 5)

% [5.1] functions
%
% [5.2] injection, surjection
%
% [5.3] inverses, bijection

# induction - TODO: (HTPI 6)

% [6.1] induction
%
% [6.2] more examples of induction
%
% [6.4] strong induction
%
% [XXX] structural induction

# number theory - TODO: (HTPI 7)

% [7.1] gcd
%
% [7.3] modular arithmetic

# infinite sets - TODO: (HTPI 8)

% [8.1] cardinality, equinumerous
%
% [MAYBE MOVE COMBINATORICS HERE?]
% 
% [8.2] countable and uncountable sets

# combinatorics - TODO: (LaP 20)

% [LaPch20] combinatorics
