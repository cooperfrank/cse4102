# 3/10/2026

Let's define a meta variable M of type val
A val is an expression that is finished calculating (a variable or lambda function, NOT application!)

ex. 5
ex. fun x -> fun y -> x + y

We have some axioms for the val

(V-Lambda axiom) states that `λx.M is a val`

ex. `(λy.λx.(λz.x)y) val` is true because it follows λx.M format (it has λy.M)

S-App Axiom:

N val /over/ (λx.M) N |-> [N/x] M
$$\frac{N val}{(λx.M) N |-> [N/x] M}$$

ex. step through example (ocaml syntax)
Given:
Step 0: `((fun x -> fun y -> x y) string_of_int) 5`
Step 1 |-> `((fun y -> string_of_int y)) 5` (replace all instances of x with `string_of_int`
Step 2 |-> `string_of_int 5`
Step 3 |-> `"5"`

The S-App Axiom is basically saying in any place we see x in the body of the function, replace it with the thing we are passing for x, which is N

