# Fall 2025 notes

## TODO:

- at end of quarter, send out AI survey form (create gradescope assignment for them to submit it)

## lecture timing

- week 0.2 (quarter starts on thursday): up to (not including) 'compiler overview::things we're leaving out'
- week 1.1: through end of 'lexing'
- week 1.2: up to (not including) 'parsing::recursive descent and LL(1)::LL(1) recursive descent'
- week 2.1: up to (not including) 'parsing::building the AST' [ASSIGN-1 OUT]
- week 2.2: through end of parsing
- week 3.1: 
- week 3.2: 
- week 4.1: 
- week 4.2: 
- week 5.1: 
- week 5.2: 
- week 6.1: 
- week 6.2: 
- week 7.1: (holiday)
- week 7.2: 
- week 8.1: 
- week 8.2: 
- week 9.1: 
- week 9.2: (holiday)
- week 10.1: 
- week 10.2: 

## TODO: tentative assignment timing

- TODO: maybe decrease type checking, increase some of the others by making assignment due dates other than on class days

- week 2.1 assign-1 (lexing and parsing) -- 14 days
- week 4.1 assign-2 (type checking)      -- 14 days
- week 6.1 assign-3 (lowering)           -- 9 days
- week 7.2 assign-4 (codegen)            -- 13 days
- week 9.* assign-5 (gc)                 -- 9 days

## grading scale

| percentage | letter grade |
|------------|--------------|
| 98--100    | A+           |
| 94--97     | A            |
| 90--94     | A-           |
| 87--89     | B+           |
| 84--86     | B            |
| 80--83     | B-           |
| 77--79     | C+           |
| 74--76     | C            |
| 70--73     | C-           |
| 67--69     | D+           |
| 64--66     | D            |
| 60--63     | D-           |
| 00--59     | F            |

## misc notes

- this quarter i'm replacing the lir optimization assignment with a garbage collection assignment, and so moved the lecure material around accordingly (still covering the material, but later and less in-depth from last time)

- i'm still not covering register allocation, mostly due to lack of time; if i do decide to cover it then i need to decide what to remove (and also create the lecture notes for it---see what i can crib from mehmet)

- be sure to tell the students that when they access JSON files for their assignments (i.e., reading the input for the verification, lowering, and codegen assignments) they should be sure to use references and not deep copying (e.g., `json& funcs = value["functions"]` not `json funcs = value["functions"]`). they also probably want to create a new data structure (AST or LIR) from the JSON rather than only using the JSON itself.

- gradescope

    - sometimes student submissions give corrupted output (e.g., not utf-8), which causes the `DiffOutputFiles` to fail and thus the entire grading script; figure out a way to tolerate these kinds of failures and treat them as incorrect outputs (maybe add a specific test for this problem in `DiffOutputFiles`)

    - students get confused by the output on a failure: we give the total number of tests passed out of the total number of tests, and they think it gives the actual test number (even though we give them the name of the test in the same message)---this is only a problem because that's how they identify the test in their communications to us which is confusing; maybe we can tweak the failure message to make it more clear?

- assign-1

    - copy-pasting the failed program from the grader output doesn't copy the special characters (e.g., `\r`, `\t`, etc); the students need to be aware of this possibility

    - we give the students the LL(1) version of the grammar so they never actually have to use the grammar refactoring rules; i think this is fair given all the other stuff they have to do, but maybe think about giving quizzes (graded or not) where they have to refactor some simple grammars

    - since test cases are generated as ASTs and then printed out as source code, the test cases have more regular syntax than the grammar allows---for example, toplevel constructs will always be in the same order, every function will have at most one let, and arithmetic ops are all parenthesized to make precedence explicit; to allow for testing the student parsers more thoroughly it would be good to create a printer that randomizes these things

        - for now i've decided that this isn't worth fixing; maybe later if i have time

# course logistics

- introduce myself and TAs

    - hitomi
    - sarah

- course slack:

    - invite will be sent to all registered students

    - all communication goes through slack: announcements, assignments, questions, etc

    - be sure to stay on top of slack notifications; set up your preferences accordingly

- office hours: TBA, will be mix of in-person and remote and at different days and times

- discussion sections: no new material; basically organized office hours

    - NOTE: need to close first section (9am), the 3 students enrolled please see me after lecture

- assessment:

    - no exams

    - 5 projects (going into finals week)

        - 1--3 person teams
        - weighted evenly, must do all 5
        - late days allowed, but with grade penalty (-11% for one week late, -31% if before end of quarter)
        - one assignment can be turned in late with no penalty (automatically whichever helps you most)
        - this late penalty scheme is intended to give all the flexibility you need, please don't ask for more late days

    - projects in C++, done from scratch---no skeleton code given

    - if you're using an online repo (e.g., github) make sure it's private! sharing your code publically is considered a violation of academic integrity (as is using code that others may have shared)---projects should be your own work

    - what about AI?

        - it's ok to use AI for your projects

        - i advise against it:

            - the point of the projects is to really learn how compilers work; using AI prevents learning

            - i don't think it will work very well

        - the only thing i require is that you disclose AI use and how you used it

            - there is _no_ penalty
            - this is for my information so i can learn how AI might impact things

    - important: this is a work-intensive course that requires a _lot_ of coding---be prepared! don't take other heavy-workload courses at the same time (like 170)

- please ask questions and correct my mistakes!

    - questions are good: they let me know when i need to do a better job of explaining things and slow the pace of the lecture if it's going too fast

    - i'm only human, and sometimes when i'm writing things down my wires get crossed and i make mistakes...i'd much prefer that they're caught right away instead of going into people's notes and causing confusion later

# intro to course

- this course is about _compilers_, which you've been using for years now

    - you put C++ (or whatever) source code in, and get an executable out (or error messages)

    - up to now it's just been a black box that works by _magic_

    - but how does it work? and why do we do it that way?

- motivation

    - computer chips are designed with an _instruction set architecture_ (ISA); an ISA is a set of binary values that correspond to certain actions by the computer (e.g., take two registers, add their values, and put the result in another register)

        - different architectures use different ISAs (e.g., x86, ARM, RISC-V, MIPS) but they are all the same basic idea

        - ISA instructions are also called _machine code_, in contrast to _source code_

    - humans can't program directly in machine code except for very small, limited cases; even when we use a somewhat higher level of abstraction (assembly language) it's still really painful

    - so we use modern, high-level programming languages like C, C++, Java, Python, JavaScript, etc (there are dozens of widely-used languages, thousands of existing languages)

        - this is important: programming languages are for humans, not computers

    - but to the computer, source code is just text---it might as well be shakespeare, it has no meaning that the computer can understand or execute

        - so we still need to be able to translate source code into machine code

- source code: [see OneNote]

```
"x = x + 2;"
```

- what the computer sees (in ASCII code):

```
['x', ' ', '=', ' ', 'x', ' ', '+', ' ', '2', ';']
```

- translated to assembly:

```
mov [esp+4], eax
add eax, 2
mov eax, [esp+4]
```

- translated to machine code:

```
[0x89, 0x44, 0x24, 0x04, 0x83, 0xC0, 0x02, 0x8B, 0x44, 0x24, 0x04]
```

- besides the fact that source code is just text, is also uses abstractions that are meaningless to the computer

    - classes, objects, structs, functions, if statements, while and for loops, variables...none of these are things that the computer knows about or understands

    - programming languages are specifically designed to give the human programmers useful abstractions to describe data and computation

        - different languages use different kinds of abstractions (e.g., object-oriented languages vs functional languages vs logic programming languages)

- so somehow we need to take a program in source code (with whatever abstractions that particular language uses) and turn it into a stream of ISA instructions (without any of those abstractions) that the computer can actually execute

- approach 1: ahead-of-time compilers

    - take source code and translate it into machine code (e.g., gcc or clang)

    - we call this 'ahead of time' because it happens before the program is ever executed

- approach 2: interpreters

    - take source code and have an interpreter take each statement and expression and implicity translate it into machine code on the fly (e.g., cpython for python)

    - it's implicit because the mapping from source code to machine code isn't present explicitly in the interpreter---an interpreter for language A is itself written in some source language B, and it looks at the source code for A and implements it in terms of B

    - but how was the interpreter itself made executable? a compiler!

    - interpreting source code is much slower than executing compiled source code because both the interpreter and the source code are executing at the same time; on the other hand, interpreters don't need to wait for compilation to start executing source code so they are faster to use in a "write-execute" cycle

- approach 3: hybrid

    - use an interpreter, but during execution _also_ in parallel compile code to machine instructions (e.g., v8 engine for javascript)

    - this is called 'just in time' compilation, or JIT compilation for short

    - get the fast startup time of interpreters but still a good part of the performance of compiled code (though still slower than ahead-of-time compilation)

- some language implementations do all three, like Java and any other language that targets the Java Virtual Machine (JVM), or .NET languages that target the .NET runtime

    - ahead-of-time compilation to java bytecode

    - bytecode interpreted by JVM

    - JVM also JIT compiles the bytecode for better performance

- so no matter what language you're using, compilers are involved in some way; there is no modern programming without compilers

- it's important to note that _languages_ are not compiled or interpreted---a language is _implemented_ with a compiler or interpreter

    - there are interpreters for C and C++
    - there are compilers for python

- programming languages are how humans express computation: we can't compute without them, and we can't make them work without compilers---as computer scientists and/or developers, there shouldn't be any magic here

- goals for this course:

    - dispel the "magic" and get a basic understanding of how source code turns into executable code

    - not just relevant to compilers for mainstream programming languages like C++, etc: parsing log or configuration files, creating domain-specific languages, etc

    - show how some of the computational theory from 138 has practical applications

    - give experience writing (relatively) large and complex software from scratch

        - still much smaller and simpler than mainstream compilers that have had hundreds of people working on them for many years

        - you won't be a compiler expert after this course, but you'll know enough to have an idea of how compilers work and a foundation you can use to learn more if you want

