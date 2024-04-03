# TODO:

- create handouts for language, compiler passes
- implement stuff for assignments
- create autograders for assignments

# Spring 2024 notes
## lecture timing

- week  1.1: through `overview` -> `putting it all together`
- week  1.2: 
- week  2.1: 
- week  2.2: 
- week  3.1: 
- week  3.2: 
- week  4.1: 
- week  4.2: 
- week  5.1: 
- week  5.2: 
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

- week 2T assign-1 (lexer) [10 days]
- week 3F assign-2 (parser/validation) [11 days]
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

    - see `docs/linked_list.cb`
    - see `docs/matrices.cb`

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
Ampersand    &
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
    
    - consider the difference between `(1 + 2) * 3` and `1 + (2 * 3)`

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

- we'll begin by assuming we have a suitable LL(1) grammar and discuss how to parse it; later we'll discuss how to transform a grammar into LL(1) if possible

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

- backtracking kills performance, can we prevent it? not in general, but for some grammars yes we can

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

- why `LL(k)`?

    - we're processing the input from `L`eft to right
    - we're tracking a `L`eftmost derivation
    - we're using `k` tokens of lookahead

### exercise

- given the folling grammar

```
S ::= aPb | Qc | cRd | TcP 
P ::= QR | TR | ε 
Q ::= fR | b 
R ::= d | gbc 
T ::= ea | Ra
```

- write pseudocode for a recursive descent LL(1) parser

- trace the parser for inputs:

    - `afgbcdb`
    - `daceagbc`

- SOLUTION

```
fn S() {
  if next token is 'a' {
    input.consume('a');
    P();
    input.consume('b');
  }
  else if next token is 'f' {
    Q();
    input.consume('c');
  }
  else if next token is 'c' {
    input.consume('c');
    R();
    input.consume('d');
  }
  else if next token is 'e' {
    T();
    input.consume('c');
    P();
  }
  else fail
}

fn P() {
  if next token is 'f' {
    Q();
    R();
  }
  else if next token is 'e' {
    T();
    R();
  }
}

fn Q() {
  if next token is 'f' {
    input.consume('f');
    R();
  }
  else if next token is 'b' {
    input.consume('b');
  }
  else fail
}

fn R() {
  if next token is 'd' {
    input.consume('d');
  }
  else if next token is 'g' {
    input.consume('gbc');
  }
  else fail
}

fn T() {
  if next token is 'e' {
    input.consume('ea');
  }
  else if next token is 'd' or 'g' {
    R();
    input.consume('a');
  }
  else fail
}
```

## building the AST

- recall that the derivation tree (aka parse tree, concrete syntax tree) contains more information than we really need after parsing

    - e.g., punctuation, parentheses, braces, etc

    - it shows _how_ the program was parsed, but all we need after parsing is the underlying structure of the program

    - this structure is called the _abstract syntax tree_ (AST)

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
E ⟶ [FX] ⟶ [GY]X ⟶ [id(x)]YX ⟶ id(x)[]X ⟶ id(x)[+]FX ⟶ id(x)+[GY]X
  ⟶ id(x)+[id(y)]YX ⟶ id(x)+id(y)[*GY]X ⟶ id(x)+id(y)*[id(z)]YX
  ⟶ id(x)+id(y)*id(z)[]X ⟶ id(x)+id(y)*id(z)[]
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

- example OR exercise: given the grammar and parser below, modify the parser to return an AST as also defined below and run it on `payawazaxa` [see OneNote]

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

## transforming a grammar to LL(1)

- TODO:

# validation TODO:

# lowering TODO:

# codegen TODO:

# register allocation TODO:

# IR optimization TODO:

# memory management TODO:

# ==== OLD ===================================================================

initial language/compiler (L1/C1)
=================================

### transforming to LL(1)

1.  intro

    we know now how to implement a suitable grammar using a recursive
    descent LL(1) parser; now we\'ll talk about how to make a grammar
    suitable.

    note that so far we haven\'t covered how to build an actual parse
    tree or AST, we\'ve just returned a boolean; we\'ll ignore building
    the AST for now and once we have a suitable grammar i\'ll show how
    to tweak it to build the AST.

2.  establishing precedence

    the initial example of an ambiguous grammar i gave earlier was a
    simple expression grammar, something like:

    E ::= id \| E + E \| E \* E \| (E)

    the problem was that a string such as \"x + y \* z\" could be parsed
    two ways:

    (x+y)\*z x+(y\*z)

    the grammar admits either one. we need to enforce a single
    interpretation for the parser; we do this by specifying the relative
    precedence of the operators. conventionally, for example,
    multiplication has a higher precedence than addition so we would
    want \"x + y \* z\" to be parsed as x+(y\*z).

    there are several strategies that have been developed to enforce
    precedence in a grammar, but we\'re going to go with the classical
    solution which involves modifying the grammar itself to enforce the
    levels of precedence. how do we do this?

    1.  decide on the levels of precedence, e.g., {()} {\*,÷} {+,-}

    2.  create a nonterminal for each level of precedence (we can reuse
        the original nonterminal for the lowest level of precedence).

    3.  factor out the operations into the appropriate nonterminal for
        their level of precedence, e.g.:

        E ::= E + F \| F F ::= F \* G \| G G ::= (E) \| id

    here we have three precedence levels, so three nonterminals: E is
    for the lowest precedence operator +, F is for the next precedence
    level \*, and G is for the highest precedence level ().

    notice the following properties:

    1.  each nonterminal rule that applies an operator has one operand
        that is the same nonterminal again and the other is the next
        highest precedence level nonterminal

        this allows the expression to have multiple of the same
        precedence level operators in a row. choosing which side is
        which controls the associativity of the operator: having the
        same nonterminal on the left makes the operator left
        associative; having it on the right makes the operator right
        associative.

    2.  each nonterminal (except the highest precedence, G) allows for
        applying an operator at the level of predecence or directly
        falling through to the next level.

        this allows the expression to not have any operators at the
        lower precedence level, e.g., an expression that doesn\'t have +
        in it.

    3.  the base cases for expressions (identifiers, constants, etc) are
        always at the highest level of precedence.

        this allows the expression to just be an identifier or constant
        without any operators at all.

    let\'s look at some examples:

    1.  x + y \* z
    2.  x + y + z
    3.  x \* y + z
    4.  (x + y) \* z

    we\'ve looked at this using arithmetic operators as our examples,
    but it applies in many other situations:

    1.  relational and logical operators: !(x \< y) && (z \< y)

    2.  subscript operators: a + b\[i\]

    3.  type casts: (double)a / b

    etc.

    1.  exercise

        here is a toy grammar:

        A ::= A R A \| p A \| a R ::= w \| x \| y \| z

        suppose we define the following precedence levels: {p} {w,x}
        {y,z}

        and say that the operators are left associative. rewrite the
        grammar to enforce the correct prededence, then verify the
        following example inputs are handled correctly:

        p a y a w a z a x a ==\> (((p a) y (a w a)) z (a x a)) a z p a w
        p a x a y a ==\> ((a z (((p a) w (p a)) x a)) y a)

        solution:

        A ::= A y B \| A z B \| B B ::= B w C \| B x C \| C C ::= p C \|
        a

3.  dealing with left recursion

    1.  what is left recursion

        once we\'ve established precedence, we have another problem to
        worry about: nontermination. this problem is an artifact of the
        way we\'re implementing the parser using recursive descent,
        i.e., implementing the PDA implicitly using recursive function
        calls. consider the factored grammar from before:

        E ::= E + F \| F F ::= F \* G \| G G ::= (E) \| id

        assume that we implement this using a recursive descent parser,
        as explained in a previous lecture, and let\'s see what happens
        on an example input \"x + y \* z\".

        call E() enter case E + F call E() enter case E + F call E() ...

        notice that we just keep recursively calling E() forever (or
        until we get a stack overflow). why does this happen? because
        there is a recursive cycle in the grammar E --\>\* E that does
        not consume any input tokens, i.e., there is no call to match()
        between invocations of E().

        this is an example of what\'s called [left
        recursion]{.underline}. a grammar is left recursive if there
        exists a derivation A --\>\* Aα for some nonterminal A. any
        recursive descent parser may fail to terminate for a left
        recursive grammar.

    2.  direct, indirect, and hidden left recursion

        we can define three different kinds of left recursion (though
        some texts lump the second two into a single category):

        direct left recursion: there is a production of the form A ::=
        Aα.

        -   there are two examples of direct left recursion in the
            previous grammar example.

        indirect left recursion: there is a set of mutually recursive
        productions the allow a left-recursive derivation.

        -   example:

            A ::= B \| alice B ::= C \| bob C ::= A charlie

            consider the derivation A --\> B --\> C --\> A charlie

        hidden left recursion: like indirect except there is an epsilon
        rule that hides the left recursion.

        -   example:

            A ::= B \| alice B ::= C \| bob C ::= DA charlie D ::= dave
            \| ε

            consider the derivation A --\> B --\> C --\> DA charlie --\>
            A charlie

        in order to create an LL(1) recursive descent parser for a
        grammar, we must transform the grammar to remove all left
        recursion.

    3.  removing direct left recursion

        direct left recursion is the easiest to fix: any left-recursive
        production can be changed to an equivalent right-recursive set
        of rules as follows:

        given: A ::= Aα \| β \| γ

        where the greek letters are sequences of terminals and
        nonterminals not starting with A. this grammar specifies βα\* or
        γα\*. we can transform it into a non-left-recursive grammar that
        specifies the same language:

        A ::= βA\' \| γA\' A\' ::= αA\' \| ε

        we\'re expressing the same strings in different ways. a left
        recursive rule must have some non-recursive base case (β and γ
        above); the left recursion is saying that we can repeat the
        recursive part as many times as we want (α above) and then
        finish with the base cases. the rewritten rules says the same
        thing, but puts the recursion on the right instead of the left.
        this means that a recursive descent parser must consume a
        terminal from the input before making the recursive call; since
        the input is finite the parser must terminate.

        note that we assume α is not ε. if it is then we have the rule
        \'A ::= A\', which we can trivially delete.

        what if we have multiple left recursive rules for A?

        given: A ::= Aα \| Aβ \| γ

        then:

        A ::= γA\' A\' ::= αA\' \| βA\' \| ε

        again, there always has to be at least one rule that is not left
        recursive or we can trivially delete all the rules.

        EXAMPLE 1:

        E ::= E + T \| E - T \| T T ::= (E) \| id

        becomes

        E ::= T E\' E\' ::= + T E\' \| - T E\' \| ε T ::= (E) \| id

        \[go through parsing example: x + y - z\] \[notice that left
        associative has become right associative\]

        EXAMPLE 2:

        E ::= E + F \| F F ::= F \* G \| G G ::= (E) \| id

        changes to

        E ::= FE\' E\' ::= + FE\' \| ε F ::= GF\' F\' ::= \* GF\' \| ε G
        ::= (E) \| id

        \[go through parsing example: x + y \* z\]

    4.  removing indirect left recursion

        to remove indirect recursion there are several possible
        strategies, of which we\'ll look at the classical solution. the
        basic idea is to inline productions to turn the indirect
        recursion into direct recursion, then apply the transformation
        that removes direct recursion.

        let\'s see an example of that in action:

        A ::= B \| alice B ::= C \| bob C ::= A charlie

        we can start from A, B, or C to expose the indirect left
        recursion:

        A --\> B --\> C --\> A charlie B --\> C --\> A charlie --\> B
        charlie C --\> A charlie --\> B charlie --\> C charlie

        it doesn\'t matter which one we choose, as long as we break the
        left recursive cycle. let\'s pick A and inline once:

        A ::= C \| bob \| alice B ::= C \| bob C ::= A charlie

        then inline again:

        A ::= A charlie \| bob \| alice B ::= C \| bob C ::= A charlie

        then remove the direct left recursion:

        A ::= bob A\' \| alice A\' A\' ::= charlie A\' \| ε B ::= C \|
        bob C ::= A charlie

        notice in this example that B and C are no longer reachable from
        the starting nonterminal A; in general this may or may not be
        true.

        let\'s see what happens if we had picked C instead of A to start
        with:

        A ::= B \| alice B ::= C \| bob C ::= B charlie \| alice charlie

        and inline again:

        A ::= B \| alice B ::= C \| bob C ::= C charlie \| bob charlie
        \| alice charlie

        now remove the direct left recursion:

        A ::= B \| alice B ::= C \| bob C ::= bob charlie C\' \| alice
        charlie C\' C\' ::= charlie C\' \| ε

        it can be tedious and error-prone to manually try to find left
        recursive cycles. we can apply an algorithm to preventatively
        transform the grammar so that left recursive cycles can\'t
        possibly happen. this gives us a larger and more complex grammar
        than we might get if we did it manually because it transforms
        rules even when they aren\'t left recursive.

        the key insight of the algorithm is to order the nonterminals
        arbitrarily. then a left recursive cycle can only possibly
        happen if a nonterminal with order i directly derives a
        nonterminal with order j s.t. j \< i. we then inline the
        lower-ordered nonterminal into this rule. continue this process
        for all rules, then remove any direct left recursion from the
        final rule set:

        let the nonterminal be arbitrarily labeled A1..An. then:

        for i = 1 to n: if Ai ::= Aj α for j \< i then inline Aj into
        Ai\'s right-hand side transform any direct left recursive rules

        let\'s look at the following example again, assuming that A \< B
        \< C:

        A ::= B \| alice B ::= C \| bob C ::= A charlie

        rule A ::= B `=> CHECK
        rule A ::` alice `=> CHECK
        rule B ::` C `=> CHECK
        rule B ::` bob `=> CHECK
        rule C ::` A charlie `=> A < C, inline: C ::` B charlie \| alice
        charlie rule C ::= alice charlie `=> CHECK
        rule C ::` B charlie `=> B < C, inline: C ::` C charlie \| bob
        charlie rule C ::= bob charlie `=> CHECK
        rule C ::` C charlie ==\> CHECK done

        now there is one direct left recursive rule: C ::= C charlie.
        transform that and we\'re finished.

    5.  removing hidden left recursion

        note that the algorithm for preventing indirect left recursion
        doesn\'t work if there is hidden left recusion.

        A ::= B \| alice B ::= C \| bob C ::= DA charlie D ::= dave \| ε

        \[go through each rule, show that they all CHECK\]

        there are two basic ways to handle hidden left recursion, which
        end up actually being pretty similar. the first is transform the
        grammar to eliminate ε rules. there is a standard algorithm to
        do so, which you may have learned in 138. the second is to (1)
        compute which nonterminals are [nullable]{.underline}, i.e.,
        which nonterminals can derive ε (directly or indirectly),
        then (2) modify the above algorithm to take this information
        into account. this end up looking a lot like what you would do
        to remove ε rules altogether.

        A ::= B \| alice B ::= C \| bob C ::= DA charlie \| A charlie D
        ::= dave

    6.  exercise

        remove all left recursion in the following grammar:

        A ::= BC B ::= CA \| b C ::= AA \| a

        solution:

        \[after inlining\] A ::= BC B ::= CA \| b C ::= CACA \| bCA \| a

        \[after direct recursion elimination\] A ::= BC B ::= CA \| b C
        ::= bCAC\' \| aC\' C\' ::= ACAC\' \| ε

