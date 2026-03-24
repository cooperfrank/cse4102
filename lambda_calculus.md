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

## Church Booleans
We need to encode three things for booleans (remember, we only have functions and applications):
1. if
2. true
3. false

### Defining These
if statements follow form `if e1 then e2 else e3`

In church encoding, we define an if statement as 
`if e1 then e2 else e3` \triangleq e1 e2 e3
true \triangleq  \lambda t . \lambda f . t (returns true, the first argument)
false \triangleq \lambda t . \lambda f . f (returns false, the second argument)

true M2 M3 |->* M2 
false M2 M3 |->* M3

iszero M1 
iszero \triangleeq \lambda n . n (\lambda z . false) true
>(where n is a church numberal)

### Stepping Through
(\lambda z . false ) true |-> false
(\lambda z . false) ((\lambda z . false) trye) |-> (\lambda z . false) false) |-> false
So, for any n > 0, this returns false

For iszero 0, we have
iszero 0 (\lambda n . n (\lambda z.false) true) (\lambda s . \lambdaz . z)
|-> ((\lambda s . \lambda z . z) (\lambda z . false)) true
|-> (\lambda z . z) true
|-> true

For iszero 1, we have 
*STEP THROUGH THIS LIEK FOR 0*
So, we can see that iszero is working as expected

ifzero M1 M2 M3 \triangleeq \lambda n . \lambda t . \lambda f . if (iszero n) t f

