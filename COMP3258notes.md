# COMP3258 Functional Programming

## Functional Programming
- No side-effects
- Computation by applying functions to arguments

## Haskell
### Prelude
#### List Operations
```haskell
[1..5] -- [1,2,3,4,5]
head [1,2,3,4,5]    -- first element
tail [1,2,3,4,5]    -- rest
[1,2,3,4,5] !! 2    -- nth element
length [1,2,3,4,5]  -- length
[1,2,3] ++ [4,5]    -- append lists
1 : [2,3,4,5]       -- cons
null []             -- empty?
init [1,2,3,4,5,6]  -- last element
take 3 [1,2,3,4,5]
drop 3 [1,2,3,4,5]
reverse [1,2,3,4,5]
sum [1,2,3,4,5]
product [1,2,3,4,5]
```

### Functions
- Take priority over other operators
- Must begin with lowercase
- Lists end with `s` by convention

```haskell
> myAdd1 x = x + 1
> myAdd1 1
2
```

### Types
- _Type error_ if applying function to arguments of the wrong type
- Expression types can be automatically calculated at compile through _type inference_
    - Type errors are found during compilation making programs safer and faster
- Use `:type expression` to calculate the type of an expression
- Basic Types:
    - `Bool`: `True` or `False`
    - `Char`
    - `String`
    - `Int`: fixed-precision
    - `Integer`: arbitrary-precision
    - `Float`
- Type Classes
    - `Num`: can apply math (`+`, `-`, `*`, `negate`, `abs`, `signum`)
    - `Eq`: can be compared (`==`, `/=`)
    - `Ord`: can be ordered (`<`, `>=`, `min`, etc)
- List Type: sequence of values of the same type, any size
- Tuple Type: sequence of values of different types, fixed size
- Function Type: mapping on values of one type to another
    - `add1 :: Int -> Int`
    - **Currying**: taking arguments one at a time
        - `add :: Int -> (Int -> Int)`
        - Allows partially applied functions
    - **Polymorphic**: can take multiple types
        - `length :: [a] -> Int`

### Defining Functions
#### Conditions
```haskell
-- If
abs :: Int -> Int
abs n = if n >= 0 then n else -n

-- Guarded Equations
abs n | n >= 0      = n
      | otherwise   = -n

-- Pattern Matching
not :: Bool -> Bool
not False = True
not True = False

-- List Patterns
head :: [a] -> a
head (x:_) = x
tail :: [a] -> [a]
tail (_:xs) = xs
```

#### Lambdas
- Used to formalize functions defined by currying
- Clarifies functions that return functions
- Avoids naming functions that are only used once

```haskell
add x y = x+y
-- means
add = λx -> (λy -> x+y)

const ::a -> b -> a const x _ = x
-- is more naturally defined by
const ::a -> (b -> a) const x = λ_ -> x
```

#### Sections
```haskell
(1+) (1/) (*2) (/2)
```

#### Recursion
- Allows some functions to be simplified
- Recursive functions can be proven through induction

#### List Comprehensions
- Constructs new lists from old lists

```haskell
> [x^2 | x <- [1..5]]
[1,4,9,16,25]
> [(x,y) | x <- [1,2,3], y <- [4,5]]
[(1,4),(1,5),(2,4),(2,5),(3,4),(3,5)]

-- *Dependant Generators* depend on variables introduced earlier
concat :: [[a]] -> [a]
concat xss = [x | xs <- xss, x <- xs]

-- *Guards* restrict values produced
factors :: Int -> [Int]
factors n = [x | x <- [1..n], n `mod` x == 0]
```

### Higher-Order Functions
- Takes a function as an argument or returns a function as a result

```haskell
map :: (a -> b) -> [a] -> [b]
filter :: (a -> Bool) -> [a] -> [a]
foldr :: (a -> b -> b) -> b -> [a] -> b
```

#### Composition
- `(.)` Combines two functions into a single function

```haskell
f . g = \ x -> f (g x)

odd :: Int -> Bool
odd = not . even
```

### Type Declaration
```haskell
type Pos = (Int, Int)

-- Can have parameters
type Pair a = (a, a)
mult :: Pair Int -> Int
mult (m, n) = m * n
```

### Data Declaration
```haskell
data Bool = False | True

-- Can have parameters
data Shape = Circle Float
           | Rect Float Float
area :: Shape -> Float area (Circle r) = pi * r^2
area (Rect x y) = x * y

data Maybe a = Nothing | Just a
safediv :: Int -> Int -> Maybe Int
safediv _ 0 = Nothing
safediv m n = Just (m `div` n)
safehead :: [a] -> Maybe a
safehead [] = Nothing
safehead xs = Just (head xs)

-- Can be recursive
data Nat = Zero | Succ Nat
```