# compiler overview

## compilation

- to put compilation in context:

```
[source + macros] --PREPROCESSOR--> [source code] --COMPILER--> assembly --ASSEMBLER--> 
[relocatable machine code] --LINKER--> executable machine code
```

- we often call this whole process "compilation", but technically the compiler part is from source code to assembly

    - compilers like gcc, clang, etc automatically invoke the preprocessor, assembler, and linker as third-party executables; you can configure which ones they use

- traditionally compilers are broken into three pieces:

    - front end: responsible for syntax and validation
    - "middle" end: responsible for optimization
    - back end: responsible for generating machine code

```
[source code] -[front end]→ [internal representation] -[back end]→ [assembly]
                                    ⇵
                              [middle end]
```

## front end

- responsible for three things:

    - lexer (aka scanner, tokenizer)
    - parser
    - validation (type checking, etc)

### lexer

- the lexer takes source code (as a single string) and turns it into a sequence of _tokens_

    - lexer stands for "lexicographic analyzer"

    - a token is a "word" in our programming language
    
    - the lexer categorizes substrings of the input into classes of recognized things in our language

- lexing example [see OneNote]

```
// as an example
let sum:int = 0, total:int = 5, x:int = 2;
while sum < total {
  sum = sum + x * 10;
}
```

- as a string:

```
['/', '/', ' ', 'a', 's', ' ', 'a', 'n', ' ', 'e', 'x', 'a', 'm', 'p', 'l', 'e',
 'l', 'e', 't', ' ', 's', 'u', 'm', ':', 'i', 'n', 't', ' ', '=', ' ', '0', ',',
 ' ', 't', 'o', 't', 'a', 'l', ':', 'i', 'n', 't', ' ', '=', ' ', '5', ',', ' ',
 'x', ':', 'i', 'n', 't', ' ', '=', ' ', '2', ';', '\n', 'w', 'h', 'i', 'l', 'e',
 ' ', 's', 'u', 'm', ' ', '<', ' ', 't', 'o', 't', 'a', 'l', ' ', '{', '\n', ' ',
 ' ', 's', 'u', 'm', ' ', '=', ' ', 's', 'u', 'm', ' ', '+', ' ', 'x', ' ', '*',
 ' ', '1', '0', ';', '\n', '}']
```

- after lexing:

```
[LET, VAR(sum), COLON, TYPE(int), EQ, NUM(0), COMMA, VAR(total), COLON, TYPE(int),
 EQ, NUM(5), COMMA, VAR(x), COLON, TYPE(int), EQ, NUM(2), SEMICOLON, WHILE, VAR(sum),
 LT, VAR(total), OPEN_BRACE, VAR(sum), EQ, VAR(sum), PLUS, VAR(x), MUL, NUM(10),
 SEMICOLON, CLOSE_BRACE]
```

- notice that lexing eliminated the comments and whitespace, what's left are the building blocks of the source code

- the tokenizer recognizes that, e.g., `let` is a keyword instead of a variable, that `sum` is a variable, that `10` is a number, etc

- some tokens carry extra information (e.g., for VAR what the name is); these are ignored by the parser but necessary for later stages

- how does the lexer know what the tokens should be?

    - the tokens are specified by the language designer as regular expressions

    - these are converted into NFA (or DFA) and used to process the string into tokens

- the lexer classifies sequences of characters into elements of our language (i.e., tokens), but still leaves those tokens as a flat sequence without any structure

    - we know at this point that the source code only contains valid tokens, but we still don't know if it's syntactically correct

### parser

- the parser has two jobs:

    - verify that the token stream represents a syntactically correct program in our language

    - build an internal representation of that program for the compiler to operate on

- verifying syntactic correctness:

    - the syntax of the language is specified by the language designer as a CFG, with the tokens as the alphabet

    - to show a program is syntactically correct we have to parse the tokens and show that a derivation exists from the start symbol to the token stream

        - recall from 138 'derivation trees', aka 'parse trees', aka 'syntax tree'

- the internal representation usually isn't the actual derivation tree because it contains a lot of things we don't care about when compiling (e.g., punctuation); instead we usually use an _abstract syntax tree_ (AST) that keeps the structure of the derivation tree but only keeps the important information

- parsing example [see OneNote; draw tree]

```
LET
  - VAR(sum), TYPE(int), NUM(0)
  - VAR(total), TYPE(int), NUM(5)
  - VAR(x), TYPE(int), NUM(2)
WHILE
  - Guard
    - LT
      - Left: VAR(sum)
      - Right: VAR(total)
  - Body
    - Assignment
      - Left: VAR(sum)
      - Right:
        - PLUS
          - Left: VAR(sum)
          - Right:
            - MUL
              - Left: VAR(x)
              - Right: NUM(10)
```

- notice that the punctuation like colons, semicolons, braces, etc are gone

    - they were necessary in the concrete grammar to figure out the correct AST structure (where does a statement end; what statements belong to the while body; etc)

    - they aren't needed now because the AST structure makes them superfluous

    - notice that the AST structure also makes precedence explicit for the arithmetic

- we know at this point that the program is syntactically correct, but we still don't know if it's valid

### validation

- just because a program is syntactically correct doesn't mean it's a valid program

    - analogy with English: "colorless green ideals sleep furiously" (from chomsky)

- example of a syntactically correct but invalid program:

```
let x:int = 0, y:int = 10;
x = new int;
y = x * y;
```

- here `x` is an integer but we're storing a pointer value (i.e., address); multiplying by an address doesn't make sense

- we need to make several checks, but perhaps the most important is _type checking_

    - everything is declared with a proper type
    - anything declared as a given type is only used in ways that make sense for that type

- at this point we know that the program is a valid program and we can go to the middle end

## middle end

- the primary job of the so-called middle-end is to optimize the code in order to make it more efficient

    - we often _lower_ the AST into a simpler, lower-level intermediate representation (IR) that is easier to reason about and exposes more optimization opportunities

- let's look at some examples (to keep things simple we'll just modify the source code itself instead of showing the IR):

- original [see OneNote]

```
let sum:int = 0, total:int = 5, x:int = 2;
while sum < total {
  sum = sum + x * 10;
}
```

- loop invariant code motion

```
let sum:int = 0, total:int = 5, x:int = 2, tmp:int;
tmp = x * 10;
while sum < total {
  sum = sum + tmp;
}
```

- constant folding

```
let sum:int = 0, total:int = 5, x:int = 2, tmp:int;
tmp = 20;
while sum < 5 {
  sum = sum + 20;
}
```

- dead code elimination

```
let sum:int = 0;
while sum < 5 {
  sum = sum + 20;
}
```

- we call this process "optimization", but there isn't really a sense of "optimal" code meaning the best possible code---just better than the original

    - the more optimizations we apply the better the result (with diminishing returns) but the longer compilation takes

- once the code has been optimized as much as desired we're done with the middle-end

## back end

- the primary job of the back end is to translate the IR into whatever ISA we're targeting (we'll be targeting x86-64 in this class)

    - a secondary job is to apply ISA-specific optimizations

    - the particular ISA-specific optimization we'll look at is _register allocation_

- step 1: translate the IR into assembly assuming an infinite number of registers are available

    - i'll use a pseudo assembly to keep things simple

- EXAMPLE using original source code and assuming each variable corresponds to its own register [see OneNote]

```
    MOV #0, sum    // move constant into register
    MOV #5, total
    MOV #2, x
L1: CMP sum, total // set condition flag
    JLT L2         // jump if less-than
    EXIT
L2: MOV #10, tmp
    MUL x, tmp     // tmp = x * tmp
    ADD sum, tmp   // tmp = sum + tmp
    MOV tmp, sum   // sum = tmp
    JMP L1
```

- step 2: register allocation

    - in reality no ISA has an infinite number of registers; we need to map an arbitrary number of variables into a fixed number of registers

    - no instruction will use more than two variables; the easy fix is to always use the following process so that we only ever need two registers:

        - load the first variable from memory to a register
        - load the second variable from memory to a register
        - perform the operation
        - store the result to memory

    - memory is _really_ slow, this has a huge performance penalty; we want to keep things in registers as much as possible and go to memory as little as possible

    - register allocation gives us a smarter way to assign variables to registers so that we have to go to memory as few times as possible

## runtime

- a compiled program usually still needs runtime support to actually execute

    - C/C++: the runtime standard library provides things like `malloc/free` and `new/delete`, dealing with I/O, etc

    - Java, Javascript, etc: the runtime provides garbage collection, etc

- this matters because the compiler needs to know about the runtime in order to emit the correct code

- our language will be garbage collected, and so the compiler will need to emit code that gives the garbage collector the information it needs to do its job

    - you'll also be implementing the garbage collector itself

## putting it all together

```
[source code] --LEXER--> [token stream] --PARSER--> [AST] --LOWERING--> [LIR] --CODEGEN--> [x86-64]
                                                      ⇵                  ⇵                  ⇵
                                                 VALIDATION         OPTIMIZATION       REGISTER ALLOC.
```

```
executable machine code + RUNTIME == execution
```

## what we'll be doing

- we will be covering most of these pieces, and you will be implementing them for your projects

    - lexer
    - parser
    - validation
    - lowering
    - codegen
    - garbage collection
    
- we'll leave out the following for time (and because we can get a working compiler and runtime without them)

    - optimization
    - register allocation

- the language we'll be implementing is a C-like language

    - ints and arithmetic
    - pointers, arrays, and dynamic memory allocation
    - structs
    - functions
    - assignment, conditionals, and loops

- note that these language features are sufficient to implement things like object-oriented and functional languages

    - an object is a struct + function pointers
    - so is a closure

## things we're leaving out

- compiler implementations often have other goals in addition to just emitting executable code, like:

    - fast compilation
    - optimal instruction selection
    - good error messages
    - IDE support
    - debugger support
    - testing and/or verified compilation

