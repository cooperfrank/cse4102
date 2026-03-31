Here is a consolidated reference sheet of all the syntax, static semantics, dynamic semantics, and theorems from your notes, corrected for minor typos and organized for quick problem-solving.

# Simply Typed Lambda Calculus (STLC) Reference

## 1. Syntax

**Types ($T$):**
$$T \Coloneqq \text{unit} \mid \text{bool} \mid T \to T \mid T \times T$$

**Expressions ($e$):**
$$e \Coloneqq x \mid () \mid \text{true} \mid \text{false} \mid \lambda x:T.\ e \mid e\ e \mid \text{if } e \text{ then } e \text{ else } e \mid (e, e) \mid \text{fst } e \mid \text{snd } e$$

---

## 2. Static Semantics (Typing Rules)
**Judgment:** $\Gamma \vdash e : T$ (Under context $\Gamma$, expression $e$ has type $T$)

### Base Values & Variables
$$\frac{}{\Gamma \vdash () : \text{unit}} \text{ (T-Unit)}$$

$$\frac{}{\Gamma \vdash \text{true} : \text{bool}} \text{ (T-True)} \quad \quad \frac{}{\Gamma \vdash \text{false} : \text{bool}} \text{ (T-False)}$$

$$\frac{\Gamma(x) = T}{\Gamma \vdash x : T} \text{ (T-Var)}$$

### Functions (Abstraction & Application)
$$\frac{\Gamma, x : \tau_1 \vdash e : \tau_2}{\Gamma \vdash \lambda x : \tau_1.\ e : \tau_1 \to \tau_2} \text{ (T-Lambda)}$$

$$\frac{\Gamma \vdash e_1 : \tau_1 \to \tau_2 \quad \Gamma \vdash e_2 : \tau_1}{\Gamma \vdash e_1\ e_2 : \tau_2} \text{ (T-App)}$$

### Conditionals
$$\frac{\Gamma \vdash e_1 : \text{bool} \quad \Gamma \vdash e_2 : T \quad \Gamma \vdash e_3 : T}{\Gamma \vdash \text{if } e_1 \text{ then } e_2 \text{ else } e_3 : T} \text{ (T-If)}$$

### Tuples (Pairs)
$$\frac{\Gamma \vdash e_1 : \tau_1 \quad \Gamma \vdash e_2 : \tau_2}{\Gamma \vdash (e_1, e_2) : \tau_1 \times \tau_2} \text{ (T-Pair)}$$

$$\frac{\Gamma \vdash e : \tau_1 \times \tau_2}{\Gamma \vdash \text{fst } e : \tau_1} \text{ (T-Fst)} \quad \quad \frac{\Gamma \vdash e : \tau_1 \times \tau_2}{\Gamma \vdash \text{snd } e : \tau_2} \text{ (T-Snd)}$$

---

## 3. Dynamic Semantics (Evaluation Rules)

### Value Axioms ($e \text{ val}$)
$$\frac{}{\lambda x:T.\ e \text{ val}} \text{ (V-Lambda)} \quad \quad \frac{}{() \text{ val}} \text{ (V-Unit)}$$

$$\frac{}{\text{true val}} \text{ (V-True)} \quad \quad \frac{}{\text{false val}} \text{ (V-False)}$$

$$\frac{v_1 \text{ val} \quad v_2 \text{ val}}{(v_1, v_2) \text{ val}} \text{ (V-Pair)}$$

### Stepping Rules ($e \mapsto e'$)

**Function Application:**
$$\frac{e_1 \mapsto e_1'}{e_1\ e_2 \mapsto e_1'\ e_2} \text{ (S-AppLeft)}$$

$$\frac{v_1 \text{ val} \quad e_2 \mapsto e_2'}{v_1\ e_2 \mapsto v_1\ e_2'} \text{ (S-AppRight)}$$ *(Note: Standard left-to-right evaluation implies $e_1$ must be a value before stepping $e_2$)*

$$\frac{v \text{ val}}{(\lambda x:T.\ e)\ v \mapsto [v/x] e} \text{ (S-App)}$$

**Conditionals:**
$$\frac{e_1 \mapsto e_1'}{\text{if } e_1 \text{ then } e_2 \text{ else } e_3 \mapsto \text{if } e_1' \text{ then } e_2 \text{ else } e_3} \text{ (S-SearchIf)}$$

$$\frac{}{\text{if true then } e_2 \text{ else } e_3 \mapsto e_2} \text{ (S-IfTrue)}$$

$$\frac{}{\text{if false then } e_2 \text{ else } e_3 \mapsto e_3} \text{ (S-IfFalse)}$$

**Tuples (Pairs):**
$$\frac{e_1 \mapsto e_1'}{(e_1, e_2) \mapsto (e_1', e_2)} \text{ (S-PairLeft)}$$

$$\frac{v_1 \text{ val} \quad e_2 \mapsto e_2'}{(v_1, e_2) \mapsto (v_1, e_2')} \text{ (S-PairRight)}$$

$$\frac{e \mapsto e'}{\text{fst } e \mapsto \text{fst } e'} \text{ (S-SearchFst)} \quad \quad \frac{e \mapsto e'}{\text{snd } e \mapsto \text{snd } e'} \text{ (S-SearchSnd)}$$

$$\frac{v_1 \text{ val} \quad v_2 \text{ val}}{\text{fst } (v_1, v_2) \mapsto v_1} \text{ (S-Fst)} \quad \quad \frac{v_1 \text{ val} \quad v_2 \text{ val}}{\text{snd } (v_1, v_2) \mapsto v_2} \text{ (S-Snd)}$$

---

## 4. Type Safety Theorems

* **Progress:** If $\bullet \vdash e : \tau$, then either $e \text{ val}$ or there exists some $e'$ such that $e \mapsto e'$. *(Well-typed programs don't get stuck).*
* **Preservation:** If $\bullet \vdash e : \tau$ and $e \mapsto e'$, then $\bullet \vdash e' : \tau$. *(Stepping preserves types).*
* **Type Safety / Soundness:** If $\bullet \vdash e : \tau$, then $e \mapsto^* v$ where $v \text{ val}$. *(Well-typed programs evaluate to a value of the correct type without infinite loops).*
