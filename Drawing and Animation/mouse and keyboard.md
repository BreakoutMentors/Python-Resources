# Mouse Inputs

These commands are part of the Processing graphics, so you need to import the library (see [this project](https://trinket.io/python/2a112790dc) with the basic structure).

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

## Other things to know

Can also get the previous x or y for when the mouse moves:
```python
print(mouse.px)
print(mouse.py)
``` 

