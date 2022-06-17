---
title: Intro to Lambda Calculus
date: 2017-11-20 13:27:44
tags:
---

[Overview](#Overview "Overview")
---------------------------------------------

*Note: This is a work in progress.*

For the past couple of days I've been learning about lambda calculus. I
thought I would review what I've learned as well as offer a few
resources were helpful for me.

### [Description](#Description "Description")

According to wikipedia, "[Lambda calculus](https://en.wikipedia.org/wiki/Lambda_calculus) (also written
as λ-calculus) is a formal system in mathematical logic for expressing
computation based on function abstraction and application using variable
binding and substitution". It was created by [Alonzo Church](https://en.wikipedia.org/wiki/Alonzo_Church) who also happened
to have [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing) as a
student.

### [Resources](#Resources "Resources")

For learning a little about these topics, I found [glebec's lambda-talk repo](https://github.com/glebec/lambda-talk) to be extremely useful and
interesting. I also found this [computerphile video on lambda calculus](https://www.youtube.com/watch?v=eis11j_iGMs) to be a good
introduction as well.

[Details](#Details "Details")
------------------------------------------

### [Basics](#Basics "Basics")

The most basic function in lambda calculus is identity. Like so:

```js
I = a => a // λa.a  <- lambda calculus notation
I(1) // 1
```

In Haskell it's built in to the language as `id`.
There are also various other functions named after birds because Haskell
curry was apparently an avid bird watcher and the functions were named
in honor of that.
Here are some of the notes that I took on glebec's lambda-talk.

```js
parse = n => (
  n = n.name,
  (n == "K")  ? true  :
  (n == "KI") ? false : false
) // parsing to unpack meaning from the lambda calculus.

I = a => a        // λa.a  - Idiot        - Identity
K = a => b => a   // λab.a - Kestrel      - first, const
KI = a => b => b  // λab.b - Kite         - second
M = f => f(f)     // λf.ff - Mockingbird  - self-application

// Combinator - functions with no free variables. e.g. λabc.ab , not λa.b

C = f => a => b => f(b)(a) // λfab.fba    - flip arguments

// Church encodings - Booleans/Logic

const T = K  // true behaves like Kestrel.
const F = KI // false behaves like Kite.
not = f => f(F)(T) // Kestral will pick the fst, Kite the snd
and = f => q => f(q)(F)
or  = f => q => f(T)(q)
beq = f => q => f(q)(not(q)) 

// e.g.
```

### [SKI combinator calculus](#SKI-combinator-calculus "SKI combinator calculus")

There are also particular groups of combinators that are turing complete
by themselves.
One such grouping is called SKI combinator calculus. The last two
combinators `K` and `I` I already mentioned. Crazy enough, SKI
combinator calculus can be shortened to just SK combinator calculus,
because `SKK` is equivalent to `I`. Notes below on more details.\

```js
S = a => b => c => a(c)(b(c)) // λabc.ac(bc) - Starling
S(K)(K)(2) // 2
```

I also found a good video on the topic: [SKI cardboard calculus](https://www.youtube.com/watch?v=fZQMmgElRMI)
As a fun side note: look at this [Hello World using SKI combinator calculus](https://gist.github.com/shangaslammi/3438688). (Haskell is
required to run the program)

### [Church Numbers](#Church-Numbers "Church Numbers")

Church numbers are a way of representing numeric values in lambda
calculus. Take a look at this code for example:

```js
num = x => x(y => y + 1)(0) // For converting function to number.

n0 = f => a => a // Exactly like the Kite. Zero is false in lambda calculus.
n1 = f => a => f(a) // Exactly like Identity, except it is refered to as I* (Identity once removed).
n2 = f => a => f(f(a)) // An additional f is added.
n3 = f => a => f(f(f(a))) // And so on..
n4 = f => a => f(n3(f)(a))

// e.g.
num(n4) // 4
num(n3) // 3
```

You can also do things such as addition on church numbers to get new
numbers. But first, it is helpful to define a succession function that
will take a number and return that number incremented by one. Here are
my notes on Church Numbers:

```js
succ = f => a => f(a)
```