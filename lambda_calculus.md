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

---

# Church Numerals

In lambda calculus, **Church numerals** are a way to represent non-negative integers using only functions. Since the system has no "built-in" numbers, we define them by how many times a function is applied to an argument.

---

## The Encodings ($s, z$ notation)

The standard way to write these is using $s$ (for **Successor**) and $z$ (for **Zero**). The number $n$ is defined as the $n$-fold composition of the function $s$ applied to the term $z$.

| Number | Lambda Expression | Intuition |
| --- | --- | --- |
| **0** | $\lambda s. \lambda z. z$ | Return the "zero" value as-is. |
| **1** | $\lambda s. \lambda z. s z$ | Apply the successor function once. |
| **2** | $\lambda s. \lambda z. s (s z)$ | Apply the successor function twice. |
| **3** | $\lambda s. \lambda z. s (s (s z))$ | Apply the successor function three times. |
| **$n$** | $\lambda s. \lambda z. s^n z$ | Apply the successor function $n$ times. |

---

## What do $s$ and $z$ mean?

These variable names are mnemonics for the **Peano axioms**, which define natural numbers starting from zero and moving forward one "step" at a time.

* **$s$ (Successor):** Represents the "increment" operation. It is a function that takes one argument and returns "one more" of whatever that argument was.
* **$z$ (Zero):** Represents the "base case" or the starting point. It is the value you have before any operations are applied.

### The "Loop" Intuition

You can think of a Church numeral as a **higher-order loop**. If you were to translate this to a standard programming language (like Python or JS), the numeral **3** would essentially mean: *"Take a function and a starting value, then run that function three times on that value."*

> **Quick Tip:** Because $\lambda s. \lambda z. z$ ignores $s$ and just returns $z$, it is functionally identical to the Church encoding for **False**.

---

### The Successor Function ($SUCC$)

The expression $S = \lambda n. \lambda s. \lambda z. s (n s z)$ is the formal definition of the **Successor** function. Its job is to take a Church numeral $n$ and return $n + 1$.

#### How it Works (Step-by-Step)

1.  **Input ($n$):** It accepts an existing Church numeral (e.g., **2**).
    
2.  **The "Unwrapping" ($n s z$):** The part inside the parentheses, $(n s z)$, tells the numeral $n$ to apply the function $s$ to the base value $z$ exactly $n$ times.
    
3.  **The "Extra Step" ($s (...)$):** The $s$ outside the parentheses applies the successor function **one more time** to the result of the previous step.
    
4.  **The Result:** You now have $n + 1$ applications of $s$.

OCaml Analogy:
```ocaml
let rec apply_n_times n s z =
  if n = 0 then z
  else s (apply n times (n - 1) s z)
```
Here is a refined, professional version of your notes. I have improved the formatting for readability, corrected the lambda calculus syntax (specifically for Multiplication), and ensured the OCaml analogies remain clear.

## Arithmetic in Church Encoding

In Church Calculus, arithmetic is performed by treating numbers as **higher-order functions**. Since a numeral $n$ represents "apply a function $n$ times," we can define operations by controlling what function is being applied and what the starting value is.

### 1. Addition ($PLUS$)

**Intuition:** To compute $x + y$, we start with the value $y$ and apply the **Successor function** ($S$) to it $x$ times.

#### OCaml Analogy

```ocaml
(* start with y, then apply the successor function x times *)
let plus x y = apply_n_times x succ y

```

#### Lambda Calculus Definition

$$\text{PLUS} \equiv \lambda x. \lambda y. x \, S \, y$$

* **$x$**: The first numeral (acting as a loop).
* **$S$**: The Successor function ($\lambda n. \lambda s. \lambda z. s (n s z)$) passed as the function to be repeated.
* **$y$**: The second numeral, passed as the starting "base" value.

---

### 2. Multiplication ($MULT$)

**Intuition:** Multiplication is repeated addition. To compute $x \times y$, we start with **Zero** and apply the "Add $y$" function $x$ times.

> $x \times y = \underbrace{y + y + \dots + y}_{x \text{ times}} + 0$

#### OCaml Analogy

```ocaml
(* start with zero, then apply the (plus y) function x times *)
let mult x y = apply_n_times x (plus y) zero

```

#### Lambda Calculus Definition

$$\text{MULT} \equiv \lambda x. \lambda y. x (\text{PLUS} \, y) 0$$

* **$(\text{PLUS} \, y)$**: This is **partial application**. It creates a function that "adds $y$" to whatever it receives.
* **$x$**: Applies that "add $y$" function $x$ times.
* **$0$**: The starting point for the repeated addition.

---

#### Summary Table of Operations

| Operation | Logic | Lambda Expression |
| --- | --- | --- |
| **Successor** | Add 1 to $n$ | $\lambda n. \lambda s. \lambda z. s (n s z)$ |
| **Addition** | Apply $S$ to $y$, $x$ times | $\lambda x. \lambda y. x S y$ |
| **Multiplication** | Apply $(+y)$ to $0$, $x$ times | $\lambda x. \lambda y. x (\text{PLUS } y) 0$ |

---

**Would you like to see how we define Exponentiation?** It follows the same pattern: $x^y$ is just applying the "Multiply by $x$" function $y$ times to the number **1**.