- we won't discuss these, but they are important for real-world compilers

## final thoughts

- adding language features can make every part of the compiler more complicated; language designers have to be careful to balance useful language features with how complicated they make the language implementation

- building a compiler is easy as long as (1) you control the language definition and can make it (and its syntax) simple and easy to handle; (2) you don't care how efficient the compiler itself is; (3) you don't care how efficient the resulting executable is; and (4) you don't care about having good error messages

- i have carefully designed the language we're compiling to be mostly straightforward, with only a few small complications to give you a taste of complexity

- of course, for real compilers we want _all_ of these things, and building a real compiler for a language you don't control is very difficult

# introducing cflat

- [show example programs] [see OneNote]

    - see `cflat-docs/examples/linked-list.cb`
    - see `cflat-docs/examples/matrices.cb`

- [show grammar] [see OneNote]

    - see `cflat-docs/syntax.md`

# lexing

- [remind students what the lexer does; show example in OneNote]

## how do we define the language tokens?

- our first step is to define the tokens, i.e., the elements of our language

- these come from our grammar: keywords, symbols, punctuation, constants, identifiers, etc

    - [see grammar to point out tokens]
    - [see OneNote for token list]

- we specify what each kind of token looks like using regular expressions

    - we're using somewhat extended regular expression notation, but it maps easily to the standard expressions you learned in 138

```
   r+ ⟶ rr*
   r? ⟶ r | ϵ
[abc] ⟶ a | b | c
[a-z] ⟶ a | b | ... | z
[^ab] ⟶ Σ - {a, b}
```

- we also need to specify what whitespace and comments look like (we cheated a bit in the grammar by using `-` in the c-comment regex, this is technically regular but can be very expensive to compute)

```
whitespace = (' ' | '\t' | '\n' | '\r')+

c++-comment = `//`[^'\n']*
-- notice we don't require '\n' at the end, which handles comments that go to
   EOF without terminating

c-comment = `/*`([^`*`] | `*`⋆[^`*/`])⋆`*`+/
-- using ⋆ for kleene star and * for the literal asterisk character

skip = (whitespace | c++-comment | c-comment)*
```

- notice that some tokens should carry extra information (the lexer and parser don't actually care about this extra info, but we'll need it for the rest of the compiler)

    - if we have a Num, what were the digits? if we have an Id, what were the characters?

    - we can represent this information as indices in the input string (example coming shortly)

## how do we extract tokens (aka 'tokenize')?

- now we know what our tokens are and what they look like; the next step is to map the actual characters in the input stream to a sequence of tokens

    - the sequence of characters that make up a token is called a _lexeme_

- example: "foo = bar + 01"

```
lexeme token
------ -----
foo    Id("foo") // Id(0,3)
=      Gets
bar    Id("bar") // Id(6,3)
+      Plus
01     Num("01") // Num(12,2)
```

- we know how to recognize regular languages: convert a regular expression to an NFA, then simulate the NFA on some input

- there are two ways we could go when implementing this idea:

    1. have explicit Regex and NFA data structures and implement Regex-->NFA conversion and NFA simulation in code

    2. manually figure out the NFA and hardcode its simulation using control flow (basically, a switch statement inside a for loop)

- i'm going to explain things using (1) because it is the most general method and makes the underlying theory clear

    - it's also what you would use if you were implementing a lexer generator (where there is no fixed set of tokens) or if you were designing the language and the set of tokens may change over time

- i recommend that you use (2) for your acual project when it is assigned because it is simpler and easier, assuming you have a fixed, predetermined set of tokens as we do for cflat

    - i have asked the TAs to explain how that works in more detail during the next discussion section

- EXAMPLE [see OneNote]

```
RE
| EmptyLang
| EmptyString
| CharRange { lower: char, upper: char } // e.g., [a-z]
| Star { r: RE }
| Concat { first: RE, second: RE }
| Union { left: RE, right: RE }

// for regex: `/*`([^`*`] | `*`⋆[^`*/`])⋆`*`+/
re = RE::char_seq("/*").then(
        RE::anything_but('*').or(
          RE::char('*').star().then(RE::anything_but(['*','/'])
        ).star()
     )
     .then(RE::char('*').plus())
     .then(RE::char('/')))

NFA
- states: set<State>
- transitions: map<State, vector<(option<RE::CharRange>, set<State>)>>
- init: State
- accepting: set<State>

nfa = re.convert_to_nfa()

nfa.accepts("/* this is a comment */");
```

- but we don't have a single regular expression and a single lexeme, we have a set of regular expressions corresponding to different lexemes and an input that contains a sequence of lexemes

- tag each accepting state of each nfa with the token it maps to (or Skip if it maps to a comment or whitespace), then union all the nfas together into a single nfa

    - this handles the fact that we're looking for multiple lexemes: when we get a match, look at the accepting state to see what token should be produced

    - keep walking though the input emitting tokens as we find them, starting at the position after the previous match, until we've processed the entire input

- EXAMPLE/EXERCISE

```
suppose we have:
- TkAB(chars) = a+b
- TkBA(chars) = b+a
- skip = c+

[draw NFA]

process input "aabbaccabcbba"

token stream: [TkAB(0..2), TkBA(3..4), TkAB(7..8), TkBA(10..12)]
```

- still a problem: ambiguity between the token regular expressions

    - consider the following examples:

        - "ab" ⟶ `[Id(ab)]` vs `[Id(a), Id(b)]`
        - "whilex" ⟶ `[While, Id(x)]` vs `[Id(whilex)]`
        - "while" ⟶ `[While]` vs `[Id(while)]`

    - how do we figure out which token to emit if there are multiple possibilities?

- solution 1: maximal munch

    - take the longest possible match

    - when lexing, record the last accepting state reached but keep on going until matching fails---then backtrack to the last accepted character

    - this handles the first two examples:

        - "ab" ⟶ `[Id(ab)]`
        - "whilex" ⟶ `[Id(whilex)]`

- solution 2: priorities

    - maximal munch doesn't help if both possible lexemes are the same size (like our third example, "while")

    - we give priorities to the different accepting states, and if there are multiple accepting states then we pick the one with highest priority

    - for our language we'll say that keywords have priority over identifiers; this handles the third example:

        - "while" ⟶ `[While]`

- EXAMPLE/EXERCISE

```
suppose we have:
- TkAB(chars) = a*b*
- TkBA(chars) = b*a*
- with priority(TkAB) > priority(TkBA)

[draw NFA]

process input "bbaabbbb"

token stream: [TkBA(0..3), TkAB(4..7)]
```

## final thoughts

- lexing can be easy if we define our syntax carefully, but full of pitfalls if we're careless

- in general it's good to reduce ambiguity as much as possible; which token a lexeme corresponds to shouldn't depend on what context it's used in

    - famous C++ example: `vector<set<int>>` is a syntax error because `>>` is read as a bitshift operator, you need to write `vector<set<int> >`

    - this may have been fixed in more recent versions of the language, i'm not sure

- why regular expressions, why not contex-free grammars?

    - we can use CFGs (they can do anything REs can do), and there are examples where people have done so (see "scannerless parsers")

    - in general lexing using regular expressions makes the parser a lot simpler and more efficient

    - it does also explain why you can't nest c-style comments: proper nesting is context-free, you can't describe it using regular expressions

# parsing
## overview

- we describe the syntax of our language as a context-free grammar, with tokens as the terminal symbols of our alphabet

    - [show `cflat-docs/syntax.md` again]

- our task is to prove that the given source code is really a member of our language. given a CFG and a string, we can prove that the string is a member of the CFG's language using a derivation tree (aka parse tree, concrete syntax tree)

```
grammar:
 E ::= [1] Id | [2] E Op E | [3] ( E )
Op ::= [4] + | [5] *

string: "(x + y) * z"

tokens: [OpenParen, Id(x), Plus, Id(y), CloseParen, Mul, Id(z)]
-- for clarity we'll abbreviate the tokens using the corresponding characters

derivation tree [arrange as tree]:
E -[2]→ E Op E
  -[3]→ ( E ) Op E
  -[2]→ ( E Op E ) Op E
  -[1]→ ( x Op E ) Op E
  -[4]→ ( x + E ) Op E
  -[1]→ ( x + y ) Op E
  -[5]→ ( x + y ) * E
  -[1]→ ( x + y ) * z
```

- the internal nodes of the tree are nonterminals, with the root being the grammar start symbol; the leaves of the tree are the tokens (in order)

- if we can create such a derivation tree, then the token stream represents a syntactically correct program

    - the derivation i went through in the example is called a _leftmost derivation_ because i always expanded the leftmost leaf in the derivation tree

    - we could expand the leaves in any order and get the same tree, but leftmost is a natural order to use when we're processing the input from the beginning to the end---we're matching the beginning of the input before matching the rest of the input

- this is the essence of parsing: proving that an input belongs to the language by constructing a derivation tree

    - as discussed previously we rarely actually construct the entire derivation tree explicitly, instead we construct an abstract syntax tree that is more useful for compilation

    - however, IDEs _do_ construct the derivation tree so that they can do things like syntax highlighting, etc

## ambiguity

- using the same grammar, we can give a derivation tree for the input `x + y + x * z + z`; in fact, we can give several

- the biggest problem we face in parsing we also had to deal with in lexing: ambiguity

    - a grammar is _ambiguous_ if there is at least one input in the language that has more than one parse tree

- this is a problem because the parse tree determines how a program will be executed
    
    - consider `1 + 2 * 3` and the difference between `(1 + 2) * 3` and `1 + (2 * 3)` (draw as trees)

    - the syntactic structure of the program influences its meaning, the same as in english

    - consider "she saw the man with a telescope"; what does it mean?

- arithmetic expressions are an easy example, but it can come up in lots of places---a famous example is "dangling elses"

```
stmt ::= `if` exp `then` stmt `else` stmt
       | `if` exp `then` stmt
       | x `=` exp

