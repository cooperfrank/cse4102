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

### The Lambda Rule (Functions)
This rule defines how we determine the type of a function (also called an abstraction). To find the type of $\lambda x : \tau.\ e$, we temporarily add the parameter $x$ and its declared type $\tau$ to our context (written as $\Gamma, x : \tau$). If, under this new extended context, the body of the function ($e$) evaluates to a type $\tau'$, then the entire function evaluates to the type $\tau \to \tau'$.

$$\frac{\Gamma, x : \tau \vdash e : \tau'}{\Gamma \vdash \lambda x : \tau.\ e : \tau \to \tau'} \text{ (T-Lambda)}$$

### The Application Rule (Function Calls)
This rule defines how we type-check function calls to ensure we aren't passing the wrong data type into a function. It requires two conditions (premises) to be met:
1.  The left expression ($e_1$) must evaluate to a function type: $\tau \to \tau'$.
2.  The right expression, or argument ($e_2$), must evaluate to a type that exactly matches the function's expected input: $\tau$.

If both of these conditions are true, then the entire application ($e_1\ e_2$) evaluates to the function's return type: $\tau'$.

$$\frac{\Gamma \vdash e_1 : \tau \to \tau' \quad \Gamma \vdash e_2 : \tau}{\Gamma \vdash e_1\ e_2 : \tau'} \text{ (T-App)}$$

---
---

# Extension: Booleans and Conditionals

To add boolean logic and conditional branching to our Simply Typed Lambda Calculus, we must expand our syntax and introduce new typing rules.

## 1. Extended Syntax

We extend both our type syntax ($T$) and our expression syntax ($e$) to account for boolean values (`true`, `false`) and the `if-then-else` construct.

### Extended Type Syntax
$$T \Coloneqq \text{unit} \mid \text{bool} \mid T \to T$$

### Extended Expression Syntax
$$e \Coloneqq x \mid \lambda x:T.\ e \mid e\ e \mid () \mid \text{true} \mid \text{false} \mid \text{if } e \text{ then } e \text{ else } e$$

---

## 2. New Typing Rules (Static Semantics)

We need axioms to define the types of our new constant values, and a rule to handle the conditional logic.

### Boolean Axioms
Like `()`, boolean values are constants. They don't require context lookups, so their premises are empty.

$$\frac{}{\Gamma \vdash \text{true} : \text{bool}} \text{ (T-True)}$$

$$\frac{}{\Gamma \vdash \text{false} : \text{bool}} \text{ (T-False)}$$

### The Conditional Rule (T-If)
To properly type an `if-then-else` expression, three premises must be satisfied:
1. The condition ($e_1$) must evaluate to a `bool`.
2. The "then" branch ($e_2$) must evaluate to some type $T$.
3. The "else" branch ($e_3$) must evaluate to the **exact same** type $T$.

If all three conditions are met, the entire expression evaluates to type $T$.

$$\frac{\Gamma \vdash e_1 : \text{bool} \quad \Gamma \vdash e_2 : T \quad \Gamma \vdash e_3 : T}{\Gamma \vdash \text{if } e_1 \text{ then } e_2 \text{ else } e_3 : T} \text{ (T-If)}$$

---

## 3. Example: Proving a Conditional Expression

Let's use our typing rules to construct a formal proof tree showing that the following expression is well-typed and evaluates to `unit` under an empty context ($\Gamma$):

$$\text{if } (\lambda x:\text{bool}.\ x)\ \text{true} \text{ then } () \text{ else } ()$$

To keep the derivation readable, we will split it into two parts. First, we'll derive the type of the condition (the application). Then, we'll plug that derivation into the final `T-If` rule.

### Part 1: Deriving the Condition (Let's call this sub-tree $D_1$)
We must prove that applying `true` to the identity function `(\x:bool. x)` results in a `bool`. We use `T-App`, combining `T-Lambda` (which internally uses `T-Var`) and `T-True`.

$$D_1 = \frac{\displaystyle \frac{\Gamma, x:\text{bool} \vdash x : \text{bool} \text{ (T-Var)}}{\Gamma \vdash \lambda x:\text{bool}.\ x : \text{bool} \to \text{bool}} \text{ (T-Lambda)} \quad \frac{}{\Gamma \vdash \text{true} : \text{bool}} \text{ (T-True)}}{\Gamma \vdash (\lambda x:\text{bool}.\ x)\ \text{true} : \text{bool}} \text{ (T-App)}$$

### Part 2: The Final Derivation
Now, we use $D_1$ as the first premise for our `T-If` rule. The second and third premises simply use `T-Unit`.

$$\frac{D_1 \quad \frac{}{\Gamma \vdash () : \text{unit}} \text{ (T-Unit)} \quad \frac{}{\Gamma \vdash () : \text{unit}} \text{ (T-Unit)}}{\Gamma \vdash \text{if } (\lambda x:\text{bool}.\ x)\ \text{true} \text{ then } () \text{ else } () : \text{unit}} \text{ (T-If)}$$

Because we successfully built this tree using only our established rules, we have formally proven that the expression is well-typed and has the type `unit`.

## Extending Dynamic Semantics with Booleans