s 0 \triangleeq [\lambda n . \lambda s . \lambda z . s (n s z)] (\lambda s . \lambda z . z)
|-> \lambda s . \lambda z . s ((\la,bda s . \lambda z . z) s z === \lambda s . \lambda z . s z (our original definition of 1)

# Pairs

We know pairs are defined as $(a, b)$ and support `fst` and `snd` to extract the values. We can also nest pairs to build more complex structures, like $(a, (b, c))$.

## Formal Definitions

### The Pair Constructor
To create a pair containing elements $M_1$ and $M_2$, we construct a function that takes a selector function $s$ and applies it to our two values:

$$(M_1, M_2) \triangleq \lambda s . s \ M_1 \ M_2$$

### The Selectors (`fst` and `snd`)
To extract values, we define `fst` and `snd` to pass a specific selector function into the pair $p$.

**First Element (`fst`):**
$$\text{fst} \triangleq \lambda p . p (\lambda a . \lambda b . a)$$

**Second Element (`snd`):**
$$\text{snd} \triangleq \lambda p . p (\lambda a . \lambda b . b)$$


## A Note on Church Booleans
Notice the inner functions used to select the elements: $\lambda a . \lambda b . a$ and $\lambda a . \lambda b . b$. 

These happen to be the exact definitions for Church booleans. Because of this, they can be directly replaced with **TRUE** and **FALSE**:
* `TRUE` $\triangleq \lambda a . \lambda b . a$ (chooses the first argument)
* `FALSE` $\triangleq \lambda a . \lambda b . b$ (chooses the second argument)


## Fully Expanded Beta Reduction

**1. The Fully Expanded Starting Expression:**
We substitute the definitions for both `fst` and the pair immediately:
$$(\lambda p . p (\lambda a . \lambda b . a)) (\lambda s . s \ M_1 \ M_2)$$

**2. Apply the left function to the right function (Substitute $p$):**
The entire right-hand term $(\lambda s . s \ M_1 \ M_2)$ is passed in as the argument for $p$. We replace the $p$ in the body of the first function with our pair definition.
$$(\lambda s . s \ M_1 \ M_2) (\lambda a . \lambda b . a)$$

**3. Apply the pair function to the selector (Substitute $s$):**
Now, the selector function $(\lambda a . \lambda b . a)$ is passed in as the argument for $s$.
$$(\lambda a . \lambda b . a) \ M_1 \ M_2$$

**4. Evaluate the selector (Substitute $a$):**
We pass $M_1$ into the selector as the argument $a$. The selector now holds onto $M_1$ and ignores whatever comes next.
$$(\lambda b . M_1) \ M_2$$

**5. Finish the evaluation (Substitute $b$):**
We pass $M_2$ in as the argument $b$. Since $b$ is not used in the body of the function, $M_2$ is discarded.
$$M_1$$


You got it. If that is how your course or original notes are structuring it, it's best to keep those variables exactly as you have them! 

Here is the revised version with `s` and `z` placed right back into the `predpair` definition, keeping the rest of the flow and formatting identical.

***

# The Predecessor Function in Lambda Calculus

The goal of the predecessor function (`pred`) is to return the number strictly before $n$:
$$\text{pred} \ n \rightarrow n - 1$$

## 1. The Challenge with Church Numerals
Recall how we represent numbers using Church numerals—they are functions that apply a "step" ($s$) to a "base" ($z$) $n$ times:
* **0** $\triangleq \lambda s . \lambda z . z$
* **1** $\triangleq \lambda s . \lambda z . s \ z$

Because Church numerals are built entirely by moving *forward* (repeatedly applying functions), moving *backward* (un-applying a function to get $n-1$) is not naturally supported. We cannot simply "undo" an application.

## 2. The Solution: `predpair`
Instead of trying to move backward, we can solve this by building forward! We define a helper function called `predpair`. 

The goal of `predpair` is to take a number $n$ and return a pair containing both $n$ and $n-1$:
$$\text{predpair} \ n = (n, n-1)$$

At first glance, this seems to suffer from the same problem: we still need a way to compute $n-1$. However, we can generate this pair dynamically from the ground up using a "sliding window" approach.

### The Sliding Window Progression
We start at zero and repeatedly step forward. At each step, the *new* first element becomes the old first element plus one, and the *new* second element is just a copy of the old first element.
* $\text{predpair} \ 0 = (0, 0)$  *(Base case: no negative numbers in standard Church numerals)*
* $\text{predpair} \ 1 = (1, 0)$
* $\text{predpair} \ 2 = (2, 1)$
* $\text{predpair} \ 3 = (3, 2)$

Notice how the second element of the pair gracefully trails exactly one step behind, giving us $n-1$!

## 3. Formal Definitions

To formalize this, we pass a step function into our Church numeral $n$. This step function takes the previous pair $p$, increments the first element using `succ`, and shifts the old first element into the second slot. 

**The `predpair` Function:**
$$\text{predpair} \triangleq \lambda n . \lambda s . \lambda z . n \ (\lambda p . (\text{succ} \ (\text{fst} \ p), \text{fst} \ p)) \ (0, 0)$$

**The `pred` Function:**
Now that `predpair` does all the heavy lifting of carrying $n-1$ alongside $n$, finding the predecessor is trivially easy. We just apply `predpair` to $n$ and extract the second element using `snd`!

$$\text{pred} \triangleq \lambda n . \text{snd} \ (\text{predpair} \ n)$$


# Minus
Now that we've defined predecessor, we can define minus
Minus \triangleq λa.λb. b pred a 

(applies predecessor b times to a, because b is a church numeral encoding)

# Comparisons
We can also define less or equal from this
Once we define less or equal and predecessor, we can define pretty much any comparison operation
Now we have everything we need for logic with natural numbers
Here is a polished version of your notes. I kept the exact logical progression you laid out—moving from OCaml to the naive lambda definition, explaining the self-reference problem, and finally introducing the "magic" `fix` function to solve it. 

# The Factorial Function and Recursion

## 1. Recursion in OCaml
In a language like OCaml, defining the factorial function is straightforward because the language explicitly supports self-reference using `let rec`:

```ocaml
let rec fact n =
    if n = 0 then 1
    else n * (fact (n - 1))
;;
```

## 2. The Naive Lambda Calculus Translation
If we try to translate this directly into lambda calculus, it might look like this:

$$\text{fact} \triangleq \lambda n . \text{ifzero} \ n \ 1 \ (\text{times} \ n \ (\text{fact} \ (\text{pred} \ n)))$$

**The Problem:** This looks logically correct, but pure lambda calculus does not have a built-in way to define terms in terms of themselves (like `let rec`). We cannot reference `fact` inside the definition of `fact` before it actually exists!

* NOTE: THIS DEFINITION IS COMPLETELY INCORRECT, DO NOT USE ANYWHERE

## 3. Abstracting the Recursive Call
To get around the self-reference issue, we can rewrite the function so that it takes `fact` as an input parameter, rather than trying to call it globally. Let's call this new function $\text{fact}'$:

$$\text{fact}' \triangleq \lambda \text{fact} . \lambda n . \text{ifzero} \ n \ 1 \ (\text{times} \ n \ (\text{fact} \ (\text{pred} \ n)))$$

Here, we are passing the function in as a parameter instead of defining it recursively. Now, let's try to define our actual `fact` function by wrapping $\text{fact}'$ and passing `fact` into it:

$$\text{fact} \triangleq \text{fact}' \ \text{fact}$$

**The Problem Returns:** We run into the exact same issue! We still can't pass `fact` into the definition because `fact` hasn't been fully defined yet.

## 4. The Magic `fix` Function
For now, let's assume we have access to a "magic" function called `fix` that possesses the following property:

$$\text{fix} \ f = f \ (\text{fix} \ f)$$

* NOTE: fix is short for fixed-point combinator

If we had this magic `fix` function, it would solve our self-reference problem entirely. We could define `fact` by applying `fix` to our $\text{fact}'$ function:

1. **Apply `fix`:**
   $$\text{fact} \triangleq \text{fix} \ \text{fact}'$$

2. **Expand based on the magic property:**
   $$\text{fact} \triangleq \text{fact}' \ (\text{fix} \ \text{fact}')$$