// code
a = false;
c = 1;
if a then
if b then c = 2;
else c = 3;
```

- what is the value of `c`?

    - remember that whitespace is not significant, we skip it during lexing

    - [show two parse trees, one where the `else` is attached to the first `if` and one where it is attached to the second]

- so we want to avoid grammars that are ambiguous

    - unfortunately, determining whether a grammar is ambiguous is undecidable

    - but we _can_ determine whether a grammar is _deterministic_

    - deterministic implies unambiguous, though unambiguous does _not_ imply deterministic

- recall that a PDA is an NFA + a stack, a DPDA is a DFA + a stack, and DPDA are strictly less expressive than PDA (unlike regular languages where DFA and NFA are equally expressive)

    - a PDA is deterministic if, from each state, the current input and the current top of the stack allows for at most one transition

    - PDAs and CFGs are equivalent (we can transform each into the other)

    - since DPDA are less expressive than PDA, there must be CFG that cannot be expressed as DPDA; these include all ambiguous grammars (and also some non-ambiguous grammars)

    - a grammar that can be expressed with a DPDA is a deterministic grammar, and there is an algorithm that decides whether this is true

- it's important to note that ambiguity is a property of a _grammar_, not a _formal language_

    - the same formal language can have multiple grammars, some of which are ambiguous and some of which are not

    - there exist formal languages that only have ambiguous grammars, but they don't arise for realistic programming languages

- this gives us a solution: if the original grammar for our programming language syntax is not deterministic, transform it into one that is (while still describing the same syntax)

    - this isn't always possible, but again for realistic programming languages it tends to be

    - if it isn't, then we have to change our programming language syntax until it is

- example (dangling elses again)

```
stmt ::= `if` exp `then` withelse `else` stmt
       | `if` exp `then` stmt
       | x `=` exp

withelse ::= `if` exp `then` withelse `else` withelse
           | x `=` exp
```

- this version of the grammar recognizes exactly the same set of inputs, but only allows for a single parse tree

    - it requires any `else` to be associated with the "nearest" `if`

- of course, we can always just change our programming language to make it obviously deterministic

    - require that expressions use parentheses everywhere
    - require that braces be used everywhere
    - etc

- this makes implementing the compiler easy, but can make programmers frustrated and annoyed

    - it's often true that we can either make things convenient/nice for the programmer but complicated for the compiler implementor or vice-versa

    - usually we should default to making things nice for the programmer even if it makes our lives difficult as compiler implementors

## parsing strategies

- we know what the goal of parsing is, but how do we do it? there are actually a large number of strategies with different tradeoffs in complexity, expressiveness, performance, ease of use, etc

- let's take the same example we used earlier

```
grammar:
 E ::= [1] Id | [2] E Op E | [3] ( E )
Op ::= [4] + | [5] *

string: "(x + y) * z"
```

- we can divide all of the strategies into two basic categories: top-down and bottom-up

- TOP-DOWN: start from the root of the derivation tree, i.e., the grammar start symbol (`E` in this case) and work our way down to the leaves, selecting productions for each node that will result in the leaves matching the input string

    - this seems like it involves a lot of guess-work, but clever versions of the approach can be very efficient

    - [show top-down approach for example]

- BOTTOM-UP: start from the leaves of the derivation tree, i.e., the input, and work our way up the tree selecting productions in reverse

    - [show bottom-up approach for example]

- besides top-down vs bottom-up, parsing strategies can also be divided by what class of grammars they work for

    - there are strategies that work for _any_ grammar, including ambiguous ones (e.g., CYK, Earley, GLL, GLR, etc); they are O(n³) and, for ambiguous inputs, result in a parse forest instead of a parse tree

    - there are strategies that work for any _deterministic_ grammar (e.g., LR, a bottom-up strategy); these are linear but complex to implement (usually we resort to automatically generating an LR parser instead of coding it by hand)

    - there are strategies that work for a _subset_ of deterministic grammars (e.g., LL(k), a top-down strategy); these are also linear and, even though they are less expressive, they have other nice properties

        - LL(k) parsers can be handwritten, giving very fine control over parsing and allowing for easier error recovery and error messages

        - a popular choice for mainstream compilers, including gcc and clang

        - this is what we'll be implementing for our language

## recursive descent and LL(1)
### intro

- LL(1) is an example of a top-down parser, and a common strategy for these types of parsers (which we'll be using) is _recursive descent_

    - there are other strategies, but recursive descent is the most popular

- we'll go over a naive recursive descent strategy first, then show how an LL(1) grammar can make it much more efficient

- to illustrate the ideas we'll use the following simple grammar

```
S ::= aSa | bSb | c
```

### naive recursive descent

- from 138 we know that we can transform any CFG into a PDA

    - there are several possible transformations, but we'll use the one that yields a top-down strategy

```
q0 -[ϵ, ϵ⟶S$]→ q1 -[ϵ, $⟶ϵ]→ q2 (accepting)
                ⇵
             [ϵ, S⟶aSa]
             [ϵ, S⟶bSb]
             [ϵ, S⟶c]
             [a, a⟶ϵ]
             [b, b⟶ϵ]
             [c, c⟶ϵ]
```

- this is _not_ a DPDA because there are 3 possible transitions if `S` is on top of the stack

    - for now we'll assume we have some oracle that lets us always pick the right transition to use

- let's see what happens with input `abcba`; pay particular attention to the stack

```
ϵ ⟶ S$ ⟶ aSa$ ⟶ ̸aSa$ ⟶ ̸abSba$ ⟶ ̸a̸bSba$ ⟶ ̸a̸bcba$ ⟶ ̸a̸b̸cba$ ⟶ ̸a̸b̸c̸ba$ 
  ⟶ ̸a̸b̸c̸b̸a$ ⟶ ̸a̸b̸c̸b̸a̸$
```

- the stack is going through a top-down derivation for the input

    - when the top of the stack is `S` we expand it by pushing a production

    - when the top of the stack is a terminal we match it against the input

    - starting from `S` we work our way to the entire input string, and because of how the stack works we're getting a leftmost derivation

- this approach gives us what we want, but relies on having an oracle; what if we don't have one?

    - then we need to guess, and if we guess wrong then we need to backtrack, i.e., if the PDA rejects we need to rollback to the last place we guessed and guess something different

    - this can be exponential if we always guess wrong at first; we'll see soon how an LL(1) grammar can prevent this problem from happening

- how would we implement this idea in code?

    - the PDA stack is what's keeping track of the derivation

    - we don't need to actually translate the grammar to a PDA, we can use the computer's implicit function stack [remind them how a function stack works]

    - in other words, we use recursion

- here's how it works for our example in pseudocode [see OneNote]

```
S ::= aSa | bSb | c

fn S() {
  saved_input = curr_input;

  try { // aSa
    curr_input.consume('a');
    S();
    curr_input.consume('a');
  }
  else try { // bSb
    curr_input = saved_input;
    curr_input.consume('b');
    S();
    curr_input.consume('b');
  }
  else try { // c
    curr_input = saved_input;
    curr_input.consume('c')
  }
  else {
    curr_input = saved_input;
    fail
  }
}
```

- [show what happens for input `abcba`]

- notice that the recursive function stack is mimicking what happened with the PDA stack---it's tracking the derivation

    - when we make a recursive call we save our current place in the code, process the call, then resume from where we left off---e.g., the stack lets us remember that after we match an `a` and then try to match `S()`, we still need to match another `a`

- here's the general idea for any grammar

    - create a set of mutually recursive functions, one per nonterminal

    - each function `A()` will have a case for each rule `A ::= α` in the grammar

    - when `A()` is called it tries each case in turn until it succeeds or runs out of cases and fails

        - suppose we have rules `A ::= α₁α₂...αₙ | β₁β₂...βₘ`, where each `αᵢ` and `βᵢ` may be either a terminal (i.e., a character) or a nonterminal

        - then we create a function `A()` that tries the first rule, then if it doesn't work it tries the second rule, then if that doesn't work it fails

        - for each rule we look at `αᵢ` (or `βᵢ` depending on which rule we're trying) and:

            - if `αᵢ` is a terminal, tries to consume matching characters from the input; if successful goes to `αᵢ₊₁` else fails this rule

            - if `αᵢ` is a nonterminal, calls the corresponding function; if the function returns successfully then we go to `αᵢ₊₁` else we fail this rule

### LL(1) recursive descent

- backtracking kills performance, and for some grammars recursion can result in non-termination---how can we make this strategy efficient?

    - that's where an LL(1) grammar comes in

- consider the PDA we created for our example gramar and note that we could make it a DPDA by adding _lookahead_

```
q0 -[ϵ, ϵ⟶S$]→ q1 -[ϵ, $⟶ϵ]→ q2 (accepting)
                |↑
      [a, ϵ⟶ϵ] || [ϵ, a⟶ϵ]
                ↓|
                qA
                ⇵
            [ϵ, S⟶aSa]

[repeat construction for qB, qC]
```

- let's see what happens with input `abcba`

    - [same as before, except not relying on oracle]

    - it's completely deterministic, no guessing involved

- what does that mean for our recursive descent algorithm? it means no backtracking is necessary [see OneNote]

```
fn S() {
  if next token is 'a' { // aSa
    input.consume('a');
    S();
    input.consume('a');
  }
  else if next token is 'b' { // bSb
    input.consume('b');
    S();
    input.consume('b');
  }
  else if next token is 'c' { // c
    input.consume('c');
  }
  else fail
}
```

- there is a special case we need to worry about: what if a nonterminal has an ϵ production?

    - just treat it as the default case, i.e., the thing to do if none of the other cases are true

- new example grammar

```
A ::= xy | yBz
B ::= wy | ϵ
```

- code

```
fn A() {
  if next token is 'x' {
    input.consume('x');
    input.consume('y');
  }
  else if next token is 'y' {
    input.consume('y');
    B();
    input.consume('z');
  }
  else fail
}

