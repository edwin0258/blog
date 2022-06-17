---
title: Haskell Monads
date: 2017-11-13 13:27:44
tags:
---

[Introduction](#Introduction "Introduction")
---------------------------------------------------------

A few days ago I was introduced to the concept of `monads`. In this post
I want to go over the concept to solidify it in my memory.

Monads weren't too scary because the concept was slowly built up to with
`functors` and `monoids` with
[learnyouahaskell](http://learnyouahaskell.com). I would recommend it to
anyone who wants to learn a little bit about the Haskell programming
language. Also, for anyone who doesn't know Haskell, it could be
described as a pure, lazy functional programming language, you should
check it out.

[Definition](#Definition "Definition")
---------------------------------------------------

Monad is basically a description for a type that follows some set rules.
It is also a monoid by default, because a monad follows all the same
rules a monoid does. As learnyouahaskell described it can also be
described as a beefed up applicative functor. I found a good
introduction to monoids and monads on youtube, try watching this video
called [Don't fear the
monad](https://www.youtube.com/watch?v=ZhuHCtR3xq8).

For reference, here is the Monad type class.

```haskell
class Monad m where  
    return :: a -> m a  
  
    (>>=) :: m a -> (a -> m b) -> m b  
  
    (>>) :: m a -> m b -> m b  
    x >> y = x >>= \_ -> y  
  
    fail :: String -> m a  
    fail msg = error msg
```

`return` is like `pure` for a functor. It returns the purest form of the
type which in this case is a value with a type. `>>=` is used to take
the values out of a monad and apply them to a function that takes a
general value. The result of that function is a monad of the same type.
`>>` takes two arguments and returns the last argument; however, if the
first argument is `mempty` then it will propagate to the result making
it `mempty`. For example:

```haskell
ghci> Nothing >> Just 1
Nothing
ghci> Just 1 >> Just 3
Just 3
```

[Examples](#Examples "Examples")
---------------------------------------------

The most basic example of a monad is the `Maybe` type. Here is how
`Maybe` is implemented as an instance of `Monad`

```haskell
instance Monad Maybe where  
    return x = Just x  
    Nothing >>= f = Nothing  
    Just x >>= f  = f x  
    fail _ = Nothing
```

`Maybe` on return will return `Just` with the value. If `Nothing` is
passed to `>>=` then Nothing will propagate to the return of the
function. If `Just` with a value is passed to `>>=` then the value will
be taken out of `Just` and passed to the function. `>>` has a default
implementation so it's not added to `Maybe`. Here is the default
implementation:

```haskell
(>>) :: (Monad m) => m a -> m b -> m b  
m >> n = m >>= \_ -> n
```

[Using Monad](#Using-Monads "Using Monads")
---------------------------------------------------------

Here's an example using the Maybe monad with `>>=` and a `do` block
which is syntactical sugar for `>>=`

```haskell
Just 55 >>= (\x -> Just (5 + x)) -- Just 60
```

And the do block

```haskell
example = do
    x <- Just 55
    Just (x + 5)
```

And here is a list example (lists are also monads)

```haskell
[1,2,3,4] >>= \x -> [(+2), (*3)] >>= \ch -> return (x, ch x)
-- [(1,3),(1,3),(2,4),(2,6),(3,5),(3,9),(4,6),(4,12)]
```

[Conclusion](#Conclusion "Conclusion")
---------------------------------------------------

I'm just getting into monads but I can tell they are a very powerful
tool. I'm excited to keep exploring Haskell and all of its cool
features.