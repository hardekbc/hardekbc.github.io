# Fall 2025 notes

## TODO:

- at end of quarter, send out AI survey form (create gradescope assignment for them to submit it)

## lecture timing

- week 0.2 (quarter starts on thursday): up to (not including) 'compiler overview::things we're leaving out'
- week 1.1: through end of 'lexing'
- week 1.2: up to (not including) 'parsing::recursive descent and LL(1)::LL(1) recursive descent'
- week 2.1: up to (not including) 'parsing::building the AST'
- week 2.2: through end of 'parsing'
- week 3.1: up to (not including) 'validation::categorizing type systems'
- week 3.2: just the very beginning of 'validation::the cflat type system'
- week 4.1: through end of 'front-end recap'
- week 4.2: up to (not including) lowering expressions in `lower.md`
- week 5.1: through 'codegen::necessary context::linker'
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

## assignment timing

- week 2.1 assign-1 (lexing and parsing) -- 14 days
- week 4.1 assign-2 (type checking)      -- 14 days
- week 6.1 assign-3 (lowering)           -- 9 days
- week 7.2 assign-4 (codegen)            -- 13 days
- week 9.1/2 assign-5 (gc)               -- 14 days

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

- this quarter i'm replacing the lir optimization assignment with a garbage collection assignment, and so moved the lecure material around accordingly (still covering the material, but later and less in-depth from last time in spring'24)

- gradescope is truncating some of the test cases in its output when a student's submission is incorrect...not at the end, but in the middle (it says "truncating" somewhere in the output). it isn't clear what the character limit is that will cause this to happen; i need to investigate and try to figure out a way to get the student the failing test case without truncation.

    - i'm going to try an experiment: generate an equivalent test suite to give to the students, distinct from the one used on gradescope, and stop having gradescope reveal the test case. the idea is that if they can pass the secondary test suite, they should be able to pass the gradescope one (maybe there will be a handful of cases where we need to step in when they pass the secondary suite but still have trouble with the gradescope suite). we'll see how it goes.

- assign-1

    - copy-pasting the failed program from the grader output doesn't copy the special characters (e.g., `\r`, `\t`, etc); the students need to be aware of this possibility

    - we give the students the LL(1) version of the grammar so they never actually have to use the grammar refactoring rules; i think this is fair given all the other stuff they have to do, but maybe think about giving quizzes (graded or not) where they have to refactor some simple grammars

    - since test cases are generated as ASTs and then printed out as source code, the test cases have more regular syntax than the grammar allows---for example, toplevel constructs will always be in the same order, and every function will have at most one let; to allow for testing the student parsers more thoroughly it would be good to create a printer that randomizes these things

        - for now i've decided that this isn't worth fixing; maybe later if i have time

    - the lexing and parsing is actually pretty fast, i could easily add more test cases to each test suite

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

# lexing [~1 lecture]

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

# parsing [~3 lectures]
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

# validation [~3 lectures]
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

- one thing to be clear on: programmers need to think about types either way. when the programmer is doing something to a variable, they need to have an idea of what kind of value it contains and what operations can be done to it, i.e., its type.

    - in static typing those types are written down and checked by the compiler
    - in dynamic typing those types are just in the programmer's head

