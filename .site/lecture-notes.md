# Spring 2024 notes
## TODO:

- remember to run plagiarism detection on all assignments after the deadline (and after late assignments are turned in); just use the built-in gradescope detector

- need to create test suites and autograder for codegen assignment

- need to create test suites and autograder for register allocation assignment

- need to create test suites and autograder for optimization assignment

## lecture timing

- week  1.1: through `overview` -> `putting it all together`
- week  1.2: through `lexing` (projector broken, had to improvise)
- week  2.1: through `parsing` -> `parsing strategies`
- week  2.2: through `parsing` -> `formalizing LL(1)`
- week  3.1: through `parsing`
- week  3.2: `validation` except the example and discussing design space
- week  4.1: through `validation`
- week  4.2: partway through `lowering.pdf` (the setup, but not function bodies or building CFG)
- week  5.1: through `lowering` _except_ final example
- week  5.2: through `codegen` -> `necessary context`
- week  6.1: 
- week  6.2: 
- week  7.1: 
- week  7.2: 
- week  8.1: 
- week  8.2: 
- week  9.1: 
- week  9.2: 
- week 10.1: 
- week 10.2: 

## assignment timing

[NEW SCHEDULE AFTER EXTENDING ASSIGN-2 BY 3 DAYS]

- week 2T assign-1 (lexer) [10 days]
- week 3F assign-2 (parser/validation) [14 days]
- week 5F assign-3 (lowering) [11 days]
- week 7T assign-4 (codegen) [10 days]
- week 8F assign-5 (register allocation) [11 days]
- week 10T assign-6 (optimization) [10 days]
- finals [due Friday]

[OLD SCHEDULE]

- week 2T assign-1 (lexer) [10 days]
- week 3F assign-2 (parser/validation) [11 days] [could move validation to assign-3]
- week 5T assign-3 (lowering) [10 days]
- week 6F assign-4 (codegen) [11 days]
- week 8T assign-5 (register allocation) [10 days]
- week 9F assign-6 (optimization) [11 days]
- finals [due tuesday]

## grading scale

| percentage | letter grade |
|------------|--------------|
| 97--100    | A+           |
| 93--96     | A            |
| 90--92     | A-           |
| 87--89     | B+           |
| 83--86     | B            |
| 80--82     | B-           |
| 77--79     | C+           |
| 73--76     | C            |
| 70--72     | C-           |
| 67--69     | D+           |
| 63--66     | D            |
| 60--62     | D-           |
| 00--59     | F            |

## code

- see farrago/cflat repo under the 160-s24 branch for the implementation we used for this class

## notes

- tell the students to use AddressSanitizer when compiling their code, it will help catch memory errors (especially useful when their code seems to work fine locally but segfaults on gradescope)

    - https://github.com/google/sanitizers/wiki/AddressSanitizer (the give flags for clang, but they're the same for gcc)

- autograder `grader.py`: for some reason when student code segfaults it doesn't register as an abnormal exit and so the message the students get is "wrong output" instead of "abnormal exit" (see function `Run`)

    - this is may be because of the way we wrap the command in `RunAsStudent` to make it ran as the student user instead of root; however when we change the command to add `echo $? > status;` immediately after running the student program, the output status is `0` even though a coredump file is produced

    - i've introduced a potential fix (adding `set -e` to the command used by RunAsStudent), but i'm not sure if it works

- students get confused by the output on a failure: we give the total number of tests passed out of the total number of tests, and they think it gives the actual test number (even though we give them the name of the test in the same message)---this is only a problem because that's how they identify the test in their communications to us which is confusing; maybe we can tweak the failure message to make it more clear?

