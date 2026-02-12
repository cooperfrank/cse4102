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

# Lecture 3 - 1/29/2026

# Functions
## Anonymous Functions (Lambdas)
- Also called Lambdas
- Defining functions without giving them a name
- Defined using `fun` keyword and arrow `->`
- Example:
  - `let six = (fun (x : int) -> x + 1) 5`
  - 5 is evaluated into the lambda to return 6
- There is no difference between functions defined regularly and with lambdas under the hood; they are just syntax
- Can set a variable equal to a lambda

## Couriering
- Functions don't actually take two arguments, it is two functions that each take one argument
- Expands into one-argument lambdas
- If `my_plus` is a two-argument function, then `let plus_one = my_plus 1;;` returns a function with the first argument set to 1, only takes one more argument
- Can also be done with regular operators (like +) by changing to postfix syntax, like `let plus_one = (+) 1;;`

## Recursive Functions
- Defined with `let rec`, `rec` is the recursive keyword

**Factorial Function Example**
```ocaml
(* Factorial Function: returns n! *)
let rec fact (n : int) : int =
  if n <= 0 then 1
  else n * (fact (n - 1))
;;
```

# Compile Command
`ocamlc -o <out_file>.exe <in_file>.ml`

# Printing
- Same syntax as printf in C
- Example: `Printf.printf "fact(%d) = %d" 0 (fact 0)`
- Returns type `unit`
  - Has only value `unit`
  - This is because functions have to return, so it just returns a single value
- Can use `let _` to indicate that we don't care about return value

# Assert
- Takes any expression that returns a boolean
  - If true, nothing happens
  - If false, program will error out
 
# Lecture 4 - 2/3/2026
# Tuples
- Group a fixed number of values
- Elements may have different types
- Written with commas inside parentheses

Example:
```ocaml
let p = (3, 5)
```

Type:
```ocaml
int * int
```

- `*` in types means paired with, not multiplication

## Accessing Tuple Elements
- Built-in functions for pairs
  - `fst : 'a * 'b -> 'a`
  - `snd : 'a * 'b -> 'b`

Example:
```ocaml
let p = (10, 20)
fst p
snd p
```

- Only works for 2-tuples

# Nested Tuples
- Tuples can contain other tuples
- Parentheses show structure

Example:
```ocaml
let x = ((1, 2), 3)
```

Type:
```ocaml
(int * int) * int
```

Example:
```ocaml
let y = (1, (2, 3))
```

Type:
```ocaml
int * (int * int)
```

- `(int * int * int)` is a 3-tuple
- `((int * int) * int)` and `(int * (int * int))` are different types

# Pattern Matching
- Primary way to work with tuples
- Matches values by structure

Example:
```ocaml
let (a, b) = (3, 4)
```

Nested example:
```ocaml
let ((x, y), z) = ((1, 2), 3)
```

# Pattern Matching in Functions
- Function arguments can be pattern matched

Example:
```ocaml
let add_pair (a, b) =
  a + b
```

Type:
```ocaml
int * int -> int
```

Nested example:
```ocaml
let sum_nested ((a, b), c) =
  a + b + c
```

Type:
```ocaml
(int * int) * int -> int
```

# Why Pattern Matching Matters
- Safer than `fst` and `snd`
- Clearer and more expressive
- Scales to complex data structures

# Polymorphism
A tick followed by a variable means any type

