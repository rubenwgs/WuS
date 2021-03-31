# Rigorous Software Engineering - Week 5 (Lectures)
- Author: Ruben Schenk
- Date: 31.03.2021
- Contact: ruben.schenk@inf.ethz.ch

# 7. Analysis Math
## 7.1 Structures
`Strcutres` are important as they define the concrete and abstract domains.

### Partially Ordered Sets (posets)
A `partial order` is a binary relation $\sqsubseteq \subseteq L \times L$ on a set $L$ with the following properties:
- Reflexive: $\forall p \in L : p \sqsubseteq p$
- Transitive: $\forall p,q,r \in L : (p \sqsubseteq q \land q \sqsubseteq r) \Rightarrow p \sqsubseteq r$
- Anti-symmetric: $\forall p,q \in L : (p \sqsubseteq q \land q \sqsubseteq p) \Rightarrow p = q$

A `poset` $(L, \, \sqsubseteq)$ is a set $L$, equipped with a partial ordering $\sqsubseteq$.

#### Least and Greatest Elements

Given a poset $(L, \, \sqsubseteq)$, an element $\bot \in L$ is called the `least element` if it's smaller than all other elements of the poset: $\forall p \in L : \bot \sqsubseteq p$. The `greatest element` is an element $\top$ such that $\forall p \in L : p \sqsubseteq \top$.

#### Bounds
Given a post $(L, \, \sqsubseteq)$ and $Y \subseteq L$, then:
- $u \in L$ is an `upper bound` of $Y$ if $\forall p \in Y : p \sqsubseteq u$
- $l \in L$ is a `lower bound` of $Y$ if $\forall p \in Y : p \sqsupseteq l$

Note that the bounds for $Y$ may not exist. 

- $\bigsqcup Y \in L$ is a `least upper bound` of $Y$ if $\bigsqcup Y$ is an upper bound of $Y$ and $\bigsqcup Y \sqsubseteq u$ whenever $u$ is another upper bound of $Y$
- $\sqcap Y \in L$ is a `greatest lower bound` of $Y$ if $\sqcap Y$ is a lower bound of $Y$ and $\sqcap Y \sqsupseteq l$ whenerver $l$ is another lower bound of $Y$

### Complete Lattices
A `complete lattice` $(L, \, \sqsubseteq, \, \sqcup)$ is a poset where $\sqcup Y$ and $\sqcap Y$ exist for any $Y \subseteq L$.

## 7.2 Functions
### Monotone
We say that a function $f : A \to B$ between two posets $(A, \, \sqsubseteq)$ and $(B, \, \leq)$ is `increasing (monotone)` if: $\forall a,b \in A : a \sqsubseteq \Rightarrow f(a) \leq f(b)$.

### Fixed Points
For a poset $(L, \, \sqsubseteq)$ and a function $f : L \to L$, and element $x \in L :$
- $x$ is a `fixed point` iff. $f(x) = x$
- $x$ is a `post-fixedpoint` iff. $f(x) \sqsubseteq x$

Furthermore, $Fix(f)$ denotes the set of all fixed points and $Red(f)$ denotes the set of all post-fixedpoints.

We furthermore say that $lfp^{\sqsubseteq} f \in L$ is a `least fixed point` iff:
- $lfp^{\sqsubseteq} f$ is a fixed point
- It is the least fixed point: $\forall a \in L : a = f(a) \Rightarrow lfp^{\sqsubseteq} f \sqsubseteq a$

Examples:

<img src="./Figures/RSE_FIG_5-1.png" width="650px"/><br>

### Tarski's fixed point theorem
If $(L, \, \sqsubseteq, \, \sqcup, \, \sqcap, \, \bot, \, \top)$ is a complete lattice and $f : L \to L$ is a monotone function, then:
- $lfp^{\sqsubseteq} f$ exists, and
- $lfp^{\sqsubseteq} f = \sqcap Red(f) \in Fix(f)$

## 7.3 Approximating Functions