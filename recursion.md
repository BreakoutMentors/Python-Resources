# Recursion

You know functions are a powerful way to write code once and use it many different times. Parameters make functions more useful by allowing for simple changes each time. Recursion builds on this to make functions even more powerful.

A recursive function is a function that calls itself with a different parameter. If it calls itself forever, the program will never end, so we need a "base case" to tell it when to stop.

That's all there is to it, but it is tricky to master.

### Factorial Example
When is recursion useful? Any time there is a problem that can be broken down into smaller sub-problems. 

For example factorial in math, written 5! This is 5x4x3x2x1. Do you see that 5!=5x4! 

And when should we tell the program to stop? Here is an implementation, [click here to visualize step by step](http://pythontutor.com/visualize.html#code=def%20factorial%28num%29%3A%0A%20%20%20%20if%20num%20%3D%3D%201%3A%0A%20%20%20%20%20%20%20%20return%201%0A%20%20%20%20return%20num%20*%20factorial%28num-1%29%0A%20%20%20%20%0Avalue%20%3D%20factorial%285%29%0Aprint%28value%29&cumulative=false&curInstr=0&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false). 

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

Can you write recursive functions to print the [hailstone sequence](https://github.com/BreakoutMentors/Python-Resources/blob/master/Text%20Output%20Programs/hailstone.md) and [juggler sequence](https://en.wikipedia.org/wiki/Juggler_sequence)?

## Fractals

Graphics + recursion = fractals

[Check out this example that is only recursive in the x direction](https://trinket.io/library/trinkets/eba0451250)

How does it know when to stop? 

Can you make a different fractal for the x direction?

