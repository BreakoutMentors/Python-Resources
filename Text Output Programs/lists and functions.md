# Lists and Functions

## Lists

Up until now, we've stored things in one variable at a time. A list is a way to collect multiple values in one variable. Let's make a list:

```python
grocery_list = ["apples", "bananas", "peanut butter"]
```

We can look at specific items in the list with an index, which starts counting from zero.

```python
print grocery_list[0]
```

You can also get the length of a list using the `len()` function:

```python
print len(grocery_list)
```
<br><br>

## Functions

We have used a couple functions so far like `input()` and `len()`. Did you know you can create your own?

It is a powerful way to reuse the same code. Use the `def` keyword:

```python
def repeat_10_hellos():
  for x in range(10):
    print "hello"
```

```python
def repeat_hellos(num_repeats):
  for x in range(num_repeats):
    print "hello"
```

```python
def repeat_message(num_repeats, message):
  for x in range(num_repeats):
    print message
```

Once the function is `defined`, you can `call` it anywhere in your code:
```python
repeat_10_hellos()
```

```python
repeat_hellos(7)
```

```python
repeat_message(13, "goodbye")
```

Functions can also `return` a value:
```python
def square_number(num):
  squared = num * num
  return squared
```

```python
answer = squared(5)
```

