CSE 4102 - Programming Languages  
Spring 2026

# Lecture 0 - 1/20/2026

# Course Goals
- Learn to evaluate and discuss programming languages (PLs)
- Learn various PL paradigms
- Makes it easier to learn more PLs in the future
- Learn how to choose the right tool for the right problem
- Understand the history and key ideas behind programming languages

# Programming Language Paradigms
- **Turing Complete**: It can solve any problem that can be computed  
  - Most programming languages are Turing Complete
- A programming language is built of its **syntax** and **semantics**
  - **Syntax**: What programs look like  
    - ex. Curly braces, indentation, semi-colons, etc.
  - **Semantics**: What programs mean  
    - Available features, how does the computer interpret the program, etc.

# Ways to Divide Programming Languages

## Semantics
- Two types of semantics: static vs. dynamic
  - **Static Semantics**: Analyzed at compile time
  - **Dynamic Semantics**: Analyzed at run time
- In Python, the static semantic check is small, most happen dynamically
- In other languages, this may be different

## Types
- **Static Languages**: Types checked at compile time (no type errors at runtime)
- **Dynamic Languages**: Types checked at run time, can have type errors
- **Weakly Typed Languages**: Types checked at compile time (FINISH)

## Paradigms
- **Imperative / Procedural**: Tell the computer exactly what to do  
  - Key features: Performance, computation-centric, works like a computer
- **Functional**: Describe the computation mathematically  
  - Key features: Mathematical, NOT state-oriented
- **Object-Oriented**: Objects perform computation and carry data  
  - Key features: State-centric, has hierarchy of objects, good for simulation
- **Logic**: Provide the constraints or requirements for the answer  
  - Key features: Computation is reasoning, inference, non-determinism

# Lecture 2 - 1/22/2026
# Ways of Translating a Programming Language
1. Source Code -> Compiler -> Binary/Assembly
  - Runs natively on hardware
  - Very fast
  - Needs a compiler to target each architecture (x86, ARM, etc.)
  - Translates the program to a form executable by the machine (or assembly)
  - Compile, then run the executable: compiler no longer involved after
  - ex. C, C++
2. Source Code -> Interpreter
  - (FINISH FROM SLIDE 44)
  - Does checks on if this is syntatically correct, etc.
  - Interpreter directly runs code, does NOT translate to binary
  - Very slow
  - Very portable
  - ex. Python
3. Source Code -> Compiler -> Bytecode -> Bytecode Interpreter/VM
  - Compile partway to "made-up" assembly code called bytecode
  - VM interprets and runs bytecode
  - Independent of architecture because the VM is running Bytecode (very portable)
  - Usually the best of both worlds
  - ex. Java

Note: OCaml has two different compiler options: Native and Bytecode

## Compiler Translation
1. **Source code** first gets translated into **tokens** through a *Lexical Analyzer (Lexer)*
  - Not important information gets thrown away, like whitespace (usually)
  - ex. a = b + c - 1 --> VAR a EQUAL VAR b OP + VAR c OP - CONST 1
2. **Tokens** go through a *parser* and translate it into **abstract syntax**
  - Converts tokens into a tree structure
  - Tree structure implicitly handles order of operations (for math, if statements, etc.)
  - Called an Abstract Syntax Tree (AST)
  - Parsers look a lot different for different languages
3. AST goes through analysis, like for statically typed languages 
4. Note: Up to this point, compilers and interpreters are the same. Interpreters would be done here
5. Abstract Syntax Tree is *lowered* into an **intermediate representation** of the code
  - Simpler language
6. The **intermediate representation** is optimized
7. The assembly code is generated through a *code generator* from the intermediate representation

Steps 1-3 (up to abstract syntax) are called the **frontend** and are fairly similar across all compilers
- Becomes less and less specific to the language as steps progress
Steps 5-6 (after abstract syntax) are called the **backend** and may differ very much between compilers
- Becomes more and more specific to the machine as steps progress
- Can usually swap out backend to target different machines

More recently, languages are compiled to the same intermediate representation (with the frontend) and then that intermediate representation is compiled to the specific machine (with the backend)
- If we have `n` languages and `m` architectures, we don't need to build `n * m` compilers, just `n` frontends, `m` backends, and `1` unified intermediate representation

# Functional Programming
- Strong mathematical foundations
- Very high-level
- Really elegant for expressing many algorithms
- All variables are immutable

# OCaml
- Statically typed, functionally
  - Also has imperative and object-oriented features
- String, expressive type system
  - Makes implementing many data structures very easy
- Has type inference
- Has an industrial-strength compiler with lots of libraries that is actively maintained
