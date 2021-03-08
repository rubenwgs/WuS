# Formal Methods and Functional Programming - Week 2 (Lectures)
- Author: Ruben Schenk
- Date: 08.03.2021
- Contact: ruben.schenk@inf.ethz.ch

# Correctness
## Substitution
`Substitution` describes replacing in $A$ all occurrences of a *free variable* $x$ with some term $t$. We write $A[x / t]$ to indicate that we substitute $x$ by $t$ in $A$.

Example:
$$A \equiv \exists y.y * x = x * z \to A[x / 2-1] \equiv \exists y.y * (2-1) = (2-1 * z)$$