3. **Substitute `fact` back in:**
   $$\text{fact} \triangleq \text{fact}' \ \text{fact}$$

By using `fix`, we trick the lambda calculus into feeding the function back into itself endlessly, successfully simulating recursion without ever needing a `let rec`!

# The Y-Combinator: Defining `fix`

In our previous notes, we assumed the existence of a "magic" `fix` function that allows for recursion by satisfying the property: $\text{fix} \ f = f \ (\text{fix} \ f)$. 

In pure lambda calculus, we can actually build this function! The most famous fixed-point combinator used to achieve this is the **Y-Combinator**, discovered by Haskell Curry.

## 1. The Definition
The Y-Combinator is defined as a function that takes another function $f$ and applies a self-replicating structure to it:

$$Y \triangleq \lambda f . (\lambda x . f \ (x \ x)) \ (\lambda x . f \ (x \ x))$$

## 2. Evaluation Proof (Beta Reduction)
Let's step through the evaluation of $Y \ f$ to prove that it successfully replicates the magic `fix` property of returning $f \ (Y \ f)$.

**1. Start with the expression $Y \ f$ and expand $Y$:**
$$( \lambda f . (\lambda x . f \ (x \ x)) \ (\lambda x . f \ (x \ x)) ) \ f$$

**2. Substitute the argument $f$ into the body (Beta Reduction on $\lambda f$):**
We pass $f$ in, replacing the bound variable $f$.
$$(\lambda x . f \ (x \ x)) \ (\lambda x . f \ (x \ x))$$

**3. Apply the left function to the right function (Beta Reduction on $\lambda x$):**
We take the entire right-hand term $(\lambda x . f \ (x \ x))$ and pass it into the left-hand function as the argument $x$. 
$$f \ ( (\lambda x . f \ (x \ x)) \ (\lambda x . f \ (x \ x)) )$$

**4. Recognize the recursive pattern:**
Look closely at the inner portion of the expression we just generated: 
$$(\lambda x . f \ (x \ x)) \ (\lambda x . f \ (x \ x))$$
This is the *exact same expression* we had in Step 2! And since we know from Step 2 that this expression is the evaluated form of $Y \ f$, we can substitute $Y \ f$ right back into our formula.

$$f \ (Y \ f)$$

---

## 3. Conclusion
Through basic beta reduction, we have proven that:
$$Y \ f \rightarrow f \ (Y \ f)$$

This means the Y-Combinator **is** our magic `fix` function. By defining $\text{fact} \triangleq Y \ \text{fact}'$, the Y-Combinator will continuously feed the factorial function back into itself, allowing for infinite recursion in a system that doesn't natively support it!

NOTE: The Y-Combinator ONLY works for call-by-name, not call-by-value

For call-by-value, there is the Z-Combinator
