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
data Tree a = Leaf a
            | Node (Tree a) a (Tree a)
```

### Functional Parsers
- Parsers analyze text to determine its syntactic structure
- `data Parser = P (String -> [(a, String)])`
    - `[]` would be a failure
    - `a` is possible interpretations 
    - `String` is any leftover input

#### Basic Parsers
- `item` fails if input is empty, consumes the first character otherwise
- `failure` always fails
- `return v` always returns `v` without consuming any input
- `p +++ q` if `p` succeeds, behaves as `p`, otherwise `q`
- `parse (P p)` applies parser p to a string

#### Sequencing
```haskell
-- Allows you to take the needed parts from the syntax structure
p :: Parser (Char,Char) 
    p = do  x <- item
            item
            y ← item
            return (x,y)
```

#### Derived Parsers
- `sat p` parses a character that satisfies a predicate `p`
    - `digit`, `char` parses a digit or specific characters
- `many p` applies a parser 0+ times
    - `string` parses many characters

### Interactive Programs
- Interactive programs have *side effects*
- `IO a` defines a inpure actions with side effects
    - `IO Char` returns a character
    - `IO ()` has no result value

#### Basic Actions
- `getChar` reads, echos and returns a character from the keyboard
- `putChar c` writes a character `c`
- `return v` returns `v`

#### Derived Primitives
```haskell
-- Reading a string from the keyboard:
getLine :: IO String 
getLine = do x ← getChar
             if x == '\n' then
                return []
             else
                do xs ← getLine
                    return (x:xs)
                        
-- Writing a string to the screen:putStr :: String → IO ()
putStr []     = return ()
putStr (x:xs) = do putChar x
                   putStr xs
-- Writing a string and moving to a new line:putStrLn :: String → IO () 
putStrLn xs = do putStr xs
                 putChar '\n'
```

### Monads
- Structure composed of two basic operations: 
    - bind
    - return

```haskell
class Monad m where
    (>>=)   :: m a -> (a -> m b) -> m b
    return  :: a -> m a
```

#### Do-notation    
```haskell
-- do notation is syntactic sugar
do  x <- exp 
    morelines
exp >>= (\x -> do morelines)

do  exp 
    morelines
exp >>= (\_ -> do morelines)

do  return exp
return exp
```
 
#### Creating Monads
```haskell
-- Parser
newtype Parser a = P (String -> [(a,String)])
instance Monad Parser where
    return v = P (\inp -> [(v,inp)])
    p >>= f = P (\inp -> case parse p inp of
                    [] -> []
                    [(v,out)] -> parse (f v) out)

-- Simple
data Option a = None | Some a
instance Monad Option where
    return x        = Some x 
    None >>= f      = None 
    (Some x) >>= f  = f x
```

#### Monad Laws
- `return a >>= k = k a`
- `m >>= return = m`
- `m >>= (\x -> k x >>= h) = (m >>= k) >>= h`

### Equational Reasoning
- We can apply standard mathematical techniques to a functional language
- Interpret programs as equations, substitute equals by equals
- Induction can also be used

### Functor
```haskell
class Functor f where
fmap :: (a -> b) -> f a -> f b
```

#### Laws
- `fmap f (fmap g fa) ≣ fmap (f . g) fa`
- `fmap id fa ≣ fa`

### Countdown Problem
- List of positive natural numbers
- Artithmetic operators: `+ - * /`
- Construct an expression that equals the target number
