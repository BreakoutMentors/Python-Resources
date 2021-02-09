# Turtle Graphics

There is another way to use graphics in Trinket: with the turtle. It is similar to the Pen tool in Scratch, it can trace wherever you tell the turtle to go. [Check out this example with all the basic commands you need.](https://trinket.io/library/trinkets/e9439f4079) 

## Challenge 1: Make a Triangle and Square

Let's draw some shapes. See if you can create an equilateral triangle and a square using the forward and left or right commands. 

## Challenge 2: Use Loops and Functions

Did you use a loop to create those shapes? If not, that is fine, but update your code now. 

That will make this next step easier. Create a turtle_square function that takes one input for the side length. Now you can easily draw squares of different sizes all over the screen.

## Challenge 3: Generalize to a Polygon

What is the difference between the triangle code and the square code? How can we generalize to make a pentagon or any other polygon? Hint: what is the sum of all the turns?

See if you can make a turtle_polygon function that has two inputs: size and number of sides.

## Bonus challenge: Stars

Stars are not too different than polygons. 

## Bonus challenge: Spirals

What happens if you turn a fixed angle, but change the side length? What can you make?

Also try to make this from [How to Think Like a Computer Scientist: Learning with Python 3](http://openbookproject.net/thinkcs/python/english3e/functions.html) (hint: create the first, then change the turn angle):

![spirals](http://openbookproject.net/thinkcs/python/english3e/_images/tess_spirals.png)

## Bonus challenge: Overlapping Squares

Here is a fun challenge from [Vivax Solutions](https://www.vivaxsolutions.com/web/python-turtle.aspx):

![squares](https://raw.githubusercontent.com/BreakoutMentors/Python-Resources/master/Turtle%20Graphics/turtle%20squares.gif)

[Here is an example to get you started with filled in shapes.](https://trinket.io/library/trinkets/d53a2b5046) Be sure to use functions to keep your code short and readable. It will also allow you to easily switch to using a different shape if you want.

## Bonus challenge: Lists and Conditionals

You can of course still use lists and conditionals to make your turtle graphics more powerful. Here is a quick challenge to get you started.

Create a series of shapes in a row based on a list that contains the number of sides for each shape. For example: shapes = [4, 8, 3, 5, 10]. If the shape has an odd number of sides, make it blue. If an even number of sides, red.

## Bonus challenge: Working Clock

Did you know you can find out the current time from Python? 

```python
import datetime
current_time = datetime.datetime.now()
print(current_time.hour)
print(current_time.minute)
```
