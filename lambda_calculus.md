# Lambda Calculus: Values and Evaluation Axioms

## 1. Definitions

### Meta-Variable $M$

We define $M$ as a general expression within our language.

### The `val` Type

A **val** (value) represents an expression that has finished calculating. It is "fully reduced."

* **Includes:** Variables, constants (like `5`), and **lambda functions** (abstractions).
* **Excludes:** Applications (e.g., `f x`).

**V-Lambda Axiom:** Any lambda abstraction is a value.
$$\frac{}{\lambda x. M \text{ is a val}}$$

---

## 2. Evaluation Axioms (Small-Step)

To reduce an expression, we use three primary rules. These determine how we navigate an application $(M \ N)$ until it is ready to be executed.

### S-AppL (Step Left)

If the left side is not yet a function (lambda), we must reduce it first.


$$\frac{M \mapsto M'}{M \ N \mapsto M' \ N}$$

### S-AppR (Step Right)

If the left side is already a lambda, but the right side is not yet a value, we reduce the right side.


$$\frac{N \mapsto N'}{(\lambda x. M) \ N \mapsto (\lambda x. M) \ N'}$$

### S-App (The Computation Step)

Once the left side is a lambda and the right side is a value, we perform the substitution.


$$\frac{N \text{ is a val}}{(\lambda x. M) \ N \mapsto [N/x] M}$$

---

## 3. OCaml Evaluation Trace (CBV)

**Initial Expression:** `((fun x -> fun y -> x y) string_of_int) 5`

| Step | Expression | Action |
| --- | --- | --- |
| **0** | `((fun x -> fun y -> x y) string_of_int) 5` | Left side of outer app is an app; use **S-AppL**. |
| **1** | `(fun y -> string_of_int y) 5` | **S-App:** Replace `x` with `string_of_int`. |
| **2** | `string_of_int 5` | **S-App:** Replace `y` with `5`. |
| **3** | `"5"` | **Final Value.** |

## 4. Evaluation Strategies: CBV vs. CBN

The rules above describe **Call-by-Value (CBV)**, which is used by OCaml. The alternative is **Call-by-Name (CBN)**.

| Strategy | Rule for $N$ (the argument) | Effect on Axioms |
| --- | --- | --- |
| **Call-by-Value** | Must be a `val` before applying. | Requires **S-AppR** to reduce arguments. |
| **Call-by-Name** | Can be any expression $M$. | **S-AppR** is removed; substitution happens immediately. |

### Example: Evaluating `(λx. x) ((λy. y) (λz. z))`

#### **Call-by-Value (CBV)**

In CBV, we cannot apply the outer function until the argument `((λy. y) (λz. z))` is reduced to a value.

1. `(λx. x) ((λy. y) (λz. z))`
2. `(λx. x) (λz. z)`  *(Using **S-AppR**: Reduced the argument first)*
3. `λz. z` *(Using **S-App**: Substituted `(λz. z)` for `x`)*

#### **Call-by-Name (CBN)**

In CBN, we ignore the complexity of the argument and substitute it directly into the body.

1. `(λx. x) ((λy. y) (λz. z))`
2. `(λy. y) (λz. z)` *(Using **S-App**: Substituted the whole expression into `x`)*
3. `λz. z` *(Using **S-App**: Reduced the remaining application)*

> **Church-Rosser Theorem:** Despite the different paths taken, both strategies eventually evaluate to the same result (`λz. z`), provided the evaluation terminates
> 
---