- benefits of static

    - static type systems can guarantee the absence of entire classes of runtime errors

    - potential errors are detected at compile time instead of lurking in production code waiting to be triggered by a user

    - type annotations can serve as useful code documentation (that is automatically checked by the compiler so can't go stale)

    - static typing makes automatic refactoring more effective

    - static type information allows the compiler to do a much better job optimizing the code and results in much faster executables

- drawbacks of static:

    - determining exact program behavior is undecidable; in order to guarantee that programs don't behave badly, we must necessarily flag some programs that _wouldn't_ behave badly (example: `let x:int; if false { x = *x}`)

        - we can make type systems more powerful and expressive to recognize more programs as correct, but type checking becomes more expensive and complicated; we can easily move into the realm of undecidable type checking

    - advanced type systems can also be complicated for programmers to use and require lots of expertise

    - things break down at the boundaries of your system, e.g., when you're reading in data from elsewhere (files, network, standard in, etc); since that 'elsewhere' doesn't have the same type system you need to check for validity at runtime (which often involves parsing plus other checks)

- benefits of dynamic:

    - faster, more flexible development because the programmer doesn't need to satisfy the type checker

    - easier for the language designer because they don't need to create a type system or implement a type checker

- drawbacks of dynamic:

    - big performance penalty; programs in dynamic languages are generally much slower than comparable programs in static languages

    - refactoring is more difficult

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

# lowering [~2 lectures]

- now that we have a valid AST we could directly generate ISA code from it, but most modern compilers don't do that---instead, they _lower_ the AST into a simpler, lower-level intermediate representation (LIR)

    - LIR is essentially ISA-agnostic assembly, which is particulary useful if the compiler has multiple backends---a lot of compilation work can be implemented once, for LIR, and then we only need to do individual codegen for each backend from the LIR

    - LIR makes the code simpler and more explicit (e.g., the order of evaluation and the flow of control); the key thing is that each instruction does just one thing

    - LIR is better for optimization because it's easier to reason about and transform

- there are various forms of IR, but a common one (and the one we'll use) is _three-address code_ IR

    - no instruction (well, except for calls) will have more than three "addresses" (think "variables/constants")

- example of how LIR simplifies the AST code and makes evaluation order explicit (using more human-readable notation)

    - instead of a deeply-nested tree of expressions, we end up with a flat sequence of simple instructions

    - [see OneNote]

```
[source]
x = foo(y+z+1, z*3).fld

[AST]
Assign(
    Id("x"), 
    FieldAccess(
        Call(
            Id("foo"),
            [
                Add(Add(Id("y"), Id("z")), Num(1)),
                Mul(Id("z"), Num(3))
            ]
        ),
        "fld"
    )
)

[LIR]
_const_1 = $const 1
_const_3 = $const 3
_tmp1 = $arith mul z _const_3
_tmp2 = $arith add y z
_tmp3 = $arith add _tmp2 _const_1
_tmp4 = $call foo(_tmp3, _tmp1)
_tmp5 = $gfp _tmp4 st::fld
x = $load _tmp5
```

- our LIR is in the form of a _control-flow graph_ (CFG)

    - the abbreviation is an unfortunate pun, this is _not_ "CFG" meaning "context-free grammar"

    - a CFG is an explicit representation of the possible execution paths in a program---that is, when a program is executed, what are the possible sequences of instructions it may execute?

    - a CFG is composed of nodes called _basic blocks_ (a labeled sequence of instructions) connected by edges representing control-flow between blocks

    - a basic block is a linear sequence of instructions s.t. (1) it can only be entered at the beginning, and (2) it can only be left at the end

        - i.e., there is no way to jump into or out of the middle of a basic block: you have to enter at the first instruction and can only leave after the last instruction

        - every basic block has a label, which is used to target that basic block when transferring control from some other basic block

        - every basic block ends in a _terminal_ instruction that says what basic block to go to next

    - every function has a single _entry_ basic block; we always start executing the function at the entry block

- [introduce LIR data structure] [see OneNote]

    - [explain the LIR instructions]

    - i separate Inst from Terminal to make clear that only certain things can go at the end of basic blocks; your implementation doesn't need to differentiate

- example 1 (draw graph) [see OneNote]

```
[source]
fn main() -> int {
  let x:int, y:int, z:int;
  if x != 0 {
    x = 3; y = 2;
  } else {
    x = 2; y = 3;
  }
  z = x + y;
  return z;
}

[AST]
Function(
    name = "main",
    params = [],
    rettyp = Int,
    locals = { Decl("x", Int), Decl("y", Int), Decl("z", Int) },
    stmts = [
        If(
            BinOp(NotEq, Id("x"), Num(0)),
            [
                Assign(Id("x"), Num(3)),
                Assign(Id("y"), Num(2)),
            ],
            [
                Assign(Id("x"), Num(2)),
                Assign(Id("y"), Num(3)),
            ]
        ),
        Assign(Id("z"), Add(Id("x"), Id("y"))),
        Return(Id("z"))
    ]
)

[LIR]
fn main() -> int {
let _const_0:int, _const_2:int, _const_3:int, _tmp1:int, x:int, y:int, z:int

entry:
  _const_0 = $const 0
  _const_2 = $const 2
  _const_3 = $const 3
  _tmp1 = $cmp ne x _const_0
  $branch _tmp1 tt ff

tt:
  x = $copy _const_3
  y = $copy _const_2
  $jump exit

ff:
  x = $copy _const_2
  y = $copy _const_3
  $jump exit

exit:
  z = $arith add x y
  $ret z
}
```

- example 2 (draw graph) [see OneNote]

```
[source]
let x:int;
while x != 10 { x = x + 1; }
return x;

[AST]
locals: { Decl("x", Int) }
stmts: [
    While(
        BinOp(NotEq, Id("x"), Num(10)),
        [
            Assign(Id("x"), Add(Id("x"), Num(1)))
        ]
    ),
    Return(Id("x"))
]

[LIR]
let _const_0:int, _const_1:int, _tmp1:int, x:int

entry:
  _const_1 = $const 1
  _const_10 = $const 10
  $jump while_hdr

while_hdr:
  _tmp1 = $cmp ne x _const_10
  $branch _tmp1 while_body exit

while_body:
  x = $arith add x _const_1
  $jump while_hdr

exit:
  $ret x
```

- our task is to translate valid programs from the AST data structure to the LIR data structure; this is called _lowering_

- [go over lowering process] [see `docs/lowering.pdf`]

    - we don't need to worry about producing the best possible (most efficient) code; we can clean it up later using various optimizations on the LIR

- example of lowering process [see OneNote]

    - [go through lowering process using translation vector]

```
[source]
let x:int, y:int, z:int;
x = input();
z = 42;
while z != 0 {
  if y <= x { y = (y + 2) * x; }
  else { y = x; }
  z = x / y;
}
return y;

[AST]
locals: { Decl("y", Int), Decl("y", Int), Decl("z", Int) }
stmts: [
    Assign(Id("x"), Call(Id("input"), [])),
    Assign(Id("z"), Num(42)),
    While(
        BinOp(NotEq, Id("z"), Num(0)),
        [
            If(
                BinOp(Lte, Id("y"), Id("x")),
                [ Assign(Id("y"), Mul(Add(Id("y"), Num(2)), Id("x"))) ],
                [ Assign(Id("y"), Id("x")) ]
            ),
            Assign(Id("z"), Div(Id("x"), Id("y"))),
        ]
    ),
    Return(Id("y"))
]
```

# codegen [~4 lectures]
## intro

- once we have lowered to LIR, we're in the middle-end where we would perform optimizations; however for now we're going straight to the backend in order to get a working compiler as quickly as possible

- going from LIR to a specific ISA is called _codegen_

    - for us, the ISA will be x86-64

    - i am not going to teach you x86-64; i'll explain what the code needs to do and, for the assignment, i'll give a list of useful x86-64 instructions, a pointer to some relevant resources, and my own binary solution that you can use to figure out what to emit

## necessary context
### linker

- typically programs are divided into multiple _compilation units_ (e.g., for C/C++ a file is a compilation unit); each compilation unit results in a binary _object file_, then all the object files are linked together into an _executable_

    - source -[parser]-> compiler -[codegen]-> assembly -[assembler]-> object file

    - { object files... } -[linker]-> executable

- [demo using the code in `examples/linking/`]

```
cat main.c
cat foo.c

gcc -c -S foo.c // emit assembly into foo.s
ls
cat foo.s

gcc -c foo.c // call assembler to compile into object file
ls
objdump -d foo.o

gcc -c -S main.c // emit assembly into main.s
ls
cat main.s

gcc -c main.c // call assembler to compile into object file
ls
objdump -d main.o

gcc main.o foo.o
ls
objdump -d a.out

./a.out
echo $?
```

- notice how the call to `foo` inside of `main` changed after linking

- the compiler proper is the part that's emitting assembly; the assembler produces object files and the linker produces an executable

- note that `main` is not actually the starting point for execution, just the starting point for user code---really we begin in `_start` which does setup and then calls into `main`

### static vs dynamic libraries

- even after linking we still may not have all the code needed to execute a program; it depends on whether we're using _static linking_ or _dynamic linking_ for libraries

    - static libraries have a `.a` extension (for "archive"); they are linked into the executable by the linker just like any other object file

    - dynamic libraries have a `.so` extension (for "shared object"); they are _not_ linked into the executable during compilation---they are linked in as the executable is being loaded into memory when it is executed (each time it is executed)

- there are pros and cons to each approach, and both are widely used

    - static linking cons: larger executables; redundant copies in memory (across different executables)

    - static linking pros: link-time optimizers can optimize the code; guaranteed to have the right version of the library when executing

    - dynamic linking cons: invisible to compiler so can't be optimized; version available when executing may not match the version used when program was developed

    - dynamic linking pros: smaller executable size; a single copy can be shared by multiple programs executing at once

- we'll be using static linking for our compiler; you don't really need to know all this for writing the compiler but it's good to be aware of it in general

### process layout in memory

- when a binary is executed, the OS first loads it into memory (calling the dynamic linker if necessary) and then actually starts executing it

    - the thing that loads an executable into memory is called a `loader`

- it will be useful to know how the process is laid out in memory while it's executing

    - this is virtual memory, not physical memory; if you've taken OS then you know the difference

    - if you haven't taken OS, the important thing is that for our purposes you can think of process memory as just one big array

    - the specifics can vary depending on OS, architecture; this is a fairly generic description that is "close enough" to most versions

```
(address 0) [ code segment | static segment | data segment ] (highest address)
```

- the `code segment` (aka `text segment`) is where the program instructions are located

    - there is an architectural register called `pc` for "program counter", whose contents are the location of the next instruction to be executed (which will be in the code segment)

- the `static segment` is where globals and constant strings are located; it's "static" because their locations can be determined ahead of time during compilation

    - whenever you have a string literal in your code, like "blah", the compiler puts it into the static segment

    - a pointer to a global or constant string is a pointer into the static segment

- the `data segment` is where all of the stack and heap data lives; it's laid out as:

```
(low address) [heap --> | <-- stack] (highest address)
```

- the heap is where memory is dynamically allocated from (e.g., `new` or `malloc`); it grows from low addresses to high addresses

    - the actual mechanics of how the heap is managed are handled by the language runtime; e.g., for C it's handled by the standard C library (though you can override this if you want)

- the stack is for function calls; each call will push a "stack frame" onto the stack (so each instance will have its own stack frame) and returning from a call will pop the top stack frame off the stack

    - this is where a function's parameters and local variables are stored; they live inside the stack frame of a particular function instance

    - note that the stack grows "downward", from high addresses to low addresses; this is often true but not always---there are examples where it grows upwards, and even where it goes in a circle

    - there are two important values wrt to the stack:

        - the `stack pointer` points to the current top of the stack

        - the `frame pointer` points to the base of the current stack frame

    - stack manipulation is handled by the compiler; we'll need to insert instructions to make this happen properly

### alignment and padding

- `alignment` is talking about what addresses a piece of data in memory is allowed to start at; this is determined by the architecture we're compiling for

    - e.g., a `word aligned` architecture means that all values must begin on a word boundary (for whatever size a word is in that architecture)

- when the compiler assigns memory locations to variables, it needs to respect the requirements of whatever architecture it's targeting

- this is particularly important for aggregate data like structs or arrays; we have to make sure that proper alignment is respected

```
struct foo { char a; int b; short c; long long int d; };

assume:
-      char:8
-     short:16
-       int:32
- long long:64

example alignment requirements:
- chars are byte-aligned
- shorts and ints are word-aligned (where a word == 32 bits)
- long longs are double-word aligned
```

```
-- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
a  #  #  #  b  b  b  b  c  c  #  #  #  #  #  #  d  d  d  d  d  d  d  d
-- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
```

- note that we had to insert padding between some of the fields to make alignment work; this is why it's bad practice when c/c++ programmers try to manually index into a struct like:

```
foo x{8,16,32,64}; 
char y = **((char**)&x + 1);
```

- this code tries to read the first byte of `x.b`, but it's actually getting the padding between `x.a` and `x.b` that was inserted by the compiler, which is garbage

- C/C++ require the layout of the fields of a struct to be in the same order as they were declared, but other languages allow them to be in any order (which can help with alignment)

    - cflat will allow fields to be in a different order than they were declared

- x86-64 is mostly 64-bit word aligned (with an exception we'll talk about later), and conveniently i've defined cflat so that all data is a multiple of a word, so most of the time we won't need to worry about alignment (except in one place)

### garbage collection

- the garbage collector (GC) will execute at runtime to manage memory, but the compiler needs to supply the GC with the information it needs to do its job

- we'll go over how GC actually works after we go through codegen, but during codegen we need to handle the following:

    - allow the GC to find all pointers on the stack; since the stack is just a sequence of 64-bit words that means we need a convention that the compiler and GC agree on to determine which words are pointers

    - allow the GC to determine for heap-allocated objects whether they are arrays or singletons

        - for arrays it needs to know how many elements the array has and whether the array elements are pointers

        - for singletons it needs to know how many words it is and which words are pointers

- we'll go over the conventions that our compiler will use at the appropriate places during codegen

## stage 1: no pointers/arrays, structs, or functions other than main
### intro

- a LIR::Program that contains only the `main` function, with no structs or externs; no variables have a pointer or array type

- relevant LIR instructions: `$arith`, `$cmp`, `$const`, `$copy`, `$branch`, `$jump`, `$ret`

- we'll see at this stage that variables are only an illusion of the programming language---they don't exist in machine code

    - a program has variables, machine code has registers and memory locations

- i'm not going to give you an exact set of instructions for emitting x86-64 assembly; i'll explain what the code needs to do and then you'll have to do some exploration to figure out the exact x86-64 details

    - i will give you some useful information and my own binary solution, so as long as you understand what the code is _supposed_ to do, it shouldn't be hard to figure out how to do it from those

    - [maybe spend some time showing how to use my solution to figure out what to generate, once i've shown the example]

### example

- example: [see `examples/codegen/stage-1.{lir, s}`] [see OneNote]

- the asm won't make much sense right now; we'll come back after going through the codegen info and look at it again to go through how it implements what we just talked about

### codegen summary

1. create standard assembly program template (everything except `main` itself) 

    - for programs in stage 1 it will always be the same; the only addition later will be adding the global function pointer info

    - [point it out in the example above]

2. create the prologue for `main`: setting things up as we enter the function

    - this version is specifically for `main`, we'll tweak it a bit when we add other functions later

3. output the ISA instructions for each LIR basic block

4. create the epilogue for `main`: tearing things down as we leave the function

### context

- `main` was called from `_start`, which called it by pushing the return address on the stack and then jumping to the `main` label

- `main` has no parameters and returns an `int`

- 2 important registers:

    - `FP` holds the _frame pointer_ value, which points to the bottom of the current function's stack frame (i.e., the return address for the caller)

    - `SP` holds the _stack pointer_ value, which points to the current top of the stack (that is, whatever the topmost value on the stack is; `SP - 1` is undefined memory...remember that the stack grows _down_)

- we (the compiler writers) are responsible for managing `FP` and `SP` appropriately

### the function prologue

- `FP` is still pointing to the bottom of the stack frame for `_start`, but we're creating a new stack frame and need to set `FP` accordingly

- `SP` is the current top of the stack, and so will be the bottom of the new stack frame we're creating

- our new stack frame needs (1) a word containing the number of local variables that are pointers (for GC, will be 0 for now since we aren't using pointers yet); and (2) space for the local variables where the values of those locals will live

- remember that when `main` returns we need to restore the stack back to what it was for its caller

- [show a diagram of what we want the stack to look like after the prologue]

```
        | .                     |
        | .                     |
        | .                     |
        +-----------------------+
        | old fp                | <- fp
        +-----------------------+
        | gc info: num ptrs (0) |
        +-----------------------+
        | locals                |
        | .                     |
        | .                     |
        | .                     | <- sp
        +-----------------------+
```

- so when creating the prologue we should:

    1. emit the `main` label so `_start` has somewhere to jump

    2. emit instructions to:
    
        - push `FP` onto the stack to save its old value

        - set `FP` to the current value of `SP` (so `FP` is now saving the old value of `SP`)

        - decrement `SP` to add space for the GC word and local variables (remember the stack grows _down_, that's why we decrement)

            - we'll need to remember the stack location for each local, so we'll have to create a map that the rest of codegen for `main` can use

            - remember that the stack grows _down_: the offset will be negative

            - anytime a LIR instruction mentions a variable, the mapping will tell us where in memory that variable's value is relative to `FP` (with the stack location at `FP - (2 * <wordsize>)` being the first local, because `FP - <wordsize>` is the GC info word)

            - in the rest of these notes, for variable `x` we'll use `[x]` to mean its memory location on the stack and `*[x]` to mean the value at that memory location

        - zero initialize all local variable values (unlike C/C++, cflat defines all variable to start as 0)

            - the cflat runtime library supplies a function `_cflat_zero_words(start, num_words)` that we can call to do this (note that _cflat_zero_words starts at a lower address and goes up, so `start` should be the end of the locals on the stack)

        - call `_cflat_init_gc()` to set up the GC runtime (this is only for `main`, no other function will have this call)

        - jump to `main_entry` (to keep labels globally unique, we'll always prepend the function's name)

- there's one bit of complexity with allocating stack space: for x86-64 the stack pointer _must_ be double-word aligned (i.e., always pointing to a multiple of 16 bytes)

    - we can assume that before `main` was called the stack was correctly aligned

    - the caller pushed the return address and we pushed `FP`, keeping it aligned properly

    - then we added space for the GC word and locals, which are all 1 word

    - if that was an odd number then `SP` is no longer correctly aligned

    - FIX: when adding space for GC word + locals, if there are an odd number pretend that there's one extra (it will just be space on the stack at the end of the locals block that we'll never use)

- this is a simplified function prologue because there are no parameters or pointers to deal with...later we'll go over a more complete version that always works

### the function epilogue

- we need to reset the stack to the way it was before entering this function (this will be the same for all functions)

- emit the `main_epilogue` label

- emit instructions to:

    1. restore the previous value of `SP` by setting it to `FP` (which the prologue set to the old value of `SP`)

    2. restore the previous value of `FP` by popping it off the stack (where the prologue stored it)

    3. pop the return address from the stack (put there by the caller) and jump to it (conveniently, there's a single x86 instruction that does this)

- note that restoring the previous value of `SP` implicitly pops the current stack frame off of the stack...everything in that old stack frame is now garbage

### translating LIR basic blocks to ISA basic blocks

- for each basic block, we should emit the block's label (prepended with `main_`) and then emit ISA instructions for each LIR instruction

- `x = $const <num>`

    - `*[x]` = `<num>`

- `x = $copy y`

    - `*[x]` = `*[y]`

- `x = $arith <aop> y z`

    - `*[x]` = `*[y] <aop> *[y]`

    - if `aop` is Add, Sub, or Mul then we can just use the appropriate x64 instruction

    - if `aop` is Div then things get more complicated

        - see the x86-64 info for how division works

        - instead of getting a hardware error on division by 0, cflat will say that anything divided by 0 is 0; we could do this in several ways but we'll do it by transforming anything like `x/0` into `0/1` using conditional moves (see the x86-64 info and my `cflat` implementation)

- `x = $cmp <rop> y z`

    - compare `*[y]` and `*[z]`, setting a condition code

    - store 0 or 1 to `*[x]` depending on `<rop>` and the condition code that was set

- `$jump lbl`

    - jump to `main_<lbl>`

- `$branch x lbl1 lbl2`

    - compare `*[x]` to 0

    - if not equal, jump to `main_<lbl1>`, else, jump to `main_<lbl2>`

- `$ret x`

    - by convention, the return value goes in a special register (`%rax` for x86-64); the caller knows to look for the return value there

    - put `*[x]` into `%rax`

    - jump to `main_epilogue`

### review example

- show the example again and point out how it was generated according to these rules

## stage 2: pointers, arrays, structs
### intro

- now we can have memory allocation (singletons or arrays); note that for the purposes of codegen array-typed variables are just pointers, so we'll say "pointers" but mean "pointers or arrays"

- same function prologue for `main` as before, but the GC info (i.e., number of pointers) will be non-zero _and_ when mapping locals to stack offsets, we need to be sure all pointer-type locals come immediately after the GC info word so that the GC can find them

- note that since we can't allocate structs on the stack, all variables on the stack take 1 word

```
        | .                     |
        | .                     |
        | .                     |
        +-----------------------+
        | old fp                | <- fp
        +-----------------------+
        | gc info: num ptrs     |
        +-----------------------+
        | pointer-type locals   |
        | .                     |
        | .                     |
        | .                     |
        | non-pointer locals    |
        | .                     |
        | .                     |
        | .                     | <- sp
        +-----------------------+
```

- additional LIR instructions: `$load`, `$store`, `$alloc_single`, `$alloc_array`, `$gep`, `$gfp`

- unlike C/C++, cflat is a safe language; this means that we need to detect any array accesses that are out-of-bounds (as well as allocations for a non-positive amount of memory)

- strategy for allocation:

    - at runtime we need to know for any allocated piece of memory:

        - whether it is an array or not
        - if an array, how many elements it has and whether the elements are pointers or not
        - if not an array, how many words it is and whether each word is a pointer or not (we'll treat non-struct singletons like a 1-field struct)

    - we'll encode this information in a _header word_; all allocations must add this header word with the appropriate info, and all array accesses must check the header info to be sure the access is within bounds

        - the rest of the header info will be used by GC, we won't worry about the details for now

    - header word format (hdr[63] is the most significant bit; hdr[0] is the least significant bit):

        if allocated object is an array:
        - hdr[1] == true
        - hdr[2] == whether array elements are arrays/pointers
        - hdr[3:63] == number of elements in the array
  
        if allocated object is not an array (treat primitives as single-word structs):
        - hdr[1] == false
        - hdr[2:7] == number of fields (<= 56)
        - hdr[8:63] == whether corresponding field is an array/pointer

        hdr[0] is reserved for the runtime GC; it is always initialized to false
  
    - note that because arrays can't contain structs all array elements must be 1 word, so the array size is the number of words in the array

    - when something is allocated on the heap, we'll:
    
        - check that the allocation size is positive
        
        - allocate an additional word to serve as the header word (at the beginning of the allocation)

        - set the header word information

        - return the word immediately after the header word
        
        - `x $alloc_array y Int`: [header | 1st element | 2nd element | ... ]; we return a pointer to the 1st element

        - this means that for any _non-inner_ pointer we can access the header information by looking one word back from the pointer value

    - when we do pointer arithmetic using `$gep`, we'll check against the array size that's stored in its header and make sure that the result of the `$gep` is in-bounds

- what if the bounds-check fails?

    - we'll add some boilerplate ISA blocks to handle when there's a problem with allocation or indexing; they'll print an error message and immediately abort execution

    - `.invalid_alloc_length` is used when we try to allocate a non-positive number of elements
    
    - `.out_of_bounds` is used when we try to index an array out of bounds

### translating instructions

- `x = $load y`

    - `*[x] = **[y]`

- `$store x y`

    - `**[x] = *[y]`

- `x = $alloc_single τ`

    - compute size of allocated object: <num fields> + 1 (for header word), counting non-struct types as a single-field struct

    - compute header word

    - use `_cflat_alloc(size)` to allocate memory, getting `adr`

        - NOTE: anything in a caller-save register may potentially be overwritten by the call to `_cflat_alloc`

    - store header word at `*adr`, set `*[x] = adr + WORDSIZE`

- `x = $alloc_array amt τ`

    - check `*[amt]` > 0, else jump to `.invalid_alloc_length`

    - check `*[amt] > maximum array size (as determined by header word format), else jump to `.invalid_alloc_length`

    - compute header word

    - use `_cflat_alloc(*[amt] + 1)` to allocate memory, getting `adr`

        - NOTE: anything in a caller-save register may potentially be overwritten by the call to `_cflat_alloc`

    - store header word at `*adr`, set `*[x] = adr + WORDSIZE`

- `x = $gep y idx true`

    - because we haven't done any optimizations, the `checked` field of the `$gep` instruction will always be true (i.e., we always insert a dynamic check that the result will be in-bounds)

    - check that `*[idx]` > 0, else jump to `.out_of_bounds`

    - retrieve the header word from `*[y] - WORDSIZE`

    - check that `*[idx]` < the array's size as stored in the header word (will require some bit shifting), else jump to `.out_of_bounds`

    - set `*[x] = *[y] + (*[idx] * WORDSIZE)`

- `x = $gfp y st fld`

    - in order to construct a pointer to a particular field of a struct, we have to know the offset of each field

        - alignment is a factor, but since all fields are one word in cflat we don't need to worry about that

        - C/C++ require that fields are laid out in the declared order, but cflat doesn't

        - we'll order the fields alphabetically by identifier

    - let `off` = the offset of `fld` in `st`

    - `*[x] = *[y] + off`

### example

- example: [see `examples/codegen/stage-2_{1, 2, 3}.{lir, s}`] [see OneNote]

## stage 3: functions and calls
### intro

- now we want to generate code for abitrary functions (including ones with parameters) and calls to those functions

- additional lir instruction: `$call`

- remember that the assembly doesn't have any inherent notion of functions or calls (the `call` and `ret` instructions are just shorthand for a series of lower-level instructions that push and pop from the stack before jumping to a label)

- to implement the function abstraction, the caller and callee have to agree on a protocol that determines where the call arguments are placed and where the return value is placed

    - this is called the _calling convention_

    - different languages/architectures will have different protocols

    - we'll use the same protocol as C on x64, allowing us to call C functions as externs

- now that we have additional functions beyond `main`, we also have to adjust our template to add global declarations for each function (see examples)

### general x86-64 calling convention

- we split the calling convention into the following pieces:

    - _pre-call_: setting things up in the caller to pass info to the callee

    - _function prologue_: setting things up in the callee to accept the info

    - _function epilogue_: tearing down the callee and getting back to the caller

    - _post-return_: tearing things down back in the caller and getting the return value

- we handle the pre-call and post-return when emitting code for a `$call` instruction, we handle the function prologue and function epilogue when emitting code for a function

- there are a single set of architectural registers for the target machine, and all functions must share those registers

    - this means that we need an agreement about how to avoid having one function clobber information being used by another function

    - _caller-save_ registers: the caller is responsible for saving the values in these registers (if it still needs them) before making a call; the callee can use them freely

    - _callee-save_ registers: the caller is free to assume that any values in these registers will remain untouched by the callee; the callee can use them but only if it saves their values and then restores them before returning

- to call a function:

    - save any used caller-save registers
    - place first 6 integer/pointer args in the argument-passing registers
    - place any remaining args on the stack (first arg topmost)
    - execute `call` (push instruction address on the stack and jump to label)
    - the return value will be in `%rax` (placed there by the callee)
    - pop any args put on the stack
    - restore any saved caller-save registers

- function prologue:

    - if using any callee-save registers, push them onto the stack
    - allocate space on stack for locals (remember that any arguments not passed in registers are on the stack at a positive offset from `%rbp`)

- function epilogue:

    - place return value in `%rax`
    - deallocate stack frame
    - restore callee-save registers
    - execute `ret` (pop instruction address from stack and jump to it)

- the stack pointer must be double-word aligned (i.e., `%rsp` % 16 = 0)immediately before a `call` and after a `ret`

### our specialized calling convention

- we will use the standard calling convention with a few tweaks to simplify things for our implementation (these tweaks still conform to the convention, so we can freely call external C functions that expect the regular convention)

- we won't be keeping values in registers, so we don't need to worry about saving/restoring callee-save and caller-save registers in general

    - there is one exception: calling into the runtime library (e.g., `_cflat_alloc`) may overwrite caller-save registers because they are implented as extern functions in C; assume that any value in a caller-save register before such a call has been overwritten

    - optimizing the code to use registers more efficiently would make codegen more difficult because we would need to worry about this for every call

- we'll modify the function prologue to _also_ allocate space on the callee stack frame for the arguments passed in registers, and write their values from registers to the stack in the function prologue

- in order to allow the GC to easily find/modify pointer-typed parameters, we'll also allocate space in the callee stack frame for pointer-typed parameters passed on the stack

- this means that the arguments passed in registers _and_ pointer-typed arguments passed on the stack will all end up with a negative offset from the frame pointer, like locals, while non-pointer arguments passed on the stack will have positive offsets from the frame pointer

- remember that the number of arguments passed in registers _and_ the number of pointer-typed arguments passed on the stack must be taken into account when adjusting the stack pointer to ensure it is double-word aligned

- the final stack layout:

```
        | .                        |
        | .                        |
        | .                        |
        +--------------------------+
        | old fp                   | <- fp
        +--------------------------+
        | gc info: num ptrs        |
        +--------------------------+
        | pointer-type params      |
        | (from regs and stack)    |
        | .                        |
        | .                        |
        | pointer-type locals      |
        | .                        |
        | .                        |
        | .                        |
        | non-pointer locals       |
        | .                        |
        | .                        |
        | .                        |
        | non-ptr params from regs |
        | .                        |
        | .                        |
        | .                        | <- sp
        +--------------------------+
```

### call instruction

- `[lhs =] $call f args`

    - let in_regs = first 6 args, let in_stack = remaining args

    - if |in_stack| is odd, adjust stack to preserve alignment

    - put in_regs args into argument-passing registers

    - push in_stack args onto stack (in reverse order)

    - if call is indirect (i.e., `f` is a local or parameter), emit an indirect call to the value of `*[f]`

    - else if call is direct, emit a direct call to `f`

    - if `lhs` is not None, store the value in `%rdx` to `*[lhs]`

    - if in_stack is not empty, pop args from stack (and remove additional word if one was added to preserve alignment)

### final function prologue

- remember the intended stack layout:

```
        | .                        |
        | .                        |
        | .                        |
        +--------------------------+
        | old fp                   | <- fp
        +--------------------------+
        | gc info: num ptrs        |
        +--------------------------+
        | pointer-type params      |
        | (from regs and stack)    |
        | .                        |
        | .                        |
        | pointer-type locals      |
        | .                        |
        | .                        |
        | .                        |
        | non-pointer locals       |
        | .                        |
        | .                        |
        | .                        |
        | non-ptr params from regs |
        | .                        |
        | .                        |
        | .                        | <- sp
        +--------------------------+
```

1. emit the function label; this is where callers will jump to

2. emit instructions to:

    - push `FP` onto the stack to save its old value

    - set `FP` to the current value of `SP` (so `FP` is now saving the old value of `SP`)

    - decrement `SP` to add space for the GC word, pointer-type parameters, and local variables (remember the stack grows _down_, that's why we decrement)

    - write the number of pointer-typed locals and params to the GC word location

    - copy pointer-typed arguments from registers and the stack to the space right after the GC word

    - copy non-pointer arguments from the stack to right after the locals

    - zero initialize all local variable values

    - jump to `<function>_entry`

### example

- example: [see `examples/codegen/stage-3_{1,2,3}.{lir, s}`] [see OneNote]

## x86-64 info

- [go over `x64-info.md`]

- [go over how to use `cflat` executable to go from source to lir to assembly so they can see what the grader expects for codegen]

# TODO: memory management [~2 lectures]
## intro

- programmers are presented with an illusion that the computer has an arbitrary amount of memory

- this is false; every computer has a fixed amount of memory and if a program uses too much it will crash

- the runtime has to keep track of what memory has been allocated and what memory is in the available pool

- when the program asks for memory the runtime has to pick some available memory to allocate and remove it from the available pool

- the program has to somehow return memory back to the available pool when it doesn't need it anymore

- there are two kinds of memory a programmer deals with:

    - stack memory: this is automatically handled by the compiler, allocating memory on function entry and reclaiming it on function exit

    - heap memory: this is the one we need figure out how to deal with

- first we'll discuss some possible data structures that the runtime can use to manage memory

- then we'll discuss different strategies for how the runtime can be made aware that previously allocated memory is now available

    - this is the hard part; it's easy to know when we need memory to be allocated, it's a lot harder to know when we don't need it anymore

## data structures
### intro

- the runtime has to keep track of allocated vs available memory and efficiently move memory back and forth between these sets

- there are a varity of data structures it can use for this purpose, with various tradeoffs

    - we'll look at some basic strategies

    - real-world strategies are highly tuned and optimized versions of these basic strategies

- this is independent of how the runtime knows when allocated memory becomes available again, which we'll discuss later

### freelist

- we can think of heap memory as one big array of bytes

    - for simplicity we'll use units of words instead of bytes, just to make examples easier

    - _contiguous_ words means next to each other in memory

- unallocated memory is organized as a linked list, where contiguous words are chunked together into blocks called "free blocks"

- each free block contains metadata: (1) the size of the free block, and (2) the address of the next free block

- we reserve a distinguished location that always serves as the head of the freelist (i.e., a pointer to the first free block)

- searching through the freelist is `O(n)`, where `n` is the number of free blocks

- splicing free blocks in or out of the freelist is `O(1)`

```
      0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
--------+---+---+---+---+---+---+---+---+---+
 [head] | [free block]
```

- allocating memory

    - given a request for `N` words of memory, search through the freelist to find a free block to satisfy the request

        - there are a number of possible strategies; one of the simplest is _first fit_, i.e., the first one big enough to satisfy the request
    
    - splice out the first `N` words of the free block (leaving any remaining memory on the freelist) and return the address of the allocated memory

- deallocating memory

    - goal: given the address of memory that was previously allocated, return it back to the freelist

    - in order to do so, we have to know how much memory was allocated (otherwise we don't know how much to put back)

    - this means that when we allocate the memory, we should allocate an extra header word to track the size (just like we did for codegen of cflat arrays)

    - given the address, we read the word immediately before that address to get the size and then splice the memory back into the freelist

- example 1 [show heap as array, with 10 words of memory]

```
// we start with 1 word in the header and a single 9 word free block
a = malloc(2) // allocate 3 words
b = malloc(2) // allocate 3 words
c = malloc(2) // allocate 3 words
free(a)       // return 3 words as a free block
free(c)       // return 3 words as a free block
a = malloc(4) // OOM due to fragmentation
```

- the biggest problem with freelists is _external fragmentation_

    - allocated and unallocated memory are interleaved, and there may not be any sufficiently large free blocks to satisfy a request even if there are sufficient free words in total

    - we can mitigate (but not solve) this problem using _coalescing_: periodically sweep the freelist to find free blocks that are next to each other and merge them into a single free block

    - note that we need to add more metadata marking whether a block of memory is free or allocated so that we can do this sweep

- example 2 [show heap as array, with 10 words of memory]

```
// we start with 1 word in the header and a single 9 word free block
a = malloc(2) // allocate 3 words
b = malloc(2) // allocate 3 words
free(a)       // return 3 words as a free block
free(b)       // return 3 words as a free block
// sweep to coalesce free blocks
c = malloc(4) // allocate 5 words
```

- note that we _cannot_ coalesce memory by moving allocated memory around, because this would invalidate the pointers being used by the executing program

    - e.g., in our first example we're stuck with an OOM error

### buddy system

- instead of a linked list, the heap is organized as a binary tree

    - the root node represents the entire heap

    - each child node represents half the memory of its parent

    - memory is always allocated in powers of 2

- we store the nodes of the binary tree in a set of lists, one for each power of 2 up to the size of the heap

    - each list contains the free nodes of that size

    - searching for a free block is now `O(lg n)` instead of `O(n)`

```
  .
  .
  .
 32 ↦ _
 64 ↦ _
128 ↦ _
256 ↦ _
512 ↦ address 0 (the entire heap)
```

- allocating memory

    - given a request for `N` words of memory, first we compute the next power of 2 greater than `N`; call this `M`

    - look in the list of free blocks of size `M`; if non-empty then take a node off the list and return its address (we're done)

    - otherwise, keep going up by powers of 2 until we find a non-empty list (if there is no such list we've run out of memory)

    - once we've found a large enough free block, break it down into powers of 2 until we have a block of size `M`, putting the remaining blocks on the appropriate lists

- deallocating memory

    - given a block of `N` words (which will be a power of 2), place it on the appropriate list

        - note that we need to know the size of the allocated memory, just as with freelists

    - now coalesce the heap: look at the sibling block of the newly freed block to see if it's free; if so then remove both blocks from that list and put the combined block in the next list up; repeat until no more memory can be coalesced

    - note that this coalescing strategy is much more efficient than for freelists; instead of a linear sweep over all memory we only need to try at most `O(lg n)` times

- example (assuming 512 words of memory)

```
// start with a single 512 word free block
a = malloc(35)   // [64] + 64 + 128 + 256
b = malloc(100)  // [64] + 64 + [128] + 256
c = malloc(60)   // [64] + [64] + [128] + 256
d = malloc(121)  // [64] + [64] + [128] + [128] + 128
free(c)          // [64] + 64 + [128] + [128] + 128
free(a)          // 128 + [128] + 128
free(b)          // 256 + [128] + 128
free(d)          // 512
```

- the buddy system doesn't suffer from external fragmentation like freelists, but it does suffer from _internal fragmentation_

    - allocating larger than necessary blocks, so some memory is allocated but never used

    - this is necessary because all memory must be allocated in powers of 2

    - this is the price for a more efficient data structure

### arenas

- _arena-based_ memory management is also called _region-based_ memory management

- at a macro-level it operates kind of like the freelist data structure, except instead of individual object allocations we deal with large, contiguous chunks of memory (much larger than an individual object allocation)

    - these are the _arenas_

- the idea is that we allocate a single arena to hold a bunch of objects that will all have similar lifetimes (i.e., they will all be deallocated at the same time)

- when we allocate an object inside an arena, we use a _bump pointer_ that points to the next free address in the arena

    - so allocation is extremely fast: just increment the bump pointer

    - what if we allocate more than the arena can hold? then we need to pull another arena from the arena freelist and chain it to this one

- we never deallocate individual objects, instead we deallocate an entire arena (and any arena chained from it)

    - this is really fast because splicing an arena into the arena freelist is constant time

- arenas work well if there are a small number of arenas, each of which contain many objects that all have the same lifetime

- arenas work poorly if there are lots of arenas (then we have the same problems as with freelists) and/or objects have different lifetimes (in which case there's lots of internal fragmentation)

### copying/compacting

- the entire heap is kind of like a single arena, where memory is allocated via a bump pointer

- objects are reclaimed in batches (like arenas), but unlike arenas objects don't have to have the same lifetimes

- any live objects are copied to a new location (typically all compacted together into a contiguous chunk)

- notice that this can only work for strongly typed languages and with the cooperation of the compiler

    - if we copy a live object to a new location then we need to update all pointers to that object with the new location

    - this can't happen in weakly-typed languages (where we don't even know what's a pointer and what's not)

    - the compiler has to help as well, so we know where pointers are stored (e.g., which stack offsets)

- this strategy is solely used with some forms of tracing garbage collector, as we'll describe in a bit

    - most production-quality language runtimes for managed languages (java, .net, javascript, etc) use this strategy

## manual memory management

- once we have the runtime data structure set up, the remaining piece is how the runtime knows when allocated memory should become available

- in manual memory management, the programmer makes memory deallocation explicit when writing their code

    - e.g., `free` in C, or `delete` in C++

- that means that it is the programmer's responsibility to track all allocated memory and decide when a particular allocation is no longer needed

    - this won't work with the copying strategy, because manual memory management automatically means a language must be weakly typed

    - the most common strategies are freelist variants

- pros:

    - easy for the compiler and runtime

    - provides low-level control over exactly when memory is deallocated

    - no runtime overhead beyond that of the memory management data structure

- cons:

    - programmers make mistakes...a lot: use-after-free, double-free, memory leaks, etc

        - the symptoms of these bugs are nondeterministic and can be very difficult to detect and track down

        - a lot of these mistakes turn out to be security vulnerabilities as well

    - code is more complicated and harder to understand, because it has to integrate logic to track memory availability in addition to whatever else it's trying to do

## automatic memory management
### intro

- if we don't want the programmer to handle memory management, then either the compiler or the runtime needs to do so

    - this frees the programmer from worrying about memory and wipes out an entire class of possible bugs

- when is allocated memory safe to reclaim?

    - if the program will never access it again (i.e., it is _dead_)

    - knowing this required predicting the future: at what point can we safely say that a particular piece of allocated memory is dead?

- all automated schemes approximate deadness with _unreachability_: if the memory is no longer accessible by the program, then clearly it must be dead

    - e.g., if there is a single pointer referencing it and that pointer gets assigned a new value

    - this is an approximation of deadness because a piece of memory could still be accessible, yet the program may never actually access it

- different automated memory management schemes track reachability in different ways

### reference counting

- this strategy is typically backed by something like the freelist data structure

- reference counting associated a counter with every object that keeps track of how many pointers there are to that object

    - when we assign a pointer to that object, we increment the counter

    - when we assign a pointer away from that object, we decrement the counter

    - these increments and decrements are usually added by the compiler/interpreter, not the programmer

- when the counter reaches 0, there are no pointers to that object and so it is unreachable and can be reclaimed

- this is the way cpython (the reference implementation for python) manages memory

- this is also how c++ smart pointers work (shared_ptr and unique_ptr)

- example 1 [show heap as a graph of objects]

```
a = new Foo
b = new Bar
b.x = a
b.y = new Foo
b = new Bar   // reclaim memory
```

- example 2 [show heap as a graph of objects]

```
b = new Bar
b.x = new Foo
b.x.z = b
b = new Bar  // no memory reclaimed
```

- as the second example shows, reference counting can't handle cyclic structures

    - reference counts will never go to zero

    - we either disallow cyclic structures, or use some other mechanism to handle them

- the other main problem with reference counting is performance: every pointer assignment requires multiple accesses to memory to decrement and increment the appropriate counters

- however, it is simple to implement and collects garbage as soon as it becomes unreachable

### tracing
#### intro

- tracing collectors don't keep close track of reachability the way reference counting does; instead they wait until memory is exhausted and only then figure out what it and is not reachable

- they are called tracing collectors because they "trace" reachable memory in order to determine what objects are reachable; anything not reached during the trace must be dead

    - note that tracing requires type information: we have to know what is a pointer and what is not (which requires knowing what type of object we're pointing to in the heap)

    - this generally requires a strongly-typed language, though there are conservative tracing collectors that can work to some degree for weakly-typed languages (by making safe "guesses")

- there are a wide variety of trace-based garbage collectors; this is the most popular strategy for many languages

#### mark-sweep

- this strategy is typically backed by something like the freelist data structure

- the runtime allocator does nothing to reclaim memory until an allocation request is made that it can't satisfy

- when a collection is triggered:

    - it identifies the _root set_: the pointers into the heap that the program can directly access (e.g., local variables on the stack and globals)

    - starting from the root set, it traces all reachable objects in the heap and marks them as live

    - then it sweeps through the heap and frees any memory that isn't marked as live

- once a collection is over the allocator tries again to satisfy the previously failed request; if it can't then the program really is OOM

- example 1 [show heap as a graph of objects]

```
a = new Foo
b = new Bar
b.x = a
b.y = new Foo
b = new Bar   // doesn't reclaim memory
c = new Foo   // assume OOM, triggers collection
```

- example 2 [show heap as a graph of objects]

```
b = new Bar
b.x = new Foo
b.x.z = b
b = new Bar
c = new Foo   // assume OOM, triggers collection
```

- each mark-sweep collection does work proportional to the entire heap, which is a lot of wasted work if most of the heap is dead

- because it touches the entire heap for every collection it has poor cache locality, harming program performance

#### semi-space

- this is an example of a copying/compacting collector

- we split memory into two halves: the `from` space and the `to` space

- memory is always allocated in `from` space (hence the name), using a bump pointer (so allocation is extremely fast)

- when the `from` space is exhausted, we trace live memory just like mark-sweep _but_ instead of marking live objects, we copy them into `to` space (hence the name)

    - we have to update relevant pointers when we copy an object to a new location

- once all live objects have been copied, we swap which space is the `from` space and which is the `to` space, and start allocating from the new `from` space (which used to be the `to` space)

- example 1 [show heap as array, with 24 words of memory]

```
a = new Foo    // 3 words
b = new Bar    // 3 words
b.x = a
b.y = new Foo
b = new Bar   // doesn't reclaim memory
c = new Foo   // triggers collection
```

- example 2 [show heap as array, with 24 words of memory]

```
a = new Foo
b = new Bar
a.f = b
c = new Foo
b = new Bar
b.x = a.f
c = new Foo // triggers collection, multiple pointers to same object
```

- as example 2 shows, a naive version of copying will create multiple copies of the same object, one for each path to that object

- to prevent this problem, when we copy an object we should leave behind a _forwarding pointer_ in its old location

    - whenever we trace an object, if we see a forwarding pointer we can stop tracing and just update to the indicated location

    - note that this requires some metadata to distinguish between an object and a forwarding pointer

- [example 2 redux, this time with forwarding pointers]

- a copying collector really does require strong typing, because we have to update pointer values---we can't just guess what's a pointer and what isn't, so conservative GC doesn't work

- semi-space has some really good properties:

    - allocation is extremely fast
    
    - work for a collection is only proportional to the number of live objects
    
    - there is no fragmentation (internal or external)
    
    - it enables good locality because things are compacted together

- but it does have some disadvantages:

    - available memory is cut in half
    
    - long pause times for collections

#### generational

- generational collection is based on an empirical observation: "young objects die quickly", that is, recently allocated objects are likely to not live very long

    - this implies that we should focus our memory reclamation efforts mostly on recently allocated objects

- we divide memory into two pieces, like semi-space, except that this time we have a _nursery_ and an _older space_ where the nursery is much smaller than the older space

- we allocate into the nursery using a bump pointer

- when the nursery is exhausted, we collect _only_ the nursery, copying any live objects into the older space

    - this is called a _minor collection_

    - we leave forwarding pointers just like semi-space

- when the older space is exhausted, we collect the entire heap

    - this is called a _major collection_

    - if the nursery is much smaller than the older space then we can't use it to copy live objects during a major collection; what do we do with them?

    - generational collection is parameterized by how the older space is treated; it could be mark-sweep, semi-space, or even another generational collector

- notice that if the empirical observation is true, we'll almost always be doing minor collections and very rarely do a major collection

- example 1 [use graph, divided into nursery and older space]

```
for i in 0..10 {
  // assume major collection after 5 iterations
  a = new Foo
  for j in 0..8 {
    // assume minor collection after second iteration, then every three iterations
    b = new Foo
    b.f = new Bar
  }
}
```

- there is an issue we need to address, though: what if an object in the older space points into the nursery?

- example 2 [use graph as before]

```
a = new Foo
b = a
for i in 0..8 {
  // assume minor collection after second iteration, then every three iterations
  // remember later minor collections don't look at `a`, only `b`
  b.f = new Foo
  b = b.f
}
```

- we need to include any such pointers as part of the root set, but how?

    - we keep a _remember set_ of pointers from older space into the nursery and include it in the root set when doing a minor collection

    - whenever the program updates a pointer value, we check if it's from the older space into the nursery, and if so then update the remember set

        - this check is called a _write barrier_

- [example 2 redux, using remember sets]

- generational collection is the most popular form of automatic memory management in commercial garbage collectors

    - all the advantages of semi-space

    - entire memory can be used (unlike semi-space)

    - minor collections have small pause times (because nursery is small)
    
    - major collections can be done using many different methods

    - lots of possible optimizations (immortal space, large object space, etc)

- the main disadvantage is the requirement for write barriers, which can impact program performance

### linear types

- having the runtime dynamically track memory in order to know when to reclaim it has some downsides

    - performance overhead

    - unpredictability

- can we get the performance and predictability of manual memory management while still making it completely automated?

    - yes we can!

    - all the benefits of automatic memory management _and_ manual memory management

- the key idea is to use _linear types_

    - checked during validation, just like we did for cflat

    - gives compiler enough information to safely insert frees automatically

- type systems are closely related to formal logic, and linear type systems are related to something called linear logic

    - whereas classical logic is a logic about truth, linear logic is a logic about resources: unlike truth, resources are finite and consumable

    - consider the classical logic formula `A, A ⥰ B ⊢ A ∧ B`, and suppose `A` stands for "i have cake" and `B` stands for "i am full"; this formula says that i can both have my cake _and_ i can eat that cake, simultaneously

    - linear logic recognizes that cake is a finite and consumable resource; we can deduce `A, A ⥰ B ⊢ B` but not `A, A ⥰ B ⊢ A ∧ B`

- what does this have to do with types? memory is also a finite and consumable resource, and we can use linear logic to reason about it

    - a given heap object can only be pointed to by one pointer, e.g., `x = new Foo`

    - we can move ownership to a different pointer, but then the original one no longer has ownership: `y = x; z = *x` is an error

    - when an owning pointer goes out of scope, necessarily the object it points to becomes unreachable and can be reclaimed (this doesn't mean that heap objects are lexically scoped because we can always transfer ownership to a pointer outside the current scope, like returning it from the enclosing function)

    - effectively we're doing compile-time reference counting, where the reference counts can only be `1` or `0` and the type system guarantees that we can count correctly

- example (well-typed)

```
fn foo(p:&int) -> &int {
  let a:&int = p, b:&int;
  b = new int;
  *b = *a;
  return b;
  // object pointed to by `a` is deallocated here
  // object pointed to by `b` is not
}
```

- example (ill-typed)

```
fn foo(p:&int) -> &int {
  let a:&int = p, b:&int;
  b = new int;
  *b = *p;  // COMPILER ERROR, `p` no longer owns any object
  return b;
}
```

- linear types have been known in academia for decades, but only in the last decade has the idea made its way into the mainstream with the rust language

- strictly linear types can be a bit hard to work with, so we can introduce the idea of "borrowing" ownership

    - a pointer can retain ownership, but allow other pointers to temporarily borrow access to the object

    - the key is that the type system can guarantee that these borrows never outlive the owning pointer

- note that modern c++ has the notion of "unique_ptr", which expresses a similar idea (unique ownership of an object, allowing it to be automatically reclaimed when the owning pointer goes out of scope)

    - in c++ this is only a heuristic, there is no guarantee that it will be correct (it's trivial to write a program that passes the compiler but violates ownership)

    - there is no formal notion of borrowing; we can copy the raw pointer from a unique_ptr but there are no checks for how we use those copies

# TODO: IR optimization [~4 lectures]
## intro

- we have a fully functional compiler, but it is fairly naive in terms of the generated machine code---we'd like to optimize the program as we're compiling it in order to produce better executables

- what do we mean by optimization? what are we optimizing?

    - usually we mean performance: making the generated code execute faster
    
    - sometimes we want to optimize for other things, like code size, memory consumption, or power consumption (all of which are important for embedded systems, for example)
    
    - for our purposes we'll focus on performance optimization

- "optimization" is a somewhat misleading term, because it implies we're going to find some optimal (i.e., best) solution

    - in reality finding the best solution is np-hard or undecidable and we're just looking for a better solution (i.e., the optimized code runs faster than the unoptimized code)

- how do we optimize for performance?

    - we want to reduce the amount of required computation---either eliminate instructions or replace operations with faster versions

    - we still need to guarantee that the resulting program "does the same thing", or the compiler will break the user's program

- some examples:

    - arithmetic identities: `x = y * 0` ⟶ `x = 0`

    - constant folding: `x = 1 + 2` ⟶ `x = 3`

    - constant propagation: `x = 3; y = x + 3` ⟶ `x := 3; y := 6`

    - redundancy elimination: `x = a + b; y = a + b` ⟶ `x = a + b; y := x`

    - strength reduction: `x = y / 2` ⟶ `x = y >> 1`

- optimization = analysis + transformation

    - we need to use program analysis (aka static analysis) to determine what is true about the program's behavior (e.g., what things are constant values, what expressions are guaranteed to evaluate to the same value, etc)
    
    - then we select code transformations that, based on the information provided by the analysis, are safe (preserve behavior) and effective (improve performance)

- we can perform optimizations in the backend on the generated assembly code, but then we would have to reimplement them for each backend we add to the compiler

    - there are some optimizations that are inherently target specific, and we reserve those for the backend, but mostly we try to optimize in a target-agnostic way so that we can reuse the same optimizations for all targets

- the optimizer can also operate at a number of different scopes, ranging from small fragments of code (local optimization) to entire functions (global optimization) to set of functions (interprocedural optimization) to entire programs (whole-program optimizations)

    - the term "global" may seem odd since we're focusing in on one single function; the term comes from a time when local optimization was the norm and operating at an entire function scope was seen as extremely aggressive---operating on even larger scopes than that was out of the question

    - the larger the scope that the optimizer operates at, the more effective it is but the more complex and expensive it is

- for this class

    - we'll focus on local optizations and global optimizations

    - we'll assume there are no pointers, structs, globals, or function calls

    - so the only LIR instructions we need to worry about are: `$copy`, `$arith`, `$cmp`, `$jump`, `$branch`, and `$ret`

    - this simplifies a lot of things and is enough to give the basic idea

## safety and profitability
### intro

- we stated two important properties of optimization:

    - it should be safe (preserve program behavior)
    
    - it should be profitable (improve program performance)

- let's dig a little into each, because they aren't as straightforward as they seem

### safety

- we said that optimizations should preserve behavior, but what does that mean? if you think about it, the whole point is to change program behavior by making it run faster using different instructions---what are we preserving? what does "behavior" even mean?

- roughly, we can think of the behavior we're preserving as user-observable events independent of time

    - in other words, if we run the original program `P` and record the user-observable events (e.g., outputs) without timestamps, then do the same with the optimized program `P'`, we should get the same thing

- this definition isn't quite adequate for languages like C and C++, which have the concept of "undefined behavior"

    - literally, this means that for certain code expressions and statements the language standard doesn't say what should happen, thus the compiler implementor is free to do whatever they want
    
    - in fact, it goes even further than that: a program that executes a statement with undefined behavior isn't defined at all; the entire program execution is undefined, not just that statement
    
    - this is important to understand: the effects of undefined behavior are not confined to the statement that is undefined, or even the execution after that statement, but the _entire_ execution, even before that statement is executed

- how can undefined behavior affect the program execution before the behavior even happens? that seems like magic

    - the reason a language allows undefined behavior is so that the compiler is free to make certain assumptions when it optimizes a program, making the optimizations more effective

    - basically, the compiler is allowed to assume that undefined behavior will never happen when it checks whether a given optimization is allowed or not (i.e., would it change the program's behavior); it's up to the programmer to ensure that this assumption is true, not the compiler

    - if the assumption is false, the optimization actually _can_ change program behavior from the unoptimized version, but that's the programmer's fault not the compiler's fault

- let's look at an example (adapted from the blog of John Regehr, a CS professor whose research deals a lot with undefined behavior in C)

```
#include <limits.h>
#include <stdio.h>

int main() { 
  printf("%d\n", (INT_MAX+1) < 0);
  printf("%d\n", (INT_MAX+1) < 0);
  return 0; 
}
```

- this program asks the question (twice): if we take the maximum integer value and add 1, do we get a negative number? in other words, do integers wrap around?

    - so what happens when we run this program?

```
$ gcc test.c -o test
$ ./test
0
0

or:

$ gcc test.c -o test
$ ./test
1
1

or:

$ gcc test.c -o test
$ ./test
0
1

or:

$ gcc test.c -o test
$ ./test
42
foo
```

- any of these could happen, or anything else---the program has undefined behavior because it exhibits signed integer overflow, that is, we took a signed integer and added a value to it that caused the result to be out of the range that a signed integer can express; the C standard says this this is undefined

    - note that the result doesn't need to be consistent, even within the same execution

    - also note that unsigned integer overflow is defined

- you might think: "on an x86 the C integer add instruction is implemented using the x86 ADD assembly instruction, which operates using two's complement arithmetic, which does wrap around---thus, if i'm running the program on an x86 i can expect to get 1"---but this is bad reasoning, for the following reasons:

    1. you don't know what architectures your program will be compiled on, even if you are compiling for x86 right now

    2. not all compilers will work this way, even on an x86, even if the one you're using right now does

    3. at certain optimization levels, a compiler that may work this way previously will stop working this way because it takes advantage of the undefined behavior to optimize the program

- let's take a closer look at that third reason with another example:

```
int stupid(int a) { return (a+1) > a; }
```

- note that if we pass in `INT_MAX` for `a` then we get undefined behavior

- the compiler optimizer can reason this way:

    - case 1: `a` is not `INT_MAX`; then the answer is guaranteed to be 1

    - case 2: `a` is `INT_MAX`; then the behavior is undefined and we can do whatever we want; let's make the answer 1

- it then outputs the following optimized code:

```
int stupid(int a) { return 1; }
```

- this is completely valid, even if we're running the program on an x86 and using the x86 ADD instruction which exhibits two's complement behavior, so that `INT_MAX + 1 < INT_MAX`

    - so if we don't optimize the function works one way, but if we do optimize it works another

- we've only looked at signed integer overflow, but the C99 language standard lists 191 kinds of undefined behavior

    - do you know them all?

    - this is why it's always a good idea to use `-Wall` for C/C++ compilation and pay attention to the warnings

### profitability

- another consideration is that the transformation we apply to the code to optimize it should actually result in faster code; this can be trickier than you might think

- example: function inlining; consider:

```
fn foo(a:int) -> int { return a + 1; }

fn main() -> int {
  let x:int = foo(2);
  return x;
}
```

- at runtime, the execution will look something like:

    - `pre-call`
    - `foo prologue`
    - compute `a+1`
    - `foo epilogue`
    - `post-return`
    - return `x`

- notice that the `pre-call`, `prologue`, `epilogue`, and `post-return` are all overhead that comes because we're making a function call

- we can optimize this code by _inlining_ the function call, that is, by copying the body of the function directly into the caller:

```
fn main() -> int {
  let x:int = 2 + 1;
  return x;
}
```

- this has exposed another optimization opportunity for constant folding and propagation, yielding the following optimized program:

```
fn main() -> int {
  return 3;
}
```

- function inlining both eliminates the overhead of function calls and also exposes many opportunities for optimization that wouldn't be obvious if we looked at functions in isolation

- we can perform function inlining whenever we know exactly which function is being called (always for direct calls, sometimes for indirect calls)

- you might think: inlining one function call was good, so obviously we should inline as many as possible! but this actually is not a good idea; for one, we can't fully inline recursive calls (we would get into a cycle of infinitely repeated inlinings); but let's ignore that issue---the real problem is that inlining comes with a cost: the code becomes larger

```
fn foo(a:int) -> _ { <body> }

fn main() -> int {
  foo(2);
  foo(3);
  return 0;
}
```

- becomes:

```
fn main() -> int {
  <body with a = 2>
  <body with a = 3>
  return 0;
}
```

- there were two calls to `foo`, so we doubled the amount of code inside `foo`'s body; now consider the following:

```
fn bar(b:int) -> _ { <body> }

fn foo(a:int) -> _ {
  bar(a+10);
  bar(a+20);
}

fn main() -> int {
  foo(2);
  foo(3);
  return 0;
}
```

- becomes:

```
fn main() -> int {
  <body with b = 12>
  <body with b = 22>
  <body with b = 13>
  <body with b = 23>
  return 0;
}
```

- we doubled the amount of code in `foo`, which doubled the amount of code in `bar`

- in general, inlining can increase code size exponentially; this impacts not just memory consumption, but also performance because the large code size can blow the instruction cache and cause large slowdowns

- in practice, compilers use a set of complicated heuristics to determine whether a function call should be inlined or not

- many optimizations have similar considerations: just because we _can_ apply the optimization doesn't mean it's a good idea; we need to figure out in each case whether applying the optimization results in a net performance benefit or loss and act accordingly

## local optimizations
### intro

- local optimizations operate on basic blocks; we iterate through each block (in arbitrary order) and apply the local optimizations to optimize each block in isolation from each other

- because they only apply to straightline code (the basic block), they tend to be much simpler than global analyses

- we'll go over some examples of common local optimizations

### constant folding and arithmetic identities

- we've already mentioned these; they're simple optimizations that operate on single instructions

    - the idea is that sometimes we don't need to wait for the program to execute in order to evaluate expressions, we can do it right in the compiler

- for each instruction: 

    - if all the operands are constants, evaluate the operation (`$arith` or `$cmp`)
    
    - if some of the operands are constants, attempt to apply an arithmetic identity (for `$arith`)

```
x * 1 = x
1 * x = x
x * 0 = 0
0 * x = 0
x + 0 = x
0 + x = x
x - 0 = 0
0 - x = -x
x - x = 0
```

- if we are able to evaluate a branch condition to a constant, we can turn the branch into an unconditional jump

### local value numbering

- this is a slightly more complicated optimization that operates on the entire basic block; our goal is to identify redundant expressions computed by the basic block so that we don't compute the same thing more than once

- example 1:

```
a = 4
b = 5
c = a + b
d = 5
e = a + b
```

- clearly the second `a + b` is redundant and we could optimize this as:

```
a = 4
b = 5
c = a + b
d = 5
e = c
```

- a naive approach would be to simply scan down the list of instructions in the basic block and record which ones we see; if we see it again, replace it with the left-hand side of the first instruction to compute that expression---but this doesn't always work

- example 2:

```
a = 4
b = 5
c = a + b
a = 5
e = a + b
```

- the naive optimization would be incorrect, because the value of the second `a + b` is different than the value of the first `a + b`

- here's another problem; example 3:

```
a = 4
b = 5
c = a + b
d = 5
e = b + a
```

- the second expression `b + a` is syntactically different than the first `a + b` but is semantically identical; we need to be able to recognize that fact while still distinguishing expressions like `a - b` and `b - a`

- our strategy will be to tag expressions with numbers s.t. semantically identical expressions (like `a + b` and `b + a` in example 3) are given the same number and hence are recognized as equivalent while semantically different expressions (like the first `a + b` and the second `a + b` in example 2) are given different numbers

    - note that our goal is not to guarantee that we find all equivalent expressions; this is uncomputable---we want to find as many as we can while still being fast and while guaranteeing that we never incorrectly find non-equivalent expressions to be equivalent

- the algorithm:

    1. initialize a table mapping expressions to value numbers (starting empty); we often use a hash table, and thus this optimization is sometimes called "hash-based value numbering"

    2. for each basic block instruction in order: 
    
        - look up each variable operand to get its value number; assign a fresh number if it doesn't have one already and record it in the table
        
        - if the operator is commutative, order the operand value numbers from least to greatest
        
        - look up the entire expression (using the operand numbers instead of variables) in the table to get its number; assign a fresh number if it doesn't have one already and record it in the table
        
        - update the table to map the left-hand side variable to the number for the right-hand side expression

    3. for any two instructions `x = <e1>` and `y = <e2>` s.t. `x` and `y` have the same value number at `y = <e2>`, we can replace `<e2>` with `x`

- [go through examples 1--3 above]

- example 4

```
x = a + d
y = a
z = y + d
```

- we can see from this example that local value numbering can discover equivalent expressions even if they use different variables altogether

- example 5

```
a = x + y
a = 1
b = x + y
c = x + y
b = 2
d = x + y
```

- how can we transform this code to optimize it based on the value numbering?

    - we know that `x + y` in all four expressions will have the same value, but `a` and `b` are redefined at various points
    
    - it would be incorrect to replace `c = x + y` with `c = a` and incorrect to replace `d = x + y` with either `d = a` or `d = b`

    - we can only safely transform `c = x + y` to `c = b` and `d = x + y` to `d = c`

- this is why we have the caveat in the algorithm that we can only replace `<e2>` with `x` _if_ `x` and `y` have the same value number _at that  instruction_

    - for the instruction `c = x + y` we see that while `a` originally had the same value number as `c`, now it has a different value number and so cannot be used
    
    - similarly, for the instruction `d = x + y` we see that neither `a` nor `b` still have the same value number as `d` and so neither can be used.

### exercise 

- optimize the following code using local value numbering:

```
a = b - c
d = c - b
e = c 
f = a * d
g = b - e
h = d * a
d = 42
i = e - b
```

- SOLUTION

```
a = b - c
d = c - b
e = c
f = a + d
g = a
h = f
d = 42
i = d
```

## global optimizations
### general intro

- we have looked at analysis and optimization at the basic block level (local optimization), but the limited scope means that we can miss many optimization opportunities; let's widen our scope to entire functions, i.e., global optimization

- remember that optimization = analysis + transformation; we split global optimization into two pieces:

    1. program analyses to determine what transformations are safe to do

    2. the actual transformations that optimize the program

- the analyses required for global optimization tend to be more complicated than for local optimization, and we have developed a specific theoretical framework for it called _dataflow analysis_

    - compiler developers tried a lot of ad-hoc global analyses at first, but found that they were very difficult to get correct; dataflow analysis provides a handy set of tools such that, if you use them correctly, you will get a correct analysis

- first we'll look at some high-level examples of optimizations we might want to perform

- then we'll dig deeper into the DFA framework for program analysis that is required to enable those optimizations

    - there's a lot of math that goes into showing that these analyses will terminate and give the correct answer; we won't go into that math, it's outside the scope of the class (and just to implement the analyses you don't need to know the math)

    - if you want to understand the math behind it all, see my graduate course CS 260

- finally we'll go in-depth into a specific set of DFA analyses and the optimizations they enable

- there are _many_ more optimizations than the ones described here, these are just a sampling

### high-level optimization examples
#### constant propagation

- we want to precompute constant values during compilation so we don't have to compute them at runtime

- there are often constants present in source code, but even more tend to get added as the code is lowered and transformed in various ways

- original code

```
entry:
  x = $copy 40
  y = $arith add x 2
  $ret y
```

- becomes

```
entry:
  x = $copy 40
  y = $arith add 40 2
  $ret 42
```

#### global common subexpression elimination

- we want to avoid computing the same expression multiple times if they will always have the same answer

- original code

```
entry:
  x = $arith add p q
  y = $arith add p q
  z = $arith add x y
  $ret z
```

- becomes

```
entry:
  x = $arith add p q
  y = $copy x
  z = $arith add x y
  $ret z
```

#### copy propagation

- we want to avoid making superfluous copies of things at runtime when possible

- this is often useful as a cleanup phase after other optimizations (can also be useful after lowering)

- original code (from result of GCSE)

```
entry:
  x = $arith add p q
  y = $copy x
  z = $arith add x y
  $ret z
```

- becomes

```
entry:
  x = $arith add p q
  y = $copy x
  z = $arith add x x
  $ret z
```

#### dead code elimination

- we want to eliminate code whose execution at runtime is irrelevant

- another one that's useful to clean up after other optimizations

- original code (from result of constant propagation)

```
entry:
  x = $copy 40
  y = $arith add 40 2
  $ret 42
```

- becomes

```
entry:
  $ret 42
```

- original code (from result of copy propagation)

```
entry:
  x = $arith add p q
  y = $copy x
  z = $arith add x x
  $ret z
```

- becomes

```
entry:
  x = $arith add p q
  z = $arith add x x
  $ret z
```

### DFA intro

- the above examples were trivially correct and easy to reason about, since they were all contained in a single block

- to safely apply such transformations globally, we need to analyze the entire function

- what does a program analysis do, exactly?

    - it computes _invariants_ about program execution: things that are guaranteed to always be true

    - this information is what tells us whether a transformation is safe

- what invariants does it compute?

    - it depends on the specific analysis

    - each analysis will decide on a set of possible invariants that it's looking for

- example: _sign analysis_ looks for invariants about whether a variable's value is _positive_, _negative_, or _zero_

```
fn abs(a:int, b:int) -> int { 
  let c:int;
  if (a < b) { c = b - a; } else { c := a - b; } 
  return c;
}
```

- given this function, we don't know what the values of the arguments will be, nor whether we will take the true or false branch of the conditional---but we can infer the invariant that the value returned by this function will always be non-negative; here's our reasoning:

    - either `a < b` or `b < a` or `a == b`
    
    - if `a < b` then we take the true branch, `c` is positive
    
    - if `b < a` then we take the false branch, `c` is positive
    
    - if `a == b` then we take the false branch, `c` is zero
    
    - therefore `c` cannot be negative at the return statement

- in this example we are reasoning about the sign of `c` (positive, negative, or zero), so the facts that we care about pertain to that; in other analyses we care about other properties (as we'll see examples of soon)

- dataflow analysis provides a general framework that is independent of the particular facts we want to reason about; basically, it's a template into which we plug in different kinds of things depending on the specific analysis we're trying to perform

- it's important to note that computing _exactly_ the correct set of invariants is an undecidable problem (which we can prove using the techniques you learned in 138)

    - this means we can only _approximate_ the exact set of invariants

    - it's critical that we never compute a _false_ invariant, therefore we allow the analysis to miss true invariants in return for guaranteeing that it never finds a false invariant

    - this means that we may miss some possible optimizations that we could have safely performed, but in return we'll never perform optimizations that break the program

- example: sign analysis redux

```
fn foo(a:int, b:int) -> int {
  let c:int;
  if a < b { c = b - a; }
  if b < a { c = a - b; }
  if a == b { c = 1; }
  return c;
}
```

- for this example we can reason about signedness again and determine that `c` must always be positive at the return statement

    - however, our analysis may say only that `c` will be non-negative, or even that it may be any of positive, negative, or zero

    - these are true invariants, but not as precise as they could be (being positive implies both of those things)

### DFA framework

- remember that DFA is a template; we define how it works while leaving certain parts unspecified (which will be filled in by each specific analysis)

    - we'll describe the framework here, then give a number of concrete examples

- these parts are defined by a specific analysis:

    - an _abstract store_ datatype, along with an operation called _join_ that combines two abstract stores into a new abstract store

        - this abstract store will contain the invariants that we're computing for the analysis being implemented

        - the join operation combines the invariants from two abstract stores into a new set of invariants that over-approximate the originals, i.e., if `A ⊔ B = C` then `A ⇒ C` and `B ⇒ C` (but `C` may be less precise than `A` or `B`)

    - the _initial abstract store_ `init`

        - these are the invariants that hold at the entry of the function

    - the _bottom_ abstract store `bot`

        - this is the store that says no invariants have been computed yet

    - for each LIR instruction a function `F` that transforms an input abstract store into an output abstract store for that instruction

        - sometimes we only need the non-terminals `F_copy`, `F_arith`, and `F_cmp`, each taking a store as an argument and returning a store as a result

        - sometimes the terminals are relevant too: `F_br`, `F_jmp`, `F_ret`

        - these functions describe how an instruction modifies the computed invariants

- given these parts, we can define how DFA works independent of any given analysis

- for each basic block `bb` we'll define:

    - `IN_bb` (the incoming abstract store for `bb`)
    - `OUT_bb` (the outgoing abstract store for `bb`)

- we initialize these as follows:

    - `IN_entry` = `init`
    - `IN_bb` = `bot` for all `bb` ̸= `entry`
    - `OUT_bb` = `bot` for all `bb`

- we create a worklist `work` of basic blocks, initialized to contain every basic block

    - these are the basic blocks that remain to be processed

    - it doesn't matter what order the basic blocks are processed in, the DFA framework guarantees we'll get the same answer

    - this means the worklist can be a set, queue, stack, etc (it's probably easiest to make it a stack implemented using a vector)

- while the worklist isn't empty, we continually do the following:

    - pop a basic block `bb` from the worklist

    - `IN_bb` = `⨆ OUTₚ` s.t. `p` is a predecessor of `bb` in the CFG

        - this says that the input could be coming from any of the `bb`'s predecessors, and so `IN_bb` needs to over-approximate all of the inputs

    - let `store` = `IN_bb` (IMPORTANT: this is a copy)

    - for each instruction `i` from 0..len(`bb`):

        - `store` = `F_inst(store)`, where `F_inst` is the function relevant to instruction `i`

        - note that we're updating `store` in-place; at the end of all the instructions we have the store leaving the basic block

    - if `store` ̸= `OUT_bb` (i.e., the invariants have changed):

        - `OUT_bb` = `store`

        - for each basic block `s` that is a successor of `bb` in the CFG, add `s` to the worklist

- during the worklist loop we may revisit the same basic block multiple times; this can happen because of loops

- this algorithm is designed to ensure that (1) the worklist will eventually become empty; and (2) when it does, each basic block's `IN_bb` and `OUT_bb` will contain correct invariants for the entry and exit of that basic block

    - we may need the invariants for a specific instruction inside `bb`, not just the entry or exit

    - we can compute that by taking `IN_bb` and applying `F_inst` up to the instruction in question and stopping there

- [show high-level overview of worklist algorithm in action on some CFG]

### constant propagation (constants analysis)

- we want to precompute constant values during compilation so we don't have to compute them at runtime

    - [show simple example again]

- the analysis tells us what constants exist at each program point

- the transformation is trivial given the analysis: for each instruction that uses variables, if the analysis says that a variable has a constant value at that point then we replace the variable with that value

    - then we can apply local constant folding as discussed earlier

- to fill in the DFA framework, we need to:

    - define an abstract store datatype

    - define a join operation on abstract stores

    - define the initial abstract store

    - define the bottom abstract store

    - for each `inst` in {`$copy`, `$arith`, `$cmp`}, define `F_inst`

- then we just apply the DFA worklist algorithm and we have our analysis

- the abstract store datatype

    - the invariants we're interested in are `variable X has constant value Y`

    - our abstract store will map each variable to either:
    
        - a constant value
        - an "i don't know" value (i.e., maybe it's a constant or maybe not)
        - an "no value yet" value (i.e., the variable is undefined)

    - we'll call the "i don't know value" `⊤` (TOP) and the "no value yet" `⊥` (BOTTOM) for reasons having to do with the math behind DFA (which we won't go into)

    - so the abstract store is a map from variable to one of {`n ∈ 𝐙`, `⊤`, `⊥`}

- joining abstract stores

    - given two abstract stores, we need to compute a new abstract store that is guaranteed to be correct no matter which input store is "true"

    - let's focus on some variable `x`, which each input store will map to `n ∈ 𝐙`, `⊤`, or `⊥`; we'll say `x₁` is from one store and `x₂` is from the other and `x₃` is for the new store

        - if `x₁ ↦ n` and `x₂ ↦ n`, then `x₃ ↦ n`

        - if `x₁ ↦ n₁` and `x₂ ↦ n₂` s.t. `n₁ ̸= n₂`, then `x₃ ↦ ⊤`

        - if `x₁ ↦ ⊤` or `x₂ ↦ ⊤`, then `x₃ ↦ ⊤`

        - if `x₁ ↦ ⊥` and `x₂ ↦ X`, then `x₃ ↦ X` (or vice-versa)

    - we do this for each variable in the input stores to get the complete output store

- example

```
[a ↦ 1, b ↦ 2, c ↦ ⊤, d ↦ ⊥] ⊔
[a ↦ 1, b ↦ 3, c ↦ 0, d ↦ 4]
=
[a ↦ 1, b ↦ ⊤, c ↦ ⊤, d ↦ 4]
```

- initial abstract store

    - what is guaranteed to be true at the entry of the function? we zero-initialize all locals; we don't know the parameter values

    - `init` = `[x ↦ ⊤, y ↦ 0]` for `x ∈ params`, `y ∈ locals`

- bottom abstract store

    - this is used for all the `IN` and `OUT` stores that we haven't computed yet

    - `bot` = `[x ↦ ⊥]` for `x ∈ locals`

- `x = $copy op`

    - given `store`, update with new value of `x`

    - `store[x]` = [
        `n` if `op` is `n`;
        `store[y]` if `op` is `y`
      ]

- `x = $arith <aop> op1 op2`

    - given `store`, update with new value of `x`

    - for `add` (other operations left as exercise)

```
 + | ⊥ | c₂ | ⊤
---+---+----+---
⊥  | ⊥   ⊥   ⊥
c₁ | ⊥ c₁+c₂ ⊤
⊤  | ⊥   ⊤   ⊤
```

- `x = $cmp <rop> op1 op2`

    - given `store`, update with new value of `x`

    - for `lte` (other operations left as exercise)

```
 ≤ | ⊥ | c₂ | ⊤
---+---+----+---
⊥  | ⊥   ⊥   ⊥
c₁ | ⊥ c₁≤c₂ ⊤
⊤  | ⊥   ⊤   ⊤
```

- given the computed constants, we can trivially transform the program to use the constants instead of variables where possible

- for this analysis we can actually be a bit more clever with `$branch`: if the branch condition is a constant then we know which label we'll jump to and we can ignore the other one (i.e., not put it on the worklist)

- example [see OneNote]

```
fn foo(p:int, q:int) -> _ {
  let x:int, y:int
  entry:
    $jump hdr

  hdr:
    x = $cmp lte y q
    $branch x body exit

  body:
    p = $copy 4
    q = $copy 3
    y = $arith add p q
    $jump hdr

  exit:
    $ret
}
```

- exercise [see OneNote]

```
fn foo() -> int {
  let x:int, y:int, z:int, _t1:int, _t2:int
  entry:
    x = $copy 1
    y = $copy 2
    $jump hdr

  hdr:
    _t1 = $cmp lte 0 x
    $branch _t1 body exit

  body:
    _t2 = $cmp eq x 1
    $branch _t2 tt ff

  tt:
    z = $copy 42
    $jump end

  ff:
    z = $arith add 40 y
    $jump end

  end:
    x = $copy z
    $jump hdr

  exit:
    $ret z
}
```

### global common subexpression elimination (available expressions)

- we want to determine what expressions we've already computed that we can reuse at later program points, avoiding having to compute them again

    - [show example below on OneNote; notice in the example that `p * 4` is guaranteed to have been computed along all paths to the assignment to `z` and for each path to have the same value as when it was computed (though that value is different for each path)]

- the analysis tells us what expressions are available at each program point

    - they are guaranteed to have been computed along all paths to this program point

    - for each path the value is guaranteed to be the same at this program point

- the transformation must then insert copies to take advantage of the available expressions

- as before, to fill in the DFA framework, we need to:

    - define an abstract store datatype

    - define a join operation on abstract stores

    - define the initial abstract store

    - define the bottom abstract store

    - for each `inst` in {`$copy`, `$arith`, `$cmp`}, define `F_inst`

- then we just apply the DFA worklist algorithm and we have our analysis

- the abstract store datatype

    - the invariants we're interested in are `expression E is available` (where "available" means what we specified above)

    - since we're analyzing LIR, an expression `E` is either `<aop> op1 op2` or `<rop> op1 op2`

    - so our abstract store will be a set of available expressions

- joining abstract stores

    - given two abstract stores, we need to compute a new abstract store that is guaranteed to be correct no matter which input store is "true"

    - this is just set intersection, i.e., an expression is guaranteed to be available if it was available in both input stores

- initial abstract store

    - at the entry to the function we haven't computed any expressions yet

    - `init` = `{}`

- bottom abstract store

    - we optimistically set `bot` = the set of all expressions used in the function

    - this is safe because the analysis will remove expressions as necessary (because join is set intersection)

- `x = $copy op`

    - this may change the value of `x`, so update `store` to remove any expressions using `x`

- `x = $arith <aop> op1 op2`

    - the expression `<aop> op1 op2` has been computed, so add it to `store`

    - this may change the value of `x`, so update `store` to remove any expressions using `x` (this may include the expression we just added)

- `x = $cmp <rop> op1 op2`

    - same as for `$arith`

- example [see OneNote]

```
fn foo(p:int) -> int {
  let x:int, y:int, z:int, t:int
  entry:
    x = $arith mul p 4
    t = $cmp lte 0 p
    $branch t bb1 exit

  bb1:
    p = $arith add 1 p
    y = $arith mul p 4
    $jump exit

  exit:
    z = $arith mul p 4
    $ret z
}
```

- exercise [see OneNote]

```
fn foo(a:int, b:int) -> int {
  let x:int, y:int, z:int, t:int
  entry:
    x = $arith add a b
    y = $arith mul a b
    $jump hdr

  hdr:
    z = $arith add a b
    t = $cmp lt z y
    $branch t body exit

  body:
    a = $arith add a 1
    x = $arith add a b
    $jump hdr

  exit:
    $ret x
}
```

- once we've computed the available expressions we need to transform the program to take advantage of them

    - there are various schemes; we'll use a simple one that works but may leave redundancies that other optimizations need to clean up (like copy propagation and dead code elimination)

    - for each expression `e ∈ E` create a fresh variable `Vₑ`

    - for each instance of `e` in an `$arith` or `$cmp`, if `e` is available at that point replace the `$arith` or `$cmp` with `$copy Vₑ`

    - for each definition `x = e` s.t. `e` was replaced with `$copy Vₑ` somewhere, insert a copy `Vₑ = $copy x` immediately afterward

        - notice we don't check whether _that specific_ definition needs to be available, so this may insert extraneous copies that need to be cleaned up later

- example [same as above example]; SOLUTION:

```
fn foo(p:int) -> int {
  let x:int, y:int, z:int, _t1:int, V₁:int
  entry:
    x = $arith mul p 4
    V₁ = $copy x
    _t1 = $cmp lte 0 p
    $branch _t1 bb1 exit

  bb1:
    p = $arith add 1 p
    y = $arith mul p 4
    V₁ = $copy y
    $jump exit

  exit:
    z = $copy V₁
    $ret z
}
```

- exercise [same as above exercise]; SOLUTION:

```
fn foo(a:int, b:int) -> int {
  let x:int, y:int, z:int, t:int, V₁
  entry:
    x = $arith add a b
    V₁ = $copy x
    y = $arith mul a b
    $jump hdr

  hdr:
    z = $copy V₁
    V₁ = $copy z         // THIS IS EXTRANEOUS; WE COULD JUST USE x instead of V₁
    t = $cmp lt z y
    $branch t body exit

  body:
    a = $arith add a 1
    x = $arith add a b
    V₁ = $copy x
    $jump hdr

  exit:
    $ret x
}
```

### dead code elimination (liveness)

- we want to remove code that computes something that is never used (we call this _dead code_)

    - [show simple example again]

    - this is a very useful optimization for cleaning up after other optimizations, like the ones above

- the analysis is going to tell us what variables may be _live_ at each program point; a variable is live if it may be used at some later program point

    - any variable that is not live at a program point must be dead (i.e., definitely won't be used at some later program point)

    - liveness requires us to look into the future at what will happen later in the program; how do we know whether a variable will be used or not?

    - we reverse the CFG: all edges are flipped, we treat basic blocks as if the terminal is the starting point, and we start with the function exit (the `$ret`) instead of the function entry

    - this means at a particular program point we've already seen the later program points and so we know what is live or dead at this program point

    - we can use the same DFA framework as before, just on the reversed CFG---`IN` is for the terminal instruction of a basic block, `OUT` is for the top of a basic block

- the transformation is trivial given the analysis: for each instruction that defines a variable `x`, if the analysis says that `x` is not live at this point then we remove the instruction

- there is a more generalized and powerful analysis we could use called _faint variables analysis_ that could potentially eliminate even more code because it takes transitive deadness into account, but we'll stick with the simpler liveness analysis

    - note in the example below that `b` is considered live at the `$arith` because it is later used as the operand of a `$copy`, even though it is a copy into a dead variable, and thus `a` is considered live at the first `$copy` and so only the final `$copy` can be removed; a more careful analysis would show that `a` and `b` are effectively dead and all the instructions besides `$ret` could be removed

```
a = $copy 1
b = $arith add a 1
a = $copy b
$ret 2
```

- as before, to fill in the DFA framework, we need to:

    - define an abstract store datatype

    - define a join operation on abstract stores

    - define the initial abstract store (for the reversed CFG)

    - define the bottom abstract store

    - for each `inst` in {`$copy`, `$arith`, `$cmp`, `$branch`, `$ret`}, define `F_inst` (remembering that we're going backwards)

        - notice that we're including `$branch` and `$ret`, because they can use variables as operands

- then we just apply the DFA worklist algorithm and we have our analysis

- the abstract store datatype

    - the invariants we're interested in are `variable x may be live at this point` (where "live" means what we defined above)

    - so the abstract store will be a set of variables that are live at a given program point

- joining abstract stores

    - given two abstract stores, we need to compute a new abstract store that is guaranteed to be correct no matter which input store is "true"

    - this is just set union, i.e., a variable may be live if it is live in either input store

- initial abstract store (i.e., at the exit of the function because we reversed the CFG)

    - at the exit of the function nothing is live

    - `init` = `{}`

- bottom abstract store

    - we assume nothing is live: `bot` = `{}`

- `x = $copy op`

    - update `store` to remove `x`

    - if `op` = `y` then update `store` to include `y`

- `x = $arith <aop> op1 op2`

    - update `store` to remove `x`

    - if `op1` or `op2` = `y` then update `store` to include `y`

- `x = $cmp <rop> op1 op2`

    - same as `$arith`

- `$branch x bb1 bb2`

    - update `store` to include `x`

- `$ret x`

    - update `store` to include `x`

- given the analysis, we can trivially transform the program to remove any instruction that assigns to a dead variable

- example/exercise [see OneNote]

```
fn foo(a:int, b:int) -> int {
  let x:int, y:int, z:int

  entry:
    x = $copy 0
    $jump hdr

  hdr:
    y = $cmp lt x a
    $branch y body exit

  body:
    b = $copy 2
    y = $arith add a x
    z = $arith mul a x
    x = $arith add x 1
    $jump hdr

  exit:
    z = $arith add a z
    $ret z
}
```

### copy propagation (reaching defs)

- we want to avoid superfluous copies

    - [show simple example again]

    - this transformation doesn't actually remove copies, it just makes them obsolete; a later optimization (like dead code elimination) can then remove the obsolete copies

    - the analysis and transformation are actually a lot like constant propagation; with a little tweaking we could do them both at once

- the analysis tells us what copies _reach_ each program point

    - a copy `x = $copy y` reaches a program point PP iff (1) it is guaranteed to be computed along all paths to PP; and (2) neither `x` nor `y` are assigned to along any path from the copy to PP

    - if these are true then it means `x` and `y` must have the same value at PP and so we are allowed to replace `x` with `y`, making the copy `x = $copy y` obsolete

- the transformation is trivial given the analysis: for each instruction that uses a variable `x`, if the analysis says that a copy `x = $copy y` reaches that point then we replace `x` with `y`

- as before, to fill in the DFA framework, we need to:

    - define an abstract store datatype

    - define a join operation on abstract stores

    - define the initial abstract store

    - define the bottom abstract store

    - for each `inst` in {`$copy`, `$arith`, `$cmp`}, define `F_inst`

- then we just apply the DFA worklist algorithm and we have our analysis

- the abstract store datatype

    - the invariants we're interested in are `"x = $copy y" reaches this point` (where "reaches" means what we specified above)

    - note that we only care when the operand is a variable; if it were a constant then it would have been caught by constant propagation

    - so our abstract store will be a set of copies that reach the current program point

- joining abstract stores

    - given two abstract stores, we need to compute a new abstract store that is guaranteed to be correct no matter which input store is "true"

    - this is just set intersection, i.e., a copy is guaranteed to reach this point if it reached it in both input stores

- initial abstract store

    - at the entry to the function we haven't computed any copies yet

    - `init` = `{}`

- bottom abstract store

    - we optimistically set `bot` = the set of all copies in the function

    - this is safe because the analysis will remove copies as necessary (because join is set intersection)

- `x = $copy op`

    - update `store` to remove any copy to or from `x` (note the _or from_ part)

    - if `op` = `y`, update `store` to include this copy

- `x = $arith <aop> op1 op2`

    - update `store` to remove any copy to or from `x`

- `x = $cmp <rop> op1 op2`

    - same as `$arith`

- given the computed copies, we can trivially transform the program to use the copies where possible

- example/exercise [see OneNote]

```
fn foo(a:int, b:int, c:int) -> int {
  let x:int, y:int, z:int

  entry:
    x = $copy a
    y = $copy b
    z = $copy c
    $branch a tt exit

  tt:
    y = $arith add x y
    a = $copy y
    b = $cmp eq a z
    y = $copy b
    $jump exit

  exit:
    x = $arith add y z
    $ret x
}
```

## order of optimizations

- the last thing to mention is that the _order_ of optimizations can make a big difference in how effective they are

- we saw for GCSE that our optimization left a lot of copy assignments and dead variables, which can then be cleaned up by other optimizations like copy propagation and dead code elimination; it turns out that many optimizations modify the code in such a way that they enable other optimizations, and this can even be cyclic (A enables B which enables A again)

- so what's the best order? this isn't an easy question to answer, and heavily depends on the code being optimized

    - the optimization levels in a compiler such as `gcc` or `clang` (`-O1`, `-O2`, `-O3`, etc) are just convenient flags that specify a series of optimizations determined by the compiler developers to "usually" do a pretty good job
    
    - you (the compiler user) can actually specify the exact set of optimizations that you want to apply and what order you want to apply them in, and if you do it right you can often get even faster code than by using the default compiler flags
    
    - however, figuring out what optimizations to use in what order can take a lot of trial and error, and you'll never know whether you found the best possible ordering

# register allocation [NOTE: SKIPPED FOR TIME]
## intro

- our naive codegen strategy only uses registers as temporaries: for each instruction we read from memory to registers, did the operation, then stored the result back to memory

- memory access is _extremely_ slow compared to registers, thousands of cycles vs a single cycle

    - this is more of a problem for compute-bound programs; if the program is already waiting on other things (e.g., I/O, like the network or disk) then memory access times aren't as important

- there are a lot of microarchitectural optimizations aimed at mitigating this problem, like caches, pipelining, out of order execution, speculative execution, etc; still, the compiler can do a lot to help (especially for simpler architectures that don't have all those optimizations)

- our goal should be to go to memory as little as possible and to use registers as much as possible

    - ideally, every variable should be in its own dedicated register instead of on the stack and we only go to memory when absolutely required (e.g., for a load or store instruction)

    - problem: there are arbitrarily many variables and only a fixed number of registers

    - if all the variables fit in registers then things are relatively easy (we still need to worry about caller- and callee-save registers), but if not then we have to figure out some way to map variables to registers, hopefully so that memory accesses are minimized

- this is the problem of _register allocation_: within a function, assigning variables to registers to avoid memory access (assignment may vary over the course of the function)

    - there is such a thing as interprocedural register allocation (i.e., over all functions not just within a single function), but that's generally too expensive for compilation

    - there are also register allocation schemes that only operate at the level of a single basic block rather than the whole function; these were more common in olden times and may still be used in some places, but aren't really common today
    
    - single-function register allocation is the norm

    - note that i did not specify that register allocation needs to find the _optimal_ assignment of variables to registers, just _some_ assignment; i'll talk more about that later

- register allocation has been studied for decades and there are tons of variants and optimizations; we'll be looking at the basics

## examples TODO: [need to update code examples to cflat v0.2]
### easy case [see OneNote]

- [number of variable <= number of registers]

- LIR

```
fn main() -> int {
  let x:int, i:int

  entry:
    x = $copy 10
    $jump hdr

  hdr:
    i = $cmp neq x 1
    $branch i body exit

  body:
    x = $arith sub x 1
    $jump hdr

  exit:
    $ret x
}
```

- NAIVE

```
.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $16, %rsp
  movq $0, -8(%rbp)  // i
  movq $0, -16(%rbp) // x
  jmp main_entry

main_entry:
  movq $10, -16(%rbp)
  jmp main_hdr

main_hdr:
  cmpq $0, -16(%rbp)
  movq $0, %r8
  setne %r8b
  movq %r8, -8(%rbp)
  cmpq $0, -8(%rbp)
  jne main_body
  jmp main_exit

main_body:
  movq -16(%rbp), %r8
  subq $1, %r8
  movq %r8, -16(%rbp)
  jmp main_hdr

main_exit:
  movq -16(%rbp), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

- OPTIMIZED (assuming 2 available registers)

```
.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  movq $0 %r9     // i
  movq $0 %r10    // x
  jmp main_entry

main_entry:
  movq $10, %r9
  jmp main_hdr

main_hdr:
  cmpq $1, %r9
  movq $0, %r8
  setne %r8b
  cmpq $0, %r8
  jne main_body
  jmp main_exit

main_body:
  subq $1, %r9
  jmp main_hdr

main_exit:
  movq %r9, %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

### non-easy case 1 [see OneNote]

- [number of variable > number of registers, but non-overlapping lifetimes]

- LIR

```
fn main() -> int {
  let x:int, y:int, i:int

  entry:
    x = $copy 10
    $jump hdr1

  hdr1:
    i = $cmp neq x 1
    $branch i body1 next

  body1:
    x = $arith sub x 1
    $jump hdr1

  next:
    y = $copy 1
    $jump hdr2

  hdr2:
    i = $cmp neq y 10
    $branch i body2 exit

  body2:
    y = $arith add y 1
    $jump hdr2

  exit:
    $ret y
}
```

- NAIVE

```
.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $32, %rsp
  movq $0, -8(%rbp)   // i
  movq $0, -16(%rbp)  // x
  movq $0, -24(%rbp)  // y
  jmp main_entry

main_entry:
  movq $10, -16(%rbp)
  jmp main_hdr1

main_hdr1:
  cmpq $0, -16(%rbp)
  movq $0, %r8
  setne %r8b
  movq %r8, -8(%rbp)
  cmpq $0, -8(%rbp)
  jne main_body1
  jmp main_next

main_body1:
  movq -16(%rbp), %r8
  subq $1, %r8
  movq %r8, -16(%rbp)
  jmp main_hdr2

main_next:
  movq $1, -24(%rbp)
  jmp main_hdr2

main_hdr2:
  cmpq $10, -24(%rbp)
  movq $0, %r8
  setne %r8b
  movq %r8, -8(%rbp)
  cmpq $0, -8(%rbp)
  jne main_body2
  jmp main_exit

main_body2:
  movq -24(%rbp), %r8
  addq $1, %r8
  movq %r8, -24(%rbp)
  jmp main_hdr2

main_exit:
  movq -24(%rbp), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

- OPTIMIZED (assuming 2 available registers)

```
.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  movq $0 %r9     // i
  movq $0 %r10    // x, then y
  jmp main_entry

main_entry:
  movq $10, %r9
  jmp main_hdr1

main_hdr1:
  cmpq $1, %r9
  movq $0, %r8
  setne %r8b
  cmpq $0, %r8
  jne main_body2
  jmp main_next

main_body1:
  subq $1, %r9
  jmp main_hdr1

main_next:
  movq $1, %r9
  jmp main_hdr2

main_hdr2:
  cmpq $10, %r9
  movq $0, %r8
  setne %r8b
  cmpq $0, %r8
  jne main_body2
  jmp main_exit

main_body2:
  addq $1, %r9
  jmp main_hdr2

main_exit:
  movq %r9, %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

### non-easy case 2 [see OneNote]

- [number of variable > number of registers, overlapping lifetimes]

- LIR

```
fn main() -> int {
  let x:int, y:int, i:int

  entry:
    x = $copy 10
    $jump hdr1

  hdr1:
    i = $cmp neq x 1
    $branch i body1 next

  body1:
    x = $arith sub x 1
    $jump hdr1

  next:
    y = $copy 1
    $jump hdr2

  hdr2:
    i = $cmp neq y 10
    $branch i body2 exit

  body2:
    y = $arith add y 1
    $jump hdr2

  exit:
    x = $arith add x y
    $ret x
}
```

- NAIVE

```
.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $32, %rsp
  movq $0, -8(%rbp)   // i
  movq $0, -16(%rbp)  // x
  movq $0, -24(%rbp)  // y
  jmp main_entry

main_entry:
  movq $10, -16(%rbp)
  jmp main_hdr1

main_hdr1:
  cmpq $0, -16(%rbp)
  movq $0, %r8
  setne %r8b
  movq %r8, -8(%rbp)
  cmpq $0, -8(%rbp)
  jne main_body1
  jmp main_next

main_body1:
  movq -16(%rbp), %r8
  subq $1, %r8
  movq %r8, -16(%rbp)
  jmp main_hdr2

main_next:
  movq $1, -24(%rbp)
  jmp main_hdr2

main_hdr2:
  cmpq $10, -24(%rbp)
  movq $0, %r8
  setne %r8b
  movq %r8, -8(%rbp)
  cmpq $0, -8(%rbp)
  jne main_body2
  jmp main_exit

main_body2:
  movq -24(%rbp), %r8
  addq $1, %r8
  movq %r8, -24(%rbp)
  jmp main_hdr2

main_exit:
  movq -16(%rpb), %r8
  addq -24(%rbp), %r8
  movq %r8, -16(%rbp)
  movq -16(%rbp), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

- OPTIMIZED

```
.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $16, %rsp  // space for x
  movq $0 %r9     // i
  movq $0 %r10    // x, then y
  jmp main_entry

main_entry:
  movq $10, %r9
  jmp main_hdr1

main_hdr1:
  cmpq $1, %r9
  movq $0, %r8
  setne %r8b
  cmpq $0, %r8
  jne main_body2
  jmp main_next

main_body1:
  subq $1, %r9
  jmp main_hdr1

main_next:
  movq %r9, -8(%rbp) // spill x
  movq $1, %r9
  jmp main_hdr2

main_hdr2:
  cmpq $10, %r9
  movq $0, %r8
  setne %r8b
  cmpq $0, %r8
  jne main_body2
  jmp main_exit

main_body2:
  addq $1, %r9
  jmp main_hdr2

main_exit:
  addq -8(%rbp), %r9
  movq %r9, %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

## general strategy NOTE: [assumes we've covered liveness analysis]

- how do we do this kind of assignment automatically?

    - computing an optimal solution turns out to be NP-hard, so we need to approximate

    - trade off performance of register allocation vs performance of resulting code (performance of register allocation matters, e.g., in a JIT compiler)

    - the basic idea is always the same, the details vary a lot

- do codegen, but optimistically assume there is a register for each variable in the function (so-called "pseudo-registers")

    - we can name these pseudo-registers the same as the variable they correspond to, so essentially a variable and a pseudo-register are the same thing

- perform some variation of liveness analysis (as we saw previously for dead code elimination)

    - for each program point a variable (pseudo-register) is live or dead

    - from this we can extract the _live ranges_ of a variable: a live range reaches from where a variable is defined to all the places where that definition is used

- two variables _interfere_ if the intersection of their live ranges is non-empty

    - that is, their values are needed at the same time

    - such variables cannot share a register during the interfering ranges, so they must be in separate registers

- for each live range, if the set of interfering variables is greater than the set of available registers we must pick some variable to _spill_

    - to spill a register means to write it out to memory, so the next time it's used it must be read back from memory

- for each live range we now have an assignment of variables to registers (with some variables possibly spilled so they aren't kept in registers)

- the various register allocation strategies differ in how exactly they compute the live ranges, how precise those live ranges are, and how carefully they select which variables to spill in order to minimize memory traffic

    - we'll cover one (very well-known and popular) strategy: _chaitin's graph coloring_ register allocation

## computing live ranges

- we use a similar liveness analysis here as we did for dead code elimination, but on the x86 pseudo-assembly instead of LIR

- we could compute live ranges in terms of individual instructions, but for brevity (and performance) we'll use entire blocks

    - a block defines a variable if it is defined by any instruction in the block

    - a block uses a variable if it is used by any instruction in the block

- example (draw as CFG)

```
[1: def a]
    ↓
[2: def c]
    ↓
[3: use c] ⟶ [8: def a] ⟶ [9: def b] ⟶ [10: use a,b; def c] ⟶ [11: use a,b,c]
    ↓                                                            ╱      ⇅
[4: use a]                                                      ╱ [12: use c; def c]
    ↓      ╲                                                   ╱
[5: def c]  [6: def c]                                        ╱
         ╲ ╱                                                 ╱
      [7: use c] ⟵-----------------------------------------+
```

```
variable | live ranges
---------+---------------------------------
       a | {1, 2, 3, 4}, {8, 9, 10, 11, 12}
       b | {9, 10, 11, 12}
       c | {2, 3}, {5, 7}, {6, 7}, {7, 10, 11, 12}, {7, 11, 12}
```

- if a variable's live ranges overlap then they need to be merged (the same variable can't be in two different registers at the same time)

```
variable | live ranges
---------+---------------------------------
       a | {1, 2, 3, 4}, {8, 9, 10, 11, 12}
       b | {9, 10, 11, 12}
       c | {2, 3}, {5, 6, 7, 10, 11, 12}
```

## graph coloring

- we need to assign variables to registers based on their live ranges

- chaitin observed that we can reduce this to the problem of _graph coloring_: given an undirected graph and a set of colors, color each node of the graph so that no edge connects nodes of the same color

    - this is an NP-hard problem

- for our purpose, the graph is the _interference graph_

    - a node for each live range

    - an edge between two live ranges if their intersection is non-empty

- example

```
[a1] -- [c1]

[a2] -- [c2]
   ╲   ╱
    [b]
```

- we have a color for each available register, i.e., "coloring a node" means to assign a register to a live range

- since coloring the graph is NP-hard we rely on a heuristic to approximate the solution (it is guaranteed to be correct, but not guaranteed to be optimal)

    - we will use a stack of nodes, initially empty

    - for a node `n`, let `push n` mean to remove node `n` from the graph and push it onto the stack

- suppose we have `k` colors, then:

    1. repeat as long as possible: pick a node `n` with fewer than `k` edges and push `n`

        - this node is guaranteed to be colorable: just pick a different color than its beighbors (so we need to wait until we know its neighbors' colors)

    2. if there are still nodes in the graph:

        - pick some node `n`, randomly or based on a heuristic cost function (to try to pick a "good" node) and push `n`

        - go to step 1

    3. pop off each node in turn from the stack:

        - if there is some color `c` its neighbors don't have, color it `c`

        - otherwise leave it uncolored

- example [suppose we have 2 available registers R1 and R2]

```
push a1
push c1
push a2
push b
push c2
--------------------
pop c2: R1
pop  b: R2
pop a2: [uncolored]
pop c1: R1
pop a1: R2
```

- any "colored" live range means that we can put the corresponding variable in that register over that range

- any "uncolored" live range means that we need to spill that variable over that range, i.e., we'll have to read it from memory and write it back to memory during that range

## linear scan TODO: [FILL ME IN]

# course recap

- at this point you know the basics of how to implement a fully-functional compiler for a C-like language

    - note that languages with more features can be translated down into something like cflat or LIR, e.g., the original C++ compiler actually translated C++ programs into C programs and used a C compiler

- there are still many things left to explore about compiler development in the front-, middle-, and back-ends

    - different parsing strategies and handling more complex grammars

    - more powerful and complicated type systems

    - more LIR optimizations

    - targeting more architectures, doing back-end optimizations (peephole, instruction selection, etc)

- an interesting challenge: implement a self-hosting cflat compiler

    - that is, a cflat compiler written in cflat