fn B() {
  if next token is 'w' {
    input.consume('w');
    input.consume('y');
  } else {
    // don't consume anything, but still return success
  }
}
```

- the key is that by looking at the next token we _always_ know exactly what rule we should be trying to match (and if the next token isn't one we expect then we return an error)

- a grammar s.t. we can use `k` tokens of lookahead to make the recursive descent algorithm completely deterministic is an LL(k) grammar

    - this isn't true of all grammars, or even all deterministic grammars, but we can often make it work for real programming languages

    - for us, k = 1 (and for most real programming languages)

- why `LL(k)`?

    - we're processing the input from `L`eft to right
    - we're tracking a `L`eftmost derivation
    - we're using `k` tokens of lookahead

### exercise

- given the following grammar

```
S ::= aPb | Qc | cRd | TcP 
P ::= QR | TR | ε 
Q ::= fR | b 
R ::= d | gbc 
T ::= ea | Ra
```

- write pseudocode for a recursive descent LL(1) parser; remember: 

    - for each nonterminal there is a function
    
    - the function contains a case for each production rule for that nonterminal

    - we use one token of look-ahead to determine which case to execute

- trace the parser for inputs:

    - `afgbcdb`  [PASS]
    - `daceagbc` [PASS]

- SOLUTION

```
fn S() {
  if next token is a {
    input.consume('a');
    P();
    input.consume('b');
  }
  else if next token is in {f, b} {
    Q();
    input.consume('c');
  }
  else if next token is c {
    input.consume('c');
    R();
    input.consume('d');
  }
  else if next token is in {e, d, g} {
    T();
    input.consume('c');
    P();
  }
  else fail
}

fn P() {
  if next token is in {f, b} {
    Q();
    R();
  }
  else if next token is in {e, d, g} {
    T();
    R();
  }
}

fn Q() {
  if next token is f {
    input.consume('f');
    R();
  }
  else if next token is b {
    input.consume('b');
  }
  else fail
}

fn R() {
  if next token is d {
    input.consume('d');
  }
  else if next token is g {
    input.consume('gbc');
  }
  else fail
}

fn T() {
  if next token is e {
    input.consume('ea');
  }
  else if next token is in {d, g} {
    R();
    input.consume('a');
  }
  else fail
}
```

## formalizing LL(1)
### intro

- how do we know if a grammar is LL(1) and thus suitable for implementing in this way? there are two criteria:

    - no _left recursion_

    - deterministic look-ahead (using 1 token)

- if the grammar meets these criteria, it is LL(1)

### left recursion

- a grammar is _left recursive_ if there exists a derivation `A -->* Aα` for some nonterminal `A`

    - this means that when matching `A`, we recursively need to match `A` again _without_ consuming any input

    - if we're using recursion, this means that we're caught in an infinite recursive cycle

- example

```
S ::= Sa | b

fn S() {
  if next token is `b` {
    input.consume('b');
  }
  else if next token is `a` {
    S();
    input.consume('a');
  }
}

input: "baa"
```

- in order to allow a recursive descent parsing strategy, we have to forbid left-recursive grammars

### deterministic look-ahead

- our intuition is that for any given nonterminal `A` that has multiple rules, looking at the next token in the input is sufficient to determine which rule we need to pick

    - we call a grammar for which this is true a _predictive grammar_

- our simple example from earlier: `S ::= aSa | bSb | c`

    - if we have an `S` symbol and need to expand it with one of these rules, by looking at the next token in the input we can tell which rule to use

- in general it may be a bit more complicated to figure out the symbols to use; take the grammar from the previous exercise, where some rules (e.g., for `P`) themselves start with nonterminals

```
S ::= aPb | Qc | cRd | TcP 
P ::= QR | TR | ε 
Q ::= fR | b 
R ::= d | gbc 
T ::= ea | Ra
```

- we can formalize this intuition; first we'll do so assuming there are no ϵ rules, then we'll add in ϵ rules (which make things a bit more complicated)

### look-ahead without ϵ

- let α, β be strings of terminals (T) and nonterminals (N), then:

```
FIRST(α) = { t ∈ T | α -->* tβ }
```

- in other words, FIRST(α) is the set of terminals that can be derived from α with 0 or more applications of the grammar production rules

    - there is an algorithm to compute FIRST sets (given in the textbook), but for many grammars it's easy enough to compute manually by inspection

- example

```
S ::= AB
A ::= xBw | yBz | Bwz
B ::= 0 | 1

  FIRST(S) = { x, y, 0, 1 }
  FIRST(A) = { x, y, 0, 1 }
  FIRST(B) = { 0, 1 }
FIRST(xBw) = { x }
FIRST(yBz) = { y }
FIRST(Bwz) = { 0, 1 }
```

- if a grammar is LL(1), then for any nonterminal `A ::= α | β` it must be true that FIRST(α) is disjoint from FIRST(β)

    - thus, we just need to see if the next token belongs to FIRST(α) or FIRST(β) to determine which rule to use

### look-ahead with ϵ

- example

```
A ::= xBA | f
B ::= xwB | ϵ

  FIRST(A) = { x, f }
  FIRST(B) = { x }
FIRST(xBA) = { x }
  FIRST(f) = { f }
FIRST(xwB) = { x }
  FIRST(ϵ) = {}
```

- at first it seems things are OK: for both `A` and `B` the FIRST sets of the productions rules are disjoint---but consider the input `xxf`

    - `A` is the start symbol, so we begin matching there
    - look-ahead = `x`, so we use the first rule `xBA` and consume `x`
    - now we're matching `BA`
    - look-ahead = `x`, so we use the first rule `xwB` and consume `x`
    - now we're matching `wBA`
    - ERROR: next input symbol is `f` which doesn't match `w`

- what happened? the correct way to parse input `xxf` would be:

    - expand `A` to `xBA`
    - consume `x`
    - expand `B` to ϵ
    - expand `A` to `xBA`
    - consume `x`
    - expand `B` to ϵ
    - expand `A` to `f`
    - consume `f`

- but we can't figure this out just looking at the next token because the ϵ allows us to "throw away" a nonterminal at any time (by picking the ϵ rule when expanding it)

    - therefore looking at the next token doesn't always tell us what the correct thing to do is

- enter FOLLOW sets: for any nonterminal `A`, `FOLLOW(A)` is the set of terminals that can _immediately_ follow any expansion of `A`

    - that is, for all production rules `αAβ` FIRST(β) is included in FOLLOW(A)

    - note that FOLLOW is only for nonterminals, whereas FIRST was for any string of terminals or nonterminals

    - there is another algorithm for computing FOLLOW sets (in the textbook), but again it's often easy enough to manually compute it by inspection

```
A ::= xBA | f
B ::= xwB | ϵ

FOLLOW(A) = {}
FOLLOW(B) = FIRST(A) = { x, f }
```

- if a grammar is LL(1), then for any nonterminal `A ::= α | β` it must be true that if α -->* ϵ, then FIRST(β) and FOLLOW(A) are disjoint

    - in other words, by looking at the next token we can decide whether we should expand `A` or throw it away using ϵ

    - when matching `A`, if the next token is in FIRST(α) expand to α, else if the first token is in FIRST(β) expand to β, else assume we're using the ϵ and match without consuming any input

    - this is exactly the strategy we used when making our recursive descent parser---this property of LL(1) grammars is why it's guaranteed to work correctly

- based on this requirement, the example grammar is _not_ LL(1) because FIRST(xwb) ∩ FOLLOW(B) = { x }, so they are not disjoint

- to sum up:

    - FIRST sets tell us which production rule to use based on the look-ahead, and for an LL(1) grammar this is unambiguous
    
    - FOLLOW sets tell us whether using the FIRST sets this way will actually work in the presence of ϵ rules, and for an LL(1) grammar it must be true that they will

### exercise

- compute the FIRST and FOLLOW sets of the following grammar and explain all the reasons why it is _not_ predictive

```
A ::= xBy | Bx | zCw
B ::= wB | ε
C ::= wC | DB
D ::= yD | ε
```

- solution

```
    FIRST   FOLLOW
--- ------- --------
A   w,x,z   ∅
B   w       x,y
C   w,y     w
D   y       w

for A: FIRST(xBy) ∩ FIRST(Bx) = {x}
for C: FIRST(wC) ∩ FIRST(DB) = {w}
for C: FIRST(wC) ∩ FOLLOW(C) = {w}
```

### what about regular expressions in the production rules?

- remember that the regular expressions are just convenient short-hand; expanding them back to "standard" CFGs helps understand how to deal with them

- if the grammar is LL(1), then we can always determine how to handle them based on the look-ahead

- dealing with `?`: remember that `r?` is just `r | ϵ`

    - in the following example, if the grammar is LL(1) then α₂ and α₃ must begin with different terminals and so we can immediately tell whether to consume α₂ or α₃

```
// using ?
A ::= α₁ α₂? α₃

// we don't have to actually translate to this version, but it makes
// clear how to process the ?
A ::= α₁ B α₃
B ::= α₂ | ϵ
```

- dealing with `*`: this means that the expression can happen any number of times (including 0)

    - in the example below, if the grammar is LL(1) then α₂ and α₃ must begin with different terminals and so we can immediately tell whether to consume α₂ or α₃---then we keep asking that question in a loop until the answer is to consume α₃

    - note that we had to translate the `*` using _right_ recursion since LL(1) grammars can't have left recursion, which automatically makes the parse right-associative; this is a problem if we actually want it to be left-associative

        - we have to parse and then modify the AST afterwards

        - it's easiest to not translate the grammar and just parse using the `*` operator directly per the below bullet

    - if we're directly using the `*` in our parser instead of translating then we just use a loop to get a vector of α₂, then make it left- or right-associative when we put it into the AST depending on what we have specified for the language

```
// using *
A ::= α₁ α₂* α₃

