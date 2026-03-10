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

* **Example:** `(λy. λx. (λz. x) y)` is a value because it starts with a `λ`.

### S-App Axiom (Small-step Application)

Also known as **$\beta$-reduction**, this rule defines how a function actually executes. In a "Call-by-Value" system, we only trigger this when the argument $N$ is already a value.

$$\frac{N \text{ is a val}}{(\lambda x. M) N \mapsto [N/x] M}$$

**Translation:** If you apply a function $(\lambda x. M)$ to a value $N$, you result in the body $M$, where every instance of the bound variable $x$ is replaced by $N$.

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
