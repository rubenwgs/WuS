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

Finished RSE VID W4-1