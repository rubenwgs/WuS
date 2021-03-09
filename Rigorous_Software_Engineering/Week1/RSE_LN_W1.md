# Rigorous Software Engineering- Week 1 (Lectures)
- Author: Ruben Schenk
- Date: 08.03.2021
- Contact: ruben.schenk@inf.ethz.ch

# 1. Introduction
## 1.2 Challenges
The four key dimensions to the question "Why is Software so Difficult to Get Right?" are:
- *Complexity*: Modern software systems are huge, they are created by many developers over several years. They also have a high number of discrete states and execution paths.
- *Change*: Software systems often deviate from their initial design. Typical changes include: New features, new interfaces, bug fixing, performance tuning.
- *Competing Objectives*: Functionality vs usability, cost vs robustness, performance vs portability, etc.
- *Constraints*: Such as budget, time, staff and their available skills.


## 1.4 Solution Approaches
This course is split up into four parts:
- Part I, Writing clean code: Modularity, Coupling, Design Patterns
- Part II, Software Testing: Metrics, exhaustive, random, functional, structural testing.
- Part III, Software Analysis at Scale: Math, heap, numerical, symbolic execution, concolic execution, fuzzing
- Part IV, Modeling: Model finding, Alloy, applications to memory models

# 2. Documentation
## 2.1 Why should we document?
### 2.1.1 Essential vs Incidental Properties
Source code does not exspress *which properties are stable* during software evolution, moreso, which properties are `essential` and which are `incidental`.

Example: In the following code, is it *essential* that it finds some index $i$ such that $\text{array}[i] = v$ or the *first* index $i$ ?

```java
    int find(int[] array, int v) {
        for(int i = 0; i < array.length; i++) {
            if(array[i] == v) {
                return i;
            } else {
                return -1;
            }
        }
    }
```

If a developer at a later point decides to parallelize this function, it is essential to know whether or not it is important to return the first matching index or just some matching index.

### 2.1.2 Invariants
We look at a simple example code:

```java
    HashMap<String, String> m;
    m = SomeLibrary.foo();
    String s = m.get("key");
```

Now, one might ask whether or not $s$ can be `NULL`.
- What happens if the key is not found in the map? -> The function `.get()` returns `NULL`.
- What if the key is matched? Can the value still be `NULL`?

### 2.1.3 Invariants


## 2.2 What to document?

## 2.3 How to document?