# Mouse Inputs

## Mouse Location
```python
print(mouse.x)
print(mouse.y)
``` 


## Mouse Clicked
Has to go before the `run()` call.
```python
def mouseClicked():
  print "yes"
``` 

Or:
```python
print(mouse.pressed)
```
 
## Key Pressed
Has to go before the `run()` call.
```python
def keyPressed():
  print(keyboard.key)
```
Note: Keyboard input on Trinket seems to be broken. You can switch to using the [Processing IDE](https://processing.org/reference/environment/)

## Other things to know

Can also get the previous x or y for when the mouse moves:
```python
print(mouse.px)
print(mouse.py)
``` 