Example:
`val fst3 : 'a * 'b * 'c -> 'a = <fun>`
Takes three types and returns a type equivalent to the first one ('a)

We can manually annotate this in the function definition, like
```
let fst3 (triple : 'a * 'b * 'c) : 'a =
  let (x, y, z) = triple in x
```

You also don't need to use a, b, c, etc. you can put whatever variable name you want

Polymorphic Types Example 2
```
let swap (x, y) = (y, x)
# val swap : 'a * 'b -> 'b * 'a = <fun>
```

# Lists
## Cons Operator
::
Used to prepend an element to a list

```ocaml
head :: tl

# Example:
0 :: [];; # [0]
[0];; # Equivalent to above, just syntactic sugar
1 :: 2 :: 3 :: 4 :: 5 :: [];;
[1; 2; 3; 4; 5];; # Equivalent to above, just syntactic sugar
```

- Lists can contain any type (including tuples), but all elements within a single list must be the same type

## Append Operator
- Infix operator
- @ sign
- Linear time function, usually use cons
- Also available as List.append
`[1; 2] @ [3; 4];; # [1; 2; 3; 4]`

```ocaml
# Note: match is like switch in other languages
# First match case is always run
# Catch-all case _ is generally not advised because we might miss cases we want to handle and ocaml would warn us in this case

let hd (l : int list) : int =
  match l with
  | [] -> 0 # if empty list, returns 0
  | h::_ -> h # pattern matching conditional, return h
;;
```

Example:
```ocaml
let inc_first (l: int list) : int list =
  match l with
  | [] -> []
  | h::t -> (h + 1)::t
;;

let l = [1; 2; 3];;

inc_first l;;

l;; # in this case, l is unchanged because inc_first creates a copy of l. Everything is immutable
# OCaml is optimized to only recreate the head node and append it to the rest of the list instead of making a whole new list
```

# Recursive Functions on Lists
Example:
```ocaml
let rec inc_all (l: int list) : int list =
  match l with
  | [] -> []
  | h::t -> (h + 1)::(inc_all t)
;;

inc_all [1; 2; 3] # returns [2; 3; 4]
```

Example:
```ocaml
let rec sum (l: int list) : int =
  match l with
  | [] -> 0
  | h::t -> (sum t) + h # note: head is an element, t is the rest of the list
;;
```

Example:
```ocaml
let rec is_sorted (l : int list) : bool =
  match l with
  | [] -> true
  | [h1] -> true
  | h1::h2::t -> (h1 <= h2) && is_sorted (h2::t)
```

Example:
```ocaml
let rec append (l1 : 'a list) (l2 : 'a list) : 'a list =
  match l1 with
  | [] -> l2
  | h::t -> h::(append t l2) 
```

Example:
```ocaml
let rec rev (l: 'a list) : 'a list =
  match l with
  | [] -> []
  | h::t append rev t [h] # can't use (rev t)::h because type error
```

# Lecture 5 - 2/5/2026
Note: Ask about how let and match are related

# Functions
- Functions are just values, everywhere you can use a value you can use a function too
  - Like passing functions as arguments into functions
 
```ocaml
let apply_twice (f: 'a -> 'a) x =
  f (f x)
;;

apply_twice (fun x -> x / 2) 40;;
```

```ocaml
let compose (f: 'a -> 'b) (g: 'b -> 'c) x =
  g (f x)
;;

let plus_one_then_double = compose ((+) 1) ( ( * ) 2);; (*Partial application*)
```

Example with list mapping recursion:
```ocaml
let rec inc_all l =
  match l with
  | [] -> []
  | h::t -> (h + 1)::(inc_all t)

let rec square_all l =
  match l with
  | [] -> []
  | h::t -> (h * h)::(square_all t)

(*These all follow the same pattern, let's write a function to do it*)

let rec map (f: 'a -> 'b) (l: 'a list) : 'b list =
  match l with
  | [] -> []
  | h::t -> (f h)::(map f t)
;;
(* Now we can pass in any function to map and it'll apply it to the entire list *)

(* Rewrite functions using map function *)
let inc_all = map ((+) 1)
let length_all = map String.length
let make_excited_all = map (fun s -> s ^ "!")

(* This is already implemented in OCaml's standard library as List.map *)
```

Examples with other recursive list functions that return a single thing:
```ocaml
let rec sum l =
  match l with
  | [] -> 0
  | h::t -> h + (sum t)
;;

let rec product l =
  match l with
  | [] -> 1
  | h::t -> h * (product t)
;;

let rec concat (ll: 'a list list) : 'a list =
  match ll with
  | [] -> []
  | h::t -> h @ (concat t)
;;

(* These functions all follow the same pattern, we can generalize this with a higher-order function (function that takes functions as arguments) *)
let rec fold (f: 'a -> 'b -> 'b) (l: 'a list) (u : 'b) : 'b =
  match l with
  | [] -> u
  | h::t -> f h (fold f t u)
;;
(* Fold function reduces a::b::c::...::z::[] to f(a, f(b, f(c, f(..., f(z, u))))) where u is the identity for the function *)

(* Now, we can write *)
let sum l = fold (+) l 0
let product l = fold ( * ) l 1
let concat l = fold (@) l []
```

Examples that modify lists
```ocaml
let rec only_even (l: int list) : int list =
  match l with
  | [] -> []
  | h::t -> if h mod 2 = 0 then h::(only_even t) else only_even t
;;

let rec only_nonempty (l: 'a list list) : 'a list list =
  match l with
  | [] -> []
  | h::t -> if h <> [] then h::(only_nonempty t) else only_nonempty t
;;

(* Filter higher-order function *)
let rec filter (f: 'a -> bool) (l: 'a list) : 'a list
  match l with
  | [] -> []
  | h::t -> if f h then h::(filter f t) else (filter f t)
;;

let only_even = filter (fun h -> h mod 2 = 0)
let only_nonempty = filter (fun l -> match l with [] -> false | -> true)
```

# Lecture 6 - 2/12/2026
# Options
```ocaml
type task = {
  id : int;
  title : string;
}

type assignment = {
  task_info : task; (* assuming task is a type *)
  owner : string option;
}
```

- If a assignment does not yet have an owner, we want to support this behavior.
- In other languages, we represent this with Null or None; in OCaml we use `options`

Option Definition
```ocaml type 'a option = None | Some of 'a```

```ocaml
let get_announcement (a : assignment) : string =
  match a with
  | None -> "No one is working on " ^ a.task_info.title
  | Some name -> name ^ " is working on " ^ a.task_info.title
;;

let a1 = {task_info = {id = 1; title = "Demo"}; owner = None}
```

## Some Keyword
let x = Some 1

Put one into a box, turns x into option type
x + 1 is not allowed, since + expects an int not int options

```ocaml
let safe_divide (x : int) (y : int) : int option =
  if y = 0 then
    None
  else
    Some (x / y) (* Some wraps it into option type *)
;;
```

To unpack, match against it
```ocaml
let x = Some 1 (* int option *)
let x_i = match x with None -> 0 | Some i -> i in x_i + 1;;
```

# Try-With (Try-Except)