4.  lookahead

    1.  basic idea

        now that we\'ve fixed precedence to ensure that we get the
        correct AST and we\'ve removed left recursion to ensure
        termination, the remaining thing to worry about is making sure
        that the grammar is deterministic via lookahead (thus avoiding
        the need for backtracking).

        remember that [lookahead]{.underline} means peeking ahead in the
        input to the next token(s) in order to determine which
        production rule to use next. we\'re specifically going to allow
        a fixed, constant amount of lookahead (in our case, 1 token),
        which means that we won\'t be able to handle some grammars. for
        any fixed amount of lookahead we can come up with a grammar that
        requires more lookahead to become deterministic. but 1-token
        lookahead turns out to be good enough in most cases we care
        about.

        our intuition is that the property we\'re looking for in a
        grammar is that for any given nonterminal A that has multiple
        rules, looking at the next token in the input is sufficient to
        determine which rule we need to pick. a simple example is:

        S ::= aSa \| bSb \| c

        if we have an S symbol and need to expand it with one of these
        rules, by looking at the next token in the input we can tell
        which rule to use: \'a\': aSa; \'b\': bSb; \'c\': c.

        \[show with input \"bacab\"\]

        we\'ll say that a grammar with this property is a [predictive
        grammar]{.underline}. let\'s formalize this property. first
        we\'ll assume that there are no ε rules; then we\'ll expand the
        formalization to handle ε rules.

    2.  formal property without ε

        let α, β be strings of grammar symbols (T and NT)

        FIRST(α) = { t ∈ T \| α --\>\* tγ }

        in other words, FIRST(α) is the set of the [first]{.underline}
        terminals that can be derived from α with 0 or more applications
        of the grammar\'s production rules.

        there is an algorithm in the textbook to compute FIRST sets, but
        for simple grammars we can do it by inspection.

        EXAMPLE

        S ::= AB A ::= xBw \| yBz \| Bwz B ::= 0 \| 1

        FIRST(S) = {x,y,0,1} FIRST(A) = {x,y,0,1} FIRST(B) = {0,1}
        FIRST(xBw) = {x} FIRST(yBz) = {y} FIRST(Bwz) = {0,1}

        a grammar can be parsed with no backtracking using a lookahead
        of 1 IF the following holds: for any nonterminal A such that A
        ::= α \| β, FIRST(α) ∩ FIRST(β) = ∅.

        during parsing when we\'re expanding an A node, we just need to
        look at the next token t in the input to determine which
        production to use (i.e., the one that has t in its FIRST set).

    3.  formal property with ε

        now suppose that we [do]{.underline} have ε productions (which
        is likely if we needed to remove left recursion).

        \#\# EXAMPLE 1

        S ::= AB A ::= xBw \| yBz \| Bwz B ::= 0 \| 1 \| ε

        FIRST(S) = {x,y,0,1,w} FIRST(A) = {x,y,0,1,w} FIRST(B) = {0,1}
        FIRST(xBw) = {x} FIRST(yBz) = {y} FIRST(Bwz) = {0,1,w}

        mostly we can just \"read past\" the ε to fill in the FIRST
        sets, but sometimes this isn\'t sufficient.

        \#\# EXAMPLE 2

        A ::= xBA \| f B ::= xwB \| ε

        FIRST(A) = {x,f} FIRST(B) = {x} FIRST(xBA) = {x} FIRST(f) = {f}
        FIRST(xwB) = {x} FIRST(ε) = {}

        at a glance, this looks predictive: for both A and B
        productions, the FIRST sets of the alternative productions are
        disjoint. but consider parsing the input \"xxf\":

        A expands to xBA consume x from input B expands to xwB consume x
        from input ERROR: terminal w doesn\'t match token f

        the correct way to parse this would be to expand B to ε, then A
        to xBA, then B to ε, then A to f. but we can\'t figure this out
        just by looking at the next token in the input. what\'s going
        on? ε allows us to \"throw away\" the nonterminal at any time,
        and we don\'t know when we should do that or not.

        enter FOLLOW sets. for any NT A, FOLLOW(A) is the set of
        [first]{.underline} terminals that can immediately follow any
        expansion of A. the textbook has another algorithm for this, but
        again for simple grammars we can do it by hand. for the example
        above:

        FOLLOW(A) = {} FOLLOW(B) = {x,f}

        now we revisit the criteria for being a predictable grammar. in
        addition to the original requirement for A ::= α \| β, we add
        the following:

        if α --\>\* ε then FIRST(β) ∩ FOLLOW(A) = ∅

        in other words, we can tell by looking at the next token whether
        we should expand A to something or use the ε to throw A away.

        consider our previous example and note that FIRST(xwB) ∩
        FOLLOW(B) = {x} ∩ {x,f} = {x}, and thus this grammar is
        [not]{.underline} predictive.

    4.  exercise

        compute the FIRST and FOLLOW sets of the following grammar and
        confirm that it is [not]{.underline} predictive:

        A ::= xBy \| Bx \| zCx B ::= wB \| ε C ::= wC \| DB D ::= yD \|
        ε

              FIRST   FOLLOW
          --- ------- --------
          A   w,x,z   ∅
          B   w       x,y
          C   w,y     x
          D   y       w

        -   for A, FIRST(xBy) and FIRST(Bx) are not disjoint (both
            have x)
        -   for C, FIRST(wC) and FIRST(DB) are not disjoint (both
            have w)

5.  left factoring

    1.  the transformation

        if a grammar is not predictive, sometimes we can transform it to
        make it predictive. one heuristic we can use is a transformation
        called [left factoring]{.underline}.

        EXAMPLE: suppose we add arrays and functions to expressions

        Factor ::= Id \| Id \[ ExpList \] \| Id ( ExpList ) ExpList ::=
        Exp, ExpList \| Exp

        this is not predictive, but we can make it predictive:

        Factor ::= Id Args Args ::= \[ ExpList \] \| ( ExpList ) \| ε
        ExpList ::= Exp, ExpList \| Exp

        this transformation is called LEFT-FACTORING.

        WHAT\'S HAPPENING:

        A ::= αβ1 \| αβ2 \| αβ3

        becomes:

        A ::= αZ Z ::= β1 \| β2 \| β3

        in other words, we factor out the common prefix α and push the
        things after α into a different nonterminal.

        there is an algorithm for left-factoring in the textbook, but it
        essentially just says to repeat the above transformation as
        necessary. note that not all grammars can be made predictive
        (and it is undecidable to figure out whether one can be).

    2.  note about ε elimination

        one might think that we could get rid of FOLLOW sets by
        eliminating ε from the grammar as we said was possible when we
        discussed dealing with left recursion. the problem is that this
        elimination can render the grammar non-predictive, and applying
        left factoring to make it predictive again re-introduces the ε.

        EXAMPLE:

        T ::= F T\' T\' ::= × F T\' \| ÷ F T\' \| ε

        remove ε:

        T ::= F T\' \| F T\' ::= × F T\' \| × F \| ÷ F T\' \| ÷ F

        note that the grammar is no longer predictive and we need to use
        left factoring, which reintroduces ε.

    3.  exercise

        left-factor the following grammar:

        A ::= Bxy \| Bxz \| Bw \| xyz B ::= p \| q

        solution:

        A ::= BC \| xyz B ::= p \| q C ::= xD \| w D ::= y \| z

validating AST
--------------

once we have the AST we\'re almost finished. we need to validate the AST
to make sure it isn\'t nonsense (i.e., syntactically correct but
semantically meaningless). for our language there are only a few trivial
checks that we need to perform:

1.  is every variable declared before it\'s used?
2.  is every called function defined?
3.  is every parameter name and defined function name unique?

for (1) we just write a checker to do a recursive traversal of the AST
with a set DECLARED that contains the variables declared in the current
scope. it starts empty; each time we enter a block of code we add in
that block\'s declared variables. for each statement, we look at all
variables it refers to and verify that they are in the current DECLARED
set.

the only thing we need to be a little careful of is to distinguish
between different scopes. consider:

int x; int y; if (x \< y) { int x; int z; y := x + z; } else { y := x -
z; }

there is an error here because \'z\' is not in scope in the conditional
false branch. there\'s an easy way to ensure we do it correctly: given a
DECLARED set D, when the checker makes a recursive call we always pass
in a copy of D rather than D itself. so in the above example, the
initial D would have {x,y}. we pass in a copy of D when making a
recursive checker call to the true branch and modify the copy to contain
{x,y,z}. when the recursive call returns we throw away the copy and pass
the orignal D when making a recursive checker call to the false branch.
this keeps the branches separate and everything works right.

for (2) it\'s even easier: just collect the set of defined functions,
then go through all call statements and check that they only call one of
the defined functions. we also need to check that the call uses the
correct number of arguments.

for (3) all we do is go through the defined functions and check that
their names are unique, then for every function\'s parameter list go
through and check that they are all distinct from each other. note that
many languages don\'t require unique function names as long as the
function signatures are different; we\'ll keep things simple and require
uniqueness.

in general the AST validation phase can be much more complicated. type
checking makes up a big piece, but since we only have one type it
doesn\'t really matter to us.

### example {#example-4}

def foo(int a, int a) : int { x := bar(42); return x; } def foo() : int
{ return 42; } output foo(1, 2);

frontend wrap-up
----------------

we\'ve gone through a lot of steps to get through the frontend; let\'s
recap what\'s going on and put it all together.

given: grammar for concrete syntax of the language

1.  decide on precedence levels
2.  factor grammar to enforce desired precedence
3.  eliminate left recursion
4.  check if grammar is predictive
    -   if not, apply left factoring
    -   recheck if grammar is predictive now
    -   if not, the concrete grammar needs to change somehow
5.  define AST data structure
6.  translate grammar to recursive descent parser
7.  install AST-building logic into parsing functions

given: a sequence of characters representing the source code

1.  lexer: convert to sequence tokens using DFA/NFA
2.  parser: convert sequence of tokens into AST
3.  check AST for validity

i want to emphasize that we\'ve simplified some issues that happen in
real-world languages like C, C++, Java, etc. these often require some
creative thinking to get them to fit into the frontend framework we\'ve
covered here, mostly because of ambiguity in the lexemes and/or grammar
or because lexemes aren\'t regular.

naive codegen
-------------

given an AST, our job is to translate it into a sequence of assembly
instructions for our target architecture. for the purposes of these
lectures i\'ll use a generic assembly language; the assignments will
target 32-bit x86 assembly instructions.

### necessary context

1.  linker

    on paper show the stages: source1 --\[compiler\]-\> assembly
    --\[assembler\]-\> object file 1 --- source2 --\[compiler\]-\>
    assembly --\[assembler\]-\> object file 2 ---\\ ...
    \|--\[linker\]-\> executable sourceN --\[compiler\]-\> assembly
    --\[assembler\]-\> object file N .../

    go through example in terminal showing compiling separate files into
    object code, then linking them. use objdump to show the assembly for
    each. point out (1) how calling gcc on them seperately causes an
    error unless using the -c flag; (2) how the externally defined
    functions are treated in the object files vs the linked file; (3)
    the extra functions in the linked file that are run before main.
    also discuss that gcc is actually just calling as and ld rather than
    assembling and linking itself; we\'ll use the same strategy for our
    compiler.

    \[see examples/\]

2.  static vs dynamic libraries

    even after using the linker it\'s possible we still don\'t have all
    the code. it depends on whether we\'re using [static
    linking]{.underline} or [dynamic linking]{.underline} for libraries.

    static libraries have a \'\*.a\' extension (\"archive\"). they are
    linked in with the other code the same way.

    dynamic libraries have a \'\*.so\' extension (\"shared object\").
    they are not linked in at compilation time. instead, they are linked
    in at load time when the code is being put into memory.

    there are pros and cons to each approach, and both are widely used.

    -   static: larger executable size, can be multiple copies in memory
        at once for different executables, but guaranteed to have the
        right version.
    -   dynamic: smaller executable size, can be shared by multiple
        executables, but if the library can get out of synch with the
        executable.

    you don\'t have to be aware of this for writing the compiler, but
    you should know the difference in general.

3.  process layout in memory

    it will be useful to know how the process is laid out in memory
    while executing (note: this is virtual memory, not physical memory;
    maybe go into what that means a little bit but not too much):

    (address 0) \[code segment \| static segment \| data segment\]

    where the static segment also contains globals and constant strings,
    and the data segment is laid out as:

    (low address) \[heap \| stack\]

    note that this is where the errors \"segmentation fault\" and
    \"stack overflow\" come from.

    the heap is where memory is allocated from (e.g., when you call
    [new]{.underline} or [malloc]{.underline}); it is generally handled
    by the language runtime.

    the stack is for procedure calls; each call will push a \"stack
    frame\" onto the stack, so each instance of a procedure will have
    its own stack frame. stack manipulation is generally handled by the
    compiler.

    -- the stack frame is where a procedure\'s local variables are
    stored, among other things; we\'ll get more into procedures and
    stack frames later.

    note that the stack grows downwards; this is typical for most
    architectures, including x86, but not always true (there are some
    where it grows up, and even some where it goes in a circle).

    -- two important values wrt to the stack: the \"stack pointer\"
    (points to the current top of the stack) and the \"frame pointer\",
    aka base pointer (points to the bottom of the current stack frame).

