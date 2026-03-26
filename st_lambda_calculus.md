# Simply Typed Lambda Calculus

## Expressions
For expressions, we use the meta-variable e
e ::= x | λx. e | e e | ()

This is similar to M in untyped Lambda Calculus, but including unit ()

## Types
For types, we use the meta-variable tau 𝜏 (but we will use T)

T ::= unit | T -> T

With this definition, we can write types like
unit -> unit
(unit -> unit) -> unit (takes function as argument)
unit -> (unit -> unit)

We are going to type describe our parameters for our expressions now, just like ocaml how you can do `let f (x: unit) = x` in OCaml

e ::= x | λx:T. e | e e | ()