// we don't have to actually translate to this version, but it makes
// clear how to process the *
A ::= α₁ B α₃
B ::= α₂B | ϵ
```

- dealing with `+`: remember that `r+` is just `rr*`, we can deal with it just like `r*` except we require that there's at least one α₂

## building the AST

- recall that the derivation tree (aka parse tree, concrete syntax tree) contains more information than we really need after parsing

    - e.g., punctuation, parentheses, braces, etc, as well as the exact sequence of expanded nonterminals used to build the tree

    - it shows _how_ the program was parsed, but all we need after parsing is the underlying structure of the program

    - this underlying structure is called the _abstract syntax tree_ (AST)

- example grammar (arithmetic expressions with precedence to enforce LL(1))

```
E ::= FX
X ::= + FX | ε
F ::= GY
Y ::= * GY | ε
G ::= (E) | id
```

- concrete syntax tree for `x + y * z` (draw as tree)

```
E ⟶ [FX] 
  ⟶ [GY]X 
  ⟶ [id(x)]YX 
  ⟶ id(x)[ϵ]X 
  ⟶ id(x)[+FX] 
  ⟶ id(x)+[GY]X
  ⟶ id(x)+[id(y)]YX 
  ⟶ id(x)+id(y)[*GY]X 
  ⟶ id(x)+id(y)*[id(z)]YX
  ⟶ id(x)+id(y)*id(z)[ϵ]X 
  ⟶ id(x)+id(y)*id(z)[ϵ]
```

- abstract syntax tree (draw as tree)

```
(Add
  Id(x)
  (Mul
    Id(y)
    Id(z)
  )
)
```

- it's called the _abstract_ syntax tree because it abstracts out the unimportant (now that parsing is over) information from the concrete parse tree

- we often directly compute the AST during parsing rather than creating a concrete parse tree and then abstracting it

- we do this by inserting the AST construction logic directly into the functions doing the parsing

    - instead of functions just consuming the input and returning nothing, they'll consume the input and return AST sub-trees

    - when a function calls another function, it takes the returned AST sub-tree and adds it to the AST tree it's creating

- so we need to define the AST data structure, then insert the appropriate logic into our parsing functions

- example: for the grammar above, we could use the following data structure as the AST

```
AST
| Id { name: string }
| Add { left: AST, right: AST }
| Mul { left: AST, right: AST }
```

- the recursive descent parser for the grammar [see OneNote]

```
fn E() { 
  F();
  X();
}

fn X() {
  if next token is `+` {
    input.consume('+');
    F();
    X();
  }
}

fn F() {
  G();
  Y(); 
}

fn Y() {
  if next token is '*' {
    input.consume('*');
    G();
    Y();
  }
}

fn G() {
  if next token is `(` {
    input.consume('(');
    E();
    input.consume(')');
  }
  else if next token is id(name) {
    input.consume(id);
  }
  else fail
}
```

- modified to produce an AST [see OneNote]

```
fn E() -> AST { 
  a = F();
  b = X();
  if b is nil { return a; }
  else { return AST::Add(a, b); }
}

fn X() -> AST {
  if next token is `+` {
    input.consume('+');
    a = F();
    b = X();
    if b is nil { return a; }
    else { return AST::Add(a, b); }
  }
  return nil;
}

fn F() -> AST {
  a = G();
  b = Y();
  if b is nil { return a; }
  else { return AST::Mul(a, b); }
}

fn Y() -> AST {
  if next token is '*' {
    input.consume('*');
    a = G();
    b = Y();
    if b is nil { return a; }
    else { return AST::Mul(a, b); }
  }
  return nil;
}

fn G() -> AST {
  if next token is `(` {
    input.consume('(');
    ast = E();
    input.consume(')');
    return ast;
  }
  else if next token is id(name) {
    input.consume(id);
    return AST::Id(name);
  }
  else fail
}
```

- example (or exercise): given the grammar and parser below, modify the parser to return an AST as also defined below and run it on `payawazaxa` [see OneNote]

ambiguous grammar (think of `w`, `x`, `y`, `z` as binary operators, `p` as a unary operator, and `a` as a constant):
```
A ::= AyB | AzB | B
B ::= BwC | BxC | C
C ::= pC | a
```

LL(1) grammar:
```
A ::= BX
X ::= yBX | zBX | ε
B ::= CY
Y ::= wCY | xCY | ε
C ::= pC | a
```

```
AST
| a
| w { op1: AST, op2: AST }
| x { op1: AST, op2: AST }
| y { op1: AST, op2: AST }
| z { op1: AST, op2: AST }
| p { op: AST }
```

recursive descent parser:
```
fn A() {
  B();
  X();
}

fn X() {
  if next token is 'y' or 'z' {
    if next token is 'y' { input.consume('y'); }
    else { input.consume('z'); }
    B();
    X();
  }
}

fn B() {
  C();
  Y();
}

fn Y() {
  if next token is 'w' or 'x' {
    if next token is 'w' { input.consume('w'); }
    else { input.consume('x'); }
    C();
    Y();
  }
}

fn C() {
  if next token is 'p' {
    input.consume('p');
    C();
  }
  else if next tokan is 'a' {
    input.consume('a');
  }
  else fail
}
```

modified parser (SOLUTION)
```
fn A() -> AST {
  a = B();
  b = X();
  if X matched 'y' { return AST::y(a, b); }
  else if X matched 'z' { return AST::z(a, b); }
  else { return a; }
}

fn X() -> AST {
  if next token is 'y' or 'z' {
    if next token is 'y' { input.consume('y'); }
    else { input.consume('z'); }
    a = B();
    b = X();
    if X matched 'y' { return AST::y(a, b); }
    else if X matched 'z' { return AST::z(a, b); }
    else { return a; }
  }
  return nil;
}

fn B() -> AST {
  a = C();
  b = Y();
  if Y matched 'w' { return AST::w(a, b); }
  else if Y matched 'x' { return AST::y(a, b); }
  else { return a; }
}

fn Y() -> AST {
  if next token is 'w' or 'x' {
    if next token is 'w' { input.consume('w'); }
    else { input.consume('x'); }
    a = C();
    b = Y();
    if Y matched 'w' { return AST::w(a, b); }
    else if Y matched 'x' { return AST::y(a, b); }
    else { return a; }
  }
  return nil;
}

fn C() -> AST {
  if next token is 'p' {
    input.consume('p');
    return AST::p(C());
  }
  else if next tokan is 'a' {
    input.consume('a');
    return AST::a;
  }
  else fail
}
```

AST for `payawazaxa`
```
y(
  p(a),
  z(
    w(a, a)
    x(a, a)
  )
)
```

- what is the AST for cflat? [see OneNote]

    - see `cflat-docs/ast.md`

## transforming a grammar to LL(1)

### intro

- how do we take a grammar that isn't LL(1) and turn it into a grammar that is LL(1)?

    - for example, our cflat grammar is not LL(1) and so we cannot use it for a recursive descent LL(1) parser as-is

- there are three main things to we need to worry about

    - precedence: when there are multiple ways to parse a given string, we need to refactor the grammar to enforce a single possible parse

    - left-recursion: a grammar that has nonterminals in the "wrong" places can prevent recursive descent parsers from ever terminating, so we need to refactor the grammar to make sure this doesn't happen

    - look-ahead: we need to make sure that the grammar is deterministic using a given amount of _look-ahead_

### establishing precedence

- if there are multiple ways to parse a given string, we need to force the grammar to allow only a single parse

- we do this by establishing _precedence_: we enforce that one parse is preferable and should always be used if possible

- example ambiguous grammar

```
E ::= id | E + E | E - E | E * E | E / E | (E)
```

- conventionally multiplication should have precedence over addition

    - given `a + b * c`, we should treat it as `a + (b * c)`

- there are several strategies that have been developed to enforce precedence in a grammar, but we'll use the classical solution that modifies the grammar itself to enforce the desired precedence

- method:

    - decide on the levels of precedence, e.g., {`()`} > {`*`,`/`} > {`+`,`-`}

    - create a nonterminal for each level of precedence (we can reuse the original nonterminal for the lowest predecedence level)

    - factor out the operations into the appropriate nonterminal for their level of precedence

- example:

```
G = {`()`}
F = {`*`,`/`}
E = {`+`,`-`}

E ::= E + F | E - F | F
F ::= F * G | F / G | G
G ::= (E) | id
```

- notice the following properties:

    - each nonterminal rule that applies a binary operator has one operand that is the same nonterminal again and the other is the next highest precedence level nonterminal

        - this allows the expression to have multiple of the same precedence level operators in a row
        
        - choosing which side is which controls the associativity of the operator: having the same nonterminal on the left makes the operator left associative; having it on the right makes the operator right associative

    - each nonterminal (except the highest precedence, `G`) allows for applying an operator at the level of predecence or directly falling through to the next level

        - this allows the expression to not have any operators at the lower precedence level, e.g., an expression that doesn't have `+` in it

    -  the base cases for expressions (e.e., `id`) are always at the highest level of precedence

        - this allows the expression to just be an identifier without any operators at all

- examples

```
  x + y * z ==> x + (y * z)
  x * y + z ==> (x * y) + z
  x * y * z ==> (x * y) * z
