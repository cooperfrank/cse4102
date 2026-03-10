# 3/10/2026

Let's define a meta variable M of type val
A val is an expression that is finished calculating (a variable or lambda function, NOT application!)

ex. 5
ex. fun x -> fun y -> x + y

We have some axioms for the val

(V-Lambda axiom) states that `λx.M is a val`

ex. `(λy.λx.(λz.x)y) val` is true because it follows λx.M format (it has λy.M)

S-App Axiom:

N val / (λx.M) N |-> [N/x] M
