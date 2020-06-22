# Recursion

You know functions are a powerful way to write code once and use it many different times. Parameters make functions more useful by allowing for simple changes each time. Recursion builds on this to make functions even more powerful.

A recursive function is a function that calls itself with a different parameter. If it calls itself forever, the program will never end, so we need a "base case" to tell it when to stop.

That's all there is to it, but it is tricky to master.

### Factorial Example
When is recursion useful? Any time there is a problem that can be broken down into smaller identical sub-problems. 

For example factorial in math, written 5! This is 5x4x3x2x1. Do you see that 5!=5x4! ?

When should we tell the program to stop? Here is an implementation, [click here to visualize step by step](http://pythontutor.com/visualize.html#code=def%20factorial%28num%29%3A%0A%20%20%20%20if%20num%20%3D%3D%201%3A%0A%20%20%20%20%20%20%20%20return%201%0A%20%20%20%20return%20num%20*%20factorial%28num-1%29%0A%20%20%20%20%0Avalue%20%3D%20factorial%285%29%0Aprint%28value%29&cumulative=false&curInstr=0&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false). 

```python
def factorial(num):
  if num == 1:
    return 1
  return num * factorial(num-1)
	    
value = factorial(5)
print(value)
```

### Try These

Here are three more math problems:
- [Powers](https://codingbat.com/prob/p158888)
- [Triangle numbers](https://codingbat.com/prob/p194781)
- [Fibonacci numbers](https://codingbat.com/prob/p120015)

The CodingBat website is asking for Java code, so instead write your code in your regular Python environment (you don't need the CodingBat website to tell you if it is correct).

### Printing Recursively
Those examples were returning numbers. What if you just want to print? 

```python
def print_by_letter(string):
  print(string[0])
  if len(string) > 1:
    print_by_letter(string[1:]) #substring from 1 spot to end
  
print_by_letter("hello")
```

Notice that also handles the base case slightly differently, which is possible since no value is returned. In order to stop, it just doesn't make another recursive call.

This is a good example of something that can be solved by standard loops or with recursion. Often simple practice problems can be solved either way, but ask you to do it recursively for practice.

Can you write recursive functions to print the [hailstone sequence](https://github.com/BreakoutMentors/Python-Resources/blob/master/Text%20Output%20Programs/hailstone.md) and [juggler sequence](https://en.wikipedia.org/wiki/Juggler_sequence)?

## Fractals

Graphics + recursion = fractals

[Check out this example that is only recursive in the x direction](https://trinket.io/library/trinkets/eba0451250)

![1D Fractal](https://user-images.githubusercontent.com/1643783/85183847-1fd72300-b242-11ea-8333-edf117a92eaf.png)

How does it know when to stop? Can you make a different fractal for the x direction?

Let's make the jump to two dimensional fractals. It can be hard to achieve a desired fractal, however there are many cool things you can make just by experimenting. Here is one that is colorful.
<img src="https://user-images.githubusercontent.com/1643783/85184322-3da58780-b244-11ea-8533-812a018ff46c.png" width="400" height="400">

Here is a good one to try:

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Sierpinski_carpet_5.svg/500px-Sierpinski_carpet_5.svg.png" width="400" height="400">

Can you draw the square in the middle and make 8 recursive calls? Can you use size for the base case instead of manually counting the depth? Hint: it can be difficult to jump to a perfect resursive call each time. I sometimes like to manually calculate the locations for the next level, which helps me see the math equation for the recursive call.

Here is another classic one (you can do triangles or lines):

![Koch snowflake](https://upload.wikimedia.org/wikipedia/commons/f/fd/Von_Koch_curve.gif)


## Towers of Hanoi

This is a recursion example that seems pretty magical. It would be really hard to solve with loops, and it isn't intuitive the order of steps necessary, but a few lines of recursive code solves it with ease. 

![hanoi image](https://user-images.githubusercontent.com/1643783/85320541-12a37980-b478-11ea-9b11-aa8a36b40073.png)

```
    Our goal is to move the entire tower to the middle peg.
    We can only move one disk at a time.
    We can never place a larger disk on a smaller one. 
```

[Try solving it manually here with 4 or 5 discs](https://www.mathsisfun.com/games/towerofhanoi.html) (note that the goal on that website is to move everything to the third peg).

It is trivial with 2 discs: move the 0 (smallest) disc to the spare column, move the 1 (largest) disc to the destination column, move the 0 disc to the destination column. The same pattern holds with more discs: move the whole stack above the largest to the spare column, move the largest to the destination column, move the whole stack in the spare column to the destination column. [See the visual](https://www.cs.cmu.edu/~cburch/survey/recurse/hanoiimpl.html)

Here is some recursive psuedo-code:
```
FUNCTION MoveTower(disk, source, dest, spare):
IF disk == 0, THEN:
    move disk from source to dest
ELSE:
    MoveTower(disk - 1, source, spare, dest)
    move disk from source to dest
    MoveTower(disk - 1, spare, dest, source)
```
