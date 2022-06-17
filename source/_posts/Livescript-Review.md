---
title: Livescript Review
date: 2017-11-13 13:27:44
tags:
---

[Overview](#Overview "Overview")
---------------------------------------------

[Livescript](http://livescript.net/) is a javascript preprocessor that has many functional programming features included in it. I decided to write a basic [Assembler Interpreter](https://codepen.io/edwin0258/pen/WXxpMY) on codepen to try it out. This is really the first JS preprocessor I've used, so it was an interesting experience. This is a review of the language after using it in a project.

[Indepth](#Indepth "Indepth")
------------------------------------------

Overall, livescript is more consise than vanilla JS. Take for example the guard syntax in livescript which is syntactical sugar for JS switch/case:

```livescript
letters = (a) ->                                          
    | a == "b" => "second letter"
    | a == "a" => "first letter"
    | otherwise => "another letter"
letters "d" # another letter
letters "a" # first letter
letters "b" # second letter
```

As you can see, compared to a JS switch/case it is much less verbose.

### [Comprehensions and Ranges](#Comprehensions-and-Ranges "Comprehensions and Ranges")

Other awesome features of livescript include the list comprehension and ranges. Python and Haskell both have these features and I find myself using them often. Here is an example:

```livescript
[i for i in [0 to 10]] # [0,1,2,3,4,5,6,7,8,9,10]
```

### [Strict equality and Immutability](#Strict-equality-and-Immutability "Strict equality and Immutability")

The livescript `==` is by default strict equality and all types are immutable. You can reassign variables and use regular equality with special syntax. I like that it gives you the ability to still do regular equality, but I really appreciate that strict rules are encouraged which can prevent some headaches.

### [Currying, Piping, Partial application](#Currying-Piping-Partial-application "Currying, Piping, Partial application")

You can partially apply a function in livescript and map each element of an array over that function, it's really cool, check it out:

```livescript
map (^3), map (* 2), map (+ 2), [1 2 3] # [216,512,1000]
```

Operators can be prefix in livescript.

```livescript
(^) 3 5 # 243
```

Livescript also has a special syntax for currying (`-->`). This means that you don't need to pass all the variables a function requires at once, you can pass any number of variables and assign that partially applied function to another variable for later use.

```livescript
animal = (cls, species, age) --> "This animal is a #{cls}, known by its species name of #{species}. It has an age of #{age}."

mammal = animal("mammal")
dog = mammal("dog")
jack = dog(12)
```

The last thing I want to talk about is piping, you can run data through a sort of pipe and get an output that reflects the operations performed on that data inside the pipe. This improves readability, and as you can see, all the functions in the pipe are pure and do not mutate the original data by default. (Note that for these features you will need to have [preludels](http://www.preludels.com/))

```livescript
arr = [to 20]
arr |> filter even 
    |> foldl ((acc, y) -> acc ++ (* 2) y), [] 
    |> groupBy (> 10)
    
arr # orginal array
```

[Conclusion](#Conclusion "Conclusion")
---------------------------------------------------

There are so many interesting things you can do with livescript, I've just scratched the surface here to what is possible. I did enjoy using livescript to make a project and would recommend that you give livescript a try. Although, maybe there is a better option than livescript (let me know).

[Other Resources](#Other-Resources "Other Resources")
------------------------------------------------------------------

Check out: [Learn Livescript in x mins](https://learnxinyminutes.com/docs/livescript/) for deeper dive into the prepocessor.