Previously, we established the basic judgments for evaluation: $e \text{ val}$ (is a value) and $e \mapsto e'$ (takes a step). 

To support booleans, we first formally recognize `true` and `false` as fully evaluated values:
* **V-True:** `true val`
* **V-False:** `false val`

**These are axioms**

Next, we define the stepping rules for `if-then-else` expressions:

### The Search Rule
If the condition ($e_1$) is not yet a value, we must step the condition first.
$$\frac{e_1 \mapsto e_1'}{\text{if } e_1 \text{ then } e_2 \text{ else } e_3 \mapsto \text{if } e_1' \text{ then } e_2 \text{ else } e_3} \text{ (S-SearchIf)}$$

### The Computation Axioms
Once the condition evaluates to a boolean value, we step directly into the appropriate branch.
$$\frac{}{\text{if true then } e_2 \text{ else } e_3 \mapsto e_2} \text{ (S-IfTrue)}$$

$$\frac{}{\text{if false then } e_2 \text{ else } e_3 \mapsto e_3} \text{ (S-IfFalse)}$$

### Analysis of Conditional Judgments
These three rules formally define standard "call-by-value" execution for conditionals. `S-SearchIf` acts as a congruence rule, forcing the program to evaluate the condition ($e_1$) down to a value *before* looking at the branches. Once $e_1$ becomes a boolean (`true` or `false`), the computation steps directly to the corresponding branch using the computation axioms (`S-IfTrue` or `S-IfFalse`). Because the unselected branch is discarded immediately without being evaluated, these rules successfully model the "short-circuiting" behavior expected of `if` statements.

### Example
Here is an example of an `if` expression stepping down to a `unit` value.

$$\text{if } (\lambda x:\text{bool}.\ x)\ \text{true} \text{ then } (\lambda x:\text{unit}.\ x)\ () \text{ else } ()$$
$$\mapsto \text{if true then } (\lambda x:\text{unit}.\ x)\ () \text{ else } () \quad \text{(S-SearchIf)}$$
$$\mapsto (\lambda x:\text{unit}.\ x)\ () \quad \text{(S-IfTrue)}$$
$$\mapsto () \quad \text{(S-App)}$$

---

## Extending the Language Again with Tuples (Pairs)

We can further extend our language to support 2-tuples (pairs) by adding them to our syntax and defining their static semantics.

### Extended Syntax
We add the pair type $\tau_1 \times \tau_2$ to our types. For expressions, we add pair creation $(e_1, e_2)$ along with projections to access the elements (`fst` for the first element, `snd` for the second).

**Types:**
$$T \Coloneqq \text{unit} \mid T \to T \mid \text{bool} \mid T \times T$$

**Expressions:**
$$e \Coloneqq x \mid () \mid \lambda x:T.\ e \mid e\ e \mid \text{true} \mid \text{false} \mid \text{if } e \text{ then } e \text{ else } e \mid (e, e) \mid \text{fst } e \mid \text{snd } e$$

### Static Semantics (Typing Rules)

**The Pair Rule:**
To type a pair, both elements must be individually well-typed. The resulting type is the cross product of the two individual types.
$$\frac{\Gamma \vdash e_1 : \tau_1 \quad \Gamma \vdash e_2 : \tau_2}{\Gamma \vdash (e_1, e_2) : \tau_1 \times \tau_2} \text{ (T-Pair)}$$

**The Projection Rules:**
To extract an element from a pair, the underlying expression must first be typed as a pair ($\tau_1 \times \tau_2$). `fst` extracts the first type ($\tau_1$), and `snd` extracts the second type ($\tau_2$).

$$\frac{\Gamma \vdash e : \tau_1 \times \tau_2}{\Gamma \vdash \text{fst } e : \tau_1} \text{ (T-Fst)}$$

$$\frac{\Gamma \vdash e : \tau_1 \times \tau_2}{\Gamma \vdash \text{snd } e : \tau_2} \text{ (T-Snd)}$$


### New Notes
V-Pair:
v_1 val v_2 val
--- (V-Pair)
(v_1, v_2) val

S-PairLeft:
e_1 |-> e_1'
--- S-PairLeft
(e1, e2) |-> (e1', e2)

S-PairRight
v1 val    e2 |-> e2'
--- (S-PairRight)
(v1, e2) |-> (v1, e2')

Note that this means we step the left until its a value first, then steo the right

e |-> e'
--- (S-SearchFst)
fst e |-> fst e'

v1 val v2 val
--- (S-Fst)
fst (v1, v2) |-> v1

e |-> e'
--- (S-SearchSnd)
snd e |-> snd e'

v1 val v2 val
--- (S-Fst)
snd (v1, v2) |-> v2

*AI explain the significance of each*

### Example
fst [(λx:bool.(if x then false else true, x)) true]
|-> fst (if true then false else true, true) # now we will apply S-PairLeft
|-> fst (false, true) 
|-> false



## Type Safety
Progress: If \bullet \vdash e : \tau then e val or there exists e' such that e |-> e'

Preservation: If \bullet \vdash e : \tau and e |-> e' then \bullet \vdash e' : \tau

Theorem: If \bullet \vdash e : \tau then e |->* v where v val (stops infinite loops)


