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

    - identity
        - A ∧ A === A
        - A ∨ A === A

    - domination
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

- [LECTURE 2 STOPPED HERE]

- EXAMPLES

    - ¬(P ∨ ¬Q)
      ¬P ∧ ¬¬Q   [by de morgan]
      ¬P ∧ Q     [by double negation] 

    - ¬(Q ∧ ¬P) ∨ P
      (¬Q ∨ ¬¬P) ∨ P [by de morgan]
      (¬Q ∨ P) ∨ P   [by double negation]
      ¬Q ∨ (P ∨ P)   [by associativity]
      ¬Q ∨ P         [by identity]

- EXERCISE 5: simplify the following expression as much as possible

    - ¬(P ∨ (Q ∨ ¬R)) ∧ Q

    SOLUTION
      (¬P ∧ ¬(Q ∨ ¬R)) ∧ Q      [by de morgan]
      (¬P ∧ (¬Q ∨ ¬¬R)) ∧ Q     [by de morgan]
      (¬P ∧ (¬Q ∨ R)) ∧ Q       [by double negation]
      ¬P ∧ ((¬Q ∨ R) ∧ Q)       [by associativity]
      ¬P ∧ (Q ∧ (¬Q ∨ R))       [by commutativity]
      ¬P ∧ ((Q ∧ ¬Q) ∨ (Q ∧ R)) [by distributivity]
      ¬P ∧ (Q ∧ R)              [by contradiction]

# predicate logic

- TODO:

# proofs (here? or later? or even before predicate logic?)

- TODO:

# sets

- TODO:
