# Learn You a Haskell for Great Good! - Chapter 1 & 2
- Author: Ruben Schenk
- Date: 16.03.2021
- Contact: ruben.schenk@inf.ethz.ch

# 1. Introduction
For the purpose of this tutorial we will be using GHC, the most widely Haskell compiler.

For learning it's a lot easier to interactively interact with scripts. The interactive mode is invoked by typing `ghci` at the command prompt. IF you have defined some functions in a file called `myfunctions.hs`, you load up those functions by typing in `:l myfunctions` and then you can play with them, provided `myfunctions.hs` is in the same folder from which `ghci` was invoked. IF you change the .hs script, just run `:l myfunctions` again or equivalently `:r`. To quit out of `ghci`, one might type `:q`.

# 2. Starting Out
## 2.1 Ready, set, go!

Haskell supports simple arithmetic as follows:

```haskell
    ghci> 2 + 15
    17
    ghci> 5 / 2
    2.5
    ghci>
```

Boolean algebra is also pretty straight forward. `&&` means a boolean `and`, `||` means a boolean `or` and `not` negates a `True` or `False`.

```haskell
    ghci> True && False
    False
    ghci> False || True
    True
    ghci> not (True && True)
    False
    ghci>
```

Testing for equality is done like so:

```haskell
    ghci> 5 == 5
    True
    ghci> 5 /= 4
    True
    ghci> "hello" == "hello"
    True
    ghci>
```

All functions up to now (like `+`, `/=`, etc) were sandwiched between two parameters. That's what we call an `infix` function. Most functions that aren't used with numbers are `prefix` functions. Example:

```haskell
    ghci> min 9 10
    9
    ghci> max 3.4 3.2
    3.4
    ghci>
```

## 2.2 Baby's first instructions
We start with making our first simple function. In a text editor, insert the following two functions and save it as `firstFunctions.hs`:

```haskell
    doubleMe x   = x + x
    doubleUs x y = x*2 + y*2
```

Once inside `ghci`, do `:l firstFunctions`. Now that our script is loaded, we can play with the function that we defined:

```haskell
    ghci> :l firstFunctions
    [1 of 1] Compiling Main (firstFunctions.hs, interpreted)
    Ok, one module loaded.
    ghci> doubleMe 9
    18
    ghci> doubleUs 9 10
    38
    ghci>
```

Now we're going to make a function that multiplies a number by 2 but only if that number is smaller or equal to 100.

```haskell
    doubleSmallNumber x = if x > 100
                            then x
                            else x*2
```

Compared to `if-statements` in imperative languages, in Haskell's if-statement the else part is required. Furthermore the if-statement is an `expression`, i.e. it must return a value.

## 2.3 An intro to lists