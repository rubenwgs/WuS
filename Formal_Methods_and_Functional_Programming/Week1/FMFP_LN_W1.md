# Formal Methods and Functional Programming - Week 1 (Lectures)
- Author: Ruben Schenk
- Date: 04.03.2021
- Contact: ruben.schenk@inf.ethz.ch

# Introduction
## Basic concepts in functional programming
One important notion in functional programming is that functions have no side effect, i.e. $f(x)$ always returns the same value (for the same $x$). This allows us to reason as in mathematics, i.e. if $f(0) = 2$ then $f(0) + f(0) = 2 + 2$.

The above mentioned property is called `referential transparency`, i.e. *an expression evaluates to the same value in every context.*

Another basic concept is that we use `recursion instead of iteration`. This can be shown with an easy example of `gcd`. In Java we might use an iterative function:

```java
    public static int gcd (int x, int y) {
        while(x != y) {
            if (x > y) {
                x = x - y;
            } else {
                y = y - x;
            }
        }
        return x;
    }
```

whereas in functional programming we strictly use recursion:

```haskell
    gcd x y
        | x == y    = x
        | x > y     = gcd (x - y) y
        | otherwise = gcd x (x - y)
```

## Introduction to functional programming
### Expression evaluation
In general, expression evaluation in Haskell works the same way as in mathematics, e.g. for $f(x, y) = x-y$, if we want to compute $f(5, 7)$ we substitute $5$ for $x$ and $7$ for $y$.

We differ between two types of `evaluation strategies`:
- `Eager evaluation`, where we evaluate arguments first (corresponds to the *green path* in the picture below)
- `Lazy evaluation`, which is used in Haskell, where we evaluate expression from the left and *only when needed* (corresponds to the blue path in the picture below)

<img src="./Figures/FMFP_FIG_1-1.PNG" width="750px" />

### Syntax and Types
The basic syntax of Haskell consists of the following two rules:
- Functions consist of different cases:

```haskell
    functionName x1 ... x2
        | guard1 = expr1
        | guard2 = expr2
        ...
        | guardm = exprm
```

- Programs consist of several definitions:

```haskell
    myConstant = 5

    aFunction y1 ... ym
        | guard1 = expr1
        | guard2 = expr2

    anotherFunction z1 ... zk
        ...
```

In Haskell, *indentation determines separation of definitions:
- All function definitions must start at the same indentation level.
- If a definition requires $n > 1$ lines, we indent the lines $2$ to $n$ further.

Following an example of a recommended layout:

```haskell
    f1 x1 x2
        | a long guard which may go over
          a number of lines
              = a long expression that also can
                go over several lines
        | g2  = e2
        | g3  = e3
```

Spaces are therefore very important, one should ***not use tabs!***

In Haskell we can use the following `types`:
- `Int` for integers with at least the range of $\{-2^{29},..., \, 2^{29}-1\}$ and the functions `+, *, ^, -, div, mod, abs`
- `Integer` for unbounded integers and the same functions as `Int`
- `Bool` with the values `True, False` and the binary operators `&&, ||` and the unary operator `not`
- `Char` for single characters as expected, i.e. `'a', 'b', 'c', ...`
- `String` for strings as expected, i.e. `"hello", "wordl", ...`
- `Doubles` for double precision numbers and functions like `+, -, *, /, abs, acos, ...`

We furthermore examine the type `tuple` more in detatil. Tuples are represented in `()` brackets. For example we might represente a student as a triple with his name, student id, and starting year as follows:

```haskell
    (String, Int, Int)
```

The above example is also called a `type constructor`. We can build and `element` of a student if we give it specific values, i.e.

```haskell
    ("Ueli Naef", 1234, 2015)
```

The above example is also called a `term constructor`. Functions can take tuples as arguments and/or return tupled values as shown in the example below:

```haskell
    addPair :: (Int, Int) -> Int
    addPair (x, y) = x + y
    -----------------------------
    ? addPair (3, 4)
    7
```

### Function scope
Functions have a `global scope`, i.e. a function can be called from any other function.

We can force a `local scope` with the keywords `let` and `where` as shown in the example below:

```haskell
    f x = let sq y = y * y
          in sq x + sq x
```