- assign-1:

    - `lex_gen.rs` doesn't generate any numbers larger than an i32 can hold, and so the student tests don't test that they can match arbitrarily long digits; also it generates random ids that could potentially include keywords (but probably won't, and so is missing testing that case for maximal munch) and could possibly _be_ a keyword (which would result in an incorrect expected result---not a problem for the student test cases because i used my own lex result instead, but an issue nonetheless)

        - FIXED in 160-s24 branch; need to regenerate student tests for assign-1

    - some of the students aren't actually using an NFA data structure, instead they're just, in a loop, trying each possible lexeme description in turn using string operations until they find one that works (and backtracking if they try one that doesn't work); this will give the correct answer if they try them in the correct order for maximal munch and prioritization and/or insert appropriate checks but is asymptotically less efficient---is there a way to test for this behavior and prevent it?

        - not really, this is basically a poor implementation of a backtracking NFA; maybe have them actually print out the NFA and compare with mine? at least this makes sure they've figured the NFA out correctly even if they don't use it, and if they have it why wouldn't they use it

    - a number of students are encountering an issue where copy-pasting the gradescope output for a failed test yields a test that they pass locally; this is because of special characters that don't get copied properly by the copy-paste.

        - i think for testing `\r`, `\t`, etc there isn't a way around this, but otherwise FIXED in 160-s24 branch; need to regenerate students tests for assign-1

- assign-2:

    - for the "syntactically incorrect" test suite (suite-3) there is no source file to give them due to the way we generate the input, which means we output a list of tokens on test failure; however apparently gradescope truncates output that is too long---either fix this somehow (maybe gradescope is counting number of lines and we can use spaces to separate tokens instead?) or figure out how to map the invalid token list back to source-level so we can output that

    - a more minor issue is that the output ASTs are compared by eliminating whitespace (because diff can't ignore newlines) and saying whether the resulting strings are different, but this makes it difficult to pinpoint where the student output is different from the solution when students are debugging---is there a better way to do this? maybe don't ignore newlines (and thus enable using diff), and just be very regular about where the newlines are so the students can follow easily

    - we give the students the cflat LL(1) grammar instead of making them derive it, mostly because they already need to figure out how to produce an AST and also implement the typing rules so deriving the grammar seemed too much; still this means they never actually have to transform a grammar

        - we could give them a separate quiz about transforming grammars to LL(1), online and automatically graded

        - not this time, but think about it for next time (and maybe use those quizzes for other concepts as well, just to give students practice with automated feedback); the quizzes don't have to count as a grade either

    - since test cases are generated as ASTs and then printed out as source code, the test cases have more regular syntax than the grammar allows---for example, toplevel constructs will always be in the same order, every function will have at most one let, and arithmetic ops are all parenthesized to make precedence explicit; to allow for testing the student parsers more thoroughly it would be good to create a printer that randomizes these things

    - doing parsing _and_ validation may be too much...possibly it would be easier to do validation + lowering for assign-3? it all depends which of parsing and lowering are easier; maybe ask the students (once they've done assign-3)

        - i had to extend the deadline for assign-2 by three days; doing parsing + validation is definitely too much for a regular assignment

        - it isn't clear whether i can just move validation be with lowering next time, or if validation needs to be an assignment of its own (which would have to replace some other assignment, likely register allocation)

    - the test suites aren't very good because they don't break things up in a way that allows for incremental implementation by the students---each test suite requires everything to have been implemented; next time break it up by language feature like the other assignments

# logistics

- introduce myself and TAs

    - hitomi
    - thanawat
    - jeffrey
    - josiah

- class slack:

    - if registered, should have gotten invite; if need invite, email me

    - all communication goes through slack: announcements, assignments, questions, etc

    - be sure to stay on top of slack notifications

- if you are trying to register for the class, see the CS advising staff (not me)

    - i have told them to take as many people as possible given constraints like number of TAs

    - they are responsible for managing the waitlist

- office hours:

    - mine are after every lecture (unlimited for T, 30 minutes for R)

    - TA office hours to be announced (mix of in-person and remote)

- discussion sections: no new material; review, give examples and exercises, answer questions

- assessment:

    - no exams

    - 6 projects (going into finals week)

        - 1--3 person teams
        - weighted evenly, must do all 6
        - late days allowed, but with grade penalty (-11% for one week late, -31% if before end of quarter)
        - one assignment can be turned in late with no penalty (automatically whichever helps you most)
        - this late penalty scheme is intended to give all the flexibility you need, please don't ask for more late days

    - projects in C++, done from scratch---no skeleton code given

    - if you're using an online repo (e.g., github) make sure it's private! sharing your code publically is considered a violation of academic integrity (as is using code that others may have shared)---projects should be your own work

    - important: this is a work-intensive course that requires a _lot_ of coding---be prepared! don't take other heavy-workload courses at the same time (like 170)

- expected prereq knowledge:

    - programming in C++

    - 138: REs, NFAs, PDAs, CFGs

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

- approach 1: compilers

    - take source code and translate it into machine code (e.g., gcc or clang)

    - we call this 'ahead of time' because it happens before the program is ever executed

- approach 2: interpreters

    - take source code and have an interpreter take each statement and expression and implicity translate it into machine code on the fly (e.g., cpython for python)

    - it's implicit because the mapping from source code to machine code isn't present explicitly in the interpreter---an interpreter for language A is itself written in some source language B, and it looks at the source code for A and implements it in terms of B

    - but how was the interpreter itself made executable? a compiler!

    - interpreting source code is much slower than executing compiled source code because both the interperter and the source code are executing at the same time; on the other hand, interpreters don't need to wait for compilation to start executing source code so they are faster to use in a "write-execute" cycle

- approach 3: hybrid

    - use an interpreter, but during execution _also_ in parallel compile code to machine instructions (e.g., v8 engine for javascript)

    - this is called 'just in time' compilation, or JIT compilation for short

    - get the fast startup time of interpreters but still a good part of the performance of compiled code (though still slower than ahead-of-time compilation)

- some language implementations do all three, like Java and any other language that targets the Java Virtual Machine (JVM), or .NET languages that target the .NET runtime

    - ahead of time compilation to java bytecode

    - bytecode interpreted by JVM

    - JVM also JIT compiles the bytecode for better performance

- it's important to note that _languages_ are not compiled or interpreted---a language is _implemented_ with a compiler or interpreter

    - there are interpreters for C and C++
    - there are compilers for python

- notice that in all these schemes compilers play an important role, even if it's behind the scenes somewhere

- programming languages are how humans express computation: we can't compute without them, and we can't make them work without compilers---as computer scientists and/or developers, there shouldn't be any magic here

- goals for this course:

    - dispel the "magic" and get a basic understanding of how source code turns into executable code

    - not just relevant to compilers for mainstream programming languages like C++, etc: parsing log or configuration files, creating domain-specific languages, etc

    - show how some of the computational theory from 138 has practical applications

    - give experience writing (relatively) large and complex software from scratch

        - still much smaller and simpler than mainstream compilers that have had hundreds of people working on them for many years

        - you won't be a compiler expert after this course, but you'll know enough to have an idea of how compilers work and a foundation you can use to learn more if you want

# overview
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

- the tokenizer recognizes that, e.g., "let" is a keyword instead of a variable, that "sum" is a variable, that "10" is a number, etc

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

- the primary job of the back end is to translate the IR into whatever ISA we're targeting (we'll be targeting x86 in this class)

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

## putting it all together

```
[source code] --LEXER--> [token stream] --PARSER--> [AST] --LOWERING--> [LIR] --CODEGEN--> [x86]
                                                      ⇵                  ⇵                  ⇵
                                                 VALIDATION         OPTIMIZATION       REGISTER ALLOC.
```

## what we'll be doing

- we will be covering all these pieces, and you will be implementing them for your projects

    - lexer
    - parser/validation
    - lowering
    - codegen
    - register allocation
    - optimization

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

- of course, for real compilers we want _all_ of these things, and building a real compiler for a language you don't control is very difficult

# introducing cflat

- [show example programs] [see OneNote]

    - see `examples/cflat/linked_list.cb`
    - see `examples/cflat/matrices.cb`

- [show ambiguous grammar] [see OneNote]

    - see `docs/grammar-1.md`

# lexing

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

- our tokens:

```
TOKEN        REGEX
---------------------------------------------
Num(info)    [0-9]+
Id(info)     [a-zA-Z][a-zA-Z0-9]*
Int          int
Struct       struct
Nil          nil
Break        break
Continue     continue
Return       return
If           if
Else         else
While        while
New          new
Let          let
Extern       extern
Fn           fn
Address      &
Colon        :
Semicolon    ;
Comma        ,
Underscore   _
Arrow        ->
Plus         +
Dash         -
Star         *
Slash        /
Equal        ==
NotEq        !=
Lt           <
Lte          <=
Gt           >
Gte          >=
Dot          .
Gets         =
OpenParen    (
CloseParen   )
OpenBracket  [
CloseBracket ]
OpenBrace    {
CloseBrace   }
```

- we also need to specify what whitespace and comments look like (we cheated a bit in the grammar by using `-` in the c-comment regex, this is technically regular but can be very expensive to compute)

```
whitespace = (' ' | '\t' | '\n' | '\r')+

c++-comment = //[^'\n']*
-- notice we don't require '\n' at the end, which handles comments that go to
   EOF without terminating

c-comment = /*([^*] | *⋆[^*/])⋆*+/
-- using ⋆ for kleene star and * for the literal asterisk character

unclosed-c-comment = /*([^*] | (*+[^*/]))⋆*⋆
-- similarly we need to handle comments that go to EOF without terminating

skip = (whitespace | c++-comment | c-comment)* (unclosed-c-comment | ϵ)
```

- notice that some tokens should carry extra information (the lexer and parser don't actually care about this extra info, but we'll need it for the rest of the compiler)

    - if we have a Num, what were the digits? if we have an Id, what were the characters?

    - we can represent this information as indices in the input string

- now we know what our tokens are and what they look like; the next step is to map the actual characters in the input stream to a sequence of tokens

    - the sequence of characters that make up a token is called a _lexeme_

- example: "foo = bar + 01"

```
lexeme token
------ -----
foo    Id(foo)
=      Gets
bar    Id(bar)
+      Plus
01     Num(01)
```

- we know how to recognize regular languages: convert a regular expression to an NFA, then simulate the NFA on some input

    - simulating an NFA was covered in 138, now you need to implement it in code

    - you could convert the regular expressions to NFA manually, but it's tedious and error-prone; i'd recommend implementing it in code (but it's up to you)

- EXAMPLE [see OneNote]

```
RE
| EmptyLang
| EmptyString
| CharRange { lower: char, upper: char } // e.g., [a-z]
| Star { r: RE }
| Concat { first: RE, second: RE }
| Union { left: RE, right: RE }

// for regex "/*([^*] | *⋆[^*/])⋆*+/":
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

- final thoughts:

    - lexing can be easy if we define our syntax carefully, but full of pitfalls if we're careless

    - in general it's good to reduce ambiguity as much as possible; which token a lexeme corresponds to shouldn't depend on what context it's used in

        - famous C++ example: `vector<set<int>>` is a syntax error because `>>` is read as a bitshift operator, you need to write `vector<set<int> >`

    - why regular expressions, why not contex-free grammars?

        - we can use CFGs (they can do anything REs can do), and there are examples where people have done so (see "scannerless parsers")

        - in general lexing using regular expressions makes the parser a lot simpler and more efficient

        - it does also explain why you can't nest c-style comments: proper nesting is context-free, you can't describe it using regular expressions

# parsing
## overview

- we describe the syntax of our language as a context-free grammar, with tokens as the terminal symbols of our alphabet

    - [show `docs/grammar-1.md` again]
    - [show `docs/grammar-2.md` with regex productions]

- given a CFG and a string, we can prove that the string is a member of the CFG's language using a derivation tree (aka parse tree, concrete syntax tree)

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

- EXERCISE: using the same grammar, give a derivation tree for the input `x + y + x * z + z`

    - there is more than one possible derivation

- this is the essence of parsing: proving that an input belongs to the language by constructing a derivation tree

    - as discussed previously we rarely actually construct the entire derivation tree explicitly, instead we construct an abstract syntax tree that is more useful for compilation

    - however, IDEs _do_ construct the derivation tree so that they can do things like syntax highlighting, etc

## ambiguity

- the biggest problem we face showed up in the last exercise, and is the same thing we had to deal with in lexing: ambiguity

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

- besides top-down vs bottom-up, parsing strategies can be divided by what class of grammars they work for

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

- the key is that by looking at the next token we _always_ know exactly what rule we should be trying to match (and if the next token isn't one we expect then the input is not syntactically correct)

- a grammar s.t. we can use `k` tokens of lookahead to make it completely deterministic is an LL(k) grammar

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

    - `afgbcdb`
    - `daceagbc`

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

- it's called the _abstract_ syntax tree because it abstracts out the (now that parsing is over) unimportant information from the concrete parse tree

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

    - see `docs/ast.md`

# validation

- just because a program is syntactically correct doesn't mean that it's a valid program

    - remember the example from before:

```
let x:int = 0, y:int = 10;
x = new int;
y = x * y;
```

- we need to make sure that the program "makes sense" before we continue trying to compile it

    - after this step, we can assume that the program we're compiling is valid for whatever we define "valid" to mean

    - validity is determined by the language designer

- there are often some simple rules that are easy to check, but the biggest aspect of validation is usually _type checking_ (at least, for statically typed languages like cflat)

    - a type describes a set of possible values (e.g., `int` describes the set of integers, `&int` describes the set of pointers to integers, etc)

    - if we give an expression a type, that means that, when evaluated during program execution, that expression's value will belong to the set described by that type

    - an operation is _well-typed_ if it always makes sense for all values described by the types of its operands

        - e.g., `x * y` is well-typed iff `x` and `y` are type `int`, because multiplication only makes sense for integers (not, e.g., pointers)

    - a static type system guarantees that well-typed programs can't "go wrong" in a well-defined sense

    - this is covered in detail in CS 162, so i won't spend much time on it here

- a language's _type system_ is defined by:

    - the set of types the language recognizes

    - the typing rules that describe how to assign types to parts of a program (i.e., AST nodes)

- the typing rules consist of:

    - _typing judgements_, that state that a particular typing fact holds

    - _inference rules_, that state how to make typing judgements

- there is a very strong connection between type systems and formal logic

    - judgements and inference rules come from the _natural deduction_ style of formal logic

    - the _curry-howard correspondance_ states that types are propositions and programs are proofs; from this perspective a type checker is a proof verifier

- [go over `docs/validation.pdf`]

    - [remind them what the AST data structure looks like]

    - [explain how the rules ensure only "sensible" operations happen]

- EXAMPLE: [see OneNote]

```
struct list {
  value: int,
  next: &list
}

fn main() -> int {
  let n:&list, m:&list, i:int = 3;

  n = new list;
  m = n;
  while i > 0 {
    n.next = new list;
    n.value = i;
    n = n.next;
    i = i - 1;
  }

  return aggregate(m);
}

fn aggregate(p: &list) -> int {
  let m:&list, i:int = 0;

  while m != nil {
    i = 10 * i + m.value;
    m = m.next;
  }

  return i;
}
```

```
Program(
  globals = [],
  structs = [
    Struct(
      name = list,
      fields = [
        Decl(value, Int),
        Decl(next, Ptr(list))
      ]
    )
  ],
  externs = [],
  functions = [
    Function(
      name = main,
      params = [],
      rettyp = Int,
      locals = [
        (Decl(n, Ptr(list)), _),
        (Decl(m, Ptr(list)), _),
        (Decl(i, Int), Num(3))
      ],
      stmts = [
        Assign(
          lhs = Id(n),
          rhs = New(list, Num(1))
        ),
        Assign(
          lhs = Id(m),
          rhs = Id(n)
        ),
        While(
          guard = BinOp(
            op = Gt,
            left = Id(i),
            right = Num(0)
          ),
          body = [
            Assign(
              lhs = FieldAccess(
                ptr = Id(n),
                field = next
              ),
              rhs = New(list, Num(1))
            ),
            Assign(
              lhs = FieldAccess(
                ptr = Id(n),
                field = value
              ),
              rhs = Id(i)
            ),
            Assign(
              lhs = Id(n),
              rhs = FieldAccess(
                ptr = Id(n),
                field = next
              )
            ),
            Assign(
              lhs = Id(i),
              rhs = BinOp(
                op = Sub,
                left = Id(i),
                right = Num(1)
              )
            )
          ]
        ),
        Return(
          Call(
            callee = Id(aggregate),
            args = [Id(m)]
          )
        )
      ]
    ),
    Function(
      name = aggregate,
      params = [Decl(p, Ptr(list))],
      rettyp = Int,
      locals = [
        (Decl(m, Ptr(list)), _),
        (Decl(i, Int), Num(0))
      ],
      stmts = [
        While(
          guard = BinOp(
            op = NotEq,
            left = Id(m),
            right = Nil
          ),
          body = [
            Assign(
              lhs = Id(i),
              rhs = BinOp(
                op = Add,
                left = BinOp(
                  op = Mul,
                  left = Num(10),
                  right = Id(i)
                ),
                right = FieldAccess(
                  ptr = Id(m),
                  field = value
                )
              )
            ),
            Assign(
              lhs = Id(m),
              rhs = FieldAccess(
                ptr = Id(m),
                field = next
              )
            )
          ]
        ),
        Return(
          Id(i)
        )
      ]
    )
  ]
)
```

- these typing rules can easily be turned into an executable type checker

    - note: this isn't true for all type system, just ones that meet certain conditions---which our type system does

    - [describe how the rules can be turned into recursive functions]

- cflat has a simple, static, strong, explicit type system; what does this mean?

    - simple: no polymorphism, dependent types, substructural types, etc

    - static: types are checked at compile-time

    - strong: a well-typed program cannot perform invalid operations

    - explicit: the programmer explicitly annotates type information in the code

- expressiveness:

    - determining exact program behavior is undecidable---in order to guarantee that programs don't behave badly, we must necessarily flag some programs that _wouldn't_ behave badly (example: `let x:int; if false { x = *x}`)

    - we can make type systems more and more powerful and expressive to recognize more and more programs as correct, but type checking becomes more and more expensive and complicated; we can easily move into the realm of undecidable type checking

    - besides undecidability, advanced type systems can also be complicated for programmers to use and require lots of expertise

- static vs dynamic:

    - benefits of static

        - static type systems can guarantee the absence of entire classes of runtime errors

        - potential errors are detected at compile time instead of lurking in production code waiting to be triggered by a user

        - type annotations can serve as useful code documentation (that is automatically checked by the compiler so can't go stale)

        - static type information allows the compiler to do a much better job optimizing the code and results in much faster executables

    - benefits of dynamic:

        - faster, more flexible development because the programmer doesn't need to satisfy the type checker

        - easier for the language designer because they don't need to create a type system or implement a type checker

- strong vs weak:

    - a strong type system provides guarantees (a well-typed program "can't go wrong"), a weak type system provides no guarantees (a well-typed program _can_ "go wrong")

        - C and C++ are the most well-known and widely-used languages with weak type systems

    - obviously strong guarantees are preferable, why would we use a weak type system?

        - the essential reason is _performance_, and specifically dealing with _memory_ (there are other reasons, but this is a big one)

        - garbage collection requires lots of runtime resources and causes unpredictable performance and requires high memory usage (all of this was especially true back when C and C++ were being developed; it's less true today but still true)

            - until recently, all strongly-typed languages had to use garbage collection

        - if we don't want that overhead, then (at least until recently) the only alternative was to allow the programmer to manage memory manually (e.g., `free` and `delete`)

            - but programmers often get manual memory management wrong, and that leads to memory unsafety (e.g., use-after-free errors) and that memory unsafety violates the guarantees provided by the type system

            - these errors also lead to lots of security vulnerabilities

        - why use a type system at all, then? again because of performance: having static types lets the compiler do a much better job of optimizing, and performance was the whole reason behind the weak type system anyway

    - recently a third alternative has arisen: linear type systems

        - the idea isn't that recent, it's been in academic papers for decades

        - but `rust` is the first mainstream language to adopt the idea and put it into practice

        - linear types allow for automatic memory management _without_ using a garbage collector; this means that programmers aren't allowed to make mistakes and hence the type system can be strong

- explicit vs implicit:

    - some people conflate static typing with explicit typing because that's all they're familiar with, but it isn't true

    - languages with implicit types allow the programmer to omit type annotations, so the program looks just like a dynamically-typed one; the compiler must then _infer_ what the types should be in order to perform type checking

        - obviously the more expressive the type system the more difficult and expensive this process is

        - however, it also really helps with the complexity of advanved type systems

    - a number of more modern languages take a middle-point, where the programmer must annotate _some_ type information (e.g., the types of function parameters and function return values) but the rest can be inferred by the compiler

# front-end recap

- let's review the steps we needed to go through to define our compiler front-end

- given: a grammar for the concrete syntax of the language

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

# lowering

- now that we have a valid AST we could directly generate ISA code from it, but most modern compilers don't do that---instead, they _lower_ the AST into a simpler, lower-level intermediate representation (LIR)

    - LIR is essentially ISA-agnostic assembly, which is particulary useful if the compiler has multiple backends---a lot of compilation work can be implemented once, for LIR, and then we only need to do individual codegen for each backend from the LIR

    - LIR makes the code simpler and more explicit (e.g., the order of evaluation and the flow of control); each instruction does just one thing

    - LIR is better for optimization because it's easier to reason about and transform

- there are various forms of IR, but a common one (and the one we'll use) is _three-address code_ IR

    - no instruction will have more than three "addresses" (think "variables/constants")

- example of how LIR simplifies the AST code and makes evaluation order explicit (using more human-readable notation)

    - instead of a deeply-nested tree of expressions, we end up with a flat sequence of simple instructions

    - [see OneNote]

```
[source]
*x = foo(y+z+1, z*3).fld

[AST]
Assign(
    Deref(Id(x)), 
    RhsExp(
        FieldAccess(
            ptr: Call(
                callee: Id(foo),
                args: [
                    Add(Add(Id(y), Id(z)), Num(1)),
                    Mul(Id(z), Num(3))
                ]
            )
            field: fld
        )
    )
)

[LIR]
_t1 = $arith add y z
_t2 = $arith add _t1 1
_t3 = $arith mul z 3
_t4 = $call_ext foo(_t2, _t3)
_t5 = $gfp _t4 fld
_t6 = $load _t5
$store x _t6
```

- our LIR is in the form of a _control-flow graph_ (CFG)

    - the abbreviation is an unfortunate pun, this is _not_ "CFG" meaning "context-free grammar"

    - a CFG is an explicit representation of the possible execution paths in a program---that is, when a program is executed, what are the possible sequences of instructions it may execute?

    - a CFG is composed of nodes called _basic blocks_ (a labeled sequence of instructions) connected by edges representing control-flow between blocks

    - a basic block is a linear sequence of instructions s.t. (1) it can only be entered at the beginning, and (2) it can only be left at the end

        - i.e., there is no way to jump into or out of the middle of a basic block: you have to enter at the first instruction and can only leave after the last instruction

        - every basic block has a label, which is used to target that basic block when transferring control from some other basic block

        - every basic block ends in a _terminal_ instruction that says what basic block to go to next

    - every function has a single _entry_ basic block and a single _exit_ basic block (containing the sole `$return` instruction)

        - we always start executing the function at the entry block and leave the function after the exit block

- [introduce LIR data structure] [see OneNote]

    - [explain the LIR instructions]

    - i separate LirInst from Terminal to make clear that only certain things can go at the end of basic blocks; your implementation doesn't need to differentiate

- example 1 (draw graph) [see OneNote]

```
[source]
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

[AST]
locals: [
    (Decl(x, Int), Call(callee: Id(input), args: [])),
    (Decl(y, Int), Num(0)),
    (Decl(z, Int), Num(0))
]
stmts: [
    If(
        guard: BinOp(NotEq, Id(x), Num(0)),
        tt: [
            Assign(Id(x), Num(3)),
            Assign(Id(y), Num(2)),
        ]
        ff: [
            Assign(Id(x), Num(2)),
            Assign(Id(y), Num(3)),
        ]
    ),
    Assign(Id(x), Add(Id(x), Id(y))),
    Return(Id(z))
]

[LIR]
let x:int, y:int, z:int, _t1:int
entry: 
  x = $call_ext input()
  y = $copy 0
  z = $copy 0
  _t1 = $cmp neq x 0
  $branch _t1 tt ff

tt: 
  x = $copy 3
  y = $copy 2
  $jump if_end

ff: 
  x = $copy 2
  y = $copy 3
  $jump if_end

if_end: 
  z = $arith add x y
  $ret z
```

- example 2 (draw graph) [see OneNote]

```
[source]
let x:int = input(), y:int = input(), z:int = input();
while (z != 0) {
  x = x + 1;
  y = y + 2;
  z = x / y;
}
return x;

[AST]
locals: [
    (Decl(x, Int), Call(callee: Id(input), args: [])),
    (Decl(y, Int), Call(callee: Id(input), args: [])),
    (Decl(z, Int), Call(callee: Id(input), args: []))
]
stmts: [
    While(
        guard: BinOp(NotEq, Id(z), Num(0)),
        body: [
            Assign(Id(x), Add(Id(x), Num(1))),
            Assign(Id(y), Add(Id(y), Num(2))),
            Assign(Id(z), Div(Id(x), Id(y))),
        ]
    ),
    Return(Id(x))
]

[LIR]
let x:int, y:int, z:int, _t1:int
entry:
  x = $call_ext input()
  y = $call_ext input()
  z = $call_ext input()
  $jump hdr

hdr:
  _t1 = $cmp neq z 0
  $branch _t1 body exit

body:
  x = $arith add x 1
  y = $arith add y 2
  z = $arith div x y
  $jump hdr

exit:
  $ret x
```

- our task is to translate valid programs from the AST data structure to the LIR data structure; this is called _lowering_

- [go over lowering process] [see `docs/lowering.pdf`]

    - [when translating Lvals, need to explain difference between Lval and Exp: Exp evaluates to a value, Lval evaluates to a location where a value will be stored]

        - we need them because an `Assign` can be directly to a variable OR through a pointer, and LIR makes the difference explicit

        - we also used Lvals for Call statements, but this was just to make the grammar LL(1)

    - we don't need to worry about producing the best possible (most efficient) code; we can clean it up later using various optimizations on the LIR

- example [see OneNote]

```
[source]
let x:int = input(), y:int = 0, z:int = 42;
while (z != 0) {
  while (y <= x) {
    y = y + 2;
    z = x / y;
  }
  x = x + 1;
}
return x;

[AST]
locals: [
    (Decl(x, Int), Call(callee: Id(input), args: [])),
    (Decl(y, Int), Num(0)),
    (Decl(z, Int), Num(42))
]
stmts: [
    While(
        guard: BinOp(NotEq, Id(z), Num(0)),
        body: [
            While(
                guard: BinOp(Lte, Id(y), Id(x)),
                body: [
                    Assign(Id(y), Add(Id(y), Num(2))),
                    Assign(Id(z), Div(Id(x), Id(y))),
                ]
            )
            Assign(Id(x), Add(Id(x), Num(1))),
        ]
    ),
    Return(Id(x))
]
```

- solution

INITIAL TRANSLATION VECTOR
```
label:entry
-----
|-----
| _t1 = $call_ext input()
|-----
x = $copy _t1
-----
y = $copy 0
-----
z = $copy 42
-----
$jump lbl1
label:lbl1
|-----
| _t2 = $cmp neq z 0
|-----
$branch _t2 lbl2 lbl3
label:lbl2
|-----
| $jump lbl4
| label:lbl4
||-----
|| _t3 = $cmp lt y x
||-----
| $branch _t3 lbl5 lbl6
| label:lbl5
||-----
|||-----
||| _t4 = $arith add y 2
|||-----
|| y = $copy _t4
|||-----
||| _t5 = $arith div x y
|||-----
|| z = $copy _t5
||-----
| $jump lbl4
| label:lbl6
||-----
|||-----
||| _t6 = $arith add x 1
|||-----
|| x = $copy _t6
||-----
|-----
$jump lbl1
label:lbl3
-----
$ret x
```

# codegen
## intro

- once we have lowered to LIR, we're in the middle-end where we would perform optimizations; however for now we're going straight to the backend in order to get a working compiler as quickly as possible

- going from LIR to a specific ISA is called _codegen_

    - for us, the ISA will be x86-64

    - i am not going to teach you x86-64; i'll explain what the code needs to do and, for the assignment, i'll give a list of ueful x86-64 instructions, a pointer to some online resources, and my own binary solution that you can use to figure out what to emit

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

```
(address 0) [ code segment | static segment | data segment ] (highest address)
```

- the `code segment` is where the program instructions are located

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

- this layout is where the terms `segmentation fault` and `stack overflow` come from

    - a segmentation fault is where you're trying to access a pointer that is pointing into a segment you don't have permission to access

    - a stack overflow is where you try to grow the stack too big (e.g., too many recursive function calls) and run out of room

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

- when i give out the codegen assignment i'll include the specific alignment restrictions for x86-64

## stage 1: no structs, globals, pointers, or functions other than main
### intro

- a LIR::Program that contains only the `main` function, with no structs, globals, or externs; no variables have a pointer type

- relevant LIR instructions: `$arith`, `$cmp`, `$copy`, `$branch`, `$jump`, `$ret`

- we'll see at this stage that variables are only an illusion of the programming language---they don't exist in machine code

    - a program has variables, machine code has registers and memory locations

- remember that i'm not going to give you an exact set of instructions for emitting x86-64 assembly; i'll explain what the code needs to do and then you'll have to do some exploration to figure out the exact x86-64 details

    - i will give you some useful information, some online pointers, and my own binary solution, so as long as you understand what the code is _supposed_ to do, it shouldn't be hard to figure out how to do it from those

    - [maybe spend some time showing how to use my solution to figure out what to generate, once i've shown the example]

### example

- example: [see `examples/codegen/stage-1.{lir, s}`] [see OneNote]

```
fn main() -> int {
  let x:int, y:int, z:int

  entry:
    x = $copy 2
    y = $arith add x 3
    z = $cmp lt x y
    $branch z lbl1 lbl2

  lbl1:
    y = $copy x
    $jump lbl2

  lbl2:
    $ret y
}
```

```
.data
.text

.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $32, %rsp
  movq $0, -8(%rbp)
  movq $0, -16(%rbp)
  movq $0, -24(%rbp)
  jmp main_entry

main_entry:
  movq $2, -8(%rbp)
  movq -8(%rbp), %r8
  movq %r8, -16(%rbp)
  addq $3, -16(%rbp)
  movq -8(%rbp), %r8
  cmpq -16(%rbp), %r8
  movq $0, %r8
  setl %r8b
  movq %r8, -24(%rbp)
  cmpq $0, -24(%rbp)
  jne main_lbl1
  jmp main_lbl2

main_lbl1:
  movq -8(%rbp), %r8
  movq %r8, -16(%rbp)
  jmp main_lbl2

main_lbl2:
  movq -16(%rbp), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

### codegen summary

1. create standard assembly program template (everything except `main` itself) 

    - for programs in stage 1 it will always be the same

    - [point it out in the example above]

2. create the prologue for `main`: setting things up as we enter the function

    - this version is specifically for `main`, we'll tweak it a bit when we add other functions later

3. output the ISA instructions for each LIR basic block

4. create the epilogue for `main`: tearing things down as we leave the function

### context

- `main` was called from `_start`, which called it by pushing the return address on the stack and then jumping to the `main` label

- `main` has no parameters and returns an `int`

- 2 important registers:

    - `FP` holds the _frame pointer_ value, which points to the bottom of the current function's stack frame (the return address for the caller, just below the first local)

    - `SP` holds the _stack pointer_ value, which points to the current top of the stack (that is, whatever the topmost value on the stack is; `SP + 1` is undefined memory)

- we (the compiler writers) are responsible for managing `FP` and `SP` appropriately

### the function prologue

- `FP` is still pointing to the bottom of the stack frame for `_start`, but we're creating a new stack frame and need to set `FP` accordingly

- `SP` is the current top of the stack, and so will be the bottom of the new stack frame we're creating

- our new stack frame needs space for any local variables that `main` has declared, this is where the values of those locals will live

- but remember that when `main` returns we need to restore the stack back to what it was for its caller

- so when creating the prologue we should:

    1. emit the `main` label so `_start` has somewhere to jump

    2. emit instructions to:
    
        - push `FP` onto the stack to save its old value

        - set `FP` to the current value of `SP` (note that `FP` is now saving the old value of `SP`)

        - decrement `SP` to add space for the locals (remember the stack grows _down_)

        - zero initialize the local variable values (unlike C/C++, cflat defines all variable to start as 0)

            - emit instructions that store 0 in each stack location; this isn't the most efficient method (which would be using something like `memset`) but it works

        - jump to `main_entry` (to keep labels globally unique, we'll always prepend the function's name)

    3. also, for use while emitting instructions in `main`'s body, record a mapping from each local to its offset in the stack from the frame pointer

        - remember that the stack grows _down_: the offset will be negative

        - anytime a LIR instruction mentions a variable, the mapping will tell us where in memory that variable's value is relative to `FP` (with the stack location at `FP - <wordsize>` being the first local)

        - for variable `x`, we'll use `[x]` to mean its memory location

- there's one bit of complexity with allocating stack space: for x86-64 the stack pointer _must_ be double-word aligned (i.e., always pointing to a multiple of 16 bytes)

    - we can assume that before `main` was called the stack was correctly aligned

    - the caller pushed the return address and we pushed `FP`, keeping it aligned properly

    - then we added space for locals, which are all 1 word

    - if there were an odd number of locals then `SP` is no longer correctly aligned

    - FIX: when adding space for locals, if there are an odd number pretend there's one extra (it will just be space on the stack we'll never use)

### the function epilogue

- we need to reset the stack to the way it was before entering this function (this will be the same for all functions)

- emit the `main_epilogue` label

- emit instructions to:

    1. restore the previous value of `SP` by setting it to `FP` (which the prologue set to the old value of `SP`)

    2. restore the previous value of `FP` by popping it off the stack (where the prologue stored it)

    3. pop the return address from the stack (put there by the caller) and jump to it

### translating LIR basic blocks to ISA basic blocks

- for each basic block, we should emit the block's label (prepended with `main_`) and then emit ISA instructions for each LIR instruction

- `x = $copy op`

    - if `op` is a constant, store it in `[x]`

    - if `op` is `y`, retrieve its value from `[y]` and store it in `[x]`

- `x = $arith <aop> op1 op2`

    - apply `<aop>` to the values of `op1` and `op2`, storing the result in `[x]`

    - how this is done exactly will depend on `<aop>`; different operations have different methods for x86-64

- `x = $cmp <rop> op1 op2`

    - compare the values of `op1` and `op2`, setting a condition code

    - store 0 or 1 to `[x]` depending on `<rop>` and the condition code that was set

- `$jump lbl`

    - jump to `main_<lbl>`

- `$branch op lbl1 lbl2`

    - compare the value of `op` to 0

    - if not equal, jump to `main_<lbl1>`

    - else, jump to `main_<lbl2>`

- `$ret op`

    - by convention, the return value goes in a special register (`%rax` for x86-64)

        - the caller will know to look for the return value there

    - put the value of `op` in `%rax`

    - jump to `main_epilogue`

### review previous example

## stage 2: globals

- we're not including the implicit global function pointers in this stage, those will come when we add other functions besides `main`

- we need to add a location for the globals, but they can't be on the stack because they live through the entire program and are accessible from all functions

- we put them in the static segment, which in x86 assembly is specified in the `.data` section

    - previously we emitted `.data` as part of our assembly program template but didn't put anything in it

    - we also need to initialize them to 0, just like function locals

    - we use the `.globl` directive to declare and initialize globals

- global variable locations are computed as an offset from the instruction pointer, which in x86-64 is `%rip` (this is not a pointer you manipulate directly, this is the program counter)

    - for global `g`, `g(%rip)` is the memory location of `g`'s value

- example: [see `examples/codegen/stage-2.{lir, s}`] [see OneNote]

```
g:int

fn main() -> int {
  let x:int

  entry:
    x = $copy 42
    g = $copy x
    $ret g
}
```

```
.data

.globl g
g: .zero 8

.text

.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $16, %rsp
  movq $0, -8(%rbp)
  jmp main_entry

main_entry:
  movq $42, -8(%rbp)
  movq -8(%rbp), %r8
  movq %r8, g(%rip)
  movq g(%rip), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

## stage 3: extern calls

- additional LIR instruction: `$call_ext`

- how do we call a function? we're actually going to do it differently depending on whether it's an extern or an internal function

    - we'll assume that extern calls are to functions defined in object files generated from C code (like our cflat runtime library)

    - for extern functions we need to use the standard x86-64 calling convention (though it's still simpler than the full convention because (1) we know all values are 1 word, and (2) we're being naive in how we're using registers)

    - for internal functions we can use a simpler convention that makes things easier

    - i'll talk more about the difference later, i'll just describe the convention here

- `x = $call_ext foo(op1, ...)`

    - place the first 6 arguments in the registers `%rdi, %rsi, %rdx, %rcx, %r8, %r9`, in that order

    - if there are more than 6 arguments, push the rest onto the stack (first arg topmost, i.e., push starting with last arg)

    - `call foo`

    - if any arguments were pushed to the stack, restore the stack pointer

    - the callee's return value (if any) is in `%rax`, store it to `x` if relevant

- there's one more bit of complexity: we have to maintain the stack alignment

    - remember that the stack must be double-word aligned immediately before a `call`

    - we may have pushed some arguments to the stack; if we pushed an odd number then the alignment is off

    - if that happens, we need to add one more word to the stack (and remember to remove it after the call returns, along with the pushed args)

- extern functions don't need to be declared in the assembly file, the assumption is that the linker will figure things out later

    - but having them means we can't compile the resulting assembly as an executable, only as an object file

- example 1: [see `examples/codegen/stage-3_1.{lir, s}`] [see OneNote]

```
extern foo(int) -> int

fn main() -> int {
  let x:int

  entry:
    x = $call_ext foo(42)
    $ret x
}
```

```
.data
.text

.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $16, %rsp
  movq $0, -8(%rbp)
  jmp main_entry

main_entry:
  movq $42, %rdi
  call foo
  movq %rax, -8(%rbp)
  movq -8(%rbp), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

- example 2: [see `examples/codegen/stage-3_2.{lir, s}`] [see OneNote]

```
extern foo(int, int, int, int, int, int, int) -> int

fn main() -> int {
  let x:int

  entry:
    x = $call_ext foo(1, 2, 3, 4, 5, 6, 7)
    $ret x
}
```

```
.data
.text

.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $16, %rsp
  movq $0, -8(%rbp)
  jmp main_entry

main_entry:
  movq $1, %rdi
  movq $2, %rsi
  movq $3, %rdx
  movq $4, %rcx
  movq $5, %r8
  movq $6, %r9
  pushq $7
  subq $8, %rsp
  call foo
  movq %rax, -8(%rbp)
  addq $16, %rsp
  movq -8(%rbp), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

## stage 4: internal calls
### intro

- we handle internal functions and calls differently from calls to extern functions in order to make implementing functions easier

    - otherwise when generating ISA for an instruction we have to constantly worry about where a parameter is (register vs memory) and, if it's in a register, whether we need to worry about overwriting it and thus should save it or not (e.g., if making our own call to another function)

    - there isn't anything technically hard about using the standard method, it's just more complicated (and more efficient for the resulting executable, which is why they use it)

- additional lir instructions: `$call_dir`, `$call_idr`

- we'll see in this stage that, like variables, functions are an illusion of the programming language---they don't exist in machine code

### calling conventions in general

- for externs i just gave a procedure for calling another function and getting the result, but since the callee was extern we didn't see how the callee function behaved

- now that we're dealing with internal functions where we implement both the call and the function being called let's dig in deeper

- functions are an abstraction, in order to get "function-like" behavior we need to implement it

    - a _calling convention_ specifies how "functions" (i.e., the code blocks that implement function behavior) interact

    - the caller and callee need to agree to a protocol; the caller needs to know how to set up a function call so that the callee gets its arguments, knows where to return, and knows how to return a value

    - there are various calling conventions; as the name implies it's just an agreement that both parties adhere to

- we split the calling convention into the following pieces:

    - _pre-call_: setting things up in the caller to pass info to the callee

    - _function prologue_: setting things up in the callee to accept the info

    - _function epilogue_: tearing down the callee and getting back to the caller

    - _post-return_: tearing things down back in the caller and getting the return value

- since there is no such thing as "function local variable", all functions use the same set of registers; part of the convention is about what to do so that functions don't overwrite each other's data

    - _caller-save_ registers: the caller is responsible for saving the values in these registers (if it still needs them) before making a call; the callee can use them freely

    - _callee-save_ registers: the caller is free to assume that any values in these registers will remain untouched by the callee; the callee can use them but only if it saves their values and then restores them before returning

- first i'll talk about the general x86-64 calling convention, which is a bit complicated; then i'll go over our simplified version

- caller: pre-call

    - save any caller-save registers currently being used

    - place the first 6 one-word arguments in a specific set of registers (designated by the convention as "argument passing registers")

    - place any arguments greater than one word or past the first 6 one-word arguments onto the stack in a specific order (so the callee knows which value maps to which parameter)

    - push the return address (the instruction immediately past this call) into the stack

    - jump to the callee label

- callee: function prologue

    - if we're using any callee-save registers, push them onto the stack

    - set the callee's frame pointer and allocate space on the stack for any locals

- callee: function epilogue

    - place the return value in a specific register (designated by the convention as the "return register")

    - deallocate the stack frame

    - restore the callee-save registers, including the frame pointer

    - pop the return address from the stacl

    - jump to the return address

- caller: post-return

    - pop any arguments placed onto the stack

    - restore any caller-save registers

- notice that we're using the pre-call and post-return convention for extern calls, except we're guaranteed that all arguments are only one word and we don't need to worry about caller/callee-save registers

### our calling convention for internal calls

- we're going to use our own calling convention that is simpler than the standard one for several reasons:

    - we'll always push all arguments on the stack, never pass them in registers

    - all arguments and return values are integers or pointers, i.e., 1 word

    - since we're doing naive codegen, we don't need to worry about caller/callee-save registers because we never store information in a register past the end of a LIR instruction

- `x = $call_dir foo(op1, ..., opN) then bb`

    - push `op1` though `opN` to the stack, starting with `opN` and ending with `op1` (so it's the topmost value on the stack)

    - fix stack alignment as necessary

    - `call foo`

    - if any arguments were pushed to the stack, restore the stack pointer

    - the callee's return value (if any) is in `%rax`, store it to `x` if relevant

    - jump to `bb`

- `x = $call_idr fp(op1, ...) then bb`

    - just like a direct call except use `call *op` where `op` is an operand denoting the value of the function pointer

### revisiting the function prologue

- `main` didn't have any parameters, so we didn't need to worry about them

- for functions with parameters, we see that the caller pushes the corresponding arguments on the stack _before_ calling the callee; this means that all parameter values are at a _positive_ offset from the callee's frame pointer

    - remember to account for the saved frame pointer: the first argument on the stack is at `FP + <wordsize>`

    - remember that the args were pushed left-to-right, so the first parameter's value is the one closest to the frame pointer

- when emitting a function's prologue and recording offsets for locals, we can record offsets for parameters at the same time

    - remember that locals can shadow parameters, so if a local and a parameter have the same name then any reference to that name means the local

- other than the offsets being positive, we can treat parameters just like locals

- note that this is why we're using the simpler calling convention, otherwise some parameters would be in registers and some in the stack (and we would also have to worry about overwriting those registers if we aren't careful)

### implicit function pointers

- if we have functions other than `main`, then we also have global variables with the same name that are intended to be function pointers to those functions 

- when emitting declarations for global variables, if the global has the same name as a function then we handle it differently:

    - we need to mangle the name to avoid conflicting with the label used for the function itself; append `_` to the global's name (this means that when translating LIR instructions that use this global, we need to add the `_` as well)

    - we need to initialize the value of the variable to the corresponding function's address (again using assembly directives)

### examples

- example 1: [see `examples/codegen/stage-4_1.{lir, s}`] [see OneNote]

```
fn main() -> int {
  let x:int
  entry:
    x = $call_dir foo(40, 2) then exit

  exit:
    $ret x
}

fn foo(p: int, q: int) -> int {
  let x:int
  entry:
    x = $arith add p q
    $ret x
}
```

```
.data
.text

.globl foo
foo:
  pushq %rbp
  movq %rsp, %rbp
  subq $16, %rsp
  movq $0, -8(%rbp)
  jmp foo_entry

foo_entry:
  movq 16(%rbp), %r8
  movq %r8, -8(%rbp)
  movq 24(%rbp), %r8
  addq %r8, -8(%rbp)
  movq -8(%rbp), %rax
  jmp foo_epilogue

foo_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret

.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $16, %rsp
  movq $0, -8(%rbp)
  jmp main_entry

main_entry:
  pushq $2
  pushq $40
  call foo
  movq %rax, -8(%rbp)
  addq $16, %rsp
  jmp main_exit

main_exit:
  movq -8(%rbp), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

- example 2: [see `examples/codegen/stage-4_2.{lir, s}`] [see OneNote]

```
foo: &(int) -> int

fn main() -> int {
  let x:int, fp:&(int) -> int

  entry:
    fp = $copy foo
    x = $call_idr fp(42) then exit

  exit:
    $ret x
}

fn foo(p: int) -> int {
  entry:
    $ret p
}
```

```
.data

.globl foo_
foo_: .quad "foo"

.text

.globl foo
foo:
  pushq %rbp
  movq %rsp, %rbp
  subq $0, %rsp
  jmp foo_entry

foo_entry:
  movq 16(%rbp), %rax
  jmp foo_epilogue

foo_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret

.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $16, %rsp
  movq $0, -8(%rbp)
  movq $0, -16(%rbp)
  jmp main_entry

main_entry:
  movq foo_(%rip), %r8
  movq %r8, -8(%rbp)
  subq $8, %rsp
  pushq $42
  call *-8(%rbp)
  movq %rax, -16(%rbp)
  addq $16, %rsp
  jmp main_exit

main_exit:
  movq -16(%rbp), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret
```

## stage 5: adding pointers
### intro

- now we can have memory allocation (including arrays)

    - unlike C/C++, cflat is a safe language; this means that we need to detect any array accesses that are out-of-bounds (and also allocations for a non-positive amount of memory)

- additional LIR instructions: `$load`, `$store`, `$alloc`, `$gep`

- strategy for arrays (any allocation can be treated as an array):

    - when something is allocated on the heap, we'll check that the size is positive and then we'll allocate an additional word (the `header`) that contains the number of allocated objects; we'll put this at the beginning of the object and return a pointer to the word immediately after the header (so the address of the header is at `-WORDSIZE` offset from the returned pointer)

    - when we do pointer arithmetic using `$gep`, we'll check against the number that's stored in its header and make sure that the result is in-bounds

- what if the check fails?

    - we'll add some boilerplate ISA blocks to handle when there's a problem with allocation or indexing; they'll print an error message and immediately abort execution

    - `.invalid_alloc_length` is used when we try to allocate a non-positive number of elements
    
    - `.out_of_bounds` is used when we try to index an array out of bounds

### translating instructions

- `x = $load y`

    - the value of `y` is an address in memory; the value at _that_ address is what we need to store to `x`

    - read the value of `y` from its location on the stack, then read the value at that address, then store it to `x`

- `$store x op`

    - the value of `x` is an address in memory; we want to update the value at _that_ address with the value of `op`

    - get the value of `op`, then read the value of `x`, then store the value of `op` at that address

- `x = $alloc op`

    - we only have integers and pointers so far and they're both size `WORDSIZE`, so we're allocating `op` words _plus_ one word for the header

        - the original value must be greater than 0; if this check fails we'll have the executable abort by jumping to the `.invalid_alloc_length` block

    - in order to actually allocate the memory we'll call into the cflat runtime library, which has a function `_cflat_alloc` that takes a number of words to allocate and returns a pointer to the allocated memory

    - so in total:

        - compare `op` with 0, if not greater-than then jump to `.invalid_alloc_length`

        - compute `op + 1`, then call `_cflat_alloc` passing the result as an argument (this counts as an extern call, even though it isn't declared as such); let `ptr` be the return value of the call
    
        - store `op` into the location whose address is in `ptr`

        - store `ptr + WORDSIZE` into `x` (i.e., the address of the first element past the header word)

- `x = $gep y op`

    - compare `op` with 0; if less-than then jump to `.out_of_bounds`

    - load value at `y - WORDSIZE` (the header word); call it `hdr`

    - compare `op` with `hdr`; if not less-than then jump to `.out_of_bounds`

    - store `y + (op * WORDSIZE)` into `x` (i.e., the pointer to the requested element)

### example

- example: [see `examples/codegen/stage-5.{lir, s}`] [see OneNote]

```
fn main() -> int {
  let x:&int, y:int, z:&int

  entry:
    x = $alloc 10
    z = $gep x 5
    y = $load z
    y = $arith add y 1
    $store z y
    $ret y
}
```

```
.data

out_of_bounds_msg: .string "out-of-bounds array access"
invalid_alloc_msg: .string "invalid allocation amount"
        
.text

.globl main
main:
  pushq %rbp
  movq %rsp, %rbp
  subq $32, %rsp
  movq $0, -8(%rbp)
  movq $0, -16(%rbp)
  movq $0, -24(%rbp)
  jmp main_entry

main_entry:
  movq $10, %r8
  cmpq $0, %r8
  jle .invalid_alloc_length
  movq %r8, %rdi
  incq %rdi
  call _cflat_alloc
  movq %r8, %r8
  movq %r8, 0(%rax)
  addq $8, %rax
  movq %rax, -8(%rbp)
  movq $5, %r8
  cmpq $0, %r8
  jl .out_of_bounds
  movq -8(%rbp), %r9
  movq -8(%r9), %r10
  cmpq %r10, %r8
  jge .out_of_bounds
  leaq (%r9,%r8,8), %r10
  movq %r10, -24(%rbp)
  movq -24(%rbp), %r8
  movq 0(%r8), %r9
  movq %r9, -16(%rbp)
  movq -16(%rbp), %r8
  movq %r8, -16(%rbp)
  addq $1, -16(%rbp)
  movq -16(%rbp), %r8
  movq -24(%rbp), %r9
  movq %r8, 0(%r9)
  movq -16(%rbp), %rax
  jmp main_epilogue

main_epilogue:
  movq %rbp, %rsp
  popq %rbp
  ret

.out_of_bounds:
  lea out_of_bounds_msg(%rip), %rdi
  call _cflat_panic

.invalid_alloc_length:
  lea invalid_alloc_msg(%rip), %rdi
  call _cflat_panic
```

## stage 6: adding structs TODO:
### intro

- now we can have user-defined structs

- additional LIR instructions: `$gfp`

- we also need to tweak allocations and indexing because now we may be allocating more than one word per object

    - which also means we need to worry about field layout

### translating instructions TODO:

### tweaking allocation and indexing TODO:

### example TODO:

- example: [see `examples/codegen/stage-6.{lir, s}`] [see OneNote] FIXME:

## x86-64 info TODO:

- TODO: ???

# register allocation TODO:

# IR optimization TODO:

# memory management TODO:

# ==== OLD ===================================================================

middle-end: optimization (back to L1)
=====================================

intro to analysis and optimization
----------------------------------

so far we\'ve examined the frontend (lexing, parsing), the backend
(codegen), and the runtime (memory management). let\'s spend some time
looking at the middle-end and optimizations.

what do we mean by \"optimization\"?

-- usually we mean performance: making the generated code execute
faster. sometimes we want to optimize for other things, like code size,
memory consumption, or power consumption (all of which are important for
embedded systems). for our purposes we\'ll focus on performance
optimization.

-- \"optimization\" is a somewhat misleading term, because it implies
we\'re going to find some optimal (i.e., best) solution. in reality
finding the best solution is np-hard or undecidable and we\'re just
looking for a better solution (e.g., that the optimized code runs faster
than the unoptimized code). there is a such thing as a
\"super-optimizer\" which does look for the best possible solution, but
this is an active area of research and current solutions can\'t scale
beyond tens of lines of code.

how do we optimize for performance? the general idea is to reduce the
amount of computation the generated executable needs to do: to somehow
eliminate instructions or to replace slower instructions with faster
instructions, while still guaranteeing that the resulting program \"does
the same thing\".

EXAMPLES

// arithmetic identities x := y \* 0; --\> x := 0;

// constant folding x := 1 + 2; --\> x := 3;

// constant propagation and folding x := 1 + 2; --\> x := 3; y := x + 3;
--\> y := 6;

// redundancy elimination x := a + b; --\> x := a + b; y := a + b; y :=
x;

// strength reduction x := y / 2; --\> x := y \>\> 1;

optimization = analysis + transformation. we need to use program
analysis (aka static analysis) to determine what is true about the
program\'s behavior (e.g., what things are constant values, what
expressions are guaranteed to evaluate to the same value, etc), then we
select code transformations that, based on the information provided by
the analysis, are safe (preserve behavior) and effective (improve
performance).

we can perform optimizations in the backend on the generated assembly
code, but then we would have to reimplement them for each backend we add
to the compiler. there are some optimizations that are inherently target
specific, and we reserve those for the backend, but mostly we try to
optimize in a target-agnostic way so that we can reuse the same
optimizations for all targets. thus, the \"middle-end\".

the middle-end optimizations [can]{.underline} operate directly on the
AST, but it\'s nicer if the code is simpler and more regular; it makes
the optimizations easier to specify and implement. usually, the
middle-end translates the AST into a simpler \"intermediate
representation\" (IR). it performs the optimizations on the IR, then the
backend codegen translates the IR into assembly.

the optimizer can also operate at a number of different scopes, ranging
from small fragments of code (local optimization) to entire functions
(global optimization) to set of functions (inter-procedural
optimization) to entire programs (whole-program optimizations). the
larger the scope that the optimizer operates at, the more effective it
is but the more complex and expensive it is.

-- we\'ll focus on local optizations and global optimizations. the term
\"global\" may seem odd since we\'re focusing in on one single function;
the term comes from a time when local optimization was the norm and
operating at an entire function scope was seen as extremely
aggressive---operating on even larger scopes than that was out of the
question.

safety and profitability
------------------------

we stated two important properties of optimization: -- it should be safe
(preserve program behavior) -- it should be profitable (improve program
performance)

let\'s dig a little into each, because they aren\'t as straightforward
as they seem.

### safety

we said that optimizations should preserve behavior, but what does that
mean? if you think about it, the whole point is to change program
behavior by making it run faster using different instructions. what are
we preserving? what does \"behavior\" even mean?

roughly, we can think of the behavior we\'re preserving as
user-observable events independent of time. in other words, if we run
the original program P and record the user-observable events (e.g.,
outputs) without timestamps, then do the same with the optimized program
P\', we should get the same thing.

-- for L1, this would be just the final output result.

this definition will work fine for L1, but it isn\'t adequate for
language like C and C++. these languages have the concept of \"undefined
behavior\". literally, this means that for certain code expressions and
statements the language standard doesn\'t say what should happen, thus
the compiler implementor is free to do whatever they want. in fact, it
goes even further than that: a program that executes a statement with
undefined behavior isn\'t defined at all; the entire program execution
is undefined, not just that statement. this is important to understand:
the effects of undefined behavior are not confined to the statement that
is undefined, or even the execution after that statement, but the
[entire]{.underline} execution, even before that statement is executed.

let\'s look at some examples (taken from the blog of John Regehr, a CS
professor whose research deals a lot with undefined behavior in C).

\#include \<limits.h\> \#include \<stdio.h\>

int main (void) { printf (\"%d`\n`{=latex}\", (INT~MAX~+1) \< 0); return
0; }

this program asks the question: if we take the maximum integer value and
add 1, do we get a negative number? in other words, do integers wrap
around? so what happens when we run this program?

\$ gcc test.c -o test \$ ./test 1

or:

\$ gcc test.c -o test \$ ./test 0

or:

\$ gcc test.c -o test \$ ./test 42

or:

\$ gcc test.c -o test \$ ./test Formatting root partition

any of these could happen, or anything else. the program has undefined
behavior because it exhibits signed integer overflow, that is, we took a
signed integer and added a value to it that caused the result to be out
of the range that a signed integer can express. the C standard says this
this is undefined (note that unsigned integer overflow is defined).

now, you might think \"on an x86 the C integer add instruction is
implemented using the x86 ADD assembly instruction, which operates using
two\'s complement arithmetic, which does wrap around. thus, if i\'m
running the program on an x86 i can expect to get 1.\" this is bad, for
the following reasons:

1.  you don\'t know what architectures your program will be compiled on,
    even if you are compiling for x86 right now.
2.  not all compilers will work this way, even on an x86, even if the
    one you\'re using right now does.
3.  at certain optimization levels, a compiler that may work this way
    previously will stop working this way because it takes advantage of
    the undefined behavior to optimize the program.

let\'s take a closer look at that third reason with an example:

int stupid(int a) { return (a+1) \> a; }

note that if we pass in INT~MAX~ for a, then we get undefined behavior.
the compiler optimizer can reason this way:

case 1: \'a\' is not INT~MAX~. then the answer is guaranteed to be 1.
case 2: \'a\' is INT~MAX~. then the behavior is undefined and we can do
whatever we want. let\'s make the answer 1.

it then outputs the following optimized code:

int stupid(int a) { return 1; }

this is completely valid, even if we\'re running the program on an x86
and using the x86 ADD instruction which exhibits two\'s complement
behavior, so that INT~MAX~ + 1 \< INT~MAX~.

we\'ve only looked at signed integer overflow, but the C99 language
standard lists 191 kinds of undefined behavior. this is why it\'s always
a good idea to use -Wall for C/C++ compilation and pay attention to the
warnings.

### profitability

another consideration is that the transformation we apply to the code to
optimize it should actually result in faster code. this can be trickier
than you might think.

EXAMPLE: FUNCTION INLINING

consider the following example:

def foo(int a) { return a + 1; } int x; x := foo(2); output x;

at runtime, the execution will look something like:

precall sequence function prologue compute a+1 function epilogue
post-return sequence output x

notice that the precall, prologue, epilogue, and post-return are all
overhead that comes because we\'re making a function call. we can
optimize this code by [inlining]{.underline} the function call, that is,
by copying the body of the function directly into the caller:

int x; x := 2 + 1; output x;

this has exposed another optimization opportunity for constant folding
and propagation, yielding the following optimized program:

output 3;

function inlining both eliminates the overhead of function calls and
also exposes many opportunities for optimization that wouldn\'t be
obvious if we looked at functions in isolation. we can perform function
inlining whenever we know exactly which function is being called (always
in L1; usually in C except for function pointers; sometimes in C++
because of virtual methods).

you might think: inlining one function call was good, so obviously we
should inline as many as possible! but this actually is not a good idea.
for one, we can\'t fully inline recursive calls (we would get into a
cycle of infinitely repeated inlinings); but let\'s ignore that issue.
the real problem is that inlining comes with a cost: the code becomes
larger.

def foo(int a) { \<instruction 1\>; ... \<instruction N\>; return
\<aexp\>; } int x; int y; x := foo(2); y := foo(3); output x + y;

becomes:

int x; int y; \<instruction 1\>; . . . \<instruction N\>; \<instruction
1\>; . . . \<instruction N\>; output x + y;

there were two calls to foo, so we doubled the amount of code inside
foo\'s body. consider the following:

def foo(int a) { \<instruction 1\>; ...; bar(); ...; bar();
\<instruction N\>; return \<aexp\>; } def bar() { \<instruction 1\'\>;
...; \<instruction M\'\>; return \<aexp\>; } int x; int y; x := foo(2);
y := foo(3); output x + y;

becomes:

int x; int y; \<instruction 1\>; . . . \<instruction 1\'\>; . . .
\<instruction M\'\>; . . . \<instruction N\>; \<instruction 1\>; . . .
\<instruction 1\'\>; . . . \<instruction M\'\>; . . . \<instruction N\>;
output x + y;

we doubled the amount of code in foo, which, because we inlined the
calls to bar, quadruples the amount of code in bar. in general, inlining
can increase code size exponentially. this impacts not just memory
consumption, but also performance because the large code size can blow
the instruction cache and cause large slowdowns.

in practice, compilers use a set of complicated heuristics to determine
whether a function call should be inlined or not.

many optimizations have similar considerations: just because we
[can]{.underline} apply the optimization doesn\'t mean it\'s a good
idea. we need to figure out in each case whether applying the
optimization results in a net performance benefit or loss and act
accordingly.

local optimizations
-------------------

local optimizations operate on basic blocks; we iterate through each
block (in arbitrary order) and apply the local optimizations to optimize
each block in isolation from each other.

### constant folding and arithmetic identities

we\'ve already mentioned these; they\'re simple optimizations that
operate on single instructions. the idea is that sometimes we don\'t
need to wait for the program to execute in order to evaluate
expressions, we can do it right in the compiler.

for each instruction: -- if all the operands are constants, evaluate the
operation -- if some of the operands are constant, attempt to apply an
arithmetic identity:

x \* 1 = x 1 \* x = x x \* 0 = 0 0 \* x = 0 x + 0 = x 0 + x = x x - 0 =
0 0 - x = -x x - x = 0

we can also apply logical identities under our assumption that 0 is
false and non-0 is true:

0 AND x = 0 x AND 0 = 0 non-0 AND x = x x AND non-0 = x 0 OR x = x x OR
0 = x non-0 OR x = 1 x OR non-0 = 1

we can also handle some identities for the relational operators that
probably wouldn\'t happen but are easy enough to check for that we might
as well:

x \< x = 0 x \<= x = 1 x = x = 1

### local value numbering

this is a slightly more complicated optimization that operates on the
entire basic block. our goal is to identify redundant expressions
computed by the basic block so that we don\'t compute the same thing
more than once.

example 1:

a \<- 4 b \<- 5 c \<- a + b d \<- 5 e \<- a + b

clearly the second \'a + b\' is redundant and we could optimize this as:

a \<- 4 b \<- 5 c \<- a + b d \<- 5 e \<- c

a naive approach would be to simply scan down the list of instructions
in the basic block and record which ones we see; if we see it again,
replace it with the left-hand side of the first instruction to compute
that expression. this approach would work for the example above, but
suppose we have instead:

example 2:

a \<- 4 b \<- 5 c \<- a + b a \<- 5 e \<- a + b

then the optimization would be incorrect, because the value of the
second \'a + b\' is different than the value of the first \'a + b\'.
here\'s another problem:

example 3:

a \<- 4 b \<- 5 c \<- a + b d \<- 5 e \<- b + a

the second expression \'b + a\' is syntactically different than the
first \'a + b\' but is semantically identical; we need to be able to
recognize that fact (while still distinguishing expressions like \'a -
b\' and \'b - a\').

we need a scheme that can solve both problems. our strategy will be to
tag expressions with numbers s.t. semantically identical expressions
(like \'a + b\' and \'b + a\' in example 3) are given the same number
and hence are recognized as equivalent while semantically different
expressions (like the first \'a + b\' and the second \'a + b\' in
example 2) are given different numbers.

-- the idea is that we want to distinguish between the variables (which
are just arbitrary names for values) and the values of those variables.
we are going to label variables

-- note that our goal is not to guarantee that we find all equivalent
expressions; this is uncomputable. we want to find as many as we can
while still being fast and while guaranteeing that we never incorrectly
find non-equivalent expressions to be equivalent.

the algorithm:

1.  initialize a table mapping expressions to value numbers (starting
    empty). we often use a hash table, and thus this optimization is
    sometimes called \"hash-based value numbering\".

2.  for each copy, unary, and binary operator instruction in order: A.
    look up each variable operand to get its value number; assign a
    fresh number if it doesn\'t have one already and record it in the
    table. B. if the operator is commutative, order the operand value
    numbers from least to greatest. C. look up the entire expression
    (using the operand numbers instead of variables) in the table to get
    its number; assign a fresh number if it doesn\'t have one already
    and record it in the table. D. update the table to map the left-hand
    side variable to the number for the right-hand side expression.

3.  for any two instructions \'\<var1\> \<- \<exp1\>\' and \'\<var2\>
    \<- \<exp2\>\' in order s.t. \<var1\> and \<var2\> have the same
    value number at the \'\<var2\> \<- \<exp2\>\' instruction, we can
    replace \<exp2\> with \<var1\>.

EXAMPLE 1 REDUX

a \<- 4 ; 4 = \#1, a = \#1 b \<- 5 ; 5 = \#2, b = \#2 c \<- a + b ; (\#1
+ \#2) = \#3, c = \#3 d \<- 5 ; 5 = \#2, d = \#2 e \<- a + b ; (\#1 +
\#2) = \#3, e = \#3

we see that c = \#3 and e = \#3, thus we can replace the last
instruction with \'e \<- c\'.

EXAMPLE 2 REDUX

a \<- 4 ; 4 = \#1, a = \#1 b \<- 5 ; 5 = \#2, b = \#2 c \<- a + b ; (\#1
+ \#2) = \#3, c = \#3 a \<- 5 ; 5 = \#2, a = \#2 e \<- a + b ; (\#2 +
\#2) = \#4, e = \#4

here we see that c and e have different tags, thus they are not
guaranteed to be equivalent and we cannot apply the transformation.

EXAMPLE 3 REDUX

a \<- 4 ; 4 = \#1, a = \#1 b \<- 5 ; 5 = \#2, b = \#2 c \<- a + b ; (\#1
+ \#2) = \#3, c = \#3 d \<- 5 ; 5 = \#2, d = \#2 e \<- b + a ; (\#1 +
\#2) = \#3, e = \#3

because we reordered the value numbers for + expressions, we still
recognize that c and e are equivalent in this case.

EXAMPLE 4

x \<- a + d ; a = \#1, d = \#2, (\#1 + \#2) = \#3 y \<- a ; y = \#1 z
\<- y + d ; (\#1 + \#2) = \#3, z = \#3

we can see from this example that local value numbering can discover
equivalent expressions even if they use different variables altogether.

EXAMPLE 5

a \<- x + y ; x = \#1, y = \#2, (\#1 + \#2) = \#3, a = \#3 b \<- x + y ;
(\#1 + \#2) = \#3, b = \#3 a \<- 1 ; a = \#4 c \<- x + y ; (\#1 + \#2) =
\#3, c = \#3 b \<- 2 ; b = \#5 d \<- x + y ; (\#1 + \#2) = \#3, d = \#3

how can we transform this code to optimize it based on the value
numbering? we know that \'x + y\' in all four expressions will have the
same value, but \'a\' and \'b\' are redefined at various points. in
particular, it would be incorrect to replace \'c \<- x + y\' with \'c
\<- a\' (though \'c \<- b\' is fine) and incorrect to replace \'d \<- x
+ y\' with either \'d \<- a\' or \'d \<- b\'.

this is why we have the caveat in the algorithm that we can only replace
\<exp2\> with \<var1\> [if]{.underline} \<var1\> and \<var2\> have the
same value number [at that instruction]{.underline}. for the instruction
\'c \<- x + y\' we see that while \'a\' originally also had value number
\#3, now it has value number \#4 and so cannot be used. similarly, for
the instruction \'d \<- x + y\' we see that neither \'a\' nor \'b\'
still have value number \#3 and so neither can be used.

it\'s annoying that we have to recompute \'x + y\' in the last
instruction just because we\'ve overwritten \'a\' and \'b\' already. we
could get around this problem by renaming variables whenever they are
assigned to keep them unique:

a0 \<- x0 + y0 ; x0 = \#1, y0 = \#2, (\#1 + \#2) = \#3, a0 = \#3 b0 \<-
x0 + y0 ; (\#1 + \#2) = \#3, b0 = \#3 a1 \<- 1 ; a1 = \#4 c0 \<- x0 + y0
; (\#1 + \#2) = \#3, c0 = \#3 b1 \<- 2 ; b1 = \#5 d0 \<- x0 + y0 ; (\#1
+ \#2) = \#3, d0 = \#3

now we can replace \'d0 \<- x0 + y0\' with either \'d0 \<- a0\' or \'d0
\<- b0\' with no problem. there is an issue though: renaming in this way
becomes much more complicated with we look at the entire function,
particularly when we have multiple branches that each redefine the same
variables. this leads to something called \"static single assignment
form\" (SSA), which is a popular variant of IR in modern compilers (both
gcc and llvm use it). we may talk about SSA later, but for now we\'ll
stick to the original IR we\'ve already defined.

### exercise {#exercise-18}

optimize the following code using local value numbering:

a \<- b - c d \<- c - b e \<- c f \<- a AND d g \<- b - e h \<- d AND a
d \<- 42 i \<- e - d

SOLUTION

value-numbered code:

a \<- b - c ; b = \#1, c = \#2, (\#1 - \#2) = \#3, a = \#3 d \<- c - b ;
(\#2 - \#1) = \#4, d = \#4 e \<- c ; e = \#2 f \<- a AND d ; (\#3 AND
\#4) = \#5, f = \#5 g \<- b - e ; (\#1 - \#2) = \#3, g = \#3 h \<- d AND
a ; (\#3 AND \#4) = \#5, h = \#5 d \<- 42 ; 42 = \#6, d = \#6 i \<- e -
b ; (\#2 - \#1) = \#4, i = \#4

transformed code:

a \<- b - c d \<- c - b e \<- c f \<- a AND d g \<- a h \<- f d \<- 42 i
\<- e - b

dataflow analysis
-----------------

### intro {#intro-3}

we have looked at analysis and optimization at the basic block level
(local optimization), but the limited scope means that we can miss many
optimization opportunities. let\'s widen our scope to entire functions,
i.e., global optimization.

the analyses required for global optimization tend to be more
complicated than for local optimization, and we have developed a
specific theoretical framework for it called \"dataflow analysis\".
compiler developers tried a lot of ad-hoc global analyses at first, but
found that they were very difficult to get correct; dataflow analysis
provides a handy set of tools such that, if you use them correctly, you
will get a correct analysis.

we will first introduce some fundamental ideas that will be important
for understanding how DFA works at a high level. then we\'ll look at
some specific examples of analyses and the optimizations they enable.
finally, we\'ll generalize to the DFA framework and discuss its
properties.

### DFA motivation

when we\'re analyzing a function, what we\'re trying to do is to compute
invariants about its behavior when it executes: what facts are always
true no matter what arguments we give the function?

here is a simple example:

EXAMPLE 1

def foo(int a, int b) { int c; if (a \< b) { c := b - a; } else { c := a
- b; } return c; }

given this function, we don\'t know what the values of the arguments
will be, nor whether we will take the true or false branch of the
conditional. but we can infer the invariant that the value returned by
this function will always be non-negative. here\'s our reasoning:

either a \< b or b \< a or a = b. if a \< b then we take the true
branch, c is positive if b \< a then we take the false branch, c is
positive if a = b then we take the false branch, c is zero therefore c
cannot be negative at the return statement

in this example we are reasoning about the sign of \'c\' (positive,
negative, or zero), so the facts that we care about pertain to that. in
other analyses we care about other properties (as we\'ll see examples of
soon). dataflow analysis provides a general framework that is
independent of the particular facts we want to reason about; basically,
it\'s a template into which we plug in different kinds of things
depending on the specific analysis we\'re trying to perform.

how do we perform our reasoning over the facts that we care about? in
the example we reasoned about each possible path that execution could
take through the function (i.e., it could take the true branch or the
false branch, then either way it will reach the return statement). this
is easy if there\'s only one or a few possible paths, but in general
this won\'t work.

EXAMPLE 2 A if (...) { B } { C } if (...) { D } { E }

possible paths: A -\> B -\> D A -\> B -\> E A -\> C -\> D A -\> C -\> E
2^2^ paths

EXAMPLE 3 A if (...) { B } { C } if (...) { D } { E } if (...) { F } { G
}

possible paths: A -\> B -\> D -\> F A -\> B -\> D -\> G A -\> B -\> E
-\> F A -\> B -\> E -\> G A -\> C -\> D -\> F A -\> C -\> D -\> G A -\>
C -\> E -\> F A -\> C -\> E -\> G 2^3^ paths

in general, for n conditionals there are 2^n^ paths: an exponential
blowup. things are worse for loops:

EXAMPLE 4 A while (...) { B } C

possible paths: A -\> C A -\> B -\> C A -\> B -\> B -\> C A -\> B -\> B
-\> B -\> C ...

there are an arbitrary, possibly infinite number of paths.

somehow we need to reason about the function\'s behavior without
reasoning about each path it could take. however, remember that in order
to use the analysis results to optimize the function, we need to be
safe: if we say that something is true about the function, it must
really be true or the optimization could break the program. so the
analysis must never return a false invariant (i.e., a fact that isn\'t
true for some path in the function), but it can\'t reason about each
path independently.

DFA handles this conundrum by computing a conservative
over-approximation of program behavior. what this means is that we
don\'t promise to compute all true facts, but we do promise that any
facts we compute are true.

EXAMPLE 5

def foo(int a, int b) { int c; if (a \< b) { c := b - a; } if (b \< a) {
c := a - b; } if (a = b) { c := 1; } return c; }

for this example we can reason about signedness again and determine that
\'c\' must always be positive at the return statement. however, our
analysis may only return that \'c\' will be non-negative. this is a true
invariant, but it isn\'t as precise as it could be (being positive
implies being non-negative). we will allow our analyses to be less
precise in order to make them tractable. being less precise means that
the information may not be as useful for optimization (i.e., we may miss
optimization opportunities), but because the information is always true
we are guaranteed that the optimizations will not break the program.

### DFA fundamentals

let\'s look at the general DFA framework. we\'ll examine it at a high
level here with a simple example, then look at some more examples of
common compiler analyses and optimizations, then look at the details of
the DFA framework after you have that context. when we go over the high
level below it will be very abstract and probably hard to understand. i
recommend going through it, then going through the examples, then
returning to the high level explanation again once you have the examples
as context.

we\'ll let L be a set of dataflow facts, whatever they are (for example,
variable signedness). these will be the possible answers that we compute
for the analysis.

for each basic block k in the CFG, we\'ll define IN~k~ and OUT~k~ to be
the dataflow facts that hold immediately before the basic block is
executed and immediately afterwards, respectively. so IN~k~ and OUT~k~ ∈
L. \[show picture\]

for each basic block, we\'ll compute OUT (the output set of dataflow
facts) from IN (the input set of dataflow facts) based on the IR
instructions in the basic block. however, the IR instructions operate on
values (e.g., integers) while the analysis operates on dataflow facts.
so we need to redefine the IR instructions in terms of how they operate
on the dataflow facts. since this depends on what dataflow facts we\'re
tracking for a given analysis, these \"abstract transfer functions\" are
another thing we plug into the DFA framework along with L.

-- let F~k~ be the abstract transfer function for basic block k -- then
OUT~k~ = F~k~(IN~k~)

of course, we need to know IN~k~ for each basic block. if the basic
block only has one predecessor, then that\'s easy: if the predecessor is
block p (for predecessor), then IN~k~ = OUT~p~. \[show picture\]

but what if block k has two predecessors, say p1 and p2? \[show
picture\] then IN~k~ has to be a safe approximation of both of their
outputs. in other words, IN~k~ can only contain facts that are true of
both OUT~p1~ and OUT~p2~. how do we compute this approximation? we use
an operator called the \"meet\" operator ⊓.

-- IN~k~ = OUT~p~ if p is the only predecessor of k = OUT~p1~ ⊓ OUT~p2~
if p1 and p2 are the predecessors of k

for simplicity, we\'ll say this as: IN~k~ = ⊓ OUT~p~ s.t. p is a
predecessor of k

-- the ⊓ is also specific to L, and so it is another thing that we plug
into the DFA framework along with L and the abstract transfer functions.

now we have a set of simultaneous equations, i.e., for each basic block
k:

IN~k~ = ⊓ OUT~p~ s.t. p is a predecessor of k OUT~k~ = F~k~(IN~k~)

note that because of loops, these equations may be recursive, that is,
IN~k~ may transitively depend on OUT~k~ (we\'ll see an example shortly).

the actual analysis consists of solving these equations to compute
values for IN~k~ and OUT~k~ for every basic block k; these will be the
solutions of the analysis. how do we solve them? the idea is that we
want the [least fixpoint]{.underline} of these equations, and that will
be our analysis answer.

a fixpoint of a function is an input x s.t. f(x) = x; i.e., an input
s.t. applying the function to the input yields itself. a function in
general may have any number of fixpoints, from 0 to infinity. let\'s see
some examples:

f(x) = x + 2 \[0 fixpoints\] f(x) = x² - x \[2 fixpoints\] f(x) = x² ÷ x
\[∞ fixpoints\]

if there are multiple fixpoints then we want the most precise one (the
[least]{.underline} fixpoint). for example, in the case above where
there are two fixpoints (0 and 1), the least fixpoint is 0. even if
there are multiple fixpoints, there may not be a least fixpoint. for
example, in the case above where there are infinite fixpoints there is
no least fixpoint (assuming we\'re operating over integers rather than
natural numbers).

fortunately the details of the DFA framework (to be discussed later)
will guarantee that a least fixpoint always exists and is computable. so
how do we compute it?

the traditional algorithm is a worklist algorithm. we initialize IN~k~
and OUT~k~ for every basic block to an initial value, then initialize a
worklist to contain all of the basic blocks. then:

while the worklist is not empty: remove a basic block from the worklist,
call it k compute a new IN~k~ and OUT~k~ if OUT~k~ changed: put any
basic block m s.t. IN~m~ directly depends on OUT~k~ into the worklist

the details of the DFA framework (again, to be discussed later)
guarantee that this algorithm will eventually terminate and that IN~k~
and OUT~k~ will be the least fixpoint solution for every basic block.

1.  signedness DFA example

    a signedness DFA analysis (we could make this more precise in
    various ways, e.g., being able to distinguish non-negative and
    non-positive, but we\'ll keep it very simple for the purposes of
    this example):

    Sign = { +, 0, -, Uninit, Any } L = Var -\> Sign

    ⊓ = (x ∈ Sign) . Any ⊓ x = x ⊓ Any = Any (x ∈ Sign) . Uninit ⊓ x = x
    ⊓ Uninit = x (x ∈ {~~, 0, -}) . x ⊓ y where x != y = Any (x ∈ {~~,
    0, -}) . x ⊓ x = x

    abstract transfer functions:

    var1 \<- constant : update var1 to the signedness of the constant
    (i.e., +, 0, or -) var1 \<- var2 : update var1 to the signedness of
    var2 var1 \<- var2 + var3 : update var1 to var2 \#ADD var3, where
    \#ADD is defined below var1 \<- var2 - var3 : update var1 to var2
    \#SUB var3, where \#SUB is defined below var1 \<- var2 \< var3 :
    update var1 to var2 \#LT var3, where \#LT is defined below var1 \<-
    CALL function : update var1 to Any

    \#ADD \| + \| 0 \| - \| Uninit \| Any
    -------~~--------~~--------~~--------~~--------+----

    -   \| + \| + \| Any \| Uninit \| Any

    0 \| + \| 0 \| - \| Uninit \| Any

    -   \| Any \| - \| - \| Uninit \| Any

    Uninit \| Uninit \| Uninit \| Uninit \| Uninit \| Uninit Any \| Any
    \| Any \| Any \| Uninit \| Any

    \#SUB \| + \| 0 \| - \| Uninit \| Any
    -------~~--------~~--------~~--------~~--------+----

    -   \| Any \| + \| + \| Uninit \| Any

    0 \| - \| 0 \| + \| Uninit \| Any

    -   \| - \| - \| Any \| Uninit \| Any

    Uninit \| Uninit \| Uninit \| Uninit \| Uninit \| Uninit Any \| Any
    \| Any \| Any \| Uninit \| Any

    \#LT \| + \| 0 \| - \| Uninit \| Any
    -------~~--------~~--------~~--------~~--------+----

    -   \| Any \| 0 \| 0 \| Uninit \| Any

    0 \| + \| 0 \| 0 \| Uninit \| Any

    -   \| + \| + \| Any \| Uninit \| Any

    Uninit \| Uninit \| Uninit \| Uninit \| Uninit \| Uninit Any \| Any
    \| Any \| Any \| Uninit \| Any

    SOURCE CODE:

    def foo(int a) : int { int x; int y;

    x := 1; y := -1;

    if (y \< a) { while (a \< x) { x := x - 1; y := y - 1; } } else { x
    := x + 1; y := -1; }

    return y; }

    IR:

    def foo(int a) { \[B0\] x \<- 1 y \<- -1 ~tmp0~ \<- y \< a jump~if0~
    ~tmp0~ IF~FALSE0~

    \[B1\] WHILE~START1~: ~tmp1~ \<- a \< x jump~if0~ ~tmp1~ WHILE~END1~

    \[B2\] x \<- x - 1 y \<- y - 1 jump WHILE~START1~

    \[B3\] WHILE~END1~: jump IF~END0~

    \[B4\] IF~FALSE0~: x \<- x + 1 y \<- -1

    \[B5\] IF~END0~: return y }

    ANALYSIS:

    \[show CFG\]

    initialization:

    IN~0~: \[a = Any, x = 0, y = 0, ~tmp0~ = Uninit, ~tmp1~ = Uninit\]
    OUT~0~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ = Uninit,
    ~tmp1~ = Uninit\]

    IN~1~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ = Uninit, ~tmp1~
    = Uninit\] OUT~1~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ =
    Uninit, ~tmp1~ = Uninit\]

    IN~2~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ = Uninit, ~tmp1~
    = Uninit\] OUT~2~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ =
    Uninit, ~tmp1~ = Uninit\]

    IN~3~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ = Uninit, ~tmp1~
    = Uninit\] OUT~3~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ =
    Uninit, ~tmp1~ = Uninit\]

    IN~4~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ = Uninit, ~tmp1~
    = Uninit\] OUT~4~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ =
    Uninit, ~tmp1~ = Uninit\]

    IN~5~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ = Uninit, ~tmp1~
    = Uninit\] OUT~5~: \[a = Uninit, x = Uninit, y = Uninit, ~tmp0~ =
    Uninit, ~tmp1~ = Uninit\]

    \[worklist order: 0, 4, 5, 1, 3, 2, 1, 2, 3, 5\]

    \[!! in lecture i think i just initialized the worklist with the
    first basic block. this works for this analysis and language, but in
    general you need to initialize the worklist with all basic blocks.\]

    final answer:

    IN~0~: \[a = Any, x = 0, y = 0, ~tmp0~ = Uninit, ~tmp1~ = Uninit\]
    OUT~0~: \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Uninit\]

    IN~1~: \[a = Any, x = Any, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]
    OUT~1~: \[a = Any, x = Any, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

    IN~2~: \[a = Any, x = Any, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]
    OUT~2~: \[a = Any, x = Any, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

    IN~3~: \[a = Any, x = Any, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]
    OUT~3~: \[a = Any, x = Any, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

    IN~4~: \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Uninit\]
    OUT~4~: \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Uninit\]

    IN~5~: \[a = Any, x = Any, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]
    OUT~5~: \[a = Any, x = Any, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

2.  exercise

    perform signedness DFA on the following program:

    def foo(int a) : int { int x; int y;

    x := 1; y := -1;

    while (x \< a) { if (y \< a) { x := x + 1; y := y - 1; } else { x :=
    1; y := -1; } }

    return y; }

    IR:

    def foo(int a) { \[B0\] x \<- 1 y \<- -1

    \[B1\] WHILE~START0~: ~tmp0~ \<- x \< a jump~if0~ ~tmp0~ WHILE~END0~

    \[B2\] ~tmp1~ \<- y \< a jump~if0~ ~tmp1~ IF~FALSE1~

    \[B3\] x \<- x + 1 y \<- y - 1 jump IF~END1~

    \[B4\] IF~FALSE1~ x \<- 1 y \<- -1

    \[B5\] IF~END1~: jump WHILE~START0~

    \[B6\] WHILE~END0~: return y }

    ANALYSIS:

    \[show CFG\]

    IN~0~: \[a = Any, x = 0, y = 0, ~tmp0~ = Uninit, ~tmp1~ = Uninit\]
    OUT~0~: \[a = Any, x = +, y = -, ~tmp0~ = Uninit, ~tmp1~ = Uninit\]

    IN~1~: \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\] OUT~1~:
    \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

    IN~2~: \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\] OUT~2~:
    \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

    IN~3~: \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\] OUT~3~:
    \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

    IN~4~: \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\] OUT~4~:
    \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

    IN~5~: \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\] OUT~5~:
    \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

    IN~6~: \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\] OUT~6~:
    \[a = Any, x = +, y = -, ~tmp0~ = Any, ~tmp1~ = Any\]

