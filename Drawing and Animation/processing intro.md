# Processing Graphics Intro

## Setup
To use the graphics library, see [this project](https://trinket.io/library/trinkets/2a112790dc) with the basic structure.

You need to `import` the code, then create the `setup()` and `draw()` functions that will be used when you call `run()`.

Depending upon your computer screen size, `size(600, 400)` might be a good area for your drawings.

## Creating shapes
Try the following commands in `draw()`. What do each of the input numbers do?

```python
line(30, 250, 250, 30)
ellipse(200, 200, 20, 20)
rect(300, 100, 40, 40)
triangle(20, 20, 20, 40, 40, 40)
```



## Color
Computers use Red, Green, Blue numbers between 0 and 255. Go to http://colorpicker.com/ to determine the RGB values for the colors you want.

Color as a variable:
```python
c1 = color(240, 0, 50)
```

Background:
```python
background(0, 255, 208)
```
or:
```python
background(c1) #using a color variable
```

Shape color:
```python
fill(40, 200, 40)
```
```python
noFill()
```

Shape outline:
```python
stroke(120, 120, 120)
strokeWeight(5)
```
```python
noStroke()
```