4.  alignment and padding

    architectures generally have restrictions on alignment, i.e., how
    values can match up to word boundaries. when assigning memory
    locations to variables, we need to respect the alignment
    restrictions.

    -- example: 32-bit floating point and integer values should begin on
    a full-word (32-bit) boundary.

    scheme: place values with identical alignment restrictions next to
    each other; assign offsets from most restrictive to least; if
    needed, insert padding to match restrictions.

    we won\'t have to worry about this for L1 because we only have one
    type of thing: int32. but this is something that compiler writers
    have to worry about in general. for example, consider the following
    struct:

    struct foo { char a; int b; short c; long long int d; };

    we\'ll assume an architecture where char:8, short:16, int:32, and
    long long int:64; chars are byte-aligned, shorts are word-aligned,
    ints are word-aligned, and long long ints are double-word-aligned.
    so we would have to lay this out in memory as:

      --- -- -- -- --- --- --- --- --- --- -- -- -- -- -- -- --- --- --- --- --- --- --- ---
      a            b   b   b   b   c   c                     d   d   d   d   d   d   d   d
      --- -- -- -- --- --- --- --- --- --- -- -- -- -- -- -- --- --- --- --- --- --- --- ---

    this is why it\'s bad practice when c/c++ programmers try to
    manually index into a struct, like:

    foo x{8,16,32,64}; char y = **((char**)&x + 1);

    this code tries to read the first byte of x.b, but it\'s actually
    getting the padding between x.a and x.b that was inserted by the
    compiler, which is garbage.

### symbol table

1.  intro

    during compilation it\'s generally useful to have a single
    repository of information about symbols in the program, e.g.,
    functions and variables. -- functions: number of parameters and
    their types, ... -- variables: type, memory location, array
    dimensions, ... -- structs and records: their fields, layout, and
    size, ... -- etc

    we can find this information out by traversing the code whenever we
    need it, but it\'s more efficient to figure it out once and store
    the info where we can look it up whenever we need it.

    note that this information is used by the front-end, middle-end, and
    back-end, and that it isn\'t all available at the same time; the
    info gets filled in as it becomes available (e.g., variable memory
    locations).

    naively we might think to implement the symbol table as a global map
    from symbol to info, but that doesn\'t quite work due to the issue
    of [scope]{.underline}.

2.  scope

    most PLs have the concept of [scope]{.underline}, i.e., an area of
    code for which a particular variable is defined. for example, every
    function has its own scope, and its parameters and locals are
    entirely independent of any other function\'s parameters and locals
    even if they are named the same:

    int foo(int a, int b) { int x = a + 1; int y = b + 2; return x \* y;
    } double bar(double a, double b) { double x = a - 1; double y = b -
    2; return x / y; }

    clearly we need to keep the info about foo\'s variables separate
    from the info about bar\'s variables. the first solution we might
    try is just to keep a separate symbol table for each scope (i.e.,
    each function):

    foo --\> \[a -\> ..., b -\> ..., x -\> ..., y -\> ...\] bar --\> \[a
    -\> ..., b -\> ..., x -\> ..., y -\> ...\]

    this also has a problem, namely, [nested scope]{.underline}.

3.  nested scope

    scopes can be nested inside of each other, and variables in an inner
    scope can shadow variables in an outer scope. consider the following
    example:

    // SCOPE LEVEL 0 static int w; int x;

    void example(int a, int b) { // SCOPE LEVEL 1 int c; { // SCOPE
    LEVEL 2a int b, z; } { // SCOPE LEVEL 2b int a, x; { // SCOPE LEVEL
    3 int c, x; b = a + b + c + x + w; } } }

    all modern PLs use [lexical scoping]{.underline}, which means that a
    variable always refers to its syntactically nearest definition. in
    other words, start from the scope the variable is being used in and
    see if that scope defines that variable; if so then that\'s the
    definition it refers to. otherwise look in the scope containing the
    current scope and check there. keep going up the chain of enclosing
    scopes until you find the nearest one that defines the variable;
    that\'s the definition that variable use refers to.

    level~0~ --\> level~1~ --\> level~2a~ \\-\> level~2b~ --\> level~3~

    for the above example, if we subscript the variable with the scope
    they are defined in we see that the assignment becomes:

    b~1~ = a~2b~ + b~1~ + c~3~ + x~2b~ + w~0~;

    note that there\'s no way that any variable used in scope 2b can
    refer to scope 2a because 2a does not enclose 2b.

    so how do we arrange the symbol table to make the resolution
    possible? the basic idea is to create a new table for each scope and
    chain them together in a way that mirrors the scoping tree we just
    saw. we\'ll use the following data structure and API for symbol
    table creation:

    -   SymbolTable: \[parent: pointer to enclosing symbol table; table:
        map from symbol to info\]
    -   currScope: pointer to current symbol table, initialized to NULL
    -   CreateScope():
        -   creates a new symbol table
        -   sets the table\'s parent to currScope
        -   sets currScope to the new table
    -   Insert(symbol, info): inserts symbol and its info in
        currScope\'s table
    -   Lookup(symbol): looks up symbol to get its info
        -   starts in currScope and keeps looking for symbol until it\'s
            found, walking up the parents.
    -   EndScope():
        -   sets currScope to currScope\'s parent

    let\'s see it in action for the example above. \[go through
    example\]

    note that it looks like the table end up orphaned; in reality we
    would either:

    1.  keep a pointer at the start of each scope to the appropriate
        table
    2.  have each symbol point directly to its entry in the appropriate
        table

4.  our compiler for L1

    L1 is very simple, and so is our compiler, so a full-fledged symbol
    table is kind of overkill. all we really need to keep track of is
    the memory location of each variable. note that we still do need to
    worry about scope and nested scopes.

    here\'s how we\'ll do it. we\'ll be doing code generation using a
    recursive traversal of the AST as described below. as part of the
    recursive traversal we\'ll pass a map from variable to memory
    location (we\'ll describe how to compute the memory location in a
    bit). each time we\'re making a recursive call to a new scope,
    we\'ll pass a copy of the map; inside that new scope we\'ll insert
    any declared variables and their memory locations.

    -- this is essentially the scheme i talked about in an earlier
    lecture on validating the AST by checking that all variables were
    declared before being used.

    we can get away with this scheme because we\'re not keeping around
    any other information, and once we\'re done with code generation for
    a given scope we don\'t need the memory location information, so we
    can just get rid of the maps as we leave each scope.

    also note that this isn\'t a very efficient scheme, because we\'re
    constantly creating, copying, and destroying these maps. the rest of
    our compiler isn\'t terribly efficient anyway so it doesn\'t matter,
    but in a production compiler we would want to heavily optimize the
    symbol table because it\'s accessed so often during compilation.

    1.  specifics about L1 scope

        \[show example code in L1-concrete-syntax.pdf\]

        -   the global statement block is its own scope and not in scope
            of the function bodies.
        -   every function name is in scope for every function body.

### code generation strategies

1.  recursive AST traversal

    a modern optimizing compiler will almost certainly translate the AST
    to a simpler intermediate representation (IR) before doing code
    generation. however, for our first compiler we\'ll translate
    directly from the AST to assembly. the implementation of code
    generation will be based on a recursive traversal of the AST; as we
    traverse it we\'ll emit the appropriate assembly so that by the time
    we\'ve traversed the entire tree we\'ve finished compilation.

    our compiler is written in c++, and the idiomatic way to do
    something like this in an object oriented language is called the
    \"Visitor Design Pattern\". the class poll indicated that many of
    you don\'t know what this is, so i\'ll prepare a separate lecture
    just quickly going over how it works. but for the purposes of this
    lecture, just think of it as a standard tree traversal algorithm.

    also note that because we\'re directly generating code from the AST
    rather than translating to an IR and then optimizing, the code we
    generate will be very inefficient with lots of redundant loads and
    stores; this is the price we pay for a simple, uniform code
    generation strategy.

    1.  visitor pattern

        \[separate video lecture, using a Tree data structure and
        TreeVisitor to discuss the concepts.\]

2.  stack-based

    often we can (or must) treat the target architecture as a stack
    machine: there is a system stack (logically distinct from the
    function stack) and when generating code, the operands and result
    are always on the stack and don\'t need to be explicitly stated. for
    those familiar with it, the code looks like reverse polish notation.

    example source: x = (1 + 2) \* 3

    would generate: push 1 push 2 add push 3 mul sto &x

    the JVM is an example of a stack machine, as is the CLR. the x86
    architecture isn\'t really a stack machine, but we could repurpose
    the function stack and treat it as a stack machine if we wanted to.

    advantages of stack machines: easy to generate code, no temporary
    variables, no register allocation, smaller code. disadvantages of
    stack machines: can be much slower than register-based.

3.  register-based

    a more common target architecture is a register-based machine: it
    uses high-speed memory integrated directly into the CPU (i.e.,
    registers) to hold operands and results and requires instructions to
    explicitly state where the operands are and where the result should
    be put.

    example source: x = (1 + 2) \* 3

    would generate: mov 1 R1 mov 2 R2 add R1 R2 mov 3 R3 mul R2 R3 sto
    R3 \[memory location of x\]

    \[i\'m using a somewhat generic assembly language modeled off of x86
    but not exactly the same; when there are two operands, the operation
    is applied and the result is put in the second one.\]

    x86, ARM, MIPS, etc are all register-based machines (though most
    can, as mentioned above, pretend to be stack-based machines by
    repurposing the function stack).

    the advantages are disadvantages of register-based machines are
    basically the reverse of those for stack-based machines.

    we\'ll be targeting x86 as a register-based machine for our
    compiler.

### naive register-based generation

