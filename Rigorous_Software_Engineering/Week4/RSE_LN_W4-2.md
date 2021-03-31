# Rigorous Software Engineering- Week 4 (Lectures)
- Author: Ruben Schenk
- Date: 30.03.2021
- Contact: ruben.schenk@inf.ethz.ch

# 6. Static Anaylsis
We will learn a style called `abstract interpolation`, which is a general theory of how to do approximation systematically.

## 6.1 Abstract Interpolation
We can define abstract interpolation with the following steps:
1. Select/define an abstract domain: selected based on the type of properties you want to prove
2. Define abstract semantics for the language w.r.t. to the domain: prove sound w.r.t. concrete semantic
3. Iterate abstract transformers over the abstract domain: until we reach a fixed point

The `fixed point` is the *over-approximation* of the program.

## 6.2 Example Application of Abstract Interpolation
Lets prove an assertion to see how abstract interpolation works. Consider the following code:

```java
    foo(int i) {
1:      int x = 5;
2:      int y = 7;

3:      if(i >= 0) {
4:          y = y + 1;
5:          i = i - 1;
6:          goto 3;
        }
7:      assert 0 <= x + y;
    }
```

### Step 1: Select abstraction
Lets pick the `sign` abstraction, given as follows:

<img src="./Figures/RSE_FIG_4-1.png" height="350px"/><br>

- $\top$: stands for all possible values
- $-$: stands for the negative values ($\leq 0$)
- $+$: stands for the positive values ($\geq 0$)
- $0$: stands for zero
- $\bot$: unreachable numbers (for now)

An `abstract` program state is a map from variables to elements in the domain. Example:

| pc | x | y | i |
|:--:|:-:|:-:|:-:|
| 2  | + | $\bot$ | $\top$ |

We see that at pc (program counter) 2, i.e. right before the execution of the second line, $x$ is positive, $y$ is bottom, i.e. not yet defined, and $i$ is either positive or negative.

### Step 2: Define Transformers
An `abstract transformer` describes the effect of statement and expression evaluation on an abstract state.

It is important to remember that abstract transformers are defined per `programming language` once and for all, and not per-program! <br>
This means thath any program in the programming language can use the same transformers.

A `correct abstract transformer` should always produce results that are superset of what a concrete transformer would produce.

Example of a sound transformer:

| pc | x | y | i |
|:--:|:-:|:-:|:-:|
| 4  | $\top$ | - | $\top$ |

$\Rightarrow$ `y := y + 1;` $\Rightarrow$

| pc | x | y | i |
|:--:|:-:|:-:|:-:|
| 5  | $\top$ | $\top$ | $\top$ |