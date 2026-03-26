# Simply Typed Lambda Calculus (STLC)

## 1. Syntax

### Expression Syntax (Untyped)
First, we define the core expressions using the meta-variable $e$. This is similar to standard untyped Lambda Calculus, but we include the **unit** value `()`.

$$e \Coloneqq x \mid \lambda x.\ e \mid e\ e \mid ()$$

### Type Syntax
For types, we typically use the meta-variable $\tau$ (tau), but we will use $T$ here. 

$$T \Coloneqq \text{unit} \mid T \to T$$

With this recursive definition, we can construct types such as:
* $\text{unit} \to \text{unit}$
* $(\text{unit} \to \text{unit}) \to \text{unit}$ *(Takes a function as an argument)*
* $\text{unit} \to (\text{unit} \to \text{unit})$ *(Returns a function)*

### Typed Expression Syntax
We can now apply types to describe the parameters in our expressions. This is similar to how you define typed parameters in languages like OCaml (e.g., `let f (x: unit) = x`).

$$e \Coloneqq x \mid \lambda x:T.\ e \mid e\ e \mid ()$$

---

## 2. Dynamic Semantics (Judgments)

We evaluate programs using two types of judgments: 
1.  $e \text{ val}$ (Expression $e$ is a value)
2.  $e \mapsto e'$ (Expression $e$ takes a step to $e'$)

### Value Rules
Functions and units are considered fully evaluated values.

$$\frac{}{\lambda x:T.\ e \text{ val}} \text{ (V-Lambda)}$$

$$\frac{}{() \text{ val}} \text{ (V-Unit)}$$

### Evaluation (Stepping) Rules
These rules define how applications (function calls) evaluate. We use $v$ to denote an expression that has already been evaluated to a value.

**Evaluate the left side (the function):**
$$\frac{e_1 \mapsto e_1'}{e_1\ e_2 \mapsto e_1'\ e_2} \text{ (S-AppLeft)}$$

**Evaluate the right side (the argument):**
$$\frac{e_2 \mapsto e_2'}{(\lambda x:T.\ e)\ e_2 \mapsto (\lambda x:T.\ e)\ e_2'} \text{ (S-AppRight)}$$

**Apply the function (Beta Reduction):**
Once the argument is a value ($v \text{ val}$), we substitute $v$ for $x$ in the function body $e$.
$$\frac{v \text{ val}}{(\lambda x:T.\ e)\ v \mapsto [v/x] e} \text{ (S-App)}$$

---

## 3. Examples

**Example 1: Well-Typed Application**
Applying a unit to a function that expects a unit.
$$(\lambda x:\text{unit}.\ x)\ ()$$
$$\mapsto [()/x]x$$
$$\mapsto ()$$

**Example 2: Ill-Typed Application**
Applying a function to a function that expects a unit.
$$(\lambda x:\text{unit}.\ x)\ (\lambda x:\text{unit}.\ x)$$
$$\mapsto [(\lambda x:\text{unit}.\ x)/x]x$$
$$\mapsto \lambda x:\text{unit}.\ x$$

### Note on Example 2 and Type Erasure
At first glance, Example 2 shouldn't make sense—the whole point of adding types is to prevent us from passing a function ($\text{unit} \to \text{unit}$) into a parameter expecting a `unit`. 

However, notice that **the dynamic semantics (stepping rules) ignore the types entirely.** This mirrors how most real-world compiled programming languages work via a process called **Type Erasure**. The types are checked during the compilation phase (Static Semantics), but are thrown out before execution because machine assembly doesn't have a concept of high-level types. 

Therefore, just because a program is *ill-typed* doesn't necessarily mean it will hang or throw a runtime error. In Example 2, the dynamic evaluation still executes perfectly, substituting the function in for $x$. The type system's job is to catch these logical mismatches *before* this runtime execution ever happens.


# Static Semantics

Static semantics define how we assign types to expressions. If an expression successfully evaluates to a valid type under these rules, we say the program is "well-typed." We write $e : T$ to state that an expression $e$ has type $T$ (tau).

## 1. The Typing Context ($\Gamma$)

Because variables depend on their surrounding scope, we cannot type-check them in isolation. We track variable types using a **typing context** (or environment), denoted by the uppercase Greek letter Gamma ($\Gamma$). 

### Structure of $\Gamma$
A context is simply a series of variable bindings (type definitions) separated by commas. 
**Example:** $\Gamma = f : \text{unit} \to \text{unit}, x : \text{unit}, y : \text{unit}$

### Context Extension
When we enter a new scope (like the body of a function), we can extend an existing context by appending a new variable binding to it. 
**Example:** $\Gamma, z : \text{unit}$ *(This means "the previous context $\Gamma$, plus the new fact that $z$ is a unit")*

If we extend a context and define a variable type in the extension that's already defined, we just alpha-convert it to a new variable name

### Context Lookup ($\Gamma$ as a Function)
You can think of $\Gamma$ as a dictionary or a lookup function that maps a variable name to its type. 
* **Success:** $\Gamma(x) = \text{unit}$ (We found $x$ in the context, and its type is `unit`).
* **Failure:** $\Gamma(h)$ where $h$ is not in the context results in a **type error** (undeclared variable).

*Note: In formal semantics, you will often see this lookup written as $x : T \in \Gamma$ (meaning the binding $x:T$ exists in the set $\Gamma$). Both notations mean the exact same thing.*

---

## 2. Typing Judgments

We bring the expression, the type, and the context together using a **typing judgment**:

$$\Gamma \vdash e : T$$

**How to read this:** > "Under context $\Gamma$ (within the scope of $\Gamma$), the expression $e$ has type $T$."

---

## 3. Core Typing Rules

Using the judgment notation, we define the inference rules that tell us how to type our base expressions.

### The Unit Rule
This rule states that the unit value `()` always evaluates to the type `unit`. Because a unit is a constant and doesn't depend on variables, we don't need to look anything up in $\Gamma$. This is why the top of the fraction is empty.

$$\frac{}{\Gamma \vdash () : \text{unit}} \text{ (T-Unit)}$$

### The Variable Rule
This rule formalizes how we give a type to a variable $x$. It states that if looking up $x$ in our context $\Gamma$ returns the type $T$, then we can formally conclude that $x$ has type $T$ under that context.

$$\frac{\Gamma(x) = T}{\Gamma \vdash x : T} \text{ (T-Var)}$$