(x + y) * z ==> (x + y) * z
```

- we've looked at this using arithmetic operators as our examples, but it applies in many other situations, e.g.:

    - relational and logical operators: `!(x < y) && z < y`

    - subscript operators: `a + b[i]`

    - type casts: `(double)a / b`

- exercise: rewrite the following grammar assuming that all operator are left-associative and using the following precedence levels:

    - {`p`} > {`w`,`x`} > {`y`,`z`}

```
A ::= A R A | p A | a
R ::= w | x | y | z
```

- solution

```
A ::= A y B | A z B | B
B ::= B w C | B x C | C
C ::= p C | a
```

### removing left-recursion
#### intro

- we saw before that left recursion causes non-termination in a recursive descent parser; how can we remove it?

- we can define two kinds of left recursion

    - _direct_: there is a production of the form `A ::= Aα`

    - _indirect_: there is a set of mutually recursive productions that allow a left-recursive derivation (possibly involving an ϵ rule)

- example 1: indirect left recursion

```
A ::= B | alice
B ::= C | bob
C ::= A charlie

consider A --> B --> C --> A charlie
```

- example 2: indirect left recursion with ϵ

```
A ::= B | alice
B ::= C | bob
C ::= DA charlie
D ::= dave | ε

consider A --> B --> C --> DA charlie --> A charlie
```

#### removing direct left recursion

- this is easy to fix: any left-recursive production can be changed to an equivalent right-recursive production as follows

- given: `A ::= Aα | β | γ` s.t. α,β,γ don't start with `A`

    - this grammar says that there can be β or γ then a sequence of αs

- transformed: `A ::= βB | γB`, `B ::= αB | ϵ`

    - this grammar says the same thing, but using right-recursion

- any left-recursive rule must have some non-recursive base case (β and γ in the above example), otherwise the recursive would never terminate

    - we just rearrange those base cases to use right-recursion instead of left-recursion

- what if there are multiple left-recursive rules for `A`?

    - there must still be at least one base case that we can use

    - example: `A ::= Aα | Aβ | γ`

    - transformed: `A ::= γB`, `B ::= αB | βB | ϵ`

- example

```
E ::= E+F | E-F | F
F ::= (E) | id
```

- transformed

```
E ::= FG
F ::= (E) | id
G ::= +FG | -FG | ϵ
```

#### removing indirect left recursion

- there are several strategies for removing indirect left recursion, but the conceptually simplest is to just inline productions to turn indirect recusion into direct recursion and then applying the transformation from earlier

- example

```
A ::= B | alice
B ::= C | bob
C ::= A charlie
```

```
A ::= C | bob | alice
B ::= C | bob
C ::= A charlie
```

```
A ::= A charlie | bob | alice
B ::= C | bob
C ::= A charlie
```

```
A ::= bob D | alice D
D ::= charlie D | ϵ
B ::= C | bob
C ::= A charlie
```

- note that now `B` and `C` are not reachable from the start symbol `A` and can be removed; in general that may or may not happen

- this strategy also works for dealing with ϵ

```
A ::= B | alice
B ::= C | bob
C ::= DA charlie
D ::= dave | ϵ
```

```
A ::= C | bob | alice
B ::= C | bob
C ::= DA charlie
D ::= dave | ϵ
```

```
A ::= DA charlie | bob | alice
B ::= C | bob
C ::= A charlie
D ::= dave | ϵ
```

```
A ::= dave A charlie | A charlie | bob | alice
B ::= C | bob
C ::= A charlie
D ::= dave | ϵ
```

### left-factoring for predictability

- after establishing precedence and removing left recursion, the grammar still may not be predictive

- _left factoring_ is a transformation that can possibly make it predictive (but doesn't work for all grammars)

- example: this grammar is not predictive because all three rules start with `id`

```
E ::= id | id[E] | id(E)
```

- but we can make it predictive by factoring out the `id`

```
E ::= id F
F ::= [E] | (E) | ϵ
```

- this is the left-factoring transformation; in general terms:

    - `A ::= αβ₁ | αβ₂ | ... | αβₙ`

    - becomes `A ::= αB`, `B ::= β₁ | β₂ | ... | βₙ`

### a final note [only need to cover if someone asks about it]

- you might have learned in 138 that we can transform any grammar to remove ϵ rules, which might seem to make FOLLOW sets unnecessary

- however, this transformation can make the grammar non-predictive, which we would need to fix using left-factoring

- but left-factoring may re-introduce the ϵ rules, so we have to deal with them

# validation
## what is validation

- after parsing we know that the program is syntactically correct, but that doesn't necessarily mean that it's valid

- what does "valid" mean? whatever the language designer decides it means

- there are some common, trivial rules that are easy to check, like:

    - there is a `main` function (or whatever entry point the language has)
    - no two functions have the same name
    - basically any purely syntactic check...

- non-trivial validation can be done in several different ways

    - cflat has a _static type system_, like C, C++, Java, etc; this means non-trivial validation is done by the compiler

    - other language have dynamic type systems, where non-trivial validation happens at runtime

    - we'll talk more about the pros and cons of this choice later

## what is a static type system

- recall the following example

```
let x:int = 0, y:int = 10;
x = new int;
y = x * y;
```

- this program doesn't make sense because it multiplies a pointer by an integer; other examples of things that don't make sense are: trying to access an integer as if it were a struct, trying to index into a function as if it were an array, etc

    - we can't compile these nonsensical programs because there isn't any sensible assembly instructions that we can translate them to

- a type system is a framework that allows us to precisely specify what things "make sense" for our language and what things do not

    - a type checker uses the type system in order to filter programs to only allow those that make sense

- a _type_ describes a set of values along with what operations are valid for those values

    - `int` describes the set of integers; allowed to compare and do arithmetic
    - `&int` describes the set of pointers to integers; allowed to dereference
    - `[int]` describes the set of arrays with integer elements; allowed to index into
    - etc

- saying that expression `e` has type `T` means that when `e` is evaluated at runtime, the resulting value will belong to the set described by `T`

    - `1 + 2` has type `int` because its value is `3`, which is an integer

    - `new int` has type `&int` because its value is a pointer to a location in the heap that holds an integer

- the type system provides a set of rules that allow us to assign a type to every element of the AST as long as that type is compatible with the operations being done by/on that element

    - for example, the rules would not allow us to assign a type to `x * y` in the example above because `*` is not a valid operation on pointers

    - note that type systems are language-specific; we could invent a new language where it _does_ make sense to multiply a pointer by an integer, in which its type system would allow it

- type systems are covered in detail in CS 162, so i'm only going over the very basics here; i recommend 162 to learn more

## how do type systems work

- a type system is defined by:

    - the set of types that the language recognizes
    
    - the typing rules that describe how to assign types to elements of the AST

- the set of types is usually described via a CFG (or something equivalent)

    - note that there are usually an infinite number of possible types

- EXAMPLE

```
τ ∈ Type ::= int | ref τ | τ... → τ
[sequences of types may use vector notation or overlines instead of ellipses]
```

- the typing rules consist of:

    - _typing judgements_ that state that a particular typing fact holds (e.g., that this element of the AST has this type)

    - _inference rules_ that state how we can conclude whether a particular judgement is true

    - there is a very strong connection between type systems and formal logic

        - judgements and inference rules come from the _natural deduction_ style of formal logic

        - the _curry-howard correspondance_ states that types are propositions and programs are proofs; from this perspective a type checker is a proof verifier

- a _typing judgement_ states that a typing fact holds, and looks something like `Γ ⊢ e : τ` where:

    - `Γ` is a list of typing facts about the variables in scope, called the _context_
    - `e` is the expression we're typing
    - `τ` is the type of `e` that the judgement is asserting

- EXAMPLE: `x:int, y:ref int ⊢ x + *y : int`

    - we needed the context because `x + *y` by itself says nothing about the types of `x` or `y`, so it's impossible to determine what the type of `x + *y` is without that additional information

- an _inference rule_ explains how to prove that a judgement is correct; it is essentially an "if...then..." rule and looks like this:

- EXAMPLE

```
Γ ⊢ e : ref τ
-------------
Γ ⊢ *e : τ
```

- from this example rule we are allowed to conclude:

```
x : ref int ⊢ *x : int
x : ref ref int ⊢ *x : ref int
x : ref ref int ⊢ **x : int
```

- a judgement may have 0 or more premises; all premises must be true in order to prove that the conclusion is true

    - 0 premises means it's always true, i.e., an axiom

- a type system consists of a set of inference rules that can refer to each other recursively, along with at least one non-recursive rule as a base case

- EXAMPLE

```
Γ(x) = τ
---------- [VAR]
Γ ⊢ x : τ

Γ ⊢ e : ref τ
------------- [REF]
Γ ⊢ *e : τ

Γ ⊢ e1 : int   Γ ⊢ e2 : int
--------------------------- [ADD]
Γ ⊢ e1 + e2 : int
```

- from these example rules we can now prove that `x:int, y:ref int ⊢ x + *y : int`

    - [draw expression as AST, type each node using the above rules]

- inference rules allow us to conclude typing judgements, and are the _only_ way to conclude typing judgements...if there is an expression s.t. no inference rule applies, then we cannot assign a type to that expression and it is a type error

- EXAMPLE: `x : int ⊢ *x` is ill-typed, as is `x:int, y:ref int ⊢ x + y`

- turning a type system into a type checker implementation can range from straightfoward to really complicated depending on the type system itself

    - in our case it will be straightforward

    - turn each inference rule into a separate function

    - each function takes an expression of the kind given in the conclusion of the rule and returns the type of that expression

    - if the rule has premises, the function makes calls to other functions passing the context and appropriate sub-expression as arguments

    - if there is no applicable rule then we return a type error

- EXAMPLE

```
fn type(Γ : Context, e : Exp) -> Type {
    if e is a variable then var(Γ, e)
    else if e is a dereference then ref(Γ, e)
    else if e is an addition then add(Γ, e)
}

fn var(Γ : Context, x : Variable) -> Type {
    if Γ contains x then Γ(x) 
    else fail
}

fn ref(Γ : Context, e : Exp) -> Type {
    if type(Γ, e) = ref τ then τ
    else fail
}

