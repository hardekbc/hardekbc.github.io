# Fall 2025 notes

## TODO:

- TA duties form
- close section (need to move students)
- set up course slack, invite students
- set office hours (mine, TAs)
- plan assignments
- create AI info form for assignments
- in assignment writeup, tell them to use addresssanitizer
    - https://github.com/google/sanitizers/wiki/AddressSanitizer (the given flags for clang, but they're the same for gcc)

## lecture timing

- week 0.2 (quarter starts on thursday): 
- week 1.1: 
- week 1.2: 
- week 2.1: 
- week 2.2: 
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

## TODO: assignment timing

- week ??? assign-1 (lexing and parsing) [??? days]
- week ??? assign-2 (type checking) [??? days]
- week ??? assign-3 (lowering) [??? days]
- week ??? assign-4 (codegen) [??? days]
- week ??? assign-5 (gc) [??? days]

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
