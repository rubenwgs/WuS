# Formal Methods and Functional Programming - Week 3 (Lectures)
- Author: Ruben Schenk
- Date: 11.03.2021
- Contact: ruben.schenk@inf.ethz.ch

The first example of a Haskell function using lists is the `zipper function`. It takes two lists and creates a list of tuples where for each tuple, one element is from the first input list and the second element is from the second input list. It is implemented as follows:

```haskell
    zip (x:xs) (y:ys) = (x,y) : zip xs ys
    zip _      _      = []
```

### Adivce on recursion
Defining recursive functions can be difficult. We show a 5-step "tool" which should make the process easier. Following an example of defining a function `drop` which removes the first $n$ elements of a list:

1. Step: Define the type

```haskell
    drop :: Int -> [Int] -> [Int]
```

2. Step: Enumerate the cases

```haskell
    drop 0 []     = ...
    drop 0 (x:xs) = ...
    drop n []     = ...
    drop n (x:xs) = ...
```

3. Step: Define the simple cases

```haskell
    drop 0 []     = []
    drop 0 (x:xs) = x:xs
    drop n []     = []
    drop n (x:xs) = ...
```

4. Step: Define the other cases

```haskell
    drop 0 []     = []
    drop 0 (x:xs) = x:xs
    drop n []     = []
    drop n (x:xs) = drop (n-1) xs
```

5. Step: Generalize and simplify

Another example of an interesting recursive function is `insertion sort`, defined as follows in Haskell:

```haskell
    isort :: [Int] -> [Int]
    isort []     = []
    isort (x:xs) = ins x (isort xs)

    ins :: Int -> [Int] -> [Int]
    ins a [] = [a]
    ins a (x:xs)
      | a <= x    = a : (x : xs)
      | otherwise = x : ins a xs
```