fn add(Γ : Context, e1 + e2 : Exp) -> Type {
    if type(Γ, e1) = int and type(Γ, e2) = int then int
    else fail
}
```

- how do we come up with a type system for a language?

    - it is language-dependent (the examples above are for a made-up language)

    - the language designer must decide what types they want in the language based on the values and operators present in the language

    - they must use the semantics of the language (i.e., how programs behave when they are executed) in order to create the appropriate inference rules

    - ideally they would prove that their type system is _sound_, i.e., a well-typed program should always behave meaningfully wrt its semantics

        - this doesn't mean that a well-typed program is always correct wrt what the programmer intended, just that whatever it does is always well-defined according to the language semantics

        - if `x` and `y` are int and i write `x < y` when i should have used `x <= y`, that is not a type error even if it is a logic error, because `<` is well-defined for integers

        - if i write `x(42)` when `x` is an int instead of a function then it should be a type error

        - that is, a sound type system should catch all bugs that count as errors across all programs, no matter what they're trying to do

## categorizing type systems

- type systems can be classified along a number of dimensions; we'll list some of the important ones and discuss their pros and cons

- i have my own biases, but the point isn't to convince you which choices are the right ones, it's to give you a foundation so that you can decide for yourselves

### static vs dynamic

- a language's type system can be _static_ or _dynamic_

    - static: types are checked by the compiler
    - dynamic: types are checked at runtime

- benefits of static

    - static type systems can guarantee the absence of entire classes of runtime errors

    - potential errors are detected at compile time instead of lurking in production code waiting to be triggered by a user

    - type annotations can serve as useful code documentation (that is automatically checked by the compiler so can't go stale)

    - static type information allows the compiler to do a much better job optimizing the code and results in much faster executables

- drawbacks of static:

    - determining exact program behavior is undecidable; in order to guarantee that programs don't behave badly, we must necessarily flag some programs that _wouldn't_ behave badly (example: `let x:int; if false { x = *x}`)

        - we can make type systems more powerful and expressive to recognize more programs as correct, but type checking becomes more expensive and complicated; we can easily move into the realm of undecidable type checking

    - advanced type systems can also be complicated for programmers to use and require lots of expertise

- benefits of dynamic:

    - faster, more flexible development because the programmer doesn't need to satisfy the type checker

    - easier for the language designer because they don't need to create a type system or implement a type checker

- drawbacks of dynamic:

    - big performance penalty; programs in dynamic languages are generally much slower than comparable programs in static languages

    - errors don't show up until runtime (e.g., after the program has been put in production and is being used by customers)

        - we can mitigate this somewhat with extensive testing, but static type systems catch _all_ typing errors and testing can only catch a finite set of typing errors (those covered by the tests)

        - as an anecdote, my grading script for assign-1, written in python, had a bug in it for exactly this reason: i forgot to qualify the name of a function appropriately and it wasn't caught until the autograder ran the script when grading a student's submission

### explicit vs implicit

- a static type system can be _explicit_ or _implicit_

    - explicit: type annotations are part of the language syntax
    - implicit: type annotations are optional

- EXAMPLE

```
fn mul(x: int) -> int { return x * x; }

VS

fn mul(x) { return x * x; }
```

- some people conflate static typing with explicit typing because that's all they're familiar with, but it isn't true

- languages with implicit types allow the programmer to omit type annotations, so the program looks just like a dynamically-typed one; the compiler must then _infer_ what the types should be in order to perform type checking

    - obviously the more expressive the type system the more difficult and expensive this process is

    - however, it also really helps with the complexity of advanced type systems

- the benefit and drawback of explicit type systems is that the programmer writes the types so the types are immediately evident, adding documentation and making it easier to understand

- the benefit and drawback of implicit type systems is that the programmer does _not_ write the types so the types are _not_ evident, lacking documentation and are harder to understand

    - however, this is mitigated by modern IDEs which can do type inference and automatically add the types to the code's display

- a number of modern languages take a mid-point, where the programmer must annotate _some_ type information (e.g., the types of function parameters and function return values) but the rest can be inferred by the compiler

    - c++ added the `auto` keyword that instructs the compiler to infer an appropriate type, which is really useful for complicated types with lots of templating (though it's rather rudimentary compared to other implicitly-typed languages)

### strong vs weak

- a type system can be _strong_ or _weak_

    - strong: provides guarantees (a well-typed program "can't go wrong", i.e., try to execute nonsensical operations)
    - weak: no guarantees (a well-typed program _can_ "go wrong")

- in other words, a weak type system can "lie to you", telling you that the program is well-typed (and so anything it does should make sense) but in reality the program execution could try to do nonsensical things

    - C and C++ are the most well-known and widely-used languages with weak type systems

    - not surprisingly, they are also the languages that cause the most problems with bugs and security vulnerabilities

- the terms "strong" and "weak" are a bit controversial, and some people use them to mean different things...when discussing them with others, it's good to make sure everyone is using the terms the same way or it will get really confusing

- strong guarantees seem obviously preferable to weak guarantees; why would we design a language with a weak type system?

    - the essential reason is _performance_, which comes up in a number of ways
    
    - an important example is dealing with _memory_

        - garbage collection requires lots of runtime resources, causes unpredictable performance, and requires high memory usage (all of this was especially true back when C and C++ were being developed; it's less true today but still true)

            - until recently, all strongly-typed languages had to use garbage collection

        - if we don't want that overhead, then (at least until recently) the only alternative was to allow the programmer to manage memory manually (e.g., `free`/`delete`)

            - but programmers often get manual memory management wrong, and that leads to memory unsafety (e.g., use-after-free errors) and that memory unsafety violates the guarantees provided by the type system

            - these errors also lead to lots of security vulnerabilities

            - allowing memory safety errors automatically leads to weak type systems, because those errors can be used to execute any operation we want regardless of what the type system says

        - the motivation behind the Rust programming language was to allow low-level systems programming _without_ garbage collection

            - its designers were basically fed up with c++ and wanted a safe alternative

            - the key thing that makes this possible is a _linear type system_, which is based on _linear logic_; both ideas have been in academia for decades

            - Rust is the first mainstream language to adopt the ideas and put them into practice in a production-quality language

            - linear types allow the compiler to track memory ownership and, essentially, automatically insert `free`/`delete` where necessary in a way that is guaranteed to be correct

    - it isn't _just_ memory, for example another performance-based example is signed integer overflow, which is undefined in C/C++

        - constantly checking every arithmetic operation would be expensive, so we don't bother and leave it up to the programmer to prevent overflow

    - C and C++ have literally hundreds of different kinds of undefined behaviors given in the language specs...do you know them all?

    - why use a static type system at all if it is lying to you? again because of performance: having static types lets the compiler do a much better job of optimizing code

- technically a dynamically-typed programming language _could_ use a weak type system, but there isn't much point---dynamically there is enough information that being weak would be a weird choice

    - mostly we talk about static type systems being strong or weak

## the cflat type system

- cflat's type system has a simple, static, explicit, strong type system; what does this mean?

    - _simple_: no polymorphism, dependent types, substructural types, etc
    - _static_: types are checked at compile-time
    - _explicit_: the programmer explicitly annotates type information in the code
    - _strong_: a well-typed program cannot perform invalid operations

- [go over `cflat-docs/validation.pdf`]

    - rule ARRAY: we don't check that the index is within bounds; this will be a dynamic check that we add during codegen

    - rule FIELD: notice that this implies that accessing a struct pointer automatically dereferences it, like java and unlike c++

- EXAMPLE [see OneNote]

```
struct foo { f1: [int] }

fn main() -> int {
    let a:&foo;
    a = new foo;
    a.f1 = [int; 1];
    return *bar(a.f1[0]);
}

fn bar(p: int) -> &int {
    let q: &int;
    q = new int;
    if p < 10 { *q = p; return q; } 
    else { return nil; }
}
```

```
Program
- structs = { Struct("foo", { Decl("f1", Array(Int)) }) }
- externs = {}
- functions = {
    Function(
        name = "main"
        params = []
        rettyp = Int
        locals = { Decl("a", Ptr(Struct("foo"))) }
        stmts = [
            Assign(
                Id("a"), 
                NewSingle(Struct("foo"))
            ),
            Assign(
                FieldAccess(Id("a"), "f1"), 
                NewArray(Int, Num(1))
            ),
            Return(
                Deref(
                    FunCall(
                        Id("bar"),
                        ArrayAccess(
                            FieldAccess(Id("a"), "f1"),
                            Num(0)
                        )
                    )
                )
            )
        ]
    ),
    Function(
        name = "bar"
        params = [ Decl("p", Int) ]
        rettyp = Ptr(Int)
        locals = { Decl("q", Ptr(Int)) }
        stmts = [
            Assign(
                Id("q"), 
                NewSingle(Int)
            ),
            If(
                BinOp(Lt, Id("p"), Num(10)),
                [
                    Assign(
                        Deref(Id("q")),
                        Id("p")
                    ),
                    Return(Id("q"))
                ],
                [Return(Nil)]
            )
        ]
    )
}
```

# front-end recap

- let's review the steps we needed to go through to define our compiler front-end

- start with a grammar for the concrete syntax of the language

    - determine tokens and their regex descriptions

    - define token data structure

    - translate token descriptions to NFA and use maximal munch and prioritization to remove any ambiguity

    - implement lexer to translate from source code to token stream

    - check if grammar is LL(1), if not:

        - decide on precedence levels and factor grammar to enforce them

        - eliminate any left recursion

        - check if grammar is predictive, apply left-factoring if necessary
        
        - if grammar is still not LL(1), we need to change the concrete syntax

    - define the AST data structure

    - translate grammar to recursive descent parser

    - instrument parser functions to build AST

    - validate AST

- i want to emphasize that we've simplified some issues that happen in real-world languages like C, C++, Java, etc

    - these often require some creative thinking to get them to fit into the frontend framework we've covered here, mostly because of ambiguity in the lexemes and/or grammar or because lexemes aren't strictly regular