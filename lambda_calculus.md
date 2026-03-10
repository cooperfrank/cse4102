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
This is a classic "Performance vs. Evaluation" trade-off. In the world of programming language theory, neither strategy is strictly "better"—they just have different favorite hobbies.

Here is the completed and formatted section for your notes, finishing your thought on where Call-by-Name (CBN) shines.

---

## 5. Efficiency Trade-offs: CBV vs. CBN

The choice of evaluation strategy significantly impacts performance and termination behavior.

### Case A: High-Frequency Usage (CBV Wins)

**Expression:** $(\lambda x. x \ x \ x \ x) \ N$

* **Call-By-Value (CBV):** We evaluate $N$ down to a `val` exactly **once**. We then substitute that finished value into the body four times.
* **Call-By-Name (CBN):** We substitute the raw expression $N$ into the body four times. We are then forced to evaluate that same expression **four separate times** from scratch.

> **Winner:** **CBV** is more efficient here because it avoids redundant computation.

### Case B: Zero Usage / "Dead Code" (CBN Wins)

**Expression:** $(\lambda x. (\lambda y. y)) \ N$

* **Call-By-Value (CBV):** We must evaluate $N$ to a `val` before we can apply the function. If $N$ is a massive calculation (or an infinite loop!), we waste time or crash, even though the function doesn't even use $x$.
* **Call-By-Name (CBN):** we substitute $N$ into the body $(\lambda y. y)$. Since $x$ does not appear in the body, $N$ is **discarded immediately**. We never spend a single CPU cycle evaluating it.

> **Winner:** **CBN** is more efficient here because it lazily avoids unnecessary work. It can even handle "infinite" arguments that would make CBV hang forever.

---

## 6. Summary Comparison Table

| Feature | Call-By-Value (CBV) | Call-By-Name (CBN) |
| --- | --- | --- |
| **Redundant Arguments** | Evaluates once (Efficient). | Evaluates every time used (Wasteful). |
| **Unused Arguments** | Evaluates anyway (Wasteful). | Never evaluates (Efficient). |
| **Termination** | May loop if argument loops. | More likely to terminate (ignores looping args). |
| **Language Examples** | OCaml, Java, C++, Python. | Haskell (technically "Call-by-Need"). |

---

### The "Call-by-Need" Hybrid

In practice, languages like **Haskell** use a refined version of CBN called **Call-by-Need** (Lazy Evaluation). It gets the best of both worlds: it doesn't evaluate the argument unless it's used (like CBN), but it "memoizes" the result the first time it's calculated so it doesn't have to do it again (like CBV).
