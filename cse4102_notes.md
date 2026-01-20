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