In the above code, `f x` is defined as `sq x + sq x` where `sq y` is *locally* defined as `y * y`.  This means that we can evaluate `f 10` but not `sq 10`.

The keyword `where` comes directly after a function definition and is used to define bindings over all guards:

```haskell
    f p1 p2 ... pm
        | g1 = e1
        | g2 = e2
        ...
        | gk = ek
        where
            v1 = r1
            v2 = r2
            ...
```

# Natural Deduction
To carry out `formal reasoning` about systems we need three essential parts:
1. Language
2. Semantics
3. Deductive system for carrying out proofs

As an introduction to this topic we look at an abstract example of a formal proof:
- Language: $\mathcal{L} = \{\bigoplus, \, \bigotimes, \, +, \, \times \}$
- Rules:
  - $\alpha$ : If $+$, then $\bigotimes$.
  - $\beta$ : If $+$, then $\times$.
  - $\gamma$ : If $\bigotimes$ and $\times$, then $\bigoplus$.
  - $\delta$ : $+$ holds.

Given the task "*Prove $+$*" we can proceed as follows:
1. $+$ holds by $\delta$
2. $\bigotimes$ holds by $\alpha$ with $1$.
3. $\times$ holds by $\beta$ with $1$.
4. $\bigoplus$ holds by $\gamma$ with $2$ and $3$.

Now, in a `deductive proof system` our rules look the following way:

$$
\frac{+}{\bigotimes} \alpha \qquad \frac{+}{\times} \beta \qquad \frac{\bigotimes \quad \times}{\bigoplus} \gamma \qquad \frac{}{+} \delta 
$$

Our prove can then be displayed as a `derivation tree` (following in *Parwitz style*):

<img src="./Figures/FMFP_FIG_1-2.PNG" width="350px" />

We now change the rules a little bit and look at another example of a formal proof:
- Language: $\mathcal{L} = \{\bigoplus, \, \bigotimes, \, +, \, \times \}$
- Rules:
  - $\alpha$ : If $+$, then $\bigotimes$.
  - $\beta$ : If $+$, then $\times$.
  - $\gamma$ : If $\bigotimes$ and $\times$, then $\bigoplus$.
  - $\delta$ : **We may assume $+$ when proving $\bigotimes$.**

Our linear proof changes therefore to:
1. **Assume** $+$ holds by $\delta$
2. $\bigotimes$ holds by $\alpha$ with $1$.
3. $\times$ holds by $\beta$ with $1$.
4. $\bigoplus$ holds by $\gamma$ with $2$ and $3$.

In our deductive proof system we now change our rules as follows:

$$
\frac{\Gamma \vdash +}{\Gamma \vdash \bigotimes} \alpha \qquad \frac{\Gamma \vdash +}{\Gamma \vdash \times} \beta \qquad \frac{\Gamma \vdash \bigotimes \quad \Gamma \vdash \times}{\Gamma \vdash \bigoplus} \gamma \qquad \frac{\Gamma, + \vdash \bigoplus}{\Gamma \vdash \bigoplus} \delta 
$$

Here, $\Gamma$ stands for some assumption. The first rule, read bottom-up, therefore reads as "*To prove $\bigotimes$ holds under some assumption $\Gamma$, it suffices to show that $+$ holds under the same assumption $\Gamma$.*"

Our derivation tree, now in *Gentzen-style*, looks as follows:

<img src="./Figures/FMFP_FIG_1-3.PNG" width="400px" />

## Propositional logic
### Syntax
The formal definition is given by:
- Let a set $\mathcal{V}$ of variables be given. Then $\mathcal{L}_P$, the `language of propositional logic`, is the smallest set where:
  - $X \in \mathcal{L}_P$ if $X \in \mathcal{V}$
  - $\bot \in \mathcal{L}_P$.
  - $A \land B \in \mathcal{L}_P$ if $A \in \mathcal{L}_P$ and $B \in \mathcal{L}_P$.
  - $A \lor B \in \mathcal{L}_P$ if $A \in \mathcal{L}_P$ and $B \in \mathcal{L}_P$.
  - $A \to B \in \mathcal{L}_P$ if $A \in \mathcal{L}_P$ and $B \in \mathcal{L}_P$.