### example analyses and optimizations

1.  available expressions -\> gcse

    a common compiler optimization is [common subexpression
    elimination]{.underline}, i.e., trying to avoid recomputing
    expressions multiple times.

    EXAMPLE

    def foo(int a) { int x; int y; int z;

    x := 4 \* a;

    if (0 \< a) { a := a + 1; y := 4 \* a; }

    z := 4 \* a; return z; }

    note that the expression \"4 \* a\" has been computed before we get
    to the assignment of \'z\' no matter whether we enter the true
    branch of the conditional or not. also note that it has a different
    value depending on whether we entered the true branch or not.

    optimized program:

    def foo(int a) { int x; int y; int z; int ~tmp0~;

    x := 4 \* a; ~tmp0~ := x;

    if (0 \< a) { a := a + 1; y := 4 \* a; ~tmp0~ := y; }

    z := ~tmp0~; return z; }

    we have replaced the expression computation with a set of copies. we
    can break this process into two components: -- DFA: available
    expressions analysis -- optimization: GCSE

    1.  available expressions

        DEFINITION: an expression exp is [available]{.underline} at some
        program point pp if: -- exp is computed along every path from
        the entry of the function to pp -- the variables used in exp are
        not redefined after the last evaluation of exp along any path

        in order to define a DFA to compute available expressions, we
        need to define: -- L, the dataflow facts we\'re computing -- ⊓,
        the meet operator for L -- the abstract transfer functions for L

        the facts we\'re computing are which expressions are available,
        so the elements of L will be sets of expressions. we interpret
        an element of L as \"these are the expressions that are
        currently available\".

        the meet operator tells us how to conservatively approximate two
        elements of L using a single element of L. given two sets of
        expressions E1 and E2, we know that along one path the E1
        expressions are available and along another path the E2
        expressions are available. the only safe approximation of those
        two sets is E1 `\intersect`{=latex} E2, i.e., those expressions
        that are available along both paths.

        what is the weakest thing we can say about what expressions are
        available? i.e., the safest thing that is guaranteed not to give
        incorrect information? the empty set: there are no available
        expressions.

        what is the strongest thing we can say about what expressions
        are available? the set of all expressions contained in the
        function.

        we can define a single abstract transfer function and specialize
        it per basic block:

        OUT~k~ = GEN~k~ `\union`{=latex} (IN~k~ - KILL~k~)

        GEN~k~ is defined as the set of expressions that are computed in
        basic block k (whose variables are not defined afterwards in
        that basic block).

        KILL~k~ is defined as the set of expressions for which at least
        one variable is defined in basic block k.

        let\'s look at an example:

        \[B0\] a \<- b + c b \<- a + c d \<- b + d

        GEN~0~ = { a+c } KILL~0~ = any expression that uses \'a\',
        \'b\', or \'d\'

        note that B0 does [not]{.underline} generate b+c or b+d because
        it defines b and d after those expressions are evaluated.

        so the formula OUT~k~ = GEN~k~ `\union`{=latex} (IN~k~ -
        KILL~k~) says: -- take all the expressions that are available
        coming into this block (i.e., IN~k~) -- remove those expressions
        that are killed by this block (i.e., subtract KILL~k~) -- add
        back those expressions that are generated by this block (i.e.,
        union GEN~k~)

        \[note that the order is important: if we union GEN~k~ and then
        subtract KILL~k~ we may lose precision. see the \'a+c\' example
        above.\]

        QUICK EXERCISE

        a \<- b + c x \<- y - z b \<- x + y x \<- a \* b

        what are the GEN and KILL sets?

        SOLUTION

        GEN = {y-z, a\*b} KILL = {b+c, x+y} plus any expressions that
        contain a, x, b, x

        now we have a complete available expressions DFA and we can use
        the DFA framework:

        1.  define the set of all expressions computed in the function;
            call this set E.

        2.  for each basic block k, define GEN~k~ and KILL~k~ by looking
            at the instructions in the basic block.

        3.  initialize IN~0~ to {}, i.e., there are no available
            expressions at the beginning of the function.

        4.  optimistically initialize all other IN and OUT sets to E
            (this is safe, because the analysis will remove expressions
            as necessary).

        5.  put all basic blocks onto the worklist.

        6.  apply the worklist algorithm until it is empty.

        ```{=html}
        <!-- -->
        ```
        1.  example

            SOURCE CODE

            def foo(int a, int b) : int { // returns a^b^ int x; int y;
            int z;

            x := 1; y := a;

            while (x != b) { z := x \* 2; if (z \<= b) { y := y \* y; x
            := x \* 2; } else { y := y \* a; x := x + 1; } }

            return y; }

            IR:

            def foo(int a, int b) { \[B0\] x \<- 1 y \<- a

            \[B1\] WHILE~START0~: ~tmp0~ \<- x != b jump~if0~ ~tmp0~
            WHILE~END0~

            \[B2\] z \<- x \* 2 ~tmp1~ \<- z \<= b jump~if0~ ~tmp1~
            IF~FALSE1~

            \[B3\] y \<- y \* y x \<- x \* 2 jump IF~END1~

            \[B4\] IF~FALSE1~: y \<- y \* a x \<- x + 1

            \[B5\] IF~END1~: jump WHILE~START0~

            \[B6\] WHILE~END0~: return y }

            \[show CFG\]

            E = { x!=b, x\*2, z\<=b, y\*y, y\*a, x+1 }

            GEN~0~ = {} KILL~0~ = { x!=b, x\*2, y\*y, y\*a, x+1 }

            GEN~1~ = { x!=b } KILL~1~ = {}

            GEN~2~ = { x\*2, z\<=b } KILL~2~ = { z\<=b }

            GEN~3~ = {} KILL~3~ = { x!=b, x\*2, y\*y, y\*a, x+1 }

            GEN~4~ = {} KILL~4~ = { x!=b, x\*2, y\*y, y\*a, x+1 }

            GEN~5~ = {} KILL~5~ = {}

            GEN~6~ = {} KILL~6~ = {}

            now we can initialize the IN and OUT sets for each basic
            block, insert all basic blocks into the worklist, and apply
            the worklist algorithm. the final solution is:

            IN~0~ = {} OUT~0~ = {}

            IN~1~ = {} OUT~1~ = { x!=b }

            IN~2~ = { x!=b } OUT~2~ = { x!=b, x\*2, z\<=b }

            IN~3~ = { x!=b, x\*2, z\<=b } OUT~3~ = { z\<=b }

            IN~4~ = { x!=b, x\*2, z\<=b } OUT~4~ = { z\<=b }

            IN~5~ = { z\<=b } OUT~5~ = { z\<=b }

            IN~6~ = { x!=b } OUT~6~ = { x!=b }

        2.  exercise

            perform the available expressions analysis on the following
            program:

            def foo(int a, int b) : int { int x; int y;

            x := a + b; y := a \* b;

            while (a + b \< y) { a := a + 1; x := a + b; }

            return x; }

            SOLUTION

            IR:

            def foo(int a, int b) { \[B0\] x \<- a + b y \<- a \* b

            \[B1\] WHILE~START0~: ~tmp0~ \<- a + b ~tmp1~ \<- ~tmp0~ \<
            y jump~if0~ ~tmp1~ WHILE~END0~

            \[B2\] a \<- a + 1 x \<- a + b jump WHILE~START0~

            \[B3\] WHILE~END0~: return x }

            \[show CFG\]

            E = { a+b, a\*b, ~tmp0~\<y, a+1 }

            GEN~0~ = { a+b, a\*b } KILL~0~ = { ~tmp0~\<y }

            GEN~1~ = { a+b, ~tmp0~\<y } KILL~1~ = { ~tmp0~\<y }

            GEN~2~ = { a+b } KILL~2~ = { a+b, a\*b, a+1 }

            GEN~3~ = {} KILL~3~ = {}

            ---

            IN~0~ = {} OUT~0~ = { a+b, a\*b }

            IN~1~ = { a+b } OUT~1~ = { a+b, ~tmp0~\<y }

            IN~2~ = { a+b, ~tmp0~\<y } OUT~2~ = { a+b, ~tmp0~\<y }

            IN~3~ = { a+b, ~tmp0~\<y } OUT~3~ = { a+b, ~tmp0~\<y }

    2.  GCSE

        once we have the available expressions info, how do we use it to
        eliminate common subexpressions? there are a variety of schemes
        with various tradeoffs. we\'ll look at a simple scheme that
        works, but requires other optimization passes to clean up after
        it.

        1.  for each expression in E, create a unique variable name
            (~opt0~, ~opt1~, etc).

        2.  for each definition \"var1 \<- op1 OP op2\", look up the
            unique name for expression \"op1 OP op2\" (say ~optX~) and
            insert an assignment immediately afterwards \"~optX~ \<-
            var1\".

        3.  for each use of expression \"op1 OP op2\", see if it is
            available at that point (i.e., it is available at the
            beginning of the basic block and is not killed before the
            use). if so, replace it with ~optX~.

        we end up with a bunch of extraneous copies, but they can be
        cleaned up by another pass like copy propagation and dead code
        elimination.

        1.  example

            def foo(int a, int b) { \[B0\] x \<- 1 y \<- a

            \[B1\] WHILE~START0~: ~tmp0~ \<- x != b jump~if0~ ~tmp0~
            WHILE~END0~

            \[B2\] z \<- x \* 2 ~tmp1~ \<- z \<= b jump~if0~ ~tmp1~
            IF~FALSE1~

            \[B3\] y \<- y \* y x \<- x \* 2 jump IF~END1~

            \[B4\] IF~FALSE1~: y \<- y \* a x \<- x + 1

            \[B5\] IF~END1~: jump WHILE~START0~

            \[B6\] WHILE~END0~: return y }

            E = { x!=b, x\*2, z\<=b, y\*y, y\*a, x+1 }

            IN~0~ = {} OUT~0~ = {}

            IN~1~ = {} OUT~1~ = { x!=b }

            IN~2~ = { x!=b } OUT~2~ = { x!=b, x\*2, z\<=b }

            IN~3~ = { x!=b, x\*2, z\<=b } OUT~3~ = { z\<=b }

            IN~4~ = { x!=b, x\*2, z\<=b } OUT~4~ = { z\<=b }

            IN~5~ = { z\<=b } OUT~5~ = { z\<=b }

            IN~6~ = { x!=b } OUT~6~ = { x!=b }

            STEP 1

            ~opt0~ = x != b ~opt1~ = x \* 2 ~opt2~ = z \<= b ~opt3~ =
            y \* y ~opt4~ = y \* a ~opt5~ = x + 1

            STEP 2

            def foo(int a, int b) { \[B0\] x \<- 1 y \<- a

            \[B1\] WHILE~START0~: ~tmp0~ \<- x != b ~opt0~ \<- ~tmp0~
            jump~if0~ ~tmp0~ WHILE~END0~

            \[B2\] z \<- x \* 2 ~opt1~ \<- z ~tmp1~ \<- z \<= b ~opt2~
            \<- ~tmp1~ jump~if0~ ~tmp1~ IF~FALSE1~

            \[B3\] y \<- y \* y ~opt3~ \<- y x \<- x \* 2 ~opt1~ \<- x
            jump IF~END1~

            \[B4\] IF~FALSE1~: y \<- y \* a ~opt4~ \<- y x \<- x + 1
            ~opt5~ \<- x

            \[B5\] IF~END1~: jump WHILE~START0~

            \[B6\] WHILE~END0~: return y }

            STEP 3

            def foo(int a, int b) { \[B0\] x \<- 1 y \<- a

            \[B1\] WHILE~START0~: ~tmp0~ \<- x != b ~opt0~ \<- ~tmp0~
            jump~if0~ ~tmp0~ WHILE~END0~

            \[B2\] z \<- x \* 2 ~opt1~ \<- z ~tmp1~ \<- z \<= b ~opt2~
            \<- ~tmp1~ jump~if0~ ~tmp1~ IF~FALSE1~

            \[B3\] y \<- y \* y ~opt3~ \<- y x \<- ~opt1~ ~opt1~ \<- x
            jump IF~END1~

            \[B4\] IF~FALSE1~: y \<- y \* a ~opt4~ \<- y x \<- x + 1
            ~opt5~ \<- x

            \[B5\] IF~END1~: jump WHILE~START0~

            \[B6\] WHILE~END0~: return y }

        2.  exercise

            def foo(int a, int b) { \[B0\] x \<- a + b y \<- a \* b

            \[B1\] WHILE~START0~: ~tmp0~ \<- a + b ~tmp1~ \<- ~tmp0~ \<
            y jump~if0~ ~tmp1~ WHILE~END0~

            \[B2\] a \<- a + 1 x \<- a + b jump WHILE~START0~

            \[B3\] WHILE~END0~: return x }

            E = { a+b, a\*b, ~tmp0~\<y, a+1 }

            IN~0~ = {} OUT~0~ = { a+b, a\*b }

            IN~1~ = { a+b } OUT~1~ = { a+b, ~tmp0~\<y }

            IN~2~ = { a+b, ~tmp0~\<y } OUT~2~ = { a+b, ~tmp0~\<y }

            IN~3~ = { a+b, ~tmp0~\<y } OUT~3~ = { a+b, ~tmp0~\<y }

            given the above information, optimize the given program.

            SOLUTION

            STEP 1

            ~opt0~ = a + b ~opt1~ = a \* b ~opt2~ = ~tmp0~ \< y ~opt3~ =
            a + 1

            STEP 2

            def foo(int a, int b) { \[B0\] x \<- a + b ~opt0~ \<- x y
            \<- a \* b ~opt1~ \<- y

            \[B1\] WHILE~START0~: ~tmp0~ \<- a + b ~opt0~ \<- ~tmp0~
            ~tmp1~ \<- ~tmp0~ \< y ~opt2~ \<- ~tmp1~ jump~if0~ ~tmp1~
            WHILE~END0~

            \[B2\] a \<- a + 1 ~opt3~ \<- a x \<- a + b ~opt0~ \<- x
            jump WHILE~START0~

            \[B3\] WHILE~END0~: return x }

            STEP 2

            def foo(int a, int b) { \[B0\] x \<- a + b ~opt0~ \<- x y
            \<- a \* b ~opt1~ \<- y

            \[B1\] WHILE~START0~: ~tmp0~ \<- ~opt0~ ~opt0~ \<- ~tmp0~
            ~tmp1~ \<- ~tmp0~ \< y ~opt2~ \<- ~tmp1~ jump~if0~ ~tmp1~
            WHILE~END0~

            \[B2\] a \<- a + 1 ~opt3~ \<- a x \<- a + b ~opt0~ \<- x
            jump WHILE~START0~

            \[B3\] WHILE~END0~: return x }

