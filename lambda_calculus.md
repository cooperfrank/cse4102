# Lambda Calculus: Values and Evaluation Axioms

## 1. Definitions

### Meta-Variable $M$

We define $M$ as a general expression within our language.

### The `val` Type

A **val** (value) represents an expression that has finished calculating. It is "fully reduced."

* **Includes:** Variables, constants (like `5` or `"hello"`), and **lambda functions** (abstractions).
* **Excludes:** Applications (e.g., `f x` is not a value because it can still "do" something).

> **Examples of values:**
> * `5`
> * `fun x -> fun y -> x + y`
> * `(λy. λx. (λz. x) y)`
> 
> 

---

## 2. Formal Axioms

### V-Lambda Axiom (Values)

This axiom states that any lambda abstraction is, by definition, a value. Even if the *body* of the function could be simplified later, the function itself is finished as a piece of data.

$$\frac{}{\lambda x. M \text{ is a val}}$$

* **Example:** `(λy. λx. (λz. x) y)` is a value because it follows format `λx.M`.

### S-App Axiom (Small-step Application)

Also known as **$\beta$-reduction**, this rule defines how a function actually executes. In a "Call-by-Value" system, we only trigger this when the argument $N$ is already a value.

$$\frac{N \text{ is a val}}{(\lambda x. M) N \mapsto [N/x] M}$$

**Translation:** If you apply a function $(\lambda x. M)$ to a value $N$, you result in the body $M$, where every instance of the bound variable $x$ is replaced by $N$.

**Note:** This is Call-By-Value Lambda Calculus, since we require N to be a value before application. This is as opposed to Call-by-Name Lambda Calculus, which does not require N to be a value. 
* This is brought up later, but going with Call-by-Name Lambda Calculus would affect S-App by removing the need for N to be a val, would not affect S-AppL, and would completely remove the need for S-AppR

---

## 3. Evaluation Trace

Below is a step-by-step reduction of an OCaml expression using these axioms.

**Initial Expression:**
`((fun x -> fun y -> x y) string_of_int) 5`

| Step | Expression | Action |
| --- | --- | --- |
| **0** | `((fun x -> fun y -> x y) string_of_int) 5` | Identify the innermost application. |
| **1** | `(fun y -> string_of_int y) 5` | **S-App:** Replace `x` with `string_of_int`. |
| **2** | `string_of_int 5` | **S-App:** Replace `y` with `5`. |
| **3** | `"5"` | **Final Value:** Calculation complete. |

--


The S-App rule breaks when the left side isnt a lambda, or if the right side isnt a value (not finished calculating)
To get around this, we need more rules to be able to step the left or right side separately so we can eventually result the left side to be a lambda (this may not always be true, but this gets into types and we're not there yet so we just consider the left side to always evaluate to a lambda) and the right to be a value

S-AppL Axiom:
$$\frac{M |-> M'}{M N |-> M' N}$$

S-AppR Axiom:
$$\frac{N |-> N'}{(λx.M)N |-> (λx.M)N'}$$

The way that we've defined S-AppL and S-AppR means that we must first step the left side until M is a lambda, then we can step the right side
Of course, this could be defined in a different way that makes you evaluate the right then the left, or either non-deterministically, but this is just how we define it
