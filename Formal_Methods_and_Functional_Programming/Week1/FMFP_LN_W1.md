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