2.  reaching definitions -\> copy propagation

    \[omitted for time\]

3.  liveness / neededness -\> dead code elimination

    \[omitted for time\]

4.  global constant propagation and folding

    another common compiler optimization is constant propagation (we
    often just say constant propagation when we actually are including
    constant folding).

    def foo(int a) : int { int x; int y; int z;

    y := 3; // a = Any, x = 0, y = 3, z = 0

    if (a \< 0) { x := 2; // a = Any, x = 2, y = 3, z = 0 z := y + x; //
    a = Any, x = 2, y = 3, z = 5 } else { x := 4; // a = Any, x = 4, y =
    3, z = 0 z := y + x; // a = Any, x = 4, y = 3, z = 7 }

    return z; // a = Any, x = ?, y = 3, z = ? }

    becomes:

    def foo(int a) : int { int z;

    if (a \< 0) { z := 5; } else { z := 7; }

    return z; }

    you might think that this doesn\'t come up in real programs, but in
    fact as code is lowered from AST to IR to assembly and as other
    optimizations are applied, constants become increasingly common.

    as before we can divide this process into an analysis and an
    optimization, but here the optimization itself is trivial (just
    replace variables with their constant values and evaluate the
    operations involving constant operands). so the only interesting
    part is the analysis itself.

    as before, we need to define L (the set of possible solutions), ⊓
    (the approximation operator), and the abstract transfer functions.
    the thing that makes this analysis different from the ones we\'ve
    seen before is that L contains an infinite number of possible
    solutions, because there are an infinite number of integers.

    -- you might think that since we\'re using 32-bit integers that
    there are only a finite number (albeit a large number). this is
    correct for L1 specifically, but let\'s look at the bigger picture:

    -- for C/C++, the bitwidth of an integer is unspecified. that means
    that the exact same program compiled on different machines can
    behave differently: one on an int may be 16 bits, on another 32
    bits, on another 64 bits, etc. if we\'re doing target-agnostic
    optimization then we can\'t assume a given size of int, so we have
    to treat them as if they are arbitrarily big.

    -- for other languages, the int type is arbitrary-precision, that
    is, they don\'t have a set bitwidth. a single integer can be as big
    as the machine\'s memory can hold.

    -- therefore, for this optimization we assume that there are an
    infinite number of possible integers and thus an infinite number of
    possible analysis solutions.

    the fact that L is infinite is dangerous in terms of a decidable
    analysis. if we aren\'t careful, we could easily get an analysis
    that never terminates. consider:

    def foo(int a) : int { int x; while (x \< a) { x := x + 1; } // x =
    0, 1, 2, 3, ... return x; }

    fortunately, the dataflow analysis framework gives us the tools we
    need to make certain this never happens (to be discussed shortly).

    Const = { ..., -1, 0, 1, ... } ∪ { Uninit, Any } L = Var -\> Const

      ⊓        Uninit   c1    c2    Any
      -------- -------- ----- ----- -----
      Uninit   Uninit   c1    c2    Any
      c1       c1       c1    Any   Any
      c2       c2       Any   c2    Any
      Any      Any      Any   Any   Any

    (c ∈ Const) . c ⊓ Uninit = Uninit ⊓ c = c (c ∈ Const) . c ⊓ Any =
    Any ⊓ c = Any (n ∈ Integer) . n ⊓ n = n (n1, n2 ∈ Integer) . n1 ⊓ n2
    = Any (if n1 != n2)

    notice that the meet operator is really over-approximating: the best
    approximation of 2 different integers is Any, i.e., we jump from
    \"it could be n1 or n2\" to \"it could be any integer at all\". you
    might think that we could do better, e.g.:

    n1 ⊓ n2 = \[n1..n2\] n1 ⊓ n2 = {n1, n2}

    but the dataflow analysis framework disallows these definitions of ⊓
    because they would lead to nonterminating analyses. there is a more
    comprehensive analysis framework called [abstract
    interpretation]{.underline} that would allow us to use these and
    even more complicated and precise definitions of L and ⊓ (under
    certain restrictions), but these are outside the scope of dataflow
    analysis and usually not used inside compilers.

    finally, the abstract transfer functions:

      \#ADD    Uninit   n2        Any
      -------- -------- --------- --------
      Uninit   Uninit   Uninit    Uninit
      n1       Uninit   n1 + n2   Any
      Any      Uninit   Any       Any

    \[similarly for the other operators\]

    1.  example

        def foo(int a) : int { int x; int y; int z;

        // a = Any, x = 0, y = 0, z = 0

        if (a \< 0) { x := 2; // a = Any, x = 2, y = 0, z = 0 y := 3; //
        a = Any, x = 2, y = 3, z = 0 z := x + y; // a = Any, x = 2, y =
        3, z = 5 } else { x := 3; // a = Any, x = 3, y = 0, z = 0 y :=
        2; // a = Any, x = 3, y = 2, z = 0 z := x + y; // a = Any, x =
        3, y = 2, z = 5 }

        return z; // a = Any, x = Any, y = Any, z = 5 }

### the DFA framework

now that we\'ve seen some example, let\'s look a little into the DFA
framework and why it works. the theory behind DFA uses a branch of
discrete math called Order Theory. I teach a graduate-level course on
Program Analysis that goes into this topic in detail, but we\'ll just
skim the surface.

there are two main questions:

1.  how do we know that the analysis we\'ve defined is giving us a safe
    answer?
2.  how do we know that it is decidable, i.e., that it is guaranteed to
    terminate?

the first question is outside the scope of this class (see my graduate
class for more). the second one we can at least get a conceptual idea of
why it works. recall that we use a worklist algorithm to compute the
DFA: we pull out a basic block, process it, and if its output changes we
put its successors back onto the worklist. this algorithm proceeds until
the worklist is empty. so how do we know that it will, eventually,
become empty? the DFA framework gives us some requirements that, if they
are satisfied, will guarantee termination:

1.  L contains an element that over-approximates all other elements;
    this is the safest (and least precise) analysis solution.

-- for signedness and constant propagation, that element is Any -- for
available expressions, that element is {}

1.  the ⊓ operator is always defined for any two elements of L a and b,
    and will give us the element of L that most closely approximates
    both a and b.

2.  from any given element of L, there are only a finite number of
    elements that over-approximate it until we get to the \"maximal\",
    safest element.

-- this is where constant propagation [would]{.underline} break if we
used elements of L like ranges or sets of numbers. since there are
infinite integers, there are infinite ranges/sets that over-approximate
any given solution.

1.  the abstract transfer functions are monotone. a function is monotone
    if, when x \<= y, f(x) \<= f(y). in other words, the function
    preserves the order of the elements.

    A. f(x) = x + 1 is monotone B. f(x) = -x is not monotone

these, taken together, guarantee termination. why? recall that each time
we process a basic block in the worklist algorithm, we (1) take the meet
of the input solutions; then (2) apply the abstract transfer function;
then (3) if the output solution changed, add the successor basic blocks
to the worklist.

the first three requirements mean that, for a given basic block, the
meet of its input solutions can only get less precise (never more
precise) and we can only take the meet of its input solutions a finite
number of times before we get a stable input that never changes (the
safest, least precise solution in the worst case, but something more
precise than the safest solution if we\'re lucky).

the last requirement means that, if we\'re getting an input that is less
precise, then we cannot get an output solution that is more precise.
that is, since the input solution can only get less precise, the output
solution can only get less precise.

but we know that there is a limit to how imprecise we can get (the
\"maximal\" solution) and we\'ll reach it in a finite number of steps.
therefore eventually we are guaranteed that at some point we will
process a basic block and its output won\'t change. but that means that
we won\'t put any successors on the worklist. this reasoning is true for
all basic blocks, and therefore eventually we are guaranteed that no
basic block will put any successors on the worklist. therefore the
worklist will eventually become empty.

the reasoning i\'ve given you here is rather vague and hand-wavy; the
technical description and proof requires a lot more math that we won\'t
get into. but hopefully this conveys the basic idea of why we want to
define a DFA the way that we do.

order of optimizations
----------------------

the last thing i want to mention is that the order of optimizations can
make a big difference in how effective they are (and also how many times
we apply them).

we saw for GCSE that our optimization left a lot of copy assignments and
dead variables, which we said could be cleaned up by other optimizations
(like copy propagation and dead code elimination). it turns out that
many optimizations modify the code in such a way that they enable other
optimizations, and this can even be cyclic (A enables B which enables A
again).

so what\'s the best order? this isn\'t an easy question to answer, and
heavily depends on the code being optimized. the optimization levels in
a compiler such as gcc or clang (-O1, -O2, -O3, etc) are just convenient
flags that specify a series of optimizations determined by the compiler
developers to \"usually\" do a pretty good job. you (the compiler user)
can actually specify the exact set of optimizations that you want to
apply and what order you want to apply them in, and if you do it right
you can often get even faster code than by using the default compiler
flags. however, figuring out what optimizations to use in what order can
take a lot of trial and error, and you\'ll never know whether you found
the best possible ordering.

SSA
---

\[omitted for time\]