1.  without functions or calls

    we\'ll start by assuming a program without any function definitions
    or calls; thus all we have is the single global block of statements.

    1.  variable memory locations

        we need to figure out where the variables declared in this block
        will live and put that information into our symbol table. we\'ll
        put all the variables on the function stack:

        1.  note that the stack and frame pointers are initialized to
            the base of the stack.
        2.  compute how many variables are being declared; call this N.
        3.  remember that each variable is a 4-byte integer, so we need
            4\*N bytes of space.
        4.  advance the stack pointer by 4\*N, giving us a stack frame
            (from the frame pointer to the stack pointer).
        5.  all variables are initialized to 0, so write 0 into each
            word of the stack frame.
        6.  in the symbol table map each declared variable to an offset
            from the frame pointer.
            -   remember that the stack grows downward, so the offset
                will be non-positive.

        we would have to worry about alignment and padding here except
        that everything is a 4-byte integer so it all works out.

        now whenever we need to know where a variable is stored in
        memory, we just look up the offset stored in the symbol table.

        EXAMPLE:

        int x; int y; x := 1 + 2; y := x \* 3; output y;

        -   2 variables == increase stack by 8 bytes.

            emit instruction: sub 8 STACK~REG~

        -   initialize them both to 0.

            emit instructions: sto 0 \[FRAME~REG~-0\] sto 0
            \[FRAME~REG~-4\]

            note that \[FRAME~REGISTER~-n\] means take the value of
            FRAME~REGISTER~, subtract n, and access that location in
            memory.

        -   add info to the symbol table:

            x --\> 0 \[i.e., x is at a 0 offset from the frame pointer\]
            y --\> -4 \[i.e., x is at a -4 offset from the frame
            pointer\]

        we\'ll do this each time we enter a new scope, and call this the
        \"preamble code\" for that scope. in this example, the preamble
        code we emit is:

        sub 8 STACK~REG~ sto 0 \[FRAME~REG~-0\] sto 0 \[FRAME~REG~-4\]

    2.  expressions

        we\'re targeting a register-based machine, but we don\'t want to
        do register allocation (at least, for now) so we need to
        evaluate expressions assuming a very limited set of available
        registers. to generate code for an expression we\'ll do a
        recursive traversal in post-order (that is, visit the children
        first, then the parent).

        1.  arithmetic

            let\'s start with a simple example expression: (1 + 2) \*
            (3 - 4)

            as a tree, this is \[\* \[+ 1 2\] \[- 3 4\]\]

            here\'s a scheme we might attempt (that will turn out to be
            broken): when processing the \*,+,- nodes we want the left
            subtree\'s value to end in one register (let\'s call it
            LEFT~REG~) and the right subtree\'s value to end in another
            (call it RIGHT~REG~). to generate code for this example, we
            might try the following:

            call generate~aexp~(\* node, left): call generate~aexp~(+
            node, left): call generate~aexp~(1 node, left): emit \"mov 1
            LEFT~REG~\" call generate~aexp~(2 node, right): emit \"mov 2
            RIGHT~REG~\" emit \"add RIGHT~REG~ LEFT~REG~\" call
            generate~aexp~(- node, right): call generate~aexp~(3 node,
            left): emit \"mov 3 LEFT~REG~\" call generate~aexp~(4 node,
            right): emit \"mov 4 RIGHT~REG~\" emit \"sub RIGHT~REG~
            LEFT~REG~\" \"mov LEFT~REG~ RIGHT~REG~\" emit \"mul
            RIGHT~REG~ LEFT~REG~\"

            the second parameter to generate~aexp~() tells it which
            register the result should end up in (we can pick
            arbitrarily for the root node of the expression). the final
            emitted code is:

            01: mov 1 LEFT~REG~ 02: mov 2 RIGHT~REG~ 03: add RIGHT~REG~
            LEFT~REG~ 04: mov 3 LEFT~REG~ 05: mov 4 RIGHT~REG~ 06: sub
            RIGHT~REG~ LEFT~REG~ 07: mov LEFT~REG~ RIGHT~REG~ 07: mul
            RIGHT~REG~ LEFT~REG~

            we see that there\'s a problem: line 03 puts the result of
            the add in LEFT~REG~, but then line 04 immediately
            overwrites it in order to set up computing the result of the
            sub. if we were doing a stack machine this wouldn\'t matter
            because we don\'t need registers; if we were doing register
            allocation we could handle this by assuming that there are
            an arbitrary number of registers available and then fix it
            later. but we aren\'t doing either of those things, so how
            do we handle it?

            the only thing that can store an arbitrary number of values
            is memory, so we\'re going to have to create some memory
            locations to hold temporary values during expression
            evaluation. in other words, we need to add a set of
            temporary variables in addition to the other variables
            declared in this scope. how do we know how many to add when
            we enter a particular scope? we\'ll dynamically add them to
            the symbol table as we generate code for an expression
            (remembering to adjust the stack pointer in the preamble
            code for this scope to allocate memory for them as well).

            -- note that we can reuse temporary variables between
            different expressions (e.g., for \"x := \<aexp~1~\>; y :=
            \<aexp~2~\>;\" we only need max(\#tmp(\<aexp~1~\>),
            \#tmp(\<aexp~2~\>)) temporary variables.

            let\'s see how we can handle the above example now:

            call generate~aexp~(\* node, tmp~num~ = 0): insert ~tmp0~
            into symbol table call generate~aexp~(+ node, tmp~num~ = 1):
            insert ~tmp1~ into symbol table call generate~aexp~(1 node,
            tmp~num~ = 2): emit \"mov 1 RESULT~REG~\" emit \"sto
            RESULT~REG~ \[~tmp1~\]\" call generate~aexp~(2 node,
            tmp~num~ = 2): emit \"mov 2 RESULT~REG~\" emit \"ld
            \[~tmp1~\] OTHER~REG~\" \"add OTHER~REG~ RESULT~REG~\" emit
            \"sto RESULT~REG~ \[~tmp0~\]\" call generate~aexp~(- node,
            tmp~num~ = 1): insert ~tmp1~ into symbol table // already
            there, so no action taken call generate~aexp~(3 node,
            tmp~num~ = 2): emit \"mov 3 RESULT~REG~\" emit \"sto
            RESULT~REG~ \[~tmp1~\]\" call generate~aexp~(4 node,
            tmp~num~ = 2): emit \"mov 4 RESULT~REG~\" emit \"ld
            \[~tmp1~\] OTHER~REG~\" \"sub RESULT~REG~ OTHER~REG~\" \"mov
            OTHER~REG~ RESULT~REG~\" emit \"ld \[~tmp0~\] OTHER~REG~\"
            \"mul OTHER~REG~ RESULT~REG~\"

            notice that each call to generate~aexp~ puts the result of
            that call into RESULT~REG~, and because we\'re storing the
            intermediate results in temporary variables we\'re never
            overwriting anything important. also notice that we can
            reuse temporary variables once we\'re done with them (as we
            do above with ~tmp1~). the final emitted code is:

            01: mov 1 RESULT~REG~ 02: sto RESULT~REG~ \[~tmp1~\] 03: mov
            2 RESULT~REG~ 04: ld \[~tmp1~\] OTHER~REG~ 05: add
            OTHER~REG~ RESULT~REG~ 06: sto RESULT~REG~ \[~tmp0~\] 07:
            mov 3 RESULT~REG~ 08: sto RESULT~REG~ \[~tmp1~\] 09: mov 4
            RESULT~REG~ 10: ld \[~tmp1~\] OTHER~REG~ 11: sub RESULT~REG~
            OTHER~REG~ 12: mov OTHER~REG~ RESULT~REG~ 13: ld \[~tmp0~\]
            OTHER~REG~ 14: mul OTHER~REG~ RESULT~REG~

            in total we needed two temporary variables (~tmp0~ and
            ~tmp1~) and so needed to add two entries to the symbol table
            and increase the stack pointer by 8 bytes (remember that the
            stack pointer addition happens at the beginning of the
            scope, so we need to go back to that code and update it).
            note that we used the names \"~tmp~\<n\>\", which are not
            valid variable names in our syntax, thus avoiding name
            clashes and making it easy to tell which variables were
            created by the compiler vs the user.

            so now we have it working on an example; let\'s generalize
            the algorithm for arbitrary arithmetic expressions:

            generate~aexp~(AST\* node, int tmp~num~ = 0) { if (node is a
            constant number \<n\>) { emit \"mov \<n\> RESULT~REG~\";
            return; } if (node is a variable \<x\>) { emit \"ld \[x\]
            RESULT~REG~\"; return; }

            // node must be one of +,-,\* insert ~tmp~\<tmp~num~\> into
            symbol table; generate~aexp~(node-\>left, tmp~num~+1); emit
            \"sto RESULT~REG~ \[~tmp~\<tmp~num~\>\]\";
            generate~aexp~(node-\>right, tmp~num~+1); emit \"ld
            \[~tmp~\<tmp~num~\>\] OTHER~REG~\";

            // left-hand value is in OTHER~REG~, right-hand value is in
            RESULT~REG~ if (node is +) { emit \"add OTHER~REG~
            RESULT~REG~\"; return; } if (node is -) { emit \"sub
            RESULT~REG~ OTHER~REG~\"; emit \"mov OTHER~REG~
            RESULT~REG~\"; return; } emit \"mul OTHER~REG~
            RESULT~REG~\"; }

            remember, it\'s important that inserting a [new]{.underline}
            temporary variable in the symbol table [also]{.underline}
            adjusts the amount by which the preamble code for the
            enclosing scope adds to the stack pointer to create the
            stack frame. however, we don\'t have to initialize the new
            memory locations to 0 because we\'re guaranteed to always
            store a value to them before reading them.

            1.  exercise

                generate code for the following expression:

                ((1 - 2) \* (3 \* 4)) + (5 - (6 + 7))

                tree:

                \[+ \[\* \[- 1 2\] \[\* 3 4\]\] \[- 5 \[+ 6 7\]\]

                solution:

                01: mov 1 RR 02: sto RR \[~tmp2~\] 03: mov 2 RR 04: ld
                \[~tmp2~\] OR 05: sub RR OR 06: mov OR RR 07: sto RR
                \[~tmp1~\] 08: mov 3 RR 09: sto RR \[~tmp2~\] 10: mov 4
                RR 11: ld \[~tmp2~\] OR 12: mul OR RR 13: ld \[~tmp1~\]
                OR 14: mul OR RR 15: sto RR \[~tmp0~\] 16: mov 5 RR 17:
                sto RR \[~tmp1~\] 18: mov 6 RR 19: sto RR \[~tmp2~\] 20:
                mov 7 RR 21: ld \[~tmp2~\] OR 22: add OR RR 23: ld
                \[~tmp1~\] OR 24: sub RR OR 25: mov OR RR 26: ld
                \[~tmp0~\] OR 27: add OR RR

        2.  relational / logical

            now that we\'ve handled arithmetic expressions, the
            relational expressions are easy. the main thing we need to
            do is decide how to represent the results of a relational
            expression (i.e., how do we represent a boolean). we\'ll use
            the same method as c/c++: a 0 is interpreted as false, a
            non-0 is interpreted as true.

            often assembly instructions that compare values store the
            results as a flag rather than in a register, so we need to
            compensate for that. let\'s look at an example:

            (1 \< 2) && ((3 \<= 4) \|\| (5 = 6))

            as a tree this is:

            \[&& \[\< 1 2\] \[\|\| \[\<= 3 4\] \[= 5 6\]\]\]

            so the process we want the code generator to go through is:

            call generate~rexp~(&& node, tmp~num~ = 0): insert ~tmp0~
            into symbol table call generate~rexp~(\< node, tmp~num~ =
            1): insert ~tmp1~ into symbol table call generate~aexp~(1
            node, tmp~num~ = 2) emit \"sto RESULT~REG~ \[~tmp1~\]\" call
            generate~aexp~(2 node, tmp~num~ = 2) emit \"ld \[~tmp1~\]
            OTHER~REG~\" \"cmp RESULT~REG~ OTHER~REG~\" \"setlt
            RESULT~REG~\" emit \"sto RESULT~REG~ \[~tmp0~\]\" call
            generate~rexp~(\|\| node, tmp~num~ = 1): insert ~tmp1~ into
            symbol table // does nothing call generate~rexp~(\<= node,
            tmp~num~ = 2): insert ~tmp2~ into symbol table call
            generate~aexp~(3, tmp~num~ = 3) emit \"sto RESULT~REG~
            \[~tmp2~\]\" call generate~aexp~(4, tmp~num~ = 3) emit \"ld
            \[~tmp2~\] OTHER~REG~\" \"cmp RESULT~REG~ OTHER~REG~\"
            \"setle RESULT~REG~\" emit \"sto RESULT~REG~ \[~tmp1~\]\"
            call generate~rexp~(= node, tmp~num~ = 2): insert ~tmp2~
            into symbol table // does nothing call generate~aexp~(5,
            tmp~num~ = 3) emit \"sto RESULT~REG~ \[~tmp2~\]\" call
            generate~aexp~(6, tmp~num~ = 3) emit \"ld \[~tmp2~\]
            OTHER~REG~\" \"cmp RESULT~REG~ OTHER~REG~\" \"sete
            RESULT~REG~\" emit \"ld \[~tmp1~\] OTHER~REG~\" \"or
            OTHER~REG~ RESULT~REG~\" emit \"ld \[~tmp0~\] OTHER~REG~\"
            \"and OTHER~REG~ RESULT~REG~\"

            and the final generated code is:

            01: \[code from generate~aexp~\] 02: sto RESULT~REG~
            \[~tmp1~\] 03: \[code from generate~aexp~\] 04: ld
            \[~tmp1~\] OTHER~REG~ 05: cmp RESULT~REG~ OTHER~REG~ 06:
            setlt RESULT~REG~ 07: sto RESULT~REG~ \[~tmp0~\] 08: \[code
            from generate~aexp~\] 09: sto RESULT~REG~ \[~tmp2~\] 10:
            \[code from generate~aexp~\] 11: ld \[~tmp2~\] OTHER~REG~
            12: cmp RESULT~REG~ OTHER~REG~ 13: setle RESULT~REG~ 14: sto
            RESULT~REG~ \[~tmp1~\] 15: \[code from generate~aexp~\] 16:
            sto RESULT~REG~ \[~tmp2~\] 17: \[code from generate~aexp~\]
            18: ld \[~tmp2~\] OTHER~REG~ 19: cmp RESULT~REG~ OTHER~REG~
            20: sete RESULT~REG~ 21: ld \[~tmp1~\] OTHER~REG~ 22: or
            OTHER~REG~ RESULT~REG~ 23: ld \[~tmp0~\] OTHER~REG~ 24: and
            OTHER~REG~ RESULT~REG~

            it\'s basically like the arithmetic expressions except that
            the comparison instructions set a condition flag, which we
            then need to read in order to set a register. so the
            generate~rexp~ code is:

            generate~rexp~(AST\* node, int tmp~num~ = 0) { insert
            ~tmp~\<tmp~num~\> into symbol table; if (node is a
            comparison) { generate~aexp~(node-\>left, tmp~num~+1); emit
            \"sto RESULT~REG~ \[~tmp~\<tmp~num~\>\]\";
            generate~aexp~(node-\>right, tmp~num~+1); emit \"ld
            \[~tmp~\<tmp~num~\>\] OTHER~REG~\"; emit \"cmp RESULT~REG~
            OTHER~REG~\"; emit \"set\<op\> RESULT~REG~\" // \<op\> is
            {e,lt,le} depending on the comparison being made } else {
            generate~rexp~(node-\>left, tmp~num~+1); emit \"sto
            RESULT~REG~ \[~tmp~\<tmp~num~\>\]\";
            generate~rexp~(node-\>right, tmp~num~+1); emit \"ld
            \[~tmp~\<tmp~num~\>\] OTHER~REG~\"; emit \"\<op\> OTHER~REG~
            RESULT~REG~\" // \<op\> is {and,or} depending on the node }
            }

            1.  exercise

                generate code for the following expression:

                ((1 = 2) \|\| ((3 \< 4) && (5 \<= 6))) && ((7 \< 8) \|\|
                (9 = 9))

                tree:

                \[&& \[\|\| \[= 1 2\] \[&& \[\< 3 4\] \[\<= 5 6\]\]\]
                \[\|\| \[\< 7 8\] \[= 9 9\]\]

                solution:

                01: mov 1 RR 02: sto RR \[~tmp2~\] 03: mov 2 RR 04: ld
                \[~tmp2~\] OR 05: cmp RR OR 06: sete RR 07: sto RR
                \[~tmp1~\] 08: mov 3 RR 09: sto RR \[~tmp3~\] 10: mov 4
                RR 11: ld \[~tmp3~\] OR 12: cmp RR OR 13: setlt RR 14:
                sto RR \[~tmp2~\] 15: mov 5 RR 16: sto RR \[~tmp3~\] 17:
                mov 6 RR 18: ld \[~tmp3~\] OR 19: cmp RR OR 20: setle RR
                21: ld \[~tmp2~\] OR 22: and OR RR 23: ld \[~tmp1~\] OR
                24: or OR RR 25: sto RR \[~tmp0~\] 26: mov 7 RR 27: sto
                RR \[~tmp2~\] 28: mov 8 RR 29: ld \[~tmp2~\] OR 30: cmp
                RR OR 31: setlt RR 32: sto RR \[~tmp1~\] 33: mov 9 RR
                34: sto RR \[~tmp2~\] 35: mov 9 RR 36: ld \[~tmp2~\] OR
                37: cmp RR OR 38: sete RR 39: ld OR \[~tmp1~\] 40: or OR
                RR 41: ld \[~tmp0~\] OR 42: and OR RR

        3.  short-circuiting evaluation

            it can be wasteful to evaluate an entire logical expression
            if we already know the answer part-way through (and even
            harmful if the remainder of the expression has
            side-effects). consider the following C expression:

            if (ptr != NULL && \*ptr == 42) { ... }

            clearly if ptr [is]{.underline} NULL we don\'t want to
            dereference it. this is called \"short-circuited\"
            evaluation: -- given \"\<lhs\> && \<rhs\>\", if \<lhs\> is
            false then we don\'t need to evaluate \<rhs\>. -- given
            \"\<lhs\> \|\| \<rhs\>\", if \<lhs\> is true then we don\'t
            need to evaluate \<rhs\>.

            our L1 language doesn\'t allow these kinds of expressions
            currently, but we should understand how to implement it. the
            basic idea is simple: we have to insert a conditional into
            the middle of the expression evaluation. when evaluating a
            && (or \|\|) node, after evaluating the left-hand side we
            emit instructions to check whether the result is false (or
            true) and if so then jump past the evaluation of the
            right-hand side. we\'ll save the details for when we discuss
            generating code for conditionals.

    3.  assignments

        assignment is trivial: we evaluate the right-hand side using
        generate~aexp~, which puts the result in RESULT~REG~, then store
        the result to the memory location for the left-hand side
        variable.

        generate~assign~(lhs, rhs) { generate~aexp~(rhs); emit \"sto RR
        \[lhs\]\"; }

    4.  conditionals

        1.  no nested scope

            let\'s look at an example:

            if (x \< 2) { x := 1; } else { x := 2; }

            depending on the outcome of the comparison, we want to
            execute either the true branch or the false branch. we
            don\'t necessarily know the outcome of the comparison at
            compile time, so we need to emit code for both branches and
            choose between them at runtime.

            for the above example we would get:

            01: ld \[x\] RR 02: sto RR \[~tmp0~\] 03: mov 2 RR 04: ld
            \[~tmp0~\] OR 05: cmp RR OR 06: setlt RR 07: cmp 0 RR 08:
            jmpe IF~FALSE0~ 09: mov 1 RR 10: sto RR \[x\] 11: jmp
            IF~END0~ 12: IF~FALSE0~: 13: mov 2 RR 14: sto RR \[x\] 15:
            IF~END0~:

            \[note again the inefficiency of the codegen. we could be a
            bit more clever and do things like check whether our left
            and/or right operands are leafs and if so avoid some stores
            and loads, which would be better code. but we\'re keeping
            things simple so that the code generator only has to look at
            the current AST node and nothing else.\]

            generalizing:

            generate~if~(node) { \<n\> = fresh index;
            generate~rexp~(node-\>guard); emit \"cmp 0 RESULT~REG~\";
            emit \"jmpe IF~FALSE~\_\<n\>\";
            generate~block~(node-\>true~branch~); emit \"jmp
            IF~END~\_\<n\>\"; emit \"IF~FALSE~\_\<n\>:\";
            generate~block~(node-\>false~branch~); emit
            \"IF~END~\_\<n\>:\"; }

        2.  nested scope

            remember that each branch of a conditional introduces a new
            scope which can declare its own variables. that means that
            we need to emit the preamble code again when the code
            generator enters that new scope:

            1.  see how many declared variables there are
            2.  adjust the stack pointer accordingly
            3.  initialize the new memory locations to 0
            4.  update symbol table to map the newly declared variables
                to their offsets

            and when we leave the new scope we need to adjust things
            back the way they were:

            1.  reset the stack pointer to its old position
            2.  restore the symbol table to its old value

            so the code generator for the blocks of code inside the
            true/false branches would look something like:

            generate~block~(node) { old~symboltable~ = symbol~table~;
            stack~size~ = node-\>num~declaredvariables~ \* 4; // because
            4-byte integers emit \"sub \<stack~size~\> STACK~REG~\";
            insert~insymboltable~(symbol~table~,
            node-\>declared~variables~); for each var in
            node-\>declared~variables~ { emit \"sto 0 \[var\]\"; } . . .
            emit \"add \<stack~size~\> STACK~REG~\"; symbol~table~ =
            old~symboltable~; }

            remember that you can\'t just take the existing symbol table
            and add the declared variables to it and then remove them
            when the block is over, because some of them could shadow
            existing variables in the enclosing scope. you can get
            around that problem in various ways (e.g., making the symbol
            table a stack instead of a map, where lookup scans the stack
            top-down looking for the nearest declared variable with the
            given name). for the purposes of this pseudocode i just
            always pass a copy of the symbol table to each branch.

            another consideration is that an expression inside a nested
            scope may require more temporary variables than any
            expression in an enclosing block. if so, then we need to
            backpatch the preamble code for the nested scope to allocate
            more space for those temporary variables.

            EXAMPLE:

            if (y \< 2) { int x; x := x + (1 + 2); y := x; }

            assume this \'if\' is the only statement. then the generated
            code would be:

            01: ld \[y\] RR 02: sto RR \[~tmp0~\] // note that this
            ~tmp0~ is outside the nested scope 03: mov 2 RR 04: ld
            \[~tmp0~\] OR 05: cmp RR OR 06: setlt RR 07: cmp 0 RR 08:
            jmpe IF~FALSE0~ 09: sub 8 SR // space for \'x\' and
            \'~tmp1~\' ??: sto 0 \[x\] // I FORGOT THIS ORIGINALLY 10:
            ld \[x\] RR 11: sto RR \[~tmp0~\] // reuse ~tmp0~ from
            enclosing scope 12: mov 1 RR 13: sto RR \[~tmp1~\] 14: mov 2
            RR 15: ld \[~tmp1~\] OR 16: add OR RR 17: ld \[~tmp0~\] OR
            18: add OR RR 19: sto RR \[x\] 20: ld \[x\] RR 21: sto RR
            \[y\] 22: add 8 SR // remove space for nested declarations
            23: jmp IF~END0~ 24: IF~FALSE0~: 25: IF~END0~:

        3.  exercise 1

            generate code for the following program (fully, including
            preamble code and all expressions and statements):

            if (1 \< 2) { int y; x := y; } else { x := 2; }

            solution:

            01: mov 1 RR 02: sto RR \[~tmp0~\] 03: mov 2 RR 04: ld
            \[~tmp0~\] OR 05: cmp RR OR 06: setlt RR 07: cmp 0 RR 08:
            jmpe IF~FALSE0~ 09: sub 4 SR 10: sto 0 \[y\] 11: ld \[y\] RR
            12: sto RR \[x\] 13: add 4 SR 14: jmp IF~END0~ 15:
            IF~FALSE0~: 16: mov 2 RR 17: sto RR \[x\] 18: IF~END0~:

        4.  exercise 2 (short-circuited evaluation)

            generate code for the following relational expression using
            short-circuiting:

            (1 \< 1) && (2 \< 3)

            tree:

            \[&& \[\< 1 1\] \[\< 2 3\]\]

            solution:

            01: mov 1 RR 02: sto RR \[~tmp1~\] 03: mov 1 RR 04: ld
            \[~tmp1~\] OR 05: cmp RR OR 06: setlt RR 07: cmp 0 RR 08:
            jmpe REXP~END0~ 09: sto RR \[~tmp0~\] 10: mov 2 RR 11: sto
            RR \[~tmp1~\] 12: mov 3 RR 13: ld \[~tmp1~\] OR 14: cmp RR
            OR 15: setlt RR 16: ld \[~tmp0~\] OR 17: and OR RR 18:
            REXP~END0~:

    5.  loops

        loops are a lot like conditionals except the \"true branch\" is
        the body of the loop and the \"false branch\" is the end of the
        loop. let\'s see an example:

        while (x \< 3) { x := x + 1; }

        so for the above example we would get:

        01: WHILE~START0~: 02: ld \[x\] RR 03: sto RR \[~tmp0~\] 04: mov
        3 RR 05: ld \[x\] OR 06: cmp RR OR 07: setlt RR 08: cmp 0 RR 09:
        jmpe WHILE~END0~ 10: ld \[x\] RR 11: sto RR \[~tmp0~\] 12: mov 1
        RR 13: ld \[~tmp0~\] OR 14: add OR RR 15: sto RR \[x\] 16: jmp
        WHILE~START0~ 17: WHILE~END0~:

        generalizing:

        generate~while~(node) { \<n\> = fresh index; emit
        \"WHILE~START~\_\<n\>:\"; generate~rexp~(node-\>guard); emit
        \"cmp 0 RESULT~REG~\"; emit \"jmpe WHILE~END~\_\<n\>\";
        generate~block~(node-\>body); emit \"jmp WHILE~START~\_\<n\>\";
        emit \"WHILE~END~\_\<n\>:\"; }

        1.  nested scope

            we have the same issue here with nested scope as for
            conditionals, though there\'s only one new scope: the body
            of the loop. we handle it exactly the same way. note that
            for loops the body can be entered many times; the preamble
            and cleanup code for the new scope will be executed each
            time.

        2.  exercise

            generate code for the following program (fully, including
            all preamble code, expressions, and statements):

            while (x \<= 10) { int y; y := 2; x := x + y; }

            solution:

            01: WHILE~START0~: 02: ld \[x\] RR 03: sto RR \[~tmp0~\] 04:
            mov 10 RR 05: ld \[~tmp0~\] OR 06: cmp RR OR 07: setle RR
            08: cmp 0 RR 09: jmpe WHILE~END0~ 10: sub 4 SR 11: sto 0
            \[y\] 11: mov 2 RR 12: sto RR \[y\] 13: ld \[x\] RR 14: sto
            RR \[~tmp0~\] 15: ld \[y\] RR 16: ld \[~tmp0~\] OR 17: add
            OR RR 18: sto RR \[x\] 19: add 4 SR 20: jmp WHILE~START0~
            21: WHILE~END0~:

2.  with functions and calls

    1.  what are functions and what are they for?

        functions are an illusion: they exist as a programming language
        abstraction, but the actual machine has no notion of a function.
        they are an extremely valuable illusion for programmers:

        1.  they allow code to be abstracted over the data the code is
            run on. for example:

            // imagine that these are complicated expressions (1 + 2) \*
            2 (3 + 4) \* 4

            // procedural abstraction: int proc(int a, int b) { return
            (a + b) \* b; }

        2.  if the language has higher-order functions, they also allow
            code to be abstracted over control-flow. for example:

            list = \[4, 2, 6, 1, 5, 3\]; sort(list, (a, b) =\> { return
            a \< b; }) : \[1, 2, 3, 4, 5, 6\] sort(list, (a, b) =\> {
            return a \> b; }) : \[6, 5, 4, 3, 2, 1\]

        3.  they provide modularity: code can be written and compiled
            independently but still linked together and run correctly.
            procedure bodies are isolated from each other by different
            scopes.

        the compiler\'s job is to take this abstraction and turn it into
        assembly code that preserves the intended meaning of the
        procedure abstraction. what is that intended meaning? in a PL,
        what happens when a function is called?

        1.  the callee gets fresh instances of its parameters and local
            variables (local scope, even in the presence of recursion).

        2.  the argument values are copied to the parameters.

        3.  control jumps to the beginning of the callee.

        4.  the callee is executed.

        5.  control jumps back to the caller site.

        6.  if the function returns something, the return value is
            provided as the result of the function call expression.

        the basic tools we use to achieve this are:

        1.  the stack frame (or activation record)
        2.  calling conventions

    2.  stack frame (activation record)

        we\'ve talked about the process stack before. as a reminder:

        1.  part of the process memory is set aside for the stack.
        2.  stack register: holds a pointer to the current top of the
            stack.

        \[this means that SR holds the location of the top valid word on
        the stack, rather than the next available word---if we read the
        memory location contained in SR, we get the value of the word
        that\'s currently on top of the stack\]

        to make space on the stack, decrement the stack pointer; to
        reclaim that space increment the stack pointer.

        low high \[ xx\] \^ sp

        we\'ll use the stack to implement a function\'s functionality.
        the key data structure is called a [stack frame]{.underline} (or
        sometimes, [activation record]{.underline}). a stack frame will
        hold all the information a function needs to operate correctly:
        -- memory for the local variables -- where to return to when its
        done executing -- saved values for registers the function may
        overwrite

        how will the function know where to access this information,
        since it can appear anywhere on the stack? we\'ll use the [frame
        pointer]{.underline} (which lives in a register just like the
        stack pointer). the frame pointer points to the bottom of the
        current stack frame; the currently executing function can access
        all of its info in terms of offsets from the frame pointer.

        the basic idea is that when a function is called:

        1.  the old frame pointer is saved
        2.  the frame pointer is set to the current stack pointer
        3.  the stack pointer is incremented to allocate space for the
            activation record
        4.  the activation record is filled in appropriately
        5.  control jumps to the function
        6.  when the function is done, the stack pointer is set to the
            frame pointer and the frame pointer is restored to its old
            value

        \[go through high-level example of how the stack and frame
        pointers change for a function call.\]

        note that this is kind of like nested scope, except that we
        adjust the frame pointer to preserve scope (in contrast the
        nested scope which still has access to variables from the
        enclosing scope).

    3.  calling conventions

        in order to make all of this work even when functions have been
        compiled separately (perhaps even by different compilers) there
        must be a convention that says exactly how a call will work and
        who is responsible for what between the caller and the callee.

        every function has a prologue and epilogue every function call
        has a pre-call sequence and a post-return sequence

        \[show quick diagram to make this clear\]

        there is an agreed-upon protocol for the language to enforce who
        (caller, callee) does what to make function calls work; this is
        the [calling convention]{.underline}.

        anatomy of a function call (this is a \"typical\" example
        similar to that used by C on the x86; the details can vary from
        language to language and machine to machine):

        PRE-CALL:

        -   save any caller-save registers by pushing them onto the
            stack
        -   push the call arguments onto the stack (in reverse)
        -   push the return address onto the stack
        -   jump to address of callee\'s prologue code

        PROLOGUE CODE:

        -   push the frame pointer onto the stack
        -   copy the stack pointer into the frame pointer register
            (frame reg always points to old frame reg)
        -   make space on the stack for local variables (decrement stack
            pointer)
        -   save any callee-save registers by pushing them onto the
            stack

        EPILOGUE CODE:

        -   put return value in known register
        -   restore callee-save registers by popping from stack
        -   deallocate local variables by moving frame pointer to stack
            pointer
        -   restore caller\'s frame pointer by popping it from the stack
        -   pop call site\'s return address from stack and jump to it

        POST-RETURN CODE:

        -   discard call arguments from stack
        -   restore caller-save registers by popping from stack
        -   write return value to left-hand side of call
        -   continue execution

        note that the caller-save registers only need to be pushed if
        they are holding values needed after the function call, and
        callee-save registers only need to be pushed if they may be
        overwritten by the callee function.

    4.  example

        the program:

        def foo(int a, int b) : int { int c; c := a+b; return c+1; } int
        x; x := foo(2, 3); output x;

        offsets, scope 0: x : -4

        offsets, scope foo: a : 8 b : 12 c : -4 ~tmp0~ : -8

        we\'ll assume the existence of a few handy instructions: --
        \'push\' decrements the stack pointer and stores its argument to
        the newly allocated space on the stack. -- \'pop\' increments
        the stack pointer and loads the contents of the newly
        deallocated space into its argument -- \'call\' pushes the
        current instruction address onto the stack and jumps to the
        given label -- \'ret\' pops an instruction address off the stack
        and jumps to it

        we haven\'t targeted a specific architecture yet, so we\'ll also
        assume the existence of CALLER~SAVEREG1~, CALLER~SAVEREG2~,
        CALLEE~SAVEREG1~, and CALLEE~SAVEREG2~, with the obvious
        meanings.

        ENTRY: // entry point for entire program // ENTRY PROLOGUE push
        FR // save current frame pointer mov SR FR // replace old frame
        pointer with current stack pointer sub 4 SR // allocate space
        for \'x\' sto 0 \[FR-4\] // initialize \'x\' to 0 push
        CALLEE~SAVEREG1~ // save callee-save registers push
        CALLEE~SAVEREG2~

        // PRE-CALL for x := foo(2, 3) push CALLER~SAVEREG1~ // save
        caller-save registers push CALLER~SAVEREG2~ push 3 // push
        arguments onto stack in reverse push 2 call FOO

        // POST-RETURN for x := foo(2, 3) add 8 SR // discard arguments
        from stack pop CALLER~SAVEREG2~ // restore caller-save registers
        pop CALLER~SAVEREG1~ sto RR \[FR-4\] // store return value to
        memory location of \'x\'

        // output x ld \[FR-4\] RR

        // ENTRY EPILOGUE pop CALLEE~SAVEREG2~ // restore callee-save
        registers pop CALLEE~SAVEREG1~ mov FR SR // restore old stack
        pointer, deallocating stack frame pop FR // restore old frame
        pointer ret // pop return address and jump to it, exiting
        program

        FOO: // FOO PROLOGUE push FR // save current frame pointer mov
        SR FR // replace old frame pointer with current stack pointer
        sub 8 SR // allocate space for local variables (c and ~tmp0~)
        sto 0 \[FR-4\] // initialize \'c\' to 0 push CALLEE~SAVEREG1~ //
        save callee-save registers push CALLEE~SAVEREG2~

        // c := a+b ld \[FR+8\] RR sto RR \[FR-8\] ld \[FR+12\] RR ld
        \[FR-8\] OR add OR RR sto RR \[FR-4\]

        // return c+1 ld \[FR-4\] RR sto RR \[FR-8\] mov 1 RR ld
        \[FR-8\] OR add OR RR

        // FOO EPILOGUE pop CALLEE~SAVEREG2~ // restore callee-save
        registers pop CALLEE~SAVEREG1~ mov FR SR // restore old stack
        pointer, deallocating stack frame pop FR // restore old frame
        pointer ret // pop return address and jump to it

        \[show the stack as we walk through the code\]

        how did the code generator know the offsets of the arguments to
        the callee function when they were pushed onto the stack by the
        caller function? because of the calling convention, the callee
        always knows that the first argument is at a positive 8 offset
        from the current frame pointer (to pass over the saved frame
        pointer and return address) and subsequent arguments are at
        4-byte offsets from that.

    5.  exercise

        translate the following program into assembly under the same
        assumptions as the above exercise:

        def bar(int a) : int { return a+42; } def foo(int a) : int { int
        x; int y; x := bar(a); y := bar(a+1); return x\*y; } int x; x :=
        2; x := foo(x); output x+3;

        SOLUTION

        offsets, scope 0: x : -4 ~tmp0~ : -8

        offsets, scope foo: a : 8 x : -4 y : -8 ~tmp0~ : -12

        offsets, scope bar: a : 8 ~tmp0~ : -4

        ENTRY: // ENTRY PROLOGUE push FR mov SR FR sub 8 SR sto 0
        \[FR-4\] push CALLEE~SAVEREG1~ push CALLEE~SAVEREG2~

        // x := 2 mov 2 RR sto RR \[FR-4\]

        // PRE-CALL for x := foo(x) push CALLER~SAVEREG1~ push
        CALLER~SAVEREG2~ ld \[FR-4\] RR push RR call FOO

        // POST-RETURN for x := foo(x) add 4 SR pop CALLER~SAVEREG2~ pop
        CALLER~SAVEREG1~ sto RR \[FR-4\]

        // output x+3 ld \[FR-4\] RR sto RR \[FR-8\] mov 3 RR ld
        \[FR-8\] OR add OR RR

        // ENTRY EPILOGUE pop CALLEE~SAVEREG2~ pop CALLEE~SAVEREG1~ mov
        FR SR pop FR ret

        FOO: // FOO PROLOGUE push FR mov SR FR sub 12 SR sto 0 \[FR-4\]
        sto 0 \[FR-8\] push CALLEE~SAVEREG1~ push CALLEE~SAVEREG2~

        // PRE-CALL for x := bar(a) push CALLER~SAVEREG1~ push
        CALLER~SAVEREG2~ ld \[FR+8\] RR push RR call BAR

        // POST-RETURN for x := bar(a) add 4 SR pop CALLER~SAVEREG2~ pop
        CALLER~SAVEREG1~ sto RR \[FR-4\]

        // PRE-CALL for y := bar(a+1) push CALLER~SAVEREG1~ push
        CALLER~SAVEREG2~ ld \[FR+8\] RR sto RR \[FR-12\] mov 1 RR ld
        \[FR-12\] OR add OR RR push RR call BAR

        // POST-RETURN for y := bar(a+1) add 4 SR pop CALLER~SAVEREG2~
        pop CALLER~SAVEREG1~ sto RR \[FR-8\]

        // return x\*y ld \[FR-4\] RR sto RR \[FR-12\] ld \[FR-8\] RR ld
        \[FR-12\] OR mul OR RR

        // FOO EPILOGUE pop CALLEE~SAVEREG2~ pop CALLEE~SAVEREG1~ mov FR
        SR pop FR ret

        BAR: // BAR PROLOGUE push FR mov SR FR sub 4 SR push
        CALLEE~SAVEREG1~ push CALLEE~SAVEREG2~

        // return a+42 ld \[FR+8\] RR sto RR \[FR-4\] mov 42 RR ld
        \[FR-4\] OR add OR RR

        // BAR EPILOGUE pop CALLEE~SAVEREG2~ pop CALLEE~SAVEREG1~ mov FR
        SR pop FR ret

### notes on x86 assembly

1.  some basic x86

    we\'re not going to teach x86, but we\'ll give some pointers. an
    excellent resource for you to look at is
    <https://en.wikibooks.org/wiki/X86_Assembly>. when looking here,
    keep in mind:

    -   we\'re using 32-bit x86
    -   we\'re using the gnu assembler, which uses AT&T syntax

    \[there are lots of other places to look, but remember we\'re using
    GAS/AT&T syntax!\]

    wiki page \'x86 Register and Architecture Description\':

    -   we\'re using the 32-bit registers, so \$eax, %ebx, %ecx, %edx,
        %esp, %ebp, etc
    -   %esp is the stack pointer, %ebp is the frame pointer, %eax is
        the return value register

    wiki page \'GAS Syntax\':

    -   use the \'l\' suffix to indicate we\'re operating on 32-bit
        integers
    -   we\'re using the gnu assembler, which uses AT&T syntax. when
        looking at x86 resources be sure you\'re looking at the right
        ones.

    operands:

    -   \$k for constant k
    -   %reg for register value
    -   (%reg) for memory location whose address is in reg
    -   k(%reg) for memory location at address (%reg + k)

    can only use indirection in at most one operand to the same
    instruction.

    examples:

    -   \"mov 2 RR\" == \"movl \$2, %eax\"
    -   \"sub 8 SR\" == \"subl \$8, %esp\"
    -   \"ld \[FR-4\] RR\" == \"movl -4(%ebp), %eax\"
    -   \"sto RR \[FR-12\]\" == \"movl %eax, -12(%ebp)\"

    when looking at disassembled code, don\'t worry about the header
    information contained in sections like \".file\", \".text\", etc.
    we\'ll add the necessary scaffolding in the infrastructure we give
    you for your assignments.

    wiki pages have lots of x86 instructions; here is a small cheat
    sheet i\'ll post to the class slack \[show x86-cheat-sheet.pdf\]

    some other useful instructions:

    -   pushl \<reg\>
    -   popl \<reg\>

    x86 has some instructions that abbreviate the instructions for
    pre-calls and epilogues:

    -   call \<label\>: push current code location onto stack then jump
        to label (the last part of the pre-call instruction sequence).
    -   leave: copies %ebp to %esp then restores %ebp by popping from
        the stack (the middle part of the epilogue instruction
        sequence).
    -   ret: pop a code location from the stack and jump to it (the last
        part of the epilogue instruction sequence)

    \[we used analogues of \'call\' and \'ret\' in our examples and
    exercises\]

    x86 has a specific set of caller-save and callee-save registers.
    however, we only need to save them if we\'re actually using them and
    because of our naive codegen we don\'t actually need to worry about
    them.

    -   use %eax for RR and %edx for OR
    -   use %esp and %ebp for the stack and frame pointers
    -   leave all other registers alone
    -   don\'t need to do any caller or callee saves (except for %esp
        and %ebp which we\'ve already covered)

    \[this works because no callee function will write on the
    callee-save registers (and so they don\'t need to save them) and no
    caller function needs a register to be preserved over function calls
    (and so they don\'t need to save them).\]

2.  getting 32-bit x86 assembly for C code

    commands: gcc -O0 -m32 -c \<file\>.c objdump -d \<file\>.o

    -O0 says to turn off optimizations, -m32 forces 32-bit mode, -c says
    to compile but don\'t link (without -c you will get an error message
    unless \<file\>.c contains a main() function).

    == OR ==

    use Compiler Explorer: gcc.godbolt.org

    -   in the left panel, put c++ code
    -   in the right panel:
        -   choose compiler and version (gcc or clang for x86-64)
        -   set compiler options to \"-O0 -m32\"
        -   under the Output menu, make sure \"Intel asm syntax\" is
            deselected

    1.  example

        code.c: int foo(int x, int y) { int a = x \* y; int b = a + 2;
        return b; }

        \[instead of the below which is using objdump, show them
        Compiler Explorer in action\]

        output: 00000000 \<foo\>: 0: 55 push %ebp 1: 89 e5 mov %esp,%ebp
        3: 83 ec 10 sub \$0x10,%esp 6: 8b 45 08 mov 0x8(%ebp),%eax 9: 0f
        af 45 0c imul 0xc(%ebp),%eax d: 89 45 fc mov %eax,-0x4(%ebp) 10:
        8b 45 fc mov -0x4(%ebp),%eax 13: 83 c0 02 add \$0x2,%eax 16: 89
        45 f8 mov %eax,-0x8(%ebp) 19: 8b 45 f8 mov -0x8(%ebp),%eax 1c:
        c9 leave 1d: c3 ret

        0: put old frame pointer in stack frame 1: set current frame
        pointer to current stack pointer 3: add space to stack for
        locals 6: load x from memory 9: multiply by y d: store to a 10:
        load a 13: add 2 16: store to b 19: load b 1c: tear down current
        stack frame 1d: pop return address from stack and jump to it

        note that the memory offsets to x and y are positive (meaning
        below the current stack pointer) while those for a and b are
        negative (meaning above the stack pointer).

language L2: structs and dynamic memory allocation
==================================================

new syntax
----------

\[see handouts/L2-concrete-syntax.pdf\]

new frontend
------------

nothing novel for lexing and parsing, a small extension of what we\'ve
already done.

AST validation is more interesting, specifically typechecking now that
we have more than one type. we won\'t talk about it in detail for now
(we\'ll just assume correct programs), but essentially we need to
verify:

-   struct field access is to struct that contains that field
-   no pointer arithmetic in arithmetic expressions

new codegen
-----------

for codegen we need to handle the following:

-   struct allocation on the heap
-   struct field access in an expression
-   struct field access on the left-hand side of an assignment

we also need to ensure that codegen makes runtime memory management
possible.

### managing memory: heap vs stack

for L1 we didn\'t worry about memory management because the stack took
care of it for us. when we enter a new scope the declared variables are
allocated space by pushing them on the stack; when we leave that scope
the space is deallocated by popping them off the stack. however, this
means that the lifetimes of the variables are dictated by the scope they
are declared in; once we leave that scope the variables disappear.

if we want objects that will live beyond the scope they are created in,
we need the heap. that\'s the main difference between the stack and the
heap: heap lifetimes are not bound by scope. but if we don\'t deallocate
heap objects when we leave the scope, when do we do it? that is the
subject of memory management, and there are a number of different
possible answers with their own tradeoffs. we will discuss the subject
thoroughly in a later lecture.

for L2 specifically we\'ll rely on garbage collection to automatically
deallocate structs when it\'s safe, but to do that we\'ll need to change
the codegen in our compiler to make it feasible to perform GC at
runtime. i\'ll discuss the requirements as they become relevant during
codegen.

### symbol table

we need to extend the symbol table in two ways:

-   map typename to struct info (field names, types, and offsets)
-   variable info needs to include type info

other than adding the struct info first thing, we can handle the symbol
table the same way we did before (just remembering to add the type info
from the variable declarations alongside the memory locations).

### struct allocation

let\'s examine a concrete example from the handout:

x := new %tree;

where %tree is defined as:

struct %tree { int value; %tree left; %tree right; };

the runtime memory manager is going to handle the heap, so we\'ll
delegate memory allocation to it by making a call to a predefined
function \"allocate\" using the calling convention we discussed for L1.
the allocate function will need to know how many words to allocate,
which we can determine by looking up \<typename\> in the symbol table to
get the corresponding struct info. our contract is that when we call
allocate with a number of words (one per field), it will allocate
sufficient space on the heap to hold those words and return the
beginning address of the newly allocated space (which address we will
store into the left-hand side of the assignment).

for our example, we would call \'allocate(3)\' and get back an address
(say 0x100) at the beginning of 3 words of heap memory:

  -- -- ----- ----- ----- -- --
        xxx   xxx   xxx      
  -- -- ----- ----- ----- -- --

0x100\^

and we would write the value 0x100 into \'x\'. we then need to generate
code to initialize the values of the fields, which as in L1 are always
initialized to 0 (which is also the value of nil for references):

  -- -- --- --- --- -- --
        0   0   0      
  -- -- --- --- --- -- --

0x100\^

however, the runtime memory manager will need more information about the
struct than its size in order to handle it correctly, namely it needs to
know which of its fields are pointers to other structs. this is
information that we have at compile-time but we need it at runtime (the
reason we need it will become clear when we cover memory management
schemes). to communicate this information, allocate will actually create
space for \<num~words~\>+1 words of memory, with the extra word being
[before]{.underline} the returned address:

  -- ----- ----- ----- ----- -- --
     xxx   xxx   xxx   xxx      
  -- ----- ----- ----- ----- -- --

0x100\^

this \'header word\' is going to contain the following information:

1.  the first byte, bits 0--7, holds the number of fields in the struct
2.  bits 8--30 are a bit vector s.t. a bit is set iff the corresponding
    field in the struct is a reference.
3.  the last bit, bit 31, is set to 1 (for reasons that, again, will
    become apparent when we discuss memory management).

the compiler must generate code to fill in that information. for our
example, the result of the generated code should be:

  -- ---------------------------------- --- --- --- -- --
     00000011011000000000000000000001   0   0   0      
  -- ---------------------------------- --- --- --- -- --

0x100\^

that is, there are three fields and the second two are pointers. this
scheme means that, given a pointer to a struct containing an address,
the runtime can always read the word at address-4 to get the size of the
struct and which fields are pointers.

notice that this scheme limits the number of fields a struct can have:
at most 23, the size of the bitvector. we\'re using this scheme because
it\'s relatively simple, but constraining the number of fields is a
tradeoff. we could use a more elaborate scheme to remove this
restriction. for example, we could map each typename to an index, copy
the symbol table to the static memory segment indexed by typename, and
have the header word contain the appropriate typename index instead of
struct size and a bitvector. then the runtime can read the header word
and use that index to lookup the information in the static memory\'s
symbol table. that scheme is more flexible, but more complex and
expensive. which one to use is a matter of language design.

to recap, when we generate code for \"\<access\> := new \<typename\>\",
we:

1.  generate a call to function \'allocate\' passing an argument that is
    the number of fields in \<typename\>.
2.  write the return value to the left-hand side of the assignment.
3.  for each field of the struct being allocated, store a 0 to the
    corresponding offset from the returned address.
4.  compute the header word and write it to a -4 offset from the
    returned address.

### field access in expression

example 1:

struct %foo { int a; int b; };

%foo x; x := new %foo; output x.b;

let\'s focus on the output expression. x contains the address of a foo
struct object in memory, which has two integer fields.

in order to retrieve the correct value, we need to dereference x to get
the address of the struct object in the heap, compute the offset into
that struct object of field \'b\', then read the value at that address
from memory.

AST: \[access x b\]

\[note: for convenience i\'m going to be more efficient about the
generated code rather than strictly following our naive codegen
algorithm.\]

ld \[FR-4\] RR ; get the address stored in \'x\' ld \[RR+4\] RR ; get
the value at that address + 4, because b is at a 4-byte offset from the
beginning of the struct

how did we know to use offset 4? because we look up \'x\' in the symbol
table to get its type \'foo\', then look up \'foo\' to get the
information about the field offsets. remember that the extra header word
is immediately [before]{.underline} the returned address, so offset 0
would be the first field of the struct.

example 2:

struct %foo { int a; %bar b; };

struct %bar { int c; int d; %baz e; };

struct %baz { int f; int g; };

%foo x; x := new %foo; x.b := new %bar; x.b.e := new %baz; output
x.b.e.f;

let\'s focus again on the output expression. x, x.b, and x.b.e will all
contain pointers into the heap; we\'re going to do the same thing as
before except chained in a row.

AST: \[access \[access \[access x b\] e\] f\]

when we recursive through the AST for codegen, we\'ll end up generating
code for \[access x b\] first, then \[access \<result\> e\], then
\[access \<result\> f\]:

ld \[FR-4\] RR ; get the address stored in \'x\' ld \[RR+4\] RR ; get
the address stored in \'x.b\' at offset 4 ld \[RR+8\] RR ; get the
address stored in \'x.b.e\' at offset 8 ld \[RR\] RR ; get the int
stored in \'x.b.e.f\' at offset 0

again, we look in the symbol table to compute the offsets. note that
everything starts with looking up the type of \'x\' to see that it\'s a
\'foo\', then looking at \'foo\' to see that the \'b\' field is at
offset 4 and type \'bar\', then looking at \'bar\' to see the \'e\'
field is at offset 8 and type \'baz\', then looking at \'baz\' to see
the \'f\' field is at offset 0.

### field access on lhs of assignment

when the access path is on the left-hand side of an assignment we need
to treat it differently. this is actually the same as when it\'s just a
variable as in L1, but we didn\'t highlight the difference. let\'s look
at it now, then generalize to arbitrary access paths.

example:

x := x + 1;

when we evaluate the \'x\' in \'x + 1\' we want to find the address of
\'x\' in memory and retrieve the value stored there:

ld \[FR-4\] RR add 1 RR

but when we look at \'x\' in the left-hand side we want just the address
of \'x\', which is where we\'ll put the final value:

sto RR \[FR-4\]

we say that \'x\' in the right-hand side expression is an
[rvalue]{.underline} and that \'x\' on the left-hand side is an
[lvalue]{.underline}. basically, for an lvalue we want to stop at the
address to store the value, while for an rvalue we want to go ahead and
dereference that address to get the value currently stored there.

now let\'s look at some non-trivial access paths:

struct %foo { int a; int b; int c; };

%foo x; x := new %foo; x.b := x.c;

as usual, we want to evaluate the right-hand side to a value, then store
it into the location specified by the left-hand side:

ld \[FR-4\] RR ; get the address stored in \'x\' ld \[RR+8\] RR ; get
the int stored at offset 8 ld \[FR-4\] OR ; get the address stored in
\'x\' sto RR \[OR+4\] ; store the right-hand side value into \'x.b\' at
offset 4

what if we have an lvalue access path with multiple fields?

struct %foo { int a; %bar b; int c; };

struct %bar { int d; int e; };

%foo x; x := new %foo; x.b := new %bar; x.b.d := x.c;

then we follow the access paths as normal, except that we stop right
before we dereference the last one:

ld \[FR-4\] RR ; get the address stored in \'x\' ld \[RR+8\] RR ; get
the int stored at offset 8 ld \[FR-4\] OR ; get the address stored in
\'x\' ld \[OR+4\] OR ; get the address stored at offset 4 sto RR \[OR\]
; store the right-hand value into \'x.b.d\' at offset 0

### enable collecting the root set for GC

the last piece we need to worry about is how codegen will interact with
our runtime memory management. we already handled one aspect: when we
allocate a struct, the codegen will be sure to write the necessary
information about the struct fields into the header word. but there\'s
one more thing to do.

for our L2 memory management we\'re going to use a scheme called a
\'tracing garbage collector\'. we\'ll explain what that actually means
in a different lecture, but for now just accept the following: for that
scheme to work, the GC needs to be able to look at the function stack at
any point during execution and identify which memory locations in the
stack represent pointers to structs in the heap.

this is more difficult than it might sound. remember that the stack is
constantly growing and shrinking, and the same memory location may
represent a pointer or not depending on when exactly during program
execution we look at it. how can we implement codegen in order to enable
the runtime GC to collect this information?

there are many possible strategies with different tradeoffs between
complexity, flexibility, and efficiency. i\'m going to describe one
strategy that, going along with our usual mantra of \"make it as simple
as possible as long as it works\", focuses on being easy to implement at
the expense of some flexibility.

key to this strategy is that the only heap pointers on the stack will be
either function parameters or function local variables. here\'s the
idea:

1.  move all nested variable declarations up to the top of the function
    they\'re in, renaming them as necessary to avoid name clashes. this
    transformation expands the amount of stack memory the function will
    consume, but otherwise doesn\'t change the program behavior (as long
    as we remember to initialize the values to 0 at the same points as
    before).

2.  modify the prologue instruction sequence for function codegen,
    specifically immediately after we push the old frame pointer value
    onto the stack: push two additional words onto the stack after that
    but before allocating stack space for local variables. remember that
    we need to adjust the offsets of the local variables accordingly in
    the symbol table. call these two words the \"argument info word\"
    and the \"local info word\".

3.  in the argument info word, set it as a bit vector s.t. a bit is set
    iff the corresponding function parameter is a struct pointer.
    remember that the function arguments are pushed onto the stack by
    the pre-call instruction sequence, so by looking at the argument
    info word we can tell which positive offsets from the frame pointer
    correspond to a pointer argument.

4.  in the local info word, set it as a bit vector s.t. a bit is set iff
    the corresponding local variable is a struct pointer. remember that
    the function locals are pushed onto the stack by the prologue
    instruction sequence immediately after the local info word, so by
    looking at the local info word we can tell which negative offsets
    from the frame pointer correspond to a pointer local.

so, how would this work at runtime to identify all heap pointers on the
stack? recall that the current frame pointer is always holding the
address of the old frame pointer for the caller function (this is
enforced by our calling convention). at the point when we need to
compute this information:

1.  get the current frame pointer.

2.  read the argument info word (at frame pointer - 4); for each set bit
    representing offset X, return the memory address \'frame pointer +
    X\'.

3.  read the local info word (at frame pointer - 8); for each set bit
    representing offset X, return the memory address \'frame pointer -
    X\'.

4.  get the value pointed to by the current frame pointer, which is the
    value of the old frame pointer.

5.  set the current frame pointer value to that old value, then go to 2.

repeat these steps until we\'ve walked the entire stack and looked at
all of the stack frames. then we\'ve returned all possible pointers into
the heap.

1.  example

    PROGRAM: struct %foo { int a; };

    def bar(%foo c) : int { %foo d; int e;

    d := new %foo; if (0 \< c.a) { d.a = c.a - 1; e = bar(d); }

    return e; }

    %foo x; int y;

    x := new %foo; x.a := 10; y := bar(x);

    output y;

    suppose we execute this program, and after a few function calls
    decide to find all pointers on the stack. assume there are no
    caller- or callee-save registers for convenience.

    STACK:

    ADDRESS CONTENTS

    ------------------------------------------------------------------------

    0x900: 0x1000 ; old frame pointer 0x0 ; argument info word: there
    are no pointer arguments 0x1 ; local info word: \'x\' is a pointer
    0xab0 ; the value of \'x\', a pointer into the heap 0x0 ; the value
    of \'y\', an int 0xab0 ; the call argument, i.e., \'x\' 0xd0f ; the
    return address 0x800: 0x900 ; old frame pointer 0x1 ; argument info
    word: there is 1 pointer argument 0x01 ; local info word: there is 1
    pointer local 0xac0 ; the value of \'d\' 0x0 ; the value of \'e\'
    0xac0 ; the call argument, i.e., \'d\' 0xdef ; the return address
    0x700: 0x800 ; old frame pointer 0x1 ; argument info word: there is
    1 pointer argument 0x01 ; local info word: there is 1 pointer local
    0xad0 ; the value of \'d\' 0x0 ; the value of \'e\'

    this is the stack after the first recursive call to \'bar\'. the
    current frame pointer is 0x700, which points to the most recent
    \'old frame pointer\' on the stack.

    1.  go to 0x700-4 to get the argument info word
    2.  there is 1 set bit: return 0x700+8 as the memory location of a
        pointer
    3.  go to 0x700-8 to get the local info word
    4.  there is 1 set bit: return 0x700-12 as the memory location of a
        pointer
    5.  set the current frame pointer as 0x800 (the address of the next
        most recent frame pointer)
    6.  go to 0x800-4 to get the argument info word
    7.  there is 1 set bit: return 0x800+8 as the memory location of a
        pointer
    8.  go to 0x800-8 to get the local info word
    9.  there is 1 set bit: return 0x800-12 as the memory location of a
        pointer
    10. set the current frame pointer as 0x900 (the address of the next
        next most recent frame pointer)
    11. go to 0x900-4 to get the argument info word
    12. there are no pointer arguments
    13. go to 0x900-8 to get the local info word
    14. there is 1 set bit: return 0x900-12 as the memory location of a
        pointer
    15. set the current frame pointer as 0x1000 (the address of the next
        next next most recent frame pointer)
    16. say that 1000 is the base of the stack, so we\'re done.

### exercise {#exercise-15}

generate code for the following program (again assuming no caller- or
callee-save registers):

struct %foo { int a; %bar b; };

struct %bar { int c; int d; };

def fun(%foo p, int q, %bar r) : int { %foo s; s := p; return s.a; }

%foo x; int y;

x := new %foo; x.b := new %bar; y := fun(x, 2, x.b); output y;

SOLUTION

; entry prologue push FR mov SR FR push 0b0 ; argument info: no pointer
arguments push 0b1 ; local info: x is a pointer sub 8 SR ; allocate
stack space for x and y sto 0 \[FR-4\] ; initialize x to nil sto 0
\[FR-8\] ; initialize y to 0

; x := new foo push 2 ; argument to \'allocate\': 2 words call allocate
add 4 SR ; deallocate stack space for argument sto RR \[FR-4\] ; write
return value to \'x\' sto 0 \[RR\] ; initialize x.a to 0 sto 0 \[RR+4\]
; initialize x.b to nil sto 0b00000010010...01 \[RR-4\] ; initialize
header word

; x.b := new bar push 2 ; argument to \'allocate\': 2 words call
allocate add 4 SR ; deallocate stack space for argument ld \[FR-4\] OR ;
get address of struct in heap from \'x\' sto RR \[OR+4\] ; write return
value to \'x.b\' sto 0 \[RR\] ; initialize x.b.c to 0 sto 0 \[RR+4\] ;
initialize x.b.d to 0 sto 0b00000010000...01 \[RR-4\] ; initialize
header word

; y := fun(x, 2, x.b) ld \[FR-4\] RR ; address stored in \'x\' ld
\[RR+4\] RR ; address stored in \'x.b\' push RR ; push \'x.b\' argument
to \'fun\' push 2 ; push \'2\' argument to \'fun\' ld \[FR-4\] RR ;
address stored in \'x\' push RR ; push \'x\' argument to \'fun\' call
FUN add 12 SR ; deallocate stack space for arguments sto RR \[FR-8\] ;
write return value to \'y\'

; output y ld \[FR-8\] RR

; entry epilogue mov FR SR pop FR ret

; FOO prologue FOO: push FR mov SR FR push 0b101 ; argument info: first
and third arguments are pointers push 0b1 ; local info: one local
pointer add 4 SR ; allocate stack space for \'s\' sto 0 \[FR-4\] ;
initialize \'s\' to nil

; s := p ld \[FR+16\] RR ; get value of \'p\' param sto RR \[FR-4\] ;
put it in \'s\'

; return s.a ld \[FR-4\] RR ; get value of \'s\' ld \[RR\] RR ; get
value of \'s.a\'

; FOO epilogue mov FR SR pop FR ret

memory management schemes
-------------------------

\[see memory management slides\]

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

IR: 3-address code
------------------

let\'s discuss the IR we\'ll use for the middle-end. it will be a
classic example of \"three address code\", something similar to that
used by many real compilers. the name \"three address code\" is a
reference to the fact that no instruction will involve more than three
\"addresses\" (think: variables). here is the syntactic definition of
the IR:

\[in1\] binop ::= + \| - \| \* \| \< \| \<= \| = \| AND \| OR operand
::= variable \| constant instruction ::= variable \<- operand binop
operand

  ----------------------------
  variable \<- NOT operand
  variable \<- CALL function
  variable \<- operand
  arg operand
  return operand
  output operand
  jump label
  jump~if0~ operand label
  ----------------------------

### example {#example-8}

let\'s look at an example. here is the original program:

\[in2\] def bar(int a) : int { return a + (a - 12) \* 42; }

def foo(int a) : int { int x; int y; x := bar(a); y := bar(a+1); return
x\*y; }

int x; while (x \< 2) { x := x + 1; } x := foo(x); output x+3;

here is the IR version:

\[in3\] def bar(int a) { ~tmp0~ \<- a - 12 ~tmp1~ \<- ~tmp0~ \* 42
~tmp2~ \<- a + ~tmp1~ return ~tmp2~ }

def foo(int a) { arg a x \<- CALL bar ~tmp0~ \<- a + 1 arg ~tmp0~ y \<-
CALL bar ~tmp1~ \<- x \* y return ~tmp1~ }

WHILE~START0~: ~tmp0~ \<- x \< 2 jump~if0~ ~tmp0~ WHILE~END0~

x \<- x + 1 jump WHILE~START0~

WHILE~END0~: arg x x \<- CALL foo ~tmp1~ \<- x + 3 output ~tmp1~

\[note that i\'m writing the labels on separate lines like instructions
for convenience, but they aren\'t actually instructions and don\'t take
up a line of code.\]

we see that the IR is a lot like assembly, except that it\'s
higher-level (target agnostic, keeps the abstraction of functions and
function calls, arbitrary number of variables). it does strip away the
variable definitions, type annotations, etc because we assume that these
are already in the symbol table.

this IR is nicer to work with for optimizations because we don\'t have
to deal with complex nested expressions and we can assume that every
instruction is in one of a few kinds of formats and never has more than
two operands.

how do we generate the IR from the AST? it works exactly like the
codegen we\'ve already examined, except emitting the IR instructions
instead of assembly. other than that, we can use the same strategy and
implementation. for clarity in this example i always created a new
temporary, but we could also reuse them between instructions using the
same strategy as we did for assembly codegen.

### exercise {#exercise-16}

\[in4\] translate the following program:

def foo(int a, int b) : int { int x; int y;

x := a + b \* (a - b);

while (y \<= x + 3) { y := y + 10; }

return y \* 2; }

int t1; int t2;

t1 := 42; t2 := foo(t1 + 2, t1 \* 2); if (t1 \< t2) { t1 := t2; } else {
t1 := t1 + 2; }

output t1 + 42;

SOLUTION

\[in5\] def foo(int a, int b) { ~tmp0~ \<- a - b ~tmp1~ \<- b \* ~tmp0~
x \<- a + ~tmp1~

WHILE~START0~: ~tmp2~ \<- x + 3 ~tmp3~ \<- y \<= ~tmp2~ jump~if0~ ~tmp3~
WHILE~END0~

y \<- y + 10 jump WHILE~START0~

WHILE~END0~: ~tmp4~ \<- y \* 2 return ~tmp4~ }

t1 \<- 42 ~tmp0~ \<- t1 + 2 ~tmp1~ \<- t1 \* 2 arg ~tmp0~ arg ~tmp1~ t2
\<- CALL foo

~tmp2~ \<- t1 \< t2 jump~if0~ ~tmp2~ IF~FALSE1~ t1 \<- t2 jump IF~END1~

IF~FALSE1~: t1 \<- t1 + 2

IF~END1~: ~tmp3~ \<- t1 + 42 output ~tmp3~

control-flow graph
------------------

### definition

unlike codegen, we don\'t output the IR as a linear list of
instructions. instead, we use a data structure called the [control flow
graph]{.underline}. confusingly, we abbreviate this as CFG, just like
context-free grammars. so in the frontend CFG will mean context-free
grammar and in the middle-end it will mean control flow graph; you just
have to use the surrounding context to figure out which one is meant.

a CFG models the control-flow of the program, i.e., which instructions
can execute after which other instructions. it is a directed graph whose
nodes are [basic blocks]{.underline} (a sequence of instructions, to be
defined more precisely soon). an edge from basic block A to basic block
B means that immediately after A executes, B may execute (not
necessarily [must]{.underline} execute, just [may]{.underline} execute).

a [basic~block~]{.underline} is a sequence of instructions with the
following property: control must enter at the first instruction of the
block and can only exit after the last instruction in the block, i.e.,
the block has a single entry point and a single exit point. in other
words, if program execution enters the basic block then every
instruction in the basic block must be executed.

-- note: when i was talking about optimization scope and said that local
optimizations operate on \"small fragments of code\", what i really
meant was basic blocks.

### examples

the CFG is perhaps more easily understood by example.

EXAMPLE 1

\[cfg1\] program:

x := foo(); if (x \< 10) { x := 3; y := 2; } else { x := 2; y := 3; } z
:= x + y \* 2; output z;

IR:

\[A\] x \<- CALL foo ~tmp0~ \<- x \< 10 jump~if0~ ~tmp0~ IF~FALSE0~

\[B\] x \<- 3 y \<- 2 jump IF~END0~

\[C\] x \<- 2 y \<- 3

\[D\] IF~END0~: ~tmp1~ \<- y \* 2 z \<- x + ~tmp1~ output z

each separate block of instructions is a basic block. note that: -- a
jump, return, or output always ends a basic block. -- a basic block
doesn\'t have to end in a jump, return, or output (see false branch).

-   the block has to end there because the next instruction is the
    target of a jump and we cannot allow basic blocks to be entered in
    the middle. since the way we\'re generating the IR every jump target
    has to be a label, it\'s fair to say that any labeled instruction
    will automatically start a new basic block.

-- a call does not end a basic block. while it does transfer control
somewhere else, eventually that control will always return to
immediately after the call. that means we preserve the invariant that
every instruction in the basic block must be executed.

CFG:

\[show CFG\]

EXAMPLE 2:

\[cfg2\] program:

x := foo(); while (x \< 10) { x := x + 1; y := y + 1; z := x \* y; }
output z;

IR:

\[A\] x \<- CALL foo

\[B\] WHILE~START0~: ~tmp0~ \<- x \< 10 jump~if0~ ~tmp0~ WHILE~END0~

\[C\] x \<- x + 1 y \<- y + 1 z \<- x \* y jump WHILE~START0~

\[D\] WHILE~END0~: output z

CFG:

\[show CFG\]

EXAMPLE 3:

\[cfg3\] program:

x := foo(); while (x \< 10) { x := x + 1; if (x \<= y) { while (x \< y)
{ y := y - 2; } } } output y;

IR:

\[A\] x \<- CALL foo

\[B\] WHILE~START0~: ~tmp0~ \<- x \< 10 jump~if0~ ~tmp0~ WHILE~END0~

\[C\] x \<- x + 1 ~tmp1~ \<- x \<= y jump~if0~ ~tmp1~ IF~FALSE1~

\[D\] WHILE~START2~: ~tmp2~ \<- x \< y jump~if0~ ~tmp2~ WHILE~END2~

\[E\] y \<- y - 2 jump WHILE~START2~

\[F\] WHILE~END2~: IF~FALSE1~: jump WHILE~START0~

\[G\] WHILE~END0~: output y

CFG:

\[show CFG\]

### algorithm

the basic algorithm for building a CFG is simple, though various
language features can complicate it a bit. let\'s assume that we start
with a linear sequence of IR instructions (something like what we would
get from our codegen algorithm, a vector of instructions, but IR
instructions rather than assembly instructions). also, this is per
function (including treating the \"global\" block of code ending in
\'output\' as a function itself).

\[cfg4\] we\'ll use the following example to illustrate the algorithm:

1.  x \<- CALL foo
2.  WHILE~START0~: ~tmp0~ \<- x \< 10
3.  jump~if0~ ~tmp0~ WHILE~END0~
4.  x \<- x + 1
5.  ~tmp1~ \<- x \<= y
6.  jump~if0~ ~tmp1~ IF~FALSE1~
7.  WHILE~START2~: ~tmp2~ \<- x \< y
8.  jump~if0~ ~tmp2~ WHILE~END2~
9.  y \<- y - 2
10. jump WHILE~START2~
11. WHILE~END2~: IF~FALSE1~: jump WHILE~START0~
12. WHILE~END0~: output y

```{=html}
<!-- -->
```
1.  identify all \"leader\" instructions (the instructions that start a
    basic block). a leader is the first instruction in the function, any
    labeled instruction (because it can be the target of a jump), and
    any instruction immediately following a conditional jump (because it
    is the fall-through instruction if the conditional jump is not
    taken). remember to treat multiple consecutive labels as a single
    label for these purposes. save the indices of these leader
    instructions in a vector, in the order they appear in the
    instruction sequence.

==\> the leaders for the example are indices { 00, 01, 03, 06, 08, 10,
11 }.

1.  record the basic blocks as the sequences of instructions from each
    leader up to, but not including, the next leader in sequence.

    the basic blocks for the example are:

    \[A\]

    1.  x \<- CALL foo

    \[B\]

    1.  WHILE~START0~: ~tmp0~ \<- x \< 10
    2.  jump~if0~ ~tmp0~ WHILE~END0~

    \[C\]

    1.  x \<- x + 1
    2.  ~tmp1~ \<- x \<= y
    3.  jump~if0~ ~tmp1~ IF~FALSE1~

    \[D\]

    1.  WHILE~START2~: ~tmp2~ \<- x \< y
    2.  jump~if0~ ~tmp2~ WHILE~END2~

    \[E\]

    1.  y \<- y - 2
    2.  jump WHILE~START2~

    \[F\]

    1.  WHILE~END2~: IF~FALSE1~: jump WHILE~START0~

    \[G\]

    1.  WHILE~END0~: output y

2.  for each basic block, look at the last instruction:

    A. if it is not a jump, add an edge from this basic block to the
    basic block whose leader is immediately next in sequence.

==\> basic block A has an edge to B; basic block G would have an edge
but it ends the function so it doesn\'t.

B. if it is an unconditional jump to label L, add an edge from this
basic block to the basic block whose leader is labeled L.

==\> for the example, basic block E has an edge to D; basic block F has
an edge to B.

C. if it is a conditional jump to label L:

1.  add an edge from this basic block to the basic block whose leader is
    immediately next in sequence.
2.  add an edge from this basic block to the basic block whose leader is
    labeled L.

==\> for the example, basic block B has edges to C and G; basic block C
has edges to D and F; basic block D has edges to E and F.

### exercise {#exercise-17}

\[cfg5\] create the CFG for the following program:

def foo(int a, int b) { ~tmp0~ \<- a - b ~tmp1~ \<- b \* ~tmp0~ x \<- a
+ ~tmp1~ WHILE~START0~: ~tmp2~ \<- x + 3 ~tmp3~ \<- y \<= ~tmp2~
jump~if0~ ~tmp3~ WHILE~END0~ y \<- y + 10 jump WHILE~START0~
WHILE~END0~: ~tmp4~ \<- y \* 2 return ~tmp4~ }

t1 \<- 42 ~tmp0~ \<- t1 + 2 ~tmp1~ \<- t1 \* 2 arg ~tmp0~ arg ~tmp1~ t2
\<- CALL foo ~tmp2~ \<- t1 \< t2 jump~if0~ ~tmp2~ IF~FALSE1~ t1 \<- t2
jump IF~END1~ IF~FALSE1~: t1 \<- t1 + 2 IF~END1~: ~tmp3~ \<- t1 + 42
output ~tmp3~

SOLUTION

\[cfg6\] basic blocks:

def foo(int a, int b) { \[A\] ~tmp0~ \<- a - b ~tmp1~ \<- b \* ~tmp0~ x
\<- a + ~tmp1~

\[B\] WHILE~START0~: ~tmp2~ \<- x + 3 ~tmp3~ \<- y \<= ~tmp2~ jump~if0~
~tmp3~ WHILE~END0~

\[C\] y \<- y + 10 jump WHILE~START0~

\[D\] WHILE~END0~: ~tmp4~ \<- y \* 2 return ~tmp4~ }

\[E\] t1 \<- 42 ~tmp0~ \<- t1 + 2 ~tmp1~ \<- t1 \* 2 arg ~tmp0~ arg
~tmp1~ t2 \<- CALL foo ~tmp2~ \<- t1 \< t2 jump~if0~ ~tmp2~ IF~FALSE1~

\[F\] t1 \<- t2 jump IF~END1~

\[G\] IF~FALSE1~: t1 \<- t1 + 2

\[H\] IF~END1~: ~tmp3~ \<- t1 + 42 output ~tmp3~

cfg:

\[show CFG\]

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

type systems
============

we have a fairly complete compiler at this point: lexer, parser,
optimizer, codegen, and garbage collector. for language L1 we only
skimmed an important piece of the front-end though: validating the AST
after parsing. for L1 this is a simple process that only involves a few
easy checks. when we move to language L2 things get more interesting.

The most common method of validating the AST is by a process called
[type checking]{.underline}. let\'s look at type systems and type
checking in general, then apply those ideas to L2 to develop our own
type system and type checker. you have all used languages with type
systems and type checkers before (e.g., C++, Java), but do you know what
a type actually is or what they are used for? now you will.

\[handout3-typedLC.pdf section 1\]

now that we understand what types are used for, how do we specify a type
system for a language? it turns out that type systems are closely
related to mathematical logic, and we can specify type systems in a
similar way using a system called [natural deduction]{.underline}.

simple type system
------------------

let\'s look at a simple example before we tackle the entire L2 language.

\[simple-type-system.pdf\]

### example 1

\[type check the AST example given in simple-type-system.pdf\]

### example 2 {#example-2-1}

def foo(bool a) : int { return if (a) {1} else {2}; } def bar(int a) :
int { return a + 1; } if (foo(false) \<= bar(0)) { true } else { false }

### exercise {#exercise-22}

def foo(int a) : int { return a + (if (a \<= 0) { a+1 } else { 42 }); }
if (((1 + 2) + -12) \<= foo(12)) { 3 \<= 4 } else { false }

L2 type system
--------------

now that we have a handle on the idea, let\'s look at L2.

\[L2-abstract-syntax.pdf\] \[L2-type-system.pdf\]

### example {#example-12}

\[L2-concrete-syntax.pdf section 2, the \'insert\' function\]

### exercise {#exercise-23}

\[L2-concrete-syntax.pdf section 2, the rest of the program\]
