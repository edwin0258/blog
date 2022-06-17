---
title: JS to Haskell
date: 2017-11-30 13:27:44
tags:
---

[Introduction](#Introduction "Introduction")
---------------------------------------------------------

In this post I will be solving some programming problems with JS and
Haskell. This gives a side by side comparison of the two languages and I
thought it would be interesting to see the similarities and differences.
Let's get started.

[Problem 1 (Christmas Trees)](#Problem-1-Christmas-Trees "Problem 1 (Christmas Trees)")
----------------------------------------------------------------------------------------------------

### [Overview](#Overview "Overview")

Let's pretend you are the seller of colorful christmas trees online.
Each color has a different price and your job here is to calculate the
total cost of christmas tree orders for each customer. Customers can
choose the color, lights, and height of each tree.

### [Details](#Details "Details")

 Tree Color |  Price
  ------------ | -------
  Red       |   \$250
  Blue      |   \$200
  Yellow    |   \$170
  Green     |   \$200
  Silver    |   \$340
  Gold      |   \$400
  White     |   \$450


  Light Type  |   Price
  -------------- | -------
  Unlit       |   \$+0
  White       |   \$+15
  Multi-color  |  \$+30
  Single-color |  \$+20


  Height       |      Price
  ------------------ | --------
  5 feet and under |  \$+0
  6 feet          |   \$+50
  7 feet          |   \$+100
  8 feet         |    \$+150
  8+ feet         |   \$+200

*Note: We can take multiple tree orders at a time
We are also assuming that the data entered by the user is valid.*

### [Javascript Solution](#Javascript-Solution "Javascript Solution")

```js
const trees = {
  "red":    250,
  "blue":   200,
  "green":  200,
  "yellow": 170,
  "silver": 340,
  "gold":   400,
  "white":  450
};

const lights = {
  "unlit":  0,
  "white":  15,
  "multi":  30,
  "single": 20
};

const heights = x => { 
  switch(true) {
    case (x <= 5): return 0
    case (x <= 6): return 50
    case (x <= 7): return 100
    case (x <= 8): return 150
    default:       return 200
  }
};

const treeOrder = (...orders) =>
    orders.reduce((t, [tree, type, height]) =>
        t += trees[tree] + lights[type] + heights(height),0);

```

Here I create 2 constant lookup tables for `trees` and `lights` to store
the prices. Height is a little more tricky because there are an infinite
number of possibilities the number could be (In a real world situation
there would probably be only a set number of sizes to choose from, so a
lookup table would be possible). I created a simple switch inside the
`heights` function to account for the varient in potential input.

The `treeOrder` function will take any number of orders and reduce them
to a single total value. For each order, I use pattern matching to get
the tree color, light type, and height. Using these values, I'm able to
get some price amounts which are all added to the total amount.

### [Haskell Solution](#Haskell-Solution "Haskell Solution")

```haskell
import Control.Monad

treeOrder :: [(String, String, Integer)] -> Maybe Integer
treeOrder [] = Just 0
treeOrder orders = foldM (fmap . (+)) 0 info
  where trees = [("red",    250)
                ,("blue",   200)
                ,("green",  200)
                ,("yellow", 170)
                ,("silver", 340)
                ,("gold",   400)
                ,("white",  450)]
                
        lights = [("unlit",  0)
                ,("white",  15)
                ,("multi",  30)
                ,("single", 20)]
        
        heights x
          | x <= 5 = 0
          | x <= 6 = 50
          | x <= 7 = 100
          | x <= 8 = 150
          | otherwise = 200
        
        (tree, light, height) = head orders
        
        info = [lookup tree trees
               ,lookup light lights
               ,Just $ heights height
               ,getTree $ tail orders]
```

With Haskell, I define the lookup tables and height function inside the
`where` clause of the `treeOrder` function. I am then using pattern
matching after the height function to get the tree, light, and height
values for the current order. `info` combines all the lookup values and
function value into a list. The last element of the info list is how all
orders will be added together, using recursion. So, the next orders
values will show up as the last element of the `info` list once it is
time to add everything together.

Once the full list has been calculated, I fold over the values to get a
single total value (like reduce).

[Problem 2 (Tic Tac Toe)](#Problem-2-Tic-Tac-Toe "Problem 2 (Tic Tac Toe)")
----------------------------------------------------------------------------------------

### [Overview](#Overview-1 "Overview")

Create an interface that users can play a game of tic tac toe on.

### [Details](#Details-1 "Details")

-   Users should be able to add their color (x or o) to the board.
-   When there are 3 of a color in any particular direction, that user
    wins!
-   Function should take a move or multiple moves.
-   Input is expected to be valid to avoid adding too much complexity to
    solutions.

### [Javascript Solution](#Javascript-Solution-1 "Javascript Solution")

```javascript
const transpose = array =>
  array[0].map((col, i) => array.map(row => row[i]));

const move = (board, moves, color) => 
  moves.reduce((arr, [y,x]) => {
    arr[y][x] = color;
    color = (color == "x") ? "o" : "x";
    return arr;
  }, board);
  
const genBoard = (x, y) => 
  Array(x).fill().map(() => Array(y).fill(""));

const check = board => {
  const getVal = lst => [...new Set(lst)];
  
  const chk = x => (getVal(x).length == 1) ? getVal(x)[0] : "";
  
  const horzCheck = board.map(chk);
  const vertCheck = transpose(board).map(chk);
  
  function diagonalCheck(){
    const [d1, d2] = [[],[]];
    
    for(x = 0; x < board.length; x++) {
      d1.push(board[x][x]);
      d2.push(board[x][board[0].length - 1 - x]);
    }
    
    return [d1].map(chk).concat([d2].map(chk));
  }
  
  const result = [...horzCheck, ...vertCheck, ...diagonalCheck()];
  
  if(result.includes("x") && result.includes("o")) return "Tie";
  else if(result.includes("x")) return "Black wins!";
  else if(result.includes("o")) return "White wins!";
  
  return "No one wins.";
}
```


*Running a game in console*

```javascript
> let g = move(genBoard(3,3),[[0,0],[2,1],[0,1],[0,2],[1,1],[2,2],[1,2],[2,0]],"x")
=> undefined
> check(g)
=> 'White wins!'
```

I will go over the the code by line numbers to make it a little easier
to organize my thoughts.

**1** - To make life easier, I first define a transpose function. It
will take an array and swap the columns and rows.

**4** - `move` takes a board, move, and a color. It adds the moves to
the board using a `reduce`, on each loop in the reduce the color is
changed to the opposite color. It might be a little silly to use a
`reduce`, but this way I can some avoid extra code.

**11** - `genBoard` is a helper function for generating an array of
arrays that represents the tic tac toe board.

**15** - `getVal` will return a list of unique values.

**17** - `chk` is the general checker for sections of the board. If all
values in a section are the same then we return that value, else return
an empty string.

**19/20** - More specific checks for columns and rows of the board
(horizontal and vertical).

**22** - Checking diagonals on the board is a little more tricky. By
incrementing both x and y index for each pass of a for loop I can get
the diagonal from left to right. If y is counting up and x is counting
down I can also get the right to left diagonal (line 27).

**35** - Often I omit the curly braces on single line expressions. Just
seems cleaner and more readable. Depending on all the checks, the
`check` function is now returning the correct message.

### [Haskell Solution](#Haskell-Solution-1 "Haskell Solution")

```haskell
import Data.List

genBoard :: Int -> Int -> [[String]]
genBoard x y = take y $ cycle [take x $ repeat ""]

move :: [(Int, Int)] -> [[String]] -> String -> [[String]]
move [] board _ = board
move moves board color = move (tail moves) newBoard nextColor
  where mv = head moves
        nextColor 
          | color == "x" = "o"
          | otherwise    = "x"
        newRow = replace (fst mv) color (board !! (snd mv))
        newBoard = replace (snd mv) newRow board
        
        replace n newVal (x:xs)
          | n == 0 = newVal:xs
          | otherwise = x:replace (n-1) newVal xs 

check :: [[String]] -> String
check board
  | "o" `elem` result && "x" `elem` result = "Tie"
  | "o" `elem` result = "White wins!"
  | "x" `elem` result = "Black wins!"
  | otherwise = "No one wins."
  where chk x = foldl (\acc a -> if (length $ nub a) == 1 then a !! 0:acc else acc) [] x
        
        horzCheck = chk board
        vertCheck = chk $ transpose board
        diagonalCheck = chk  
          [zipWith (!!) board [0..]
          ,zipWith (!!) board [length board-1,length board-2..]]
        
        result = horzCheck ++ vertCheck ++ diagonalCheck
```

*Running a game in GHCi*

```javascript
> let g = game [(0,0),(2,1),(0,1),(0,2),(1,1),(2,2),(1,2),(2,0)] (genBoard 3 3) "x"
> check g
=> "White wins!"
```

**1** - Import a package in haskell, `Data.List` has some useful
functions like `transpose` (I had to make this in JS).

**3** - `genBoard` is a helper function for generating a game board.
This will repeat an empty string `x` times and place it into a list. I
then take `y` out of an infinite cycle of the previous list, which
returns a list of lists with the correct dimensions. I could have also
used a list comprehension here, but I think this looks cleaner.

**6** - This type signature for the function is kind of interesting. The
`move` function takes a list of tuples representing the coordinates, a
list of lists containing strings (the board), and a string (representing
the color). It returns a new board.

**7** - If there are no more moves to make then return the final board.
This is how the recursive function will stop.

**8** - Haskell loves recursion, since there are possibly more moves, I
pass all the moves except the the current move back into the `move`
function with the next color and updated board.

**11** - The guard (similar to a switch(true)?) gets the next color, if
previous color was black then the next color will be white for example.

**13** - `newRow` is the row with the new piece placed on it from move.
Since data is immutable in haskell, I can't directly state
`(board !! y !! x) = color`. Line 14 is the whole board, so once we have
the correct row, we then can place it into a new copy of the board. It's
sort of confusing, but it gets the job done. I could have used
`Control.Lens` but this method feels a bit cleaner.

**16** - `replace` will go through the list one index at a time until n
is 0, once n is 0 it will replace the current item under it with the new
value. This function also uses recursion.

**21** - `check` has a guard that will check the `result` for the
contents and then determine the appropriate message to display.

**26** - I like to think that `foldl` is kind of like a JS `reduce`. The
rest of the fold is basically the same thought process as in JS, just
different syntax.

**31** - Using `zipWith` I can write some fairly clean code to get the
diagonals of the board.

[Conclusion](#Conclusion "Conclusion")
---------------------------------------------------

Awesome stuff! It's interesting to see two fairly different languages
side-by-side. There are definitely some similarities, such as the
`guard` and `switch` or `foldl` and `reduce`. There are also
differences; Haskell being a statically typed for example. Try to find
your own similarities and differences, cheers!