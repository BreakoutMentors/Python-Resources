# Archived Project Code

Archive of the Python source for every project referenced in the Python Plan doc or this repository. Projects live at `https://trinket.strivemath.org/library/trinkets/<id>` (Trinket is shutting down; StriveMath is the new home). Data files and images are not included — only code.


---

## 1-D fractal

Project: [`eba0451250`](https://trinket.strivemath.org/library/trinkets/eba0451250)


### main.py

```python
from processing import *

def setup():
  size(500, 150)
  frameRate(1)
  fill(0, 0, 0)


def recursiveCircle(x, size, count):
  if count == 0:
    return
  
  ellipse(x, 75, size, size) #y doesn't change
  
  #calculate the new size and move x
  new_size = size*2/3
  x_offest = size/2 + new_size/2 #radius of current circle plus new circle
  recursiveCircle(x + x_offest, new_size, count-1)

def draw():
  background(200, 50, 40)
  recursiveCircle(75, 150, 12)

run()
```


---

## 2048 finished

Project: [`f2cd62e71f`](https://trinket.strivemath.org/library/trinkets/f2cd62e71f)


### main.py

```python
#!/usr/bin/python3

from gameBoard import GameBoard
from processing import *
import random
import numpy as np


#non changing variables
title_bar_height = 23
squares_wide = 4
squares_high = 4
pixel_width = 360
pixel_height = 360
label_height = 25
undoButtonWidth = 50
undoButtonHeight = 18
undoButtonX = pixel_width - undoButtonWidth - 10
undoButtonY = 365


def setup():
  size(pixel_width, pixel_height + 25)
  background(255, 255, 255)
  textAlign(CENTER, CENTER)
  

#changing variables
class gameState():
  score = 0
gs = gameState()


#the gameBoard requires the pieces data in a 2D array, this starts it all with 0s
def makeBoard(width, height):
  board = []
  for col in range(width):
    board.append([])
    for row in range(height):
      board[col].append(0)
  return board


def keyPressed():
  if (keyboard.keyCode == UP):
    makeMove("UP")
  if (keyboard.keyCode == DOWN):
    makeMove("DOWN")
  if (keyboard.keyCode == LEFT):
    makeMove("LEFT")
  if (keyboard.keyCode == RIGHT):
    makeMove("RIGHT")


def mirrorBoard(board):
  new_board = []
  
  for col_index in range(squares_wide):
      old_col = [col[col_index] for col in board] #gets the column
      new_board.append(old_col)
  
  return new_board


def makeMove(move):
  #https://en.wikipedia.org/wiki/2048_(video_game)

  if move == "UP":
    #use mirrored board
    board = mirrorBoard(boardPieces)
    board = makeMoveHelper("LEFT", board)
    board = mirrorBoard(board)
  elif move == "DOWN":
    #use mirrored board
    board = mirrorBoard(boardPieces)
    board = makeMoveHelper("RIGHT", board)
    board = mirrorBoard(board)
  elif move == "RIGHT":
    #RIGHT just use regular board
    board = makeMoveHelper("RIGHT", boardPieces)
  else:
    #LEFT just use regular board
    board = makeMoveHelper("LEFT", boardPieces)
  
  #copy info into boardPieces being used to display
  for row in range(squares_high):
    boardPieces[row] = board[row]
  
  #add new piece
  createNewPiece()
  
  
#only for LEFT or RIGHT
def makeMoveHelper(move, board):
  
  for row in range(squares_high):
    #filter out 0s
    new_row = [value for value in board[row] if value != 0]

    #combine numbers
    skipNext = False #a given number will only be combined once
    if move == "LEFT":
      for i in range(len(new_row)-1):
        if not skipNext and new_row[i] == new_row[i+1]:
          new_row[i] = 0 #doesn't matter which one is zero if we filter 0s again after
          new_row[i+1] = new_row[i+1]*2
          skipNext = True
        else:
          skipNext = False
    else:
      #similar, but count backwards and subtract index
      for i in range(len(new_row)-1, 0, -1):
        if not skipNext and new_row[i] == new_row[i-1]:
          new_row[i] = 0 #doesn't matter which one is zero if we filter 0s again after
          new_row[i-1] = new_row[i-1]*2
          skipNext = True
        else:
          skipNext = False
      
    #filter out 0s
    new_row = [value for value in new_row if value != 0]

    #add zeros back
    while len(new_row) < 4:
      if move == "RIGHT":
        new_row.insert(0, 0) #insert at beginning for RIGHT
      else:
        new_row.append(0) #insert at end for LEFT

    board[row] = new_row
  
  return board    

  
def createNewPiece():
  
  #randomly a 2 or 4 in empty spot
  openSpots = []
  for col in range(squares_wide):
    for row in range(squares_high):
      if boardPieces[row][col] == 0:
        openSpots.append((row, col))

  if len(openSpots) > 0:
    selectedSpot = random.choice(openSpots)
    selectedValue = random.randint(1,2) * 2
    boardPieces[selectedSpot[0]][selectedSpot[1]] = selectedValue


boardPieces = makeBoard(squares_wide, squares_high)
createNewPiece()
gb = GameBoard(pixel_width, pixel_height, squares_wide, squares_high, boardPieces)


def draw():
  gb.draw()

  
run()
```


### gameBoard.py

```python
from processing import *


class GameBoard: 
  def __init__(self, pixel_width, pixel_height, squares_width, squares_height, boardPieces):
		self.pixel_width = pixel_width
		self.pixel_height = pixel_height
		self.squares_width = squares_width
		self.squares_height = squares_height
		self.square_pixels = pixel_width / squares_width
		self.board_pieces = boardPieces
		self.labelValue = ""


  def setLabel(self, newValue):
		self.labelValue = newValue


  def drawLabel(self):
    #cover up what was already there
    noStroke()
    fill(255, 255, 255)
    rect(0, self.pixel_height, self.pixel_width, self.pixel_height)
    
    fill(0, 0, 0)
    text(self.labelValue, self.pixel_width/2 - 25, self.pixel_height + 18)
		
		
  def drawSquareBackgroundColors(self):
		stroke(0, 0, 255)
		fill(255, 255, 255)
		strokeWeight(6)
		
		for i in range(self.squares_width):
			for j in range(self.squares_height):
				rect(i * self.square_pixels, j * self.square_pixels, self.square_pixels, self.square_pixels)

		
  def drawPieces(self):
    fill(0, 0, 0)
    textSize(36)
    for i in range(self.squares_width):
			for j in range(self.squares_height):
				piece = self.board_pieces[j][i]
				if(piece != 0):
				  text(str(piece), i*self.square_pixels + self.square_pixels/2, j*self.square_pixels + self.square_pixels/2)


  def draw(self): 
		self.drawSquareBackgroundColors()
		self.drawPieces()
		self.drawLabel()
```


---

## 2048 starter

Project: [`4485e3701d`](https://trinket.strivemath.org/library/trinkets/4485e3701d)


### main.py

```python
#!/usr/bin/python3

from gameBoard import GameBoard
from processing import *
import random
import numpy as np


#non changing variables
title_bar_height = 23
squares_wide = 4
squares_high = 4
pixel_width = 360
pixel_height = 360
label_height = 25
undoButtonWidth = 50
undoButtonHeight = 18
undoButtonX = pixel_width - undoButtonWidth - 10
undoButtonY = 365


def setup():
  size(pixel_width, pixel_height + 25)
  background(255, 255, 255)
  textAlign(CENTER, CENTER)
  

#changing variables
class gameState():
  score = 0
gs = gameState()


#the gameBoard requires the pieces data in a 2D array, this starts it all with 0s
def makeBoard(width, height):
  board = []
  for col in range(width):
    board.append([])
    for row in range(height):
      board[col].append(0)
  return board


def keyPressed():
  if (keyboard.keyCode == UP):
    makeMove("UP")
  if (keyboard.keyCode == DOWN):
    makeMove("DOWN")
  if (keyboard.keyCode == LEFT):
    makeMove("LEFT")
  if (keyboard.keyCode == RIGHT):
    makeMove("RIGHT")


def makeMove(move):
  #https://en.wikipedia.org/wiki/2048_(video_game)
  
  #YOUR CODE HERE
  pass

  
def createNewPiece():
  #randomly a 2 or 4 in empty spot
  
  #YOUR CODE HERE
  pass


boardPieces = makeBoard(squares_wide, squares_high)
createNewPiece()
gb = GameBoard(pixel_width, pixel_height, squares_wide, squares_high, boardPieces)


def draw():
  gb.draw()

  
run()
```


### gameBoard.py

```python
from processing import *


class GameBoard: 
  def __init__(self, pixel_width, pixel_height, squares_width, squares_height, boardPieces):
		self.pixel_width = pixel_width
		self.pixel_height = pixel_height
		self.squares_width = squares_width
		self.squares_height = squares_height
		self.square_pixels = pixel_width / squares_width
		self.board_pieces = boardPieces
		self.labelValue = ""


  def setLabel(self, newValue):
		self.labelValue = newValue


  def drawLabel(self):
    #cover up what was already there
    noStroke()
    fill(255, 255, 255)
    rect(0, self.pixel_height, self.pixel_width, self.pixel_height)
    
    fill(0, 0, 0)
    text(self.labelValue, self.pixel_width/2 - 25, self.pixel_height + 18)
		
		
  def drawSquareBackgroundColors(self):
		stroke(0, 0, 255)
		fill(255, 255, 255)
		strokeWeight(6)
		
		for i in range(self.squares_width):
			for j in range(self.squares_height):
				rect(i * self.square_pixels, j * self.square_pixels, self.square_pixels, self.square_pixels)

		
  def drawPieces(self):
    fill(0, 0, 0)
    textSize(36)
    for i in range(self.squares_width):
			for j in range(self.squares_height):
				piece = self.board_pieces[j][i]
				if(piece != 0):
				  text(str(piece), i*self.square_pixels + self.square_pixels/2, j*self.square_pixels + self.square_pixels/2)


  def draw(self): 
		self.drawSquareBackgroundColors()
		self.drawPieces()
		self.drawLabel()
```


---

## angle starter code

Project: [`9a3d7050a8`](https://trinket.strivemath.org/library/trinkets/9a3d7050a8)


### main.py

```python
from processing import *
import math

def setup():
  size(600, 600)
  strokeWeight(1)

#uses trig to determine other x, y point
def getPoint(x, y, angle, length):
  x2 = x + length*math.sin(math.radians(angle))
  y2 = y - length*math.cos(math.radians(angle))
  return (x2, y2)
  
  
def draw():
  background(0, 0, 0)
  stroke(255, 255, 255)

  #step 1
  point2 = getPoint(300, 600, 0, 300)
  line(300, 600, point2[0], point2[1])

  #step 2
  point3 = getPoint(point2[0], point2[1], 45, 150)
  point4 = getPoint(point2[0], point2[1], -45, 150)
  line(point2[0], point2[1], point3[0], point3[1])
  line(point2[0], point2[1], point4[0], point4[1])
  
  #step 3
  point5 = getPoint(point3[0], point3[1], 22.5, 75)
  point6 = getPoint(point3[0], point3[1], 67.5, 75)
  line(point3[0], point3[1], point5[0], point5[1])
  line(point3[0], point3[1], point6[0], point6[1])
  #and 2 more...

run()
```


---

## Animal hangman example

Project: [`accfa974b9`](https://trinket.strivemath.org/library/trinkets/accfa974b9)


### main.py

```python
#importing the time module
import time
import random

#welcoming the user
name = raw_input("What is your name?")

print "Hello, " + name, "time to play hangman!"

print ""

#wait for 1 second
time.sleep(1)

print "Start guessing..."
time.sleep(0.5)

#here we set the secret animal

word = random.choice(open("animals.txt").read().split()).lower()


#creates an variable with an empty value
guesses = ''

#determine the number of turns
turns = 10

# Create a while loop

#check if the turns are more than zero
while turns > 0:         

    # make a counter that starts with zero
    failed = 0             

    # for every character in secret_word    
    for char in word:      

    # see if the character is in the players guess
        if char in guesses:    
    
        # print then out the character
            print char,    

        else:
    
        # if not found, print a dash
            print "_",     
       
        # and increase the failed counter with one
            failed += 1    

    # if failed is equal to zero
    # print You Won
    if failed == 0:        
        print "You won"  

        # exit the script
        break              

    print

    # ask the user go guess a character
    guess = raw_input("guess a character:") 

    # set the players guess to guesses
    guesses += guess                    

    # if the guess is not found in the secret word
    if guess not in word:  
 
        # turns counter decreases with 1 (now 9)
        turns -= 1        
 
        # print wrong
        print "Wrong"    
 
        # how many turns are left
        print "You have", + turns, 'more guesses' 
 
        # if the turns are equal to zero
        if turns == 0:           
    
            # print "You Lose"
            print "You Lose, the word was", word
```


---

## Animation with lists

Project: [`cfa17d968e`](https://trinket.strivemath.org/library/trinkets/cfa17d968e)


### main.py

```python
from processing import *


def setup():
  frameRate(30)
  size(600, 400)
  noStroke()
    
    
def mouseClicked():
  gs.balls_x.append(mouse.x)
  gs.balls_y.append(mouse.y)
    


#changing variables
class gameState:
  balls_x = []
  balls_y = []
gs = gameState()


#non-changing variables


def moveEverything():
  #have to remember to delete from list after loop
  to_be_removed = []
  
  number_of_balls = len(gs.balls_x)
  for index in range(number_of_balls):
    #move each ball
    gs.balls_y[index] = gs.balls_y[index] + 2
  
    #mark to index to remove if it has moved off the screen
    if gs.balls_y[index] > 400:
      to_be_removed.append(index)
    
  #while there are things to be removed
  while len(to_be_removed) > 0:
    #pop the last index off the end
    index_to_remove = to_be_removed.pop()
    
    #delete from the lists at that index
    del gs.balls_x[index_to_remove]
    del gs.balls_y[index_to_remove]
    

def draw():
  moveEverything()
  
  background(233, 150, 200)
    
  fill(0, 0, 0)
  #draw each ball
  number_of_balls = len(gs.balls_x)
  for index in range(number_of_balls):
    ellipse(gs.balls_x[index], gs.balls_y[index], 30, 30)


    
run()
```


---

## Arkanoid (Finished)

Project: [`bb526f23ee60`](https://trinket.strivemath.org/library/trinkets/bb526f23ee60) (formerly `f3532e2f34`)


### main.py

```python
from processing import *
import random

def setup():
  size(400, 400) # Set size of screen to 400x400
  frameRate(60) # Set frame rate to 60
  noCursor() # Remove cursor icon while mouse in screen

class Paddle: # Also holds game data; only one instance
  x = 0
  y = 360
  width = 100
  height = 20
  direction = 0
  timer = 0
  shotTimer = 0
  score = 0

class Ball: # Only one instance
  x = 200
  y = 200
  xRange = range(-5, -3) + range(3, 5)
  yRange = range(3,4)
  yVel = random.choice(yRange)
  xVel = random.choice(xRange)
  
class Block:
  x = 0
  y = 0
  width = 50
  height = 15

class PowerUp:
  x = 0
  y = 0
  width = 15
  height = 10
  speed = 2
  timer = 240
  shotTimer = 45
  
class Laser:
  x = 0
  y = 0
  width = 4
  height = 8
  speed = 4

# Create instances and instance lists
paddle = Paddle()
ball = Ball()
blockList = []
powerUpList = []
laserList = []

# Adding in blocks with designated pattern
for i in range(24):
  blockList.append(Block())
for j in range(3):
  for i in range(8):
    blockList[j*8 + i].x = i * 50
    blockList[j*8 + i].y = j * 45
for i in range(4):
  temp = Block()
  temp.x = 50 + 100 * i
  temp.y = 30
  blockList.append(temp)
for i in range(4):
  temp = Block()
  temp.x = 100 * i
  temp.y = 60
  blockList.append(temp)

def mouseClicked(): # Creates laser when clicked during powerup
  if (paddle.timer > 0 and paddle.shotTimer == 0):
    createLaser()
    paddle.shotTimer = 45

def drawPaddle(): # Draws the players paddle, updates paddle movement
  fill(230, 40, 100)
  rect(mouse.x - 50, paddle.y, paddle.width, paddle.height)
  
  # Movement with mouse
  if (paddle.x > mouse.x - 50):
    paddle.direction = -1
  elif (paddle.x < mouse.x - 50):
    paddle.direction = 1
  else:
    paddle.direction = 0
  
  paddle.x = mouse.x - 50
  
  if (paddle.timer > 0): # lower power up timer
    paddle.timer -= 1
  if (paddle.shotTimer > 0):
    paddle.shotTimer -= 1

def drawBall(): # Draws ball and updates movement
  if (ball.x >= 390 or ball.x <= 10): # Bounce left right sides
    ball.xVel = -ball.xVel
  if (ball.y <= 10): # Bounce off top
    ball.yVel = - ball.yVel
    
  if (ball.y >= 390): # Reset if ball falls out of bottom
    ball.x = 200
    ball.y = 200
    paddle.score -= 100
  
  if (ball.x >= mouse.x - 50 and ball.x <= mouse.x + 50): # Bounce off paddle
    if (ball.y >= 350 and ball.y <= 350 + ball.yVel):
      ball.yVel = -ball.yVel
      ball.xVel += 2 * paddle.direction
      if (ball.xVel >= 5):
        ball.xVel = 5
      if (ball.xVel <= -5):
        ball.xVel = -5
  
  ball.y += ball.yVel
  ball.x += ball.xVel
  
  fill(100, 90, 250)
  ellipse(ball.x, ball.y, 20, 20)

def drawBlock(): # Draws each block within the blockList
  if blockList == []: # Ends game when no more blocks
    endGame()
  fill(50, 200, 100)
  
  for block in blockList:
    rect(block.x, block.y, block.width, block.height)
    if hitBlock(block): # Increasing score on hit
      paddle.score += 10
      if (random.randint(1,10) == 1):
        createPowerUp(block)
    for laser in laserList:
      laserHit(laser, block)
  
def drawPowerUp(): # Randomly places powerup, then draws and updates movement
  fill(random.randint(0, 255), random.randint(0, 255), random.randint(0, 255))
  for powerUp in powerUpList:
    ellipse(powerUp.x, powerUp.y, powerUp.width, powerUp.height)
    powerUp.y += 1
    hitPowerUp(powerUp)

    if (powerUp.y >= 400):
      powerUpList.remove(powerUp)

def drawLaser(): # Draws laser and updates movement
  fill(80, 100, 220)
  for laser in laserList:
    rect(laser.x, laser.y, laser.width, laser.height)
    laser.y -= laser.speed
    if (laser.y <= 0):
      laserList.remove(laser)

def drawText(): # Displays text
  fill(255, 255, 255)
  textSize(30)
  text(str(paddle.score), 340, 360)

def createPowerUp(block):
  temp = PowerUp()
  temp.x = block.x + block.width/2
  temp.y = block.y + block.height
  powerUpList.append(temp)

def createLaser():
  temp = Laser()
  temp.x = mouse.x
  temp.y = paddle.y - temp.height
  laserList.append(temp)

def hitBlock(block): #Checks the 4 sides of the ball if they touch block
  if (pointIntersect(ball.x, ball.y - 10, block.x, block.y, block.width, block.height)): #Top point
    blockList.remove(block)
    ball.yVel = -ball.yVel
    return True
  if (pointIntersect(ball.x, ball.y + 10, block.x, block.y, block.width, block.height)): #Bot point
    blockList.remove(block)
    ball.yVel = -ball.yVel
    return True
    
  if (pointIntersect(ball.x - 10, ball.y, block.x, block.y, block.width, block.height)): #Left point
    blockList.remove(block)
    ball.xVel = -ball.xVel
    return True
  if (pointIntersect(ball.x + 10, ball.y, block.x, block.y, block.width, block.height)): #Right point
    blockList.remove(block)
    ball.xVel = -ball.xVel
    return True

def hitPowerUp(powerUp): # Checks the 4 sides of the ball if they touch powerUp
  if (pointIntersect(powerUp.x, powerUp.y + 7, paddle.x, paddle.y, paddle.width, paddle.height)): #Top point
    paddle.timer = powerUp.timer
    powerUpList.remove(powerUp)
    return True
  if (pointIntersect(powerUp.x - 5, powerUp.y, paddle.x, paddle.y, paddle.width, paddle.height)): #Left point
    paddle.timer = powerUp.timer
    powerUpList.remove(powerUp)
    return True
  if (pointIntersect(powerUp.x + 5, powerUp.y, paddle.x, paddle.y, paddle.width, paddle.height)): #Right point
    paddle.timer = powerUp.timer
    powerUpList.remove(powerUp)
    return True

def laserHit(laser, block):
  if (pointIntersect(laser.x, laser.y, block.x, block.y, block.width, block.height)):
    blockList.remove(block)
    laserList.remove(laser)
    return True
  return False

def pointIntersect(px, py, rx, ry, width, height): # Check if point is in Rectangle given
  if (px >= rx and px <= rx + width):
    if (py >= ry and py <= ry + height):
      return True
  return False

def endGame(): # Finishing touches at the end
  ball.xVel = 0
  ball.yVel = 0
  fill(255, 255, 255)
  textSize(50)
  text("You Won!", 100, 100)

def draw(): # Runs every frame
  background(40)
  drawPaddle()
  drawBall()
  drawBlock()
  drawPowerUp()
  drawLaser()
  drawText()
  
run() # Starts by calling setup() once, then draw() every frame continuously
```


---

## Ball splitting animation

Project: [`13aab8492f`](https://trinket.strivemath.org/library/trinkets/13aab8492f)


### main.py

```python
from processing import *
import random, math


def setup():
  frameRate(30)
  size(600, 400)
  noStroke()
    
    
def mouseClicked():
  #check if it was within any balls
  number_of_balls = len(gs.balls_x)
  for index in range(number_of_balls):
    if pointInCircle(mouse.x, mouse.y, gs.balls_x[index], gs.balls_y[index], gs.balls_size[index]):
      #half the size
      gs.balls_size[index] = gs.balls_size[index]/2
      
      #change the direction
      gs.balls_speed_x[index] = randomDirection()
      gs.balls_speed_y[index] = randomDirection()
      
      #add a new ball
      newBall(gs.balls_x[index], gs.balls_y[index], gs.balls_size[index])



#changing variables
class gameState:
  balls_x = []
  balls_y = []
  balls_speed_x = []
  balls_speed_y = []
  balls_size = []
gs = gameState()


#non-changing variables


def randomDirection():
  return random.random() * random.randint(-3, 3)


def newBall(x, y, size):
  gs.balls_x.append(x)
  gs.balls_y.append(y)
  gs.balls_size.append(size)
  gs.balls_speed_x.append(randomDirection())
  gs.balls_speed_y.append(randomDirection()) 
newBall(300, 200, 320)


def moveEverything():
  #have to remember to delete from list after loop
  to_be_removed = []
  
  number_of_balls = len(gs.balls_x)
  for index in range(number_of_balls):
    #move each ball
    gs.balls_x[index] = gs.balls_x[index] + gs.balls_speed_x[index]
    gs.balls_y[index] = gs.balls_y[index] + gs.balls_speed_y[index]
  
    #make it appear on other side
    if gs.balls_y[index] > 420:
      gs.balls_y[index] = -20
    if gs.balls_y[index] < -20:
      gs.balls_y[index] = 420
    if gs.balls_x[index] > 620:
      gs.balls_x[index] = -20
    if gs.balls_x[index] < -20:
      gs.balls_x[index] = 620
    

def draw():
  moveEverything()
  
  background(0, 0, 0)
    
  fill(255, 255, 255)
  #draw each ball
  number_of_balls = len(gs.balls_x)
  for index in range(number_of_balls):
    ellipse(gs.balls_x[index], gs.balls_y[index], gs.balls_size[index], gs.balls_size[index])


def pointInCircle(pt_x, pt_y, circle_x, circle_y, circle_radius):
  dist = math.sqrt( (pt_x - circle_x)**2 + (pt_y - circle_y)**2 )
  if dist < circle_radius:
    return True
  else:
    return False
    
    
    
run()
```


---

## Blank card project

Project: [`f0c534af89`](https://trinket.strivemath.org/library/trinkets/f0c534af89)


### main.py

```python
from processing import *
from card import Card

#non-changing variables
width = 550
height = 450
card_width = 100
card_height = 145
deck = []

#changing variables


def createFullDeck():
  for suit in range(4):
    for value in range(13):
      next_card = Card(value + 1, suit + 1)
      next_card.loadImage()
      deck.append(next_card)
  
  #then add the special cases at the end
  for suit in range(4):
    next_card = Card(14, suit + 1)
    next_card.loadImage()
    deck.append(next_card)


def setup():
  size(width, height)
  frameRate(30)
  createFullDeck()
  

def draw():
  background(25, 205, 25)
  deck[13].draw(50, 50, card_width, card_height)
  selected_card = deck[52]
  selected_card.draw(250, 50, card_width, card_height)

run()
```


### card.py

```python
from processing import *

#From: https://opengameart.org/content/playing-cards-vector-png


class Card:
  def __init__(self, value, suit):
    self.value = value #will range 1 to 13
    self.suit = suit #will range 1 to 4
    
  def getSuitString(self):
    if self.suit == 1:
      return "clubs"
    elif self.suit == 2:
      return "spades"
    elif self.suit == 3:
      return "hearts"
    else:
      return "diamonds"

  
  def isFaceCard(self):
    #YOUR CODE HERE
    #you can add a lot of helper methods for your game
    pass

    
  def loadImage(self):
    #saved in http://breakoutmentors.com/wp-content/uploads/cards/
    #format: {value}_of_{suit}.png
    #e.g. http://breakoutmentors.com/wp-content/uploads/cards/10_of_clubs.png

    if self.value == 1: 
      value_string = "ace"
    elif self.value == 11: 
      value_string = "jack"
    elif self.value == 12: 
      value_string = "queen"
    elif self.value == 13: 
      value_string = "king"
    else:
      value_string = str(self.value)
      
    image_string = value_string + "_of_" + self.getSuitString() + ".png"
    
    #handle special cases
    if self.value == 14:
      if self.suit == 1:
        image_string = "back_of_playing_card.png"
      elif self.suit == 2:
        image_string = "final_deck.png"
      elif self.suit == 3:
        image_string = "black_joker.png"
      else:
        image_string = "red_joker.png"
        
    self.img = loadImage("http://breakoutmentors.com/wp-content/uploads/cards/"+image_string)
    
    
  def draw(self, x, y, width, height):
    image(self.img, x, y, width, height)

    
```


---

## Book number starter

Project: [`f66b6c5040`](https://trinket.strivemath.org/library/trinkets/f66b6c5040)


### main.py

```python
#A checksum is an algorithm to see if there are any errors copying a number

#ISBN, the International Standard Book Number, is a special code printed on most 
#books, such as: 0-20-508005-7 or 1-234-56789-X. The first 9 digits are assigned 
#by a book publisher, and the last symbol is computed from these previous digits 
#by a "weighted sum" as follows. 

  #First sum the first digit, and 2 times the second digit, plus 3 times the third 
  #digit, plus etc, to 9 times the ninth digit. Then divide this sum by 11 and if it
  #is less than 10 then the remainder becomes the checkSum; otherwise the checkSum 
  #is the symbol "X". 
  
  #For example: in the above case 0205008005 
  #sum = 0 + 2*2 + 4*5 + 6*8 + 9*5 = 0 + 4 + 20 + 48 + 45 = 117 
  #check = 117 % 11 = 7 

  #Similarly in the second example: 1-234-56789 the check sum is 10, so 'X'. 
  
#If the computed check is not equal to the check symbol, then there has been an 
#error in the digits. This provides a form of error detection.

digits = [0,2,0,5,0,0,8,0,0,5]

#YOUR CODE

#determine the sum

#divide by 11

#print the checksum digit





#From http://nifty.stanford.edu/2006/motil-bookcode/2.BookNumDefinition.pdf
```


---

## Breakout

Project: [`f0232106a4`](https://trinket.strivemath.org/library/trinkets/f0232106a4)


### main.py

```python
from processing import *
from classes import RGB
from classes import Ball
from classes import Block

#non-changing variables
width = 400
height = 500
spaceBetweenBricks = 5
numberOfBricks = 10
numberOfBrickRows = 10
spaceFromCeiling = 20 #space between the first row of bricks and the ceiling
brickWidth = (width-(numberOfBricks-1)*spaceBetweenBricks)/numberOfBricks
brickHeight = 10
brickColors = [RGB(255, 0,0), RGB(255, 183, 0), RGB(255,255,0), RGB(0,255,0), RGB(0,0,255), RGB(156,0,255), RGB(255, 0,0), RGB(255,183, 0), RGB(255,255,0), RGB(0,255,0)]


#changing variables
basketOfBricks= []
class gameState:
  score = 0
  lives = 3
  hasLost = False
  hasWon = False
gs = gameState()


#Paddle is an object of type Block, similar to the bricks
paddle = Block(width/2, height-50, 70, 20, RGB(255, 0, 255))
ball = Ball()


#initialize all the bricks
def setupBricks():
  for rowNumber in range(numberOfBrickRows):
      for brickNumber in range(numberOfBricks):
        brickColor = brickColors[rowNumber]
        brickY = spaceFromCeiling + (brickHeight+spaceBetweenBricks)*rowNumber
        brickX = (brickWidth+spaceBetweenBricks)*brickNumber
        basketOfBricks.append(Block(brickX, brickY, brickWidth, brickHeight, brickColor))


def drawBricks(): 
  for brickNumber in range(len(basketOfBricks)):
    brick = basketOfBricks[brickNumber]
    brick.draw()


def checkBrickCollisions(): 
  for brickNumber in range(len(basketOfBricks)):
    brick = basketOfBricks[brickNumber]
    if(ball.collidesWith(brick)):
      #could add logic for bricks that take multiple hits
      basketOfBricks.remove(brick)
      gs.score = gs.score + 10
      return #don't let it hit more than one brick at a time


def drawBall():
  noStroke()
  fill(ball.ballColor.r, ball.ballColor.g, ball.ballColor.b)
  ellipse(ball.ballX, ball.ballY, ball.ballWidth, ball.ballWidth)
  

def moveBall():
  ball.move()
  if(ball.shouldLoseLife()):
    gs.lives = gs.lives - 1
    ball.reset()


def displayText(message, x, y, isCentered):
  fill(0)
  textSize(25)
  textX = x
  if (isCentered):
    widthText = textWidth(message)
    textX = (width-widthText)/2
  text(message, textX, y)


def checkWinOrLose():
  if(gs.score == numberOfBricks*numberOfBrickRows*10):
    gs.hasWon = True
    
  if(gs.lives==0):
    gs.hasLost = True


def displayLabels():
  displayText("Score: "+ str(gs.score), 5, height-2, False)
  
  livesLabel = "Lives: " + str(gs.lives)
  displayText(livesLabel, width-textWidth(livesLabel)-5, height-2, False)
  
  if(gs.hasWon):
    displayText("You win!", 0, height/2, True)
  
  if(gs.hasLost):
    displayText("You lose!", 0, height/2, True)


def wipeScreen():
  background(240, 240, 240)


def setup():
  size(width, height)
  wipeScreen()
  setupBricks()
  frameRate(30)


def draw():
  if not gs.hasLost and not gs.hasWon:
    wipeScreen()
    
    #move everything
    moveBall()
    paddle.move(mouseX, height-50)

    #check conditions
    checkBrickCollisions()
    if(ball.collidesWith(paddle)):
      #always set the ball movement back up
      ball.speedY = -abs(ball.speedY)
    checkWinOrLose()
    
    #draw everything
    drawBricks()
    drawBall()
    paddle.draw()
    displayLabels()

      
run()
```


### classes.py

```python
from processing import *
import random

widthD = 400
heightD = 500


#/*******RGB Class**************/
class RGB:
  def __init__(self, r, g, b):
    self.r = r
    self.g = g
    self.b = b


#/*******Ball Class**************/
class Ball:
  def __init__(self):
    self.ballX = random.randint(0, widthD)
    self.ballY = heightD/2
    self.ballWidth = 16
    self.speedY = 4
    self.speedX = 4
    self.ballColor = RGB(255, 0, 0)

  def move(self):
    self.ballX += self.speedX
    self.ballY +=self.speedY
    
    #bounce off top and side walls
    if self.ballX > widthD - self.ballWidth/2:
      self.speedX  = -abs(self.speedX)
    if self.ballX < self.ballWidth/2:
       self.speedX = abs(self.speedX)
    if self.ballY < self.ballWidth/2:
      self.speedY= abs(self.speedY)
  
  def reset(self):
    self.ballX = random.randint(0, widthD)
    self.ballY = heightD/2
    self.speedY = 4
    self.speedX = 4
  
  def shouldLoseLife(self):
    if self.ballY > heightD - self.ballWidth/2:
        self.speedY = -abs(self.speedY)
        return True
    return False
    
  def collidesWith(self, brick):
    hasHitSomething = False
    
    #check the top of the ball
    if(pointInRect(self.ballX, self.ballY - self.ballWidth/2, brick.blockX, brick.blockY, brick.blockWidth, brick.blockHeight)):
        self.speedY = -self.speedY
        hasHitSomething = True
    
    #check the bottom of the ball
    elif(pointInRect(self.ballX, self.ballY + self.ballWidth/2, brick.blockX, brick.blockY, brick.blockWidth, brick.blockHeight)):
        self.speedY = -self.speedY
        hasHitSomething = True
      
    #check the right of the ball
    elif(pointInRect(self.ballX + self.ballWidth/2, self.ballY, brick.blockX, brick.blockY, brick.blockWidth, brick.blockHeight)):
        self.speedX = -self.speedX
        hasHitSomething = True
        
    #check the left of the ball
    elif(pointInRect(self.ballX - self.ballWidth/2, self.ballY, brick.blockX, brick.blockY, brick.blockWidth, brick.blockHeight)):
        self.speedX = -self.speedX
        hasHitSomething = True
        
    return hasHitSomething


#/*******Block Class**************/
class Block:
  def __init__(self, x, y, width, height, color):
    self.blockX = x
    self.blockY = y
    self.blockWidth = width
    self.blockHeight = height
    self.maxHits = 1
    self.hits = self.maxHits
    self.brickColor = color
    
   #this draws the block on the screen
  def draw(self):
    noStroke()
    fill(self.brickColor.r, self.brickColor.g, self.brickColor.b)
    rect(self.blockX, self.blockY, self.blockWidth, self.blockHeight)
  
  #this moves the block 
  #to be centered on X, Y coordinates
  def move(self, X, Y):
    self.blockX = X - self.blockWidth/2
    self.blockY = Y - self.blockHeight/2
    
    #prevents it from going off screen on the X direction
    if self.blockX + self.blockWidth > widthD:
      self.blockX = widthD - self.blockWidth
    elif self.blockX < 0:
      self.blockX = 0

    #prevents it from going off screen on the the Y direction
    if self.blockY + self.blockHeight > heightD:
      self.blockY=height-blockWidth
    elif self.blockY < 0:
      self.blockY = 0
  
  #allows you to change the number of times an individual block can be hit
  def setMaxHits(self, numberOfHits):
    self.maxHits=numberOfHits
    self.hits = self.maxHits
  
  #tells you if the brick can be hit more
  #returns 0 if the brick needs to be removed
  #useful if you want the brick hit multiple times
  def getHits(self):
   return self.hits


#generic collision detection function
def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False
    
    
    
    
```


---

## Button click example

Project: [`6038579a7a`](https://trinket.strivemath.org/library/trinkets/6038579a7a)


### main.py

```python
from processing import *

#a list of buttons - each button is a dictionary
buttons = [
  {'text': "Hello", 'x': 55, 'y': 300, 'width': 100, 'height': 50},
  {'text': "Goodbye", 'x': 155, 'y': 100, 'width': 90, 'height': 30},
  {'text': "Ouch!", 'x': 355, 'y': 200, 'width': 150, 'height': 150},
]

def setup():
  size(600, 400)
  background(200, 50, 0)
  frameRate(1)
  textSize(20)
  

def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


def mouseClicked():
  #check if it was inside each button
  for button in buttons:
    if pointInRect(mouse.x, mouse.y, button['x'], button['y'], button['width'], button['height']):
      print("Clicked: " + button['text'])


def draw():
  for button in buttons:
    fill(255, 255, 255)
    rect(button['x'], button['y'], button['width'], button['height'])
    fill(0, 0, 0)
    text(button['text'], button['x'] + 5, button['y'] + 20)


run()
```


---

## Click to Start Example

Project: [`c493716bfb`](https://trinket.strivemath.org/library/trinkets/c493716bfb)


### main.py

```python
from processing import *


def setup():
  frameRate(30)
  size(600, 400)
  noStroke()


def mouseClicked():
  gs.playing = True


#changing variables
class gameState:
  playing = False
gs = gameState()


def draw():
  background(150, 150, 250)
  
  if not gs.playing:
    #click to start label
    fill(0, 0, 0)
    textSize(40)
    text("CLICK TO START", 135, 150)
    return
  
  #playing label
  textSize(20)
  text("Playing", 5, 20)
  
  
run()
```


---

## Coin Flip

Project: [`4f47ed2fe1`](https://trinket.strivemath.org/library/trinkets/4f47ed2fe1)


### main.py

```python
import random

total_heads = 0
total_tails = 0

#create a function that randomly returns "heads" or "tails"
def flip_coin():
    return "tails"

#flip 1000 times
for n in range(1000):
    flip = flip_coin()
    if flip == "heads":
        total_heads = total_heads + 1
    else:
        total_tails = total_tails + 1

print "Heads flipped: ", total_heads
print "Tails flipped: ", total_tails
```


---

## Computer draws a star!! :))

Project: [`d4f7d9403494`](https://trinket.strivemath.org/library/trinkets/d4f7d9403494) (formerly `518448abf3`)


### main.py

```python
from processing import*

def setup():
  size(500,500)
  frameRate(10)

initial_count = 10
max_step = 25 

class winner:
  x = 100
  y = 200
  x_step = max_step
  y_step = 0
  count = 0
  branch = 1
w = winner()
    
def draw():
  fill(255,255,255)
  ellipse(w.x,w.y,50,50)
  
  if w.count <= 0:
    if w.branch == 1:
      w.x_step = max_step
      w.y_step = 0
    if w.branch == 2:
      w.x_step = - max_step 
      w.y_step = max_step / 2
    if w.branch == 3:
      w.x_step = max_step / 2
      w.y_step = - max_step
    if w. branch == 4:
      w.x_step = max_step / 2
      w.y_step = max_step
    if w.branch == 5:
      w.x_step = - max_step
      w.y_step = - max_step / 2
    if w.branch == 6:
      # resets the branches - finally works!!! :)))
      w.branch = 1 
      w.x_step = max_step
      w.y_step = 0
      
    w.count = initial_count
    w.branch = w.branch + 1
  
  w.x = w.x + w.x_step
  w.y = w.y + w.y_step
  w.count = w.count - 1
  
run()
```


---

## Connect Four

Project: [`e318316a53`](https://trinket.strivemath.org/library/trinkets/e318316a53)


### main.py

```python
from stack import Stack
from gameBoard import GameBoard
from processing import *

#non changing variables
title_bar_height = 23
squares_wide = 7
squares_high = 6
pixel_width = 420
pixel_height = 360
label_height = 25
undoButtonWidth = 50
undoButtonHeight = 18
undoButtonX = pixel_width - undoButtonWidth - 10
undoButtonY = 365


#changing variables
class gameState():
  gameOver = False
  isRedTurn = True
gs = gameState()
movesMade = Stack() #used to keep track of the column move order for undo button


#populates the list with 7 empty stacks, to represent the 7 columns of the game
def setupPieces():
  boardStacks = []
  for i in range(squares_wide):
    boardStacks.append(Stack())
  return boardStacks


#the gameBoard requires the pieces data in a 2D array, this starts it all with 0s
def makeBoard(width, height):
  board = []
  for col in range(width):
    board.append([])
    for row in range(height):
      board[col].append(0)

  return board

boardPieces = makeBoard(squares_wide, squares_high)
gb = GameBoard(pixel_width, pixel_height, squares_wide, squares_high, boardPieces)
boardStacks = setupPieces()


#converts the list of stacks (boardStacks) to the 2D array (boardPieces) used by the gameBoard
def stacksToGrid():
  for i in range(len(boardStacks)):
    stack = boardStacks[i]
    
    for j in range(stack.size()):
      value = stack.toList()[j]
      boardPieces[i][squares_high - j - 1] = value


def checkWinner():
  #checks if there is a vertical winner
  for row in range(len(boardPieces)):
    for col in range(len(boardPieces[row]) - 3):
			if(boardPieces[row][col] != 0 and boardPieces[row][col] == boardPieces[row][col+1] and boardPieces[row][col] == boardPieces[row][col+2] and boardPieces[row][col] == boardPieces[row][col+3]):
				gb.setSelectedSquares([(row, col), (row, col+1), (row, col+2), (row, col+3)])
				if (boardPieces[row][col] == 2):
					gb.setLabel("Red Wins!")
				if (boardPieces[row][col] == 1):
					gb.setLabel("Black Wins!")
				gs.gameOver = True

  #checks if there is a horizontal winner
  for col in range(len(boardPieces[0])):
	  for row in range(len(boardPieces) - 3):
	    if (boardPieces[row][col] != 0 and boardPieces[row][col] == boardPieces[row+1][col] and boardPieces[row][col] == boardPieces[row+2][col] and boardPieces[row][col] == boardPieces[row+3][col]):
				gb.setSelectedSquares([(row, col), (row+1, col), (row+2, col), (row+3, col)])
				if (boardPieces[row][col] == 2):
					gb.setLabel("Red Wins!")
				if (boardPieces[row][col] == 1):
					gb.setLabel("Black Wins!")
				gs.gameOver = True

  #checks if there is a left diagonal winner
  for row in range(len(boardPieces) - 3):
    for col in range(len(boardPieces[row]) - 3):
			if (boardPieces[row][col] != 0 and boardPieces[row][col] == boardPieces[row+1][col+1] and boardPieces[row][col] == boardPieces[row+2][col+2] and boardPieces[row][col] == boardPieces[row+3][col+3]):
				gb.setSelectedSquares([(row, col), (row+1, col+1), (row+2, col+2), (row+3, col+3)])
				if (boardPieces[row][col] == 2):
					gb.setLabel("Red Wins!")
				if (boardPieces[row][col] == 1):
					gb.setLabel("Black Wins!")
				gs.gameOver = True

  #checks if there is a right diagonal winner
  for row in range(len(boardPieces) - 3):
    for col in range(3, len(boardPieces[row])):
			if (boardPieces[row][col] != 0 and boardPieces[row][col] == boardPieces[row+1][col-1] and boardPieces[row][col] == boardPieces[row+2][col-2] and boardPieces[row][col] == boardPieces[row+3][col-3]):
				gb.setSelectedSquares([(row, col), (row+1, col-1), (row+2, col-2), (row+3, col-3)])
				if (boardPieces[row][col] == 2):
					gb.setLabel("Red Wins!")
				if (boardPieces[row][col] == 1):
					gb.setLabel("Black Wins!")
				gs.gameOver = True
	
	
def mousePressed():
  if not gs.gameOver:
    
    #if above the bottom bar
    if mouseY < pixel_height:
    
      pixels_per_square = pixel_width / squares_wide
      selectedColumn = int(mouseX / pixels_per_square)
      
      stack = boardStacks[selectedColumn]
      if stack.size() < squares_high:
        if gs.isRedTurn:
          stack.push(2)
          gb.setLabel("Black Turn")
        else:
          stack.push(1)
          gb.setLabel("Red Turn")
        
        movesMade.push(selectedColumn)
        stacksToGrid()
        checkWinner()
        gs.isRedTurn = not gs.isRedTurn
    
    else:
      #below bottom bar, check if hit the undo button
      if pointInRect(mouseX, mouseY, undoButtonX, undoButtonY, undoButtonWidth, undoButtonHeight):
        undoLastMove()


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


def undoLastMove():
  if movesMade.size() == 0:
    return
  
  #get the last column that was used
  column = movesMade.pop()
  
  #then remove it from the right column
  boardStacks[column].pop()
  
  #switch the turn
  if gs.isRedTurn:
    gb.setLabel("Black Turn")
  else:
    gb.setLabel("Red Turn")
  gs.isRedTurn = not gs.isRedTurn
  
  #stacksToGrid won't remove, have to do manually
  boardPieces[column][squares_high - boardStacks[column].size() - 1] = 0


def setup():
  size(pixel_width, pixel_height + 25)
  background(255, 255, 255)
  stacksToGrid()
  gb.setLabel("Red Turn")


def drawUndoButton():
  noFill()
  stroke(0, 0, 0)
  strokeWeight(1)
  rect(undoButtonX, undoButtonY, undoButtonWidth, undoButtonHeight)
  textSize(12)
  text("Undo", undoButtonX + 11, undoButtonY + 14)


def draw():
  gb.draw()
  drawUndoButton()
  
  
run()
```


### gameBoard.py

```python
from processing import *


class GameBoard: 
  def __init__(self, pixel_width, pixel_height, squares_width, squares_height, boardPieces):
		self.pixel_width = pixel_width
		self.pixel_height = pixel_height
		self.squares_width = squares_width
		self.squares_height = squares_height
		self.square_pixels = pixel_width / squares_width
		self.board_pieces = boardPieces
		self.selected_squares = []
		self.labelValue = ""


  def setSelectedSquares(self, sq_list):
		self.selected_squares = sq_list


  def setLabel(self, newValue):
		self.labelValue = newValue

	
  def drawSelectedSquares(self):
    noFill()
    stroke(255, 255, 0)
    strokeWeight(5)
    for i in range(len(self.selected_squares)):
      rect(self.selected_squares[i][0] * self.square_pixels, self.selected_squares[i][1] * self.square_pixels, self.square_pixels - 2, self.square_pixels - 2)


  def drawLabel(self):
    #cover up what was already there
    noStroke()
    fill(255, 255, 255)
    rect(0, self.pixel_height, self.pixel_width, self.pixel_height)
    
    fill(0, 0, 0)
    text(self.labelValue, self.pixel_width/2 - 25, self.pixel_height + 18)
		
		
  def drawSquareBackgroundColors(self):
		stroke(0, 0, 255)
		fill(255, 255, 255)
		strokeWeight(6)
		
		for i in range(self.squares_width):
			for j in range(self.squares_height):
				rect(i * self.square_pixels, j * self.square_pixels, self.square_pixels, self.square_pixels)

		
  def drawPieces(self):
		#leave a little space between the edge of the square
    pixelBuffer = 8
    diameter = self.square_pixels - pixelBuffer*2
			
    for i in range(self.squares_width):
			for j in range(self.squares_height):
				piece = self.board_pieces[i][j]
				if(piece != 0):
				  if(piece == 1):
				    fill(0, 0, 0)
				  if(piece == 2):
				    fill(255, 0, 0)
				  
				  noStroke()
				  ellipse(i * self.square_pixels + pixelBuffer + diameter/2, j*self.square_pixels + pixelBuffer + diameter/2, diameter, diameter)


  def draw(self): 
		self.drawSquareBackgroundColors()
		self.drawPieces()
		self.drawSelectedSquares()
		self.drawLabel()
```


### stack.py

```python
class Stack:
  def __init__(self):
    self.stack = []
  
  def toList(self):
    return self.stack
  
  def push(self, elem):
    self.stack.append(elem)
    
  def pop(self):
    return self.stack.pop()
    
  def size(self):
    return len(self.stack)
    
  def peek(self):
    return self.stack[self.size()- 1]
```


---

## Connect Four - starter code

Project: [`43efbd4b49`](https://trinket.strivemath.org/library/trinkets/43efbd4b49)


### main.py

```python
from stack import Stack
from gameBoard import GameBoard
from processing import *

#non changing variables
title_bar_height = 23
squares_wide = 7
squares_high = 6
pixel_width = 420
pixel_height = 360
label_height = 25
undoButtonWidth = 50
undoButtonHeight = 18
undoButtonX = pixel_width - undoButtonWidth - 10
undoButtonY = 365


#changing variables
class gameState():
  gameOver = False
  isRedTurn = True
gs = gameState()
movesMade = Stack() #used to keep track of the column move order for undo button


#populates the list with 7 empty stacks, to represent the 7 columns of the game
def setupPieces():
  boardStacks = []
  for i in range(squares_wide):
    boardStacks.append(Stack())
  return boardStacks


#the gameBoard requires the pieces data in a 2D array, this starts it all with 0s
def makeBoard(width, height):
  board = []
  for col in range(width):
    board.append([])
    for row in range(height):
      board[col].append(0)
  return board

boardPieces = makeBoard(squares_wide, squares_high)
gb = GameBoard(pixel_width, pixel_height, squares_wide, squares_high, boardPieces)
boardStacks = setupPieces()


#converts the list of stacks (boardStacks) to the 2D array (boardPieces) used by the gameBoard
def stacksToGrid():
  #YOUR CODE HERE
  #for each stack
    #copy the values to the boardPieces 2D array
  pass


def checkWinner():
  #checks if there is a vertical winner
  for row in range(len(boardPieces)):
    for col in range(len(boardPieces[row]) - 3):
			if(boardPieces[row][col] != 0 and boardPieces[row][col] == boardPieces[row][col+1] and boardPieces[row][col] == boardPieces[row][col+2] and boardPieces[row][col] == boardPieces[row][col+3]):
				gb.setSelectedSquares([(row, col), (row, col+1), (row, col+2), (row, col+3)])
				if (boardPieces[row][col] == 2):
					gb.setLabel("Red Wins!")
				if (boardPieces[row][col] == 1):
					gb.setLabel("Black Wins!")
				gs.gameOver = True

  #checks if there is a horizontal winner
  for col in range(len(boardPieces[0])):
	  for row in range(len(boardPieces) - 3):
	    if (boardPieces[row][col] != 0 and boardPieces[row][col] == boardPieces[row+1][col] and boardPieces[row][col] == boardPieces[row+2][col] and boardPieces[row][col] == boardPieces[row+3][col]):
				gb.setSelectedSquares([(row, col), (row+1, col), (row+2, col), (row+3, col)])
				if (boardPieces[row][col] == 2):
					gb.setLabel("Red Wins!")
				if (boardPieces[row][col] == 1):
					gb.setLabel("Black Wins!")
				gs.gameOver = True

  #checks if there is a left diagonal winner
  for row in range(len(boardPieces) - 3):
    for col in range(len(boardPieces[row]) - 3):
			if (boardPieces[row][col] != 0 and boardPieces[row][col] == boardPieces[row+1][col+1] and boardPieces[row][col] == boardPieces[row+2][col+2] and boardPieces[row][col] == boardPieces[row+3][col+3]):
				gb.setSelectedSquares([(row, col), (row+1, col+1), (row+2, col+2), (row+3, col+3)])
				if (boardPieces[row][col] == 2):
					gb.setLabel("Red Wins!")
				if (boardPieces[row][col] == 1):
					gb.setLabel("Black Wins!")
				gs.gameOver = True

  #checks if there is a right diagonal winner
  for row in range(len(boardPieces) - 3):
    for col in range(3, len(boardPieces[row])):
			if (boardPieces[row][col] != 0 and boardPieces[row][col] == boardPieces[row+1][col-1] and boardPieces[row][col] == boardPieces[row+2][col-2] and boardPieces[row][col] == boardPieces[row+3][col-3]):
				gb.setSelectedSquares([(row, col), (row+1, col-1), (row+2, col-2), (row+3, col-3)])
				if (boardPieces[row][col] == 2):
					gb.setLabel("Red Wins!")
				if (boardPieces[row][col] == 1):
					gb.setLabel("Black Wins!")
				gs.gameOver = True
	
	
def mousePressed():
  if not gs.gameOver:
    
    #if above the bottom bar
    if mouseY < pixel_height:
    
      pixels_per_square = pixel_width / squares_wide
      selectedColumn = int(mouseX / pixels_per_square)
      
      #YOUR CODE HERE
      #only allow a move if the column isn't full
      #push the right color on the right stack
      #use stacksToGrid and checkWinner functions
      #switch the turn and label
    
    else:
      #below bottom bar, check if hit the undo button
      if pointInRect(mouseX, mouseY, undoButtonX, undoButtonY, undoButtonWidth, undoButtonHeight):
        undoLastMove()


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


def undoLastMove():
  #YOUR CODE HERE
  #use movesMade and boardStacks to determine which piece to remove
  #(note: stacksToGrid doesn't remove, so you can either update the boardPieces 
  #2D array here OR updated stacksToGrid to clear everything first)
  pass


def setup():
  size(pixel_width, pixel_height + 25)
  background(255, 255, 255)
  stacksToGrid()
  gb.setLabel("Red Turn")


def drawUndoButton():
  noFill()
  stroke(0, 0, 0)
  strokeWeight(1)
  rect(undoButtonX, undoButtonY, undoButtonWidth, undoButtonHeight)
  textSize(12)
  text("Undo", undoButtonX + 11, undoButtonY + 14)


def draw():
  gb.draw()
  drawUndoButton()
  
  
run()
```


### gameBoard.py

```python
from processing import *


class GameBoard: 
  def __init__(self, pixel_width, pixel_height, squares_width, squares_height, boardPieces):
		self.pixel_width = pixel_width
		self.pixel_height = pixel_height
		self.squares_width = squares_width
		self.squares_height = squares_height
		self.square_pixels = pixel_width / squares_width
		self.board_pieces = boardPieces
		self.selected_squares = []
		self.labelValue = ""


  def setSelectedSquares(self, sq_list):
		self.selected_squares = sq_list


  def setLabel(self, newValue):
		self.labelValue = newValue

	
  def drawSelectedSquares(self):
    noFill()
    stroke(255, 255, 0)
    strokeWeight(5)
    for i in range(len(self.selected_squares)):
      rect(self.selected_squares[i][0] * self.square_pixels, self.selected_squares[i][1] * self.square_pixels, self.square_pixels - 2, self.square_pixels - 2)


  def drawLabel(self):
    #cover up what was already there
    noStroke()
    fill(255, 255, 255)
    rect(0, self.pixel_height, self.pixel_width, self.pixel_height)
    
    fill(0, 0, 0)
    text(self.labelValue, self.pixel_width/2 - 25, self.pixel_height + 18)
		
		
  def drawSquareBackgroundColors(self):
		stroke(0, 0, 255)
		fill(255, 255, 255)
		strokeWeight(6)
		
		for i in range(self.squares_width):
			for j in range(self.squares_height):
				rect(i * self.square_pixels, j * self.square_pixels, self.square_pixels, self.square_pixels)

		
  def drawPieces(self):
		#leave a little space between the edge of the square
    pixelBuffer = 8
    diameter = self.square_pixels - pixelBuffer*2
			
    for i in range(self.squares_width):
			for j in range(self.squares_height):
				piece = self.board_pieces[i][j]
				if(piece != 0):
				  if(piece == 1):
				    fill(0, 0, 0)
				  if(piece == 2):
				    fill(255, 0, 0)
				  
				  noStroke()
				  ellipse(i * self.square_pixels + pixelBuffer + diameter/2, j*self.square_pixels + pixelBuffer + diameter/2, diameter, diameter)


  def draw(self): 
		self.drawSquareBackgroundColors()
		self.drawPieces()
		self.drawSelectedSquares()
		self.drawLabel()
```


### stack.py

```python
class Stack:
  def __init__(self):
    self.stack = []
  
  def toList(self):
    return self.stack
  
  def push(self, elem):
    self.stack.append(elem)
    
  def pop(self):
    return self.stack.pop()
    
  def size(self):
    return len(self.stack)
    
  def peek(self):
    return self.stack[self.size()- 1]
```


---

## Conways Game of Life

Project: [`abe0e2ada3`](https://trinket.strivemath.org/library/trinkets/abe0e2ada3)


### main.py

```python
from processing import *
import random
from classes import GameBoard

#non changing variables
squares_wide = 45
squares_high = 40
pixel_width = 540
pixel_height = 480
square_pixels = pixel_width / squares_wide
playPauseButtonWidth = 160
playPauseButtonHeight = 25
playPauseButtonX = (pixel_width - playPauseButtonWidth)/2
playPauseButtonY = 485
clearButtonWidth = 80
clearButtonHeight = 25
clearButtonX = pixel_width - clearButtonWidth - 15
clearButtonY = 485


#changing variables
class gameState():
  isPaused = True
gs = gameState()


#create a 2D grid of zeros
def makePieces(width, height):
  pieces = []
  for col in range(width):
    pieces.append([])
    for row in range(height):
      pieces[col].append(0)
  return pieces


boardPieces = makePieces(squares_wide, squares_high)
gb = GameBoard(pixel_width, pixel_height, squares_wide, squares_high, boardPieces)


def mousePressed():
  #if paused you can click on the board to flip it
  if gs.isPaused:
    clickedX = int(mouseX / square_pixels)
    clickedY = int(mouseY / square_pixels)
    if clickedX < squares_wide and clickedY < squares_high:
      if boardPieces[clickedX][clickedY] == 0:
        boardPieces[clickedX][clickedY] = 1
      else:
        boardPieces[clickedX][clickedY] = 0
        
  #check if hit the pause/play button
  if pointInRect(mouseX, mouseY, playPauseButtonX, playPauseButtonY, playPauseButtonWidth, playPauseButtonHeight):
    gs.isPaused = not gs.isPaused
    
  #check if hit the clear button
  if pointInRect(mouseX, mouseY, clearButtonX, clearButtonY, clearButtonWidth, clearButtonHeight):
    clear()


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


def clear():
  for i in range(squares_wide):
    for j in range(squares_high):
				boardPieces[i][j] = 0

	
def drawButtons():
  noFill()
  rect(playPauseButtonX, playPauseButtonY, playPauseButtonWidth, playPauseButtonHeight)
  if gs.isPaused:
    label = "Start"
  else:
    label = "Pause"
    
  fill(0, 0, 0)
  textSize(25)
  widthText = textWidth(label)
  textX = (pixel_width-widthText)/2
  text(label, textX, playPauseButtonY + 22)
  
  noFill()
  rect(clearButtonX, clearButtonY, clearButtonWidth, clearButtonHeight)
  fill(0, 0, 0)
  textSize(18)
  text("Clear", clearButtonX + 18, clearButtonY + 19)

  
  
def setup():
  size(pixel_width, pixel_height + playPauseButtonHeight + 10)
  frameRate(2)


#Any live cell with fewer than two live neighbours dies, as if by underpopulation.
#Any live cell with two or three live neighbours lives on to the next generation.
#Any live cell with more than three live neighbours dies, as if by overpopulation.
#Any dead cell with exactly three live neighbours becomes a live cell, as if by reproduction.
def doStep():
  global boardPieces
  
  #start with a blank board
  newBoard = makePieces(squares_wide, squares_high)
  for i in range(squares_wide):
    for j in range(squares_high):
      isAlive = boardPieces[i][j] == 1
      numNeighbors = countNeighbors(i, j)
      
      if isAlive:
        if numNeighbors == 2 or numNeighbors == 3:
          newBoard[i][j] = 1
      else:
        if numNeighbors == 3:
          newBoard[i][j] = 1

  boardPieces = newBoard
  gb.setBoard(boardPieces)
  

#looks at all the neighbors, careful not to go out of bounds of 2D array
#if out of bounds, it should wrap around to check the other side
def countNeighbors(row, col):
  numNeighbors = 0
  
  #determine all 4 edges
  rowMinus = row - 1
  if rowMinus < 0:
    rowMinus = squares_wide - 1
  rowPlus = row + 1
  if rowPlus > squares_wide - 1:
    rowPlus = 0
  colMinus = col - 1
  if colMinus < 0:
    colMinus = squares_high - 1
  colPlus = col + 1
  if colPlus > squares_high - 1:
    colPlus = 0
    
  #check all 8 positions
  if boardPieces[rowMinus][col] == 1:
    numNeighbors += 1
  if boardPieces[rowPlus][col] == 1:
    numNeighbors += 1 
  if boardPieces[row][colMinus] == 1:
    numNeighbors += 1
  if boardPieces[row][colPlus] == 1:
    numNeighbors += 1
  if boardPieces[rowMinus][colMinus] == 1:
    numNeighbors += 1
  if boardPieces[rowMinus][colPlus] == 1:
    numNeighbors += 1
  if boardPieces[rowPlus][colMinus] == 1:
    numNeighbors += 1
  if boardPieces[rowPlus][colPlus] == 1:
    numNeighbors += 1 
  
  return numNeighbors
  

def draw():
  if not gs.isPaused:
    doStep()
    
  gb.drawBoard()
  drawButtons()
    
run()
  
```


### classes.py

```python
from processing import *

class GameBoard:
  def __init__(self, width, height, squares_wide, squares_high, boardPieces):
    self.pixel_width = width
    self.pixel_height = height
    self.squares_wide = squares_wide
    self.squares_high = squares_high
    self.boardPieces = boardPieces
    self.square_pixels = self.pixel_width / self.squares_wide
    
  def setBoard(self, newBoard):
    self.boardPieces = newBoard
    
  def drawPieces(self):
    fill(0, 0, 0)
    for i in range(self.squares_wide):
      for j in range(self.squares_high):
        piece = self.boardPieces[i][j]
        if piece == 1:
          rect(i * self.square_pixels, j*self.square_pixels, self.square_pixels, self.square_pixels)
    
  def drawBackground(self):
    background(255, 255, 255)
    fill(255, 255, 255)
    for i in range(self.squares_wide):
			for j in range(self.squares_high):
				rect(i * self.square_pixels, j * self.square_pixels, self.square_pixels, self.square_pixels)
				
  def drawBoard(self):
    self.drawBackground()
    self.drawPieces()
```


---

## Conways Game of Life - starter

Project: [`25cd8e0d44`](https://trinket.strivemath.org/library/trinkets/25cd8e0d44)


### main.py

```python
from processing import *
import random
from classes import GameBoard

#non changing variables
squares_wide = 45
squares_high = 40
pixel_width = 540
pixel_height = 480
square_pixels = pixel_width / squares_wide
playPauseButtonWidth = 160
playPauseButtonHeight = 25
playPauseButtonX = (pixel_width - playPauseButtonWidth)/2
playPauseButtonY = 485
clearButtonWidth = 80
clearButtonHeight = 25
clearButtonX = pixel_width - clearButtonWidth - 15
clearButtonY = 485


#changing variables
class gameState():
  isPaused = True
gs = gameState()


#create a 2D grid of zeros
def makePieces(width, height):
  pieces = []
  for col in range(width):
    pieces.append([])
    for row in range(height):
      pieces[col].append(0)
  return pieces


boardPieces = makePieces(squares_wide, squares_high)
gb = GameBoard(pixel_width, pixel_height, squares_wide, squares_high, boardPieces)


def mousePressed():
  #if paused you can click on the board to flip it
  if gs.isPaused:
    clickedX = int(mouseX / square_pixels)
    clickedY = int(mouseY / square_pixels)
    if clickedX < squares_wide and clickedY < squares_high:
      if boardPieces[clickedX][clickedY] == 0:
        boardPieces[clickedX][clickedY] = 1
      else:
        boardPieces[clickedX][clickedY] = 0
        
  #check if hit the pause/play button
  if pointInRect(mouseX, mouseY, playPauseButtonX, playPauseButtonY, playPauseButtonWidth, playPauseButtonHeight):
    gs.isPaused = not gs.isPaused
    
  #check if hit the clear button
  if pointInRect(mouseX, mouseY, clearButtonX, clearButtonY, clearButtonWidth, clearButtonHeight):
    clear()


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


def clear():
  for i in range(squares_wide):
    for j in range(squares_high):
				boardPieces[i][j] = 0

	
def drawButtons():
  noFill()
  rect(playPauseButtonX, playPauseButtonY, playPauseButtonWidth, playPauseButtonHeight)
  if gs.isPaused:
    label = "Start"
  else:
    label = "Pause"
    
  fill(0, 0, 0)
  textSize(25)
  widthText = textWidth(label)
  textX = (pixel_width-widthText)/2
  text(label, textX, playPauseButtonY + 22)
  
  noFill()
  rect(clearButtonX, clearButtonY, clearButtonWidth, clearButtonHeight)
  fill(0, 0, 0)
  textSize(18)
  text("Clear", clearButtonX + 18, clearButtonY + 19)

  
def setup():
  size(pixel_width, pixel_height + playPauseButtonHeight + 10)
  frameRate(2)


#Any live cell with fewer than two live neighbours dies, as if by underpopulation.
#Any live cell with two or three live neighbours lives on to the next generation.
#Any live cell with more than three live neighbours dies, as if by overpopulation.
#Any dead cell with exactly three live neighbours becomes a live cell, as if by reproduction.
def doStep():
  global boardPieces
  
  #start with a blank board
  newBoard = makePieces(squares_wide, squares_high)
  
  #YOUR CODE HERE

  boardPieces = newBoard
  gb.setBoard(boardPieces)
  

#looks at all the neighbors, careful not to go out of bounds of 2D array
#if out of bounds, it should wrap around to check the other side
def countNeighbors(row, col):
  numNeighbors = 0
  
  #YOUR CODE HERE
  
  return numNeighbors
  

def draw():
  if not gs.isPaused:
    doStep()
    
  gb.drawBoard()
  drawButtons()
    
run()
  
```


### classes.py

```python
from processing import *

class GameBoard:
  def __init__(self, width, height, squares_wide, squares_high, boardPieces):
    self.pixel_width = width
    self.pixel_height = height
    self.squares_wide = squares_wide
    self.squares_high = squares_high
    self.boardPieces = boardPieces
    self.square_pixels = self.pixel_width / self.squares_wide
    
  def setBoard(self, newBoard):
    self.boardPieces = newBoard
    
  def drawPieces(self):
    fill(0, 0, 0)
    for i in range(self.squares_wide):
      for j in range(self.squares_high):
        piece = self.boardPieces[i][j]
        if piece == 1:
          rect(i * self.square_pixels, j*self.square_pixels, self.square_pixels, self.square_pixels)
    
  def drawBackground(self):
    background(255, 255, 255)
    fill(255, 255, 255)
    for i in range(self.squares_wide):
			for j in range(self.squares_high):
				rect(i * self.square_pixels, j * self.square_pixels, self.square_pixels, self.square_pixels)
				
  def drawBoard(self):
    self.drawBackground()
    self.drawPieces()
```


---

## Country flag quiz

Project: [`deac809149`](https://trinket.strivemath.org/library/trinkets/deac809149)


### main.py

```python
import random as rd
import urllib.request
from datetime import datetime
import json
from processing import *

#a list of buttons - each button is a dictionary
buttons = [
  {'text': "Asia", 'x': 100, 'y': 216, 'width': 300, 'height': 35},
  {'text': "Africa", 'x': 100, 'y': 259, 'width': 300, 'height': 35},
  {'text': "Europe", 'x': 100, 'y': 302, 'width': 300, 'height': 35},
  {'text': "North America", 'x': 100, 'y': 345, 'width': 300, 'height': 35},
  {'text': "South America", 'x': 100, 'y': 388, 'width': 300, 'height': 35},
]

#country data by category is a map that will contain
gameData = {'mode': "category select", 
  "countryDataByCategory": {
    "Asia": [],
    "Africa": [],
    "Europe": [],
    "North America": [],
    "South America": [],
  }
}


def setup():
  size(500, 425)
  background(255, 255, 0)
  frameRate(1)
  textSize(26)
  loadCountryData()


def loadCountryData():
  url = "https://restcountries.com/v3.1/all?fields=flags,name,capital,subregion,population"
  all_countries = makeAPICall(url)
  processData(all_countries)
  

def makeAPICall(URL):
  req = urllib.request.urlopen(URL)
  html = req.read()
  values = json.loads(html)
  return values


def processData(data):
  for country in data:
    if 'subregion' not in country:
      continue #some don't have a subregion for some reason
    region = country['subregion']
    population = country['population']
  
    #only include countries with over 3M people
    if population > 3000000:
      category = getCategory(region)
      info = {
        'name': country['name']['common'],
        'flag': country['flags']['png'],
        'capital': country['capital'] #include this info in case we want it later
      }
      #add this info to our game data
      gameData['countryDataByCategory'][category].append(info)
      

#this groups the many sub-regions into the categories for our game
def getCategory(region):
  category = region
  if region == "Central America" or region == "Northern America" or region == "Caribbean":
    category = "North America"
  elif "Asia" in region or region == "Melanesia" or region == "Australia and New Zealand":
    category = "Asia"
  elif "Europe" in region: 
    category = "Europe"
  elif "Africa" in region:
    category = "Africa"
  return category


def showFlag(continent):
  global img
  global correct_answer
  gameData['mode'] = 'flag'
  
  #just use the data for that specific continent
  continentData = gameData['countryDataByCategory'][continent]
  selected_index = rd.randint(0, len(continentData)-1)
  correct_answer = continentData[selected_index]['name']
  
  #use the processing load image function
  img = loadImage(continentData[selected_index]['flag'])

  #prepare all random choices
  all_choices = [selected_index]
  while len(all_choices) < 5:
    index = rd.randint(0, len(continentData)-1)
    if not index in all_choices:
      all_choices.append(index)
  rd.shuffle(all_choices)
  
  #update the buttons
  for i in range(5):
    country = continentData[all_choices[i]]
    buttons[i]['text'] = country['name']


def draw():
  #always show the buttons
  for button in buttons:
    fill(255, 255, 255)
    rect(button['x'], button['y'], button['width'], button['height'])
    fill(0, 0, 0)
    text(button['text'], button['x'] + 6, button['y'] + 28)
  
  if gameData['mode'] == 'flag':
    image(img, 100, 5, 300, 200)


def mouseClicked():
  #check if it was inside each button
  for button in buttons:
    if pointInRect(mouse.x, mouse.y, button['x'], button['y'], button['width'], button['height']):
      if gameData['mode'] == "category select":
        showFlag(button['text'])
      elif gameData['mode'] == "flag":
        if button['text'] == correct_answer:
          print("YOU WIN")
        else:
          print("WRONG, it was " + correct_answer)


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


run()
```


---

## Dice Roll

Project: [`720d177859`](https://trinket.strivemath.org/library/trinkets/720d177859)


### main.py

```python
import random

totals = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

#function that rolls 2 dice and returns the sum of their rolls
def roll_dice():
    return 2

for i in range(1000):
    dice_total = roll_dice()
    totals[dice_total] = totals[dice_total] + 1

#why did we skip spots 0 and 1?
print "2 was rolled", totals[2], "times"
print "3 was rolled", totals[3], "times"
print "4 was rolled", totals[4], "times"
print "5 was rolled", totals[5], "times"
print "6 was rolled", totals[6], "times"
print "7 was rolled", totals[7], "times"
print "8 was rolled", totals[8], "times"
print "9 was rolled", totals[9], "times"
print "10 was rolled", totals[10], "times"
print "11 was rolled", totals[11], "times"
print "12 was rolled", totals[12], "times"
```


---

## Final Breakout

Project: [`71baa9a602a3`](https://trinket.strivemath.org/library/trinkets/71baa9a602a3) (formerly `d581ab9701`)


### main.py

```python
from processing import *
from classes import RGB
from classes import Ball
from classes import Block
import random

#non-changing variables
width = 400
height = 500
spaceBetweenBricks = 5
numberOfBricks = 10
numberOfBrickRows = 10
spaceFromCeiling = 20 #space between the first row of bricks and the ceiling
brickWidth = (width-(numberOfBricks-1)*spaceBetweenBricks)/numberOfBricks
brickHeight = 10
brickColors = [RGB(255, 0,0), RGB(255, 183, 0), RGB(255,255,0), RGB(0,255,0), RGB(0,0,255), RGB(156,0,255), RGB(255, 0,0), RGB(255,183, 0), RGB(255,255,0), RGB(0,255,0)]


#changing variables
basketOfBricks= []
class gameState:
  score = 0
  lives = 3
  hasLost = False
  hasWon = False
gs = gameState()


#Paddle is an object of type Block, similar to the bricks
paddle = Block(width/2, height-50, 70, 20, RGB(255, 0, 255))
balls = [Ball()]


#initialize all the bricks
def setupBricks():
  for rowNumber in range(numberOfBrickRows):
      for brickNumber in range(numberOfBricks):
        brickColor = brickColors[rowNumber]
        brickY = spaceFromCeiling + (brickHeight+spaceBetweenBricks)*rowNumber
        brickX = (brickWidth+spaceBetweenBricks)*brickNumber
        basketOfBricks.append(Block(brickX, brickY, brickWidth, brickHeight, brickColor))
        if(rowNumber == 0):
          basketOfBricks[len(basketOfBricks)-1].setMaxHits(3)


def drawBricks(): 
  for brickNumber in range(len(basketOfBricks)):
    brick = basketOfBricks[brickNumber]
    brick.draw()


def checkBrickCollisions(): 
  for brickNumber in range(len(basketOfBricks)):
    brick = basketOfBricks[brickNumber]
    for ball in balls:
      if(ball.collidesWith(brick)):
        #could add logic for bricks that take multiple hits
        if random.randint(0, 10) == 4:
          balls.append(Ball(ball.ballX, ball.ballY))
        
        brick.hits = brick.hits - 1
        if(brick.getHits() == 0):
          basketOfBricks.remove(brick)
          gs.score = gs.score + 10
          return #don't let it hit more than one brick at a time


def drawBalls():
  for ball in balls:
    noStroke()
    fill(ball.ballColor.r, ball.ballColor.g, ball.ballColor.b)
    ellipse(ball.ballX, ball.ballY, ball.ballWidth, ball.ballWidth)
  

def moveBalls():
  for ball in balls:
    ball.move()
    if(ball.shouldLoseLife()):
      gs.lives = gs.lives - 1
      ball.reset()


def displayText(message, x, y, isCentered):
  fill(0)
  textSize(25)
  textX = x
  if (isCentered):
    widthText = textWidth(message)
    textX = (width-widthText)/2
  text(message, textX, y)


def checkWinOrLose():
  if(gs.score == numberOfBricks*numberOfBrickRows*10):
    gs.hasWon = True
    
  if(gs.lives==0):
    gs.hasLost = True


def displayLabels():
  displayText("Score: "+ str(gs.score), 5, height-2, False)
  
  livesLabel = "Lives: " + str(gs.lives)
  displayText(livesLabel, width-textWidth(livesLabel)-5, height-2, False)
  
  if(gs.hasWon):
    displayText("You win!", 0, height/2, True)
  
  if(gs.hasLost):
    displayText("You lose!", 0, height/2, True)


def wipeScreen():
  background(240, 240, 240)


def setup():
  size(width, height)
  wipeScreen()
  setupBricks()
  frameRate(30)


def draw():
  if not gs.hasLost and not gs.hasWon:
    wipeScreen()
    
    #move everything
    moveBalls()
    paddle.move(mouseX, height-50)

    #check conditions
    checkBrickCollisions()
    for ball in balls:
      if(ball.collidesWith(paddle)):
      #always set the ball movement back up
        ball.speedY = -abs(ball.speedY)
      checkWinOrLose()
    
    #draw everything
    drawBricks()
    drawBalls()
    paddle.draw()
    displayLabels()

      
run()
```


### classes.py

```python
from processing import *
import random

widthD = 400
heightD = 500


#/*******RGB Class**************/
class RGB:
  def __init__(self, r, g, b):
    self.r = r
    self.g = g
    self.b = b


#/*******Ball Class**************/
class Ball:
  def __init__(self, x = random.randint(0, widthD), y = heightD/2):
    self.ballX = x
    self.ballY = y
    self.ballWidth = 16
    self.speedY = 4
    self.speedX = random.randrange(3, 5)
    if(random.randint(1,2) == 1):
      self.speedX = -self.speedX
    self.ballColor = RGB(255, 0, 0)

  def move(self):
    self.ballX += self.speedX
    self.ballY +=self.speedY
    
    #bounce off top and side walls
    if self.ballX > widthD - self.ballWidth/2:
      self.speedX  = -abs(self.speedX)
    if self.ballX < self.ballWidth/2:
       self.speedX = abs(self.speedX)
    if self.ballY < self.ballWidth/2:
      self.speedY= abs(self.speedY)
  
  def reset(self):
    self.ballX = random.randint(0, widthD)
    self.ballY = heightD/2
    self.speedY = 4
    self.speedX = 4
  
  def shouldLoseLife(self):
    if self.ballY > heightD - self.ballWidth/2:
        self.speedY = -abs(self.speedY)
        return True
    return False
    
  def collidesWith(self, brick):
    hasHitSomething = False
    
    #check the top of the ball
    if(pointInRect(self.ballX, self.ballY - self.ballWidth/2, brick.blockX, brick.blockY, brick.blockWidth, brick.blockHeight)):
        self.speedY = -self.speedY
        hasHitSomething = True
    
    #check the bottom of the ball
    elif(pointInRect(self.ballX, self.ballY + self.ballWidth/2, brick.blockX, brick.blockY, brick.blockWidth, brick.blockHeight)):
        self.speedY = -self.speedY
        hasHitSomething = True
      
    #check the right of the ball
    elif(pointInRect(self.ballX + self.ballWidth/2, self.ballY, brick.blockX, brick.blockY, brick.blockWidth, brick.blockHeight)):
        self.speedX = -self.speedX
        hasHitSomething = True
        
    #check the left of the ball
    elif(pointInRect(self.ballX - self.ballWidth/2, self.ballY, brick.blockX, brick.blockY, brick.blockWidth, brick.blockHeight)):
        self.speedX = -self.speedX
        hasHitSomething = True
        
    return hasHitSomething


#/*******Block Class**************/
class Block:
  def __init__(self, x, y, width, height, color):
    self.blockX = x
    self.blockY = y
    self.blockWidth = width
    self.blockHeight = height
    self.maxHits = 1
    self.hits = self.maxHits
    self.brickColor = color
    
   #this draws the block on the screen
  def draw(self):
    noStroke()
    fill(self.brickColor.r, self.brickColor.g, self.brickColor.b)
    rect(self.blockX, self.blockY, self.blockWidth, self.blockHeight)
  
  #this moves the block 
  #to be centered on X, Y coordinates
  def move(self, X, Y):
    self.blockX = X - self.blockWidth/2
    self.blockY = Y - self.blockHeight/2
    
    #prevents it from going off screen on the X direction
    if self.blockX + self.blockWidth > widthD:
      self.blockX = widthD - self.blockWidth
    elif self.blockX < 0:
      self.blockX = 0

    #prevents it from going off screen on the the Y direction
    if self.blockY + self.blockHeight > heightD:
      self.blockY=height-blockWidth
    elif self.blockY < 0:
      self.blockY = 0
  
  #allows you to change the number of times an individual block can be hit
  def setMaxHits(self, numberOfHits):
    self.maxHits=numberOfHits
    self.hits = self.maxHits
  
  #tells you if the brick can be hit more
  #returns 0 if the brick needs to be removed
  #useful if you want the brick hit multiple times
  def getHits(self):
   return self.hits


#generic collision detection function
def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False
    
    
    
    
```


---

## FINISHED - Jump Game

Project: [`d502a87209`](https://trinket.strivemath.org/library/trinkets/d502a87209)


### main.py

```python
from processing import *


def setup():
  frameRate(30)
  size(600, 400)
  noStroke() 
  
  #non-changing variables
  global bg_color, ground_color, mario_img, goomba_img
  bg_color = color(181, 237, 254)
  ground_color = color(97, 68, 59)
  #image variables
  global mario_img, goomba_img
  mario_url = "https://s-media-cache-ak0.pinimg.com/originals/ae/4e/18/ae4e18b06e042a7661ddb0bd288a6738.png"
  mario_img = loadImage(mario_url)
  goomba_url = "https://i.ibb.co/k006c2Z/pngguru-com.png"
  goomba_img = loadImage(goomba_url)
    
def keyPressed():
  if keyboard.key == " " and gs.mario_state == "ground":
    gs.mario_state = "up"



#changing variables
class gameState:
  mario_x = 50
  mario_y = 255
  mario_state = "ground"
  goomba_x = 500
  goomba_speed = 6
  score = 0
gs = gameState()




def moveEverything():
  #move goomba
  gs.goomba_x = gs.goomba_x - gs.goomba_speed 
  if gs.goomba_x < -50:
    gs.goomba_x = 600
    gs.score = gs.score + 1

  #move mario up
  if gs.mario_state == "up":
    gs.mario_y = gs.mario_y - 10
    #if he gets to the top, start moving back down next time
    if gs.mario_y < 100:
      gs.mario_state = "down"
     
  #move mario down 
  if gs.mario_state == "down":
    gs.mario_y = gs.mario_y + 10
    #if he gets to the bottom stop
    if gs.mario_y > 250:
      gs.mario_state = "ground"

  if marioHitGoomba():
    gs.goomba_speed = 0


def draw():
  moveEverything()
  
  background(bg_color)
  
  #draw ground
  fill(ground_color)
  rect(0, 350, 600, 100)
  
  #draw mario
  image(mario_img, gs.mario_x, gs.mario_y, 75, 110)
  
  #draw goomba
  image(goomba_img, gs.goomba_x, 302, 50, 50)

  #draw score label
  textSize(20)
  fill(0, 0, 0)
  text("Score: " + str(gs.score), 5, 20)


def marioHitGoomba():
  #check if either corners hit goomba rect
  corner1 = pointInRect(gs.mario_x + 5, gs.mario_y + 90, gs.goomba_x, 315, 50, 50)
  corner2 = pointInRect(gs.mario_x + 55, gs.mario_y + 90, gs.goomba_x, 315, 50, 50)
  return corner1 or corner2


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


run()
```


---

## FINISHED - Keep the Ball Up Game

Project: [`c1e47938a0`](https://trinket.strivemath.org/library/trinkets/c1e47938a0)


### main.py

```python
from processing import *


def setup():
    frameRate(30)
    size(600, 400)
    noStroke()
    

#changing variables
class gameState:
  ball_x = 50
  ball_y = 100
  ball_speed_x = 5
  ball_speed_y = 3
  paddle_x = 250
  score = 0
gs = gameState()


#non-changing variables
ball_size = 25
paddle_y = 370
paddle_width = 80
paddle_height = 20
    

def moveEverything():
  #move in x and y
  gs.ball_x = gs.ball_x + gs.ball_speed_x
  gs.ball_y = gs.ball_y + gs.ball_speed_y

  #bounce off edges
  #sides
  if gs.ball_x + ball_size/2 > 600 or gs.ball_x < ball_size/2:
    gs.ball_speed_x = -gs.ball_speed_x
  #top
  if gs.ball_y < ball_size/2:
    gs.ball_speed_y = -gs.ball_speed_y
  #bottom
  if gs.ball_y > 400:
    gs.ball_y = 50
    gs.score = 0
    
  #move paddle
  gs.paddle_x = mouse.x
  if mouse.x > 600 - paddle_width:
    gs.paddle_x = 600 - paddle_width
    
  #bounce off paddle
  if ballHitPaddle():
    gs.ball_speed_y = -gs.ball_speed_y
    gs.score = gs.score + 1


def draw():
  moveEverything()
  
  bg_color = color(250, 250, 150)
  background(bg_color)
  
  #draw ball
  ball_color = color(200, 20, 60)
  fill(ball_color)
  ellipse(gs.ball_x, gs.ball_y, ball_size, ball_size)
  
  #draw paddle
  paddle_color = color(0, 0, 0)
  fill(paddle_color)
  rect(gs.paddle_x, paddle_y, paddle_width, paddle_height)

  #draw score label
  textSize(20)
  text("Score: " + str(gs.score), 5, 20)


def ballHitPaddle():
  #check if the point directly below the ball is inside the paddle rectangle
  return pointInRect(gs.ball_x, gs.ball_y + ball_size/2, gs.paddle_x, paddle_y, paddle_width, paddle_height)


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False

run()
```


---

## FINISHED - Snake Game

Project: [`585b9609a0`](https://trinket.strivemath.org/library/trinkets/585b9609a0)


### main.py

```python
from processing import *
import random


def setup():
    frameRate(30)
    size(600, 400)
    noStroke()


#keyPressed has to go before run()
def keyPressed():
  #keyboard.key doesn't work for arrow keys, have to use keyCode
  if keyboard.keyCode == 37:
    gs.direction = "L"
  if keyboard.keyCode == 38:
    gs.direction = "U"
  if keyboard.keyCode == 39:
    gs.direction = "R"
  if keyboard.keyCode == 40:
    gs.direction = "D"


def mouseClicked():
  gs.playing = True


#changing variables
class gameState:
  snake_x = 20
  snake_y = 200
  direction = "R"
  target_x = 360
  target_y = 360
  score = 0
  playing = False
  speed = 5
gs = gameState()


#non-changing variables
snake_size = 20
target_size = 20


def moveEverything():
  #move the snake the correct direction
  if gs.direction == "R":
    gs.snake_x = gs.snake_x + gs.speed
  elif gs.direction == "L":
    gs.snake_x = gs.snake_x - gs.speed
  elif gs.direction == "U":
    gs.snake_y = gs.snake_y - gs.speed
  elif gs.direction == "D":
    gs.snake_y = gs.snake_y + gs.speed
  
  #end game if you hit the edge
  #right edge
  if gs.snake_x + snake_size > 600:
    restart()
  elif gs.snake_x < 0:
    restart()
  elif gs.snake_y < 0:
    restart()
  elif gs.snake_y + snake_size > 400:
    restart()


def restart():
  gs.score = 0
  gs.playing = False
  gs.direction = "R"
  gs.snake_x = 20
  gs.snake_y = 200
  gs.speed = 5
  

def newTargetLocation():
  gs.target_x = random.randint(0, 600 - snake_size)
  gs.target_y = random.randint(0, 400 - snake_size)

  
def draw():
  background(150, 250, 250)
  
  if not gs.playing:
    #click to start label
    fill(0, 0, 0)
    textSize(40)
    text("CLICK TO START", 135, 150)
    return
  
  moveEverything()
  
  if snakeHitTarget():
    newTargetLocation()
    gs.score = gs.score + 1
    gs.speed = gs.speed + .2
  
  #draw snake
  snake_color = color(250, 20, 30)
  fill(snake_color)
  rect(gs.snake_x, gs.snake_y, snake_size, snake_size)
  
  #draw target
  fill(0, 0, 0)
  rect(gs.target_x, gs.target_y, target_size, target_size)
  
  #draw score label
  textSize(20)
  text("Score: " + str(gs.score), 5, 20)
  
  
def snakeHitTarget():
  #check if any of the 4 corners of the snake are inside the target
  
  #top left corner
  if pointInRect(gs.snake_x - 1, gs.snake_y - 1, gs.target_x, gs.target_y, target_size, target_size):
    return True
  #top right corner
  elif pointInRect(gs.snake_x - 1, gs.snake_y - 1 + snake_size, gs.target_x, gs.target_y, target_size, target_size):
    return True
  #bottom left corner
  elif pointInRect(gs.snake_x - 1 + snake_size, gs.snake_y - 1, gs.target_x, gs.target_y, target_size, target_size):
    return True
  #bottom right corner
  elif pointInRect(gs.snake_x + snake_size - 1, gs.snake_y + snake_size - 1, gs.target_x, gs.target_y, target_size, target_size):
    return True
  else:
    return False

  
def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


run()
```


---

## First animation

Project: [`2081273daf`](https://trinket.strivemath.org/library/trinkets/2081273daf)


### main.py

```python
from processing import *


def setup():
  frameRate(30)
  size(600, 400)
  noStroke()


#changing variables
class gameState:
  ball_x = 20
  ball_y = 50
gs = gameState()


#non-changing variables
ball_size = 25


def moveEverything():
  gs.ball_x = gs.ball_x + 5

  #conditionals for if it hits edge


def draw():
  moveEverything()
  
  background(100, 150, 200)
    
  fill(0, 230, 0)
  ellipse(gs.ball_x, gs.ball_y, ball_size, ball_size)


run()
```


---

## First shapes

Project: [`2a112790dc`](https://trinket.strivemath.org/library/trinkets/2a112790dc)


### main.py

```python
# Graphics Library
from processing import *


# Function that happens one time at the beginning
def setup():
  # Size actually makes the screen, (width, height)
  size(400, 400)
  frameRate(30)
  background(0, 255, 208)
  

# Function that happens continuously
def draw():
  background(0, 255, 208)
  line(30, 250, 250, 30)
  ellipse(200, 200, 20, 20)
  rect(300, 100, 40, 40)
  triangle(20, 20, 20, 40, 40, 40)


run()
```


---

## Flappy Bird (Finished)

Project: [`922d43ae474f`](https://trinket.strivemath.org/library/trinkets/922d43ae474f) (formerly `abc1389776`)


### main.py

```python
from processing import *
import random

def setup(): # Runs once at the beginning
  size(400, 400) # Creates screen with size (x, y)
  frameRate(60) # 60 frames per second
  noStroke() # Removes outlines of drawn shapes
  
class Game(): # Class to store game info
  counter = 240 # Timer of how often to create obstacles
  maxCounter = 240 # Max timer length for creating obstacles
  score = 0
  powerUpCounter = 0 # Timer for how long to keep powerup
  
class Guy(): # Main character
  x = 180
  y = 20
  xVel = 0
  yVel = 0
  xAcc = 0
  yAcc = 0
  width = 30
  height = 40
  jumped = False
  alive = True
  
class PowerUp():
  x = 0
  y = 0
  width = 20
  height = 20
  
class Wall():
  x = 0
  y = 0
  width = 50
  height = 0
  passed = False
  
# Creating instances and instance lists
guy = Guy()
walls = []
powerups = []

### Utility Functions

def pointInRect(x, y, w, h, px, py): # Returns whether or not px,py is within given rectangle(x, y, width, height)
  if (px >= x and px <= x + w):
    if (py >= y and py <= y + h):
      return True
  return False

def rectCollision(x1, y1, w1, h1, x2, y2, w2, h2): # Returns whether 2 rectangels collide, larger rectangle first
  if (pointInRect(x1, y1, w1, h1, x2, y2)): # top left
    return True
  
  if (pointInRect(x1, y1, w1, h1, x2 + w2, y2)): # top right
    return True
  
  if (pointInRect(x1, y1, w1, h1, x2, y2 + h2)): # bottom left
    return True
  
  if (pointInRect(x1, y1, w1, h1, x2 + w2, y2 + h2)): # bottom right
    return True
  
  return False

def keyPressed(): # Map space bar to jump
  if (keyboard.keyCode == 32):
    guy.jumped = True
    
def mouseClicked():
  guy.jumped = True

def updatePhysics(): # Updates x,y of main character based on physics
  guy.yAcc = .15 # gravity
  
  if (guy.y+35 >= 290 and guy.y+35 <= 310):
    guy.yVel = 0
    guy.yAcc = 0
    
  if (guy.jumped == True):
    guy.yVel = -3
    guy.jumped = False
  
  guy.yVel += guy.yAcc
  guy.xVel += guy.xAcc
  guy.x += guy.xVel
  guy.y += guy.yVel


def genWall(): # Create pair of walls with random height
  if Game.counter == 0 and guy.alive:
    tempWall = Wall()
    tempWall.x = 400
    tempWall.y = random.randint(75, 275)
    tempWall.height = 300 - tempWall.y
    
    tempWall2 = Wall()
    tempWall2.x = 400
    tempWall2.y = 0
    tempWall2.height = tempWall.y - 100
    
    walls.append(tempWall)
    walls.append(tempWall2)
    
    Game.counter = Game.maxCounter
    Game.maxCounter -= 10
  else:
    Game.counter -= 1

def genPowerUp(): # Randomly create powerup
  if (Game.counter == Game.maxCounter / 2 and guy.alive):
    if (random.randint(1, 7) == 1):
      tempPowerUp = PowerUp()
      tempPowerUp.x = 400 + 25
      tempPowerUp.y = random.randint(50, 250)
      powerups.append(tempPowerUp)
  
def wallCollision(): # Detects collision with every wall
  for wall in walls:
    if (rectCollision(wall.x, wall.y, wall.width, wall.height, guy.x, guy.y, guy.width, guy.height)):
      guy.alive = False

def powerCollision(): # Detects collision with powerups
  for powerup in powerups:
    if (rectCollision(guy.x, guy.y, guy.width, guy.height, powerup.x, powerup.y, powerup.width, powerup.height)):
      powerups.remove(powerup)
      Game.powerUpCounter = 480

def pointCount(): # Adds point for every wall that passes
  for wall in walls:
    if (not wall.passed):
      if (wall.x <= guy.x):
        wall.passed = True
        Game.score += 1

### Draw Functions

def drawGuy(): # Draw and update position of main character
  if (guy.alive):
    if (Game.powerUpCounter > 0):
      Game.powerUpCounter -= 1 
      guy.width = 15
      guy.height = 20
    if (Game.powerUpCounter == 0):
      guy.width = 30
      guy.height = 40
    fill(40, 40, 120)
    rect(guy.x, guy.y, guy.width, guy.height)
    updatePhysics()

def drawWalls(): # Draw and update position of each wall
  for wall in walls:
    fill(151, 167, 193)
    rect(wall.x, wall.y, wall.width, wall.height)
    
    if (wall.x <= -50):
      walls.remove(wall)
    if (guy.alive):
      wall.x -= 1

def drawPowerUps(): # Draw and update position of powerup
  for powerup in powerups:
    fill(random.randint(0, 255), random.randint(0, 255), random.randint(0, 255))
    ellipse(powerup.x, powerup.y, powerup.width, powerup.height)
    
    if (powerup.x <= -50):
      powerups.remove(powerup)
    if (guy.alive):
      powerup.x -= 1

def drawBg(): # Draw background
  fill(90, 50, 20)
  rect(0, 300, 400, 100)

def drawScore(): # Display score
  textSize(30)
  fill(0, 0, 0)
  text(Game.score, 10, 30)

def draw(): # Happens every frame, for other functions to be used must be called here
  background(80, 110, 230)
  drawBg()
  drawGuy()
  drawWalls()
  drawPowerUps()
  genWall()
  genPowerUp()
  wallCollision()
  powerCollision()
  pointCount()
  drawScore()
  
run() # Calls setup() once at beginning, then calls draw() every frame continuously
```


---

## Game of sticks solver

Project: [`8e77a375fda6`](https://trinket.strivemath.org/library/trinkets/8e77a375fda6) (formerly `f9a1a79b38`)


### main.py

```python
#!/bin/python3
#python 3 for print format

#In the game of sticks there is a heap of sticks on a board. 
#On their turn, each player picks up 1 to 3 sticks. 
#The one who has to pick the final stick will be the loser.

#Strong solution --
#For every position, assuming alternating play
#For player whose turn it is:
#  Win if there exists a losing child
#  Lose if all children win


WIN = "Win"
LOSE = "Lose"
GAMENOTOVER = "Game NOT Over!"


def primitive_value(position):
	#returns WIN/LOSE if primitive (game over), otherwise GAMENOTOVER
	if position == 0:
		return WIN
	else:
		return GAMENOTOVER


def generate_moves(position):
	#returns all the moves from position (not a primitive)
	if position == 1:
		return [1]
	elif position == 2:
	  return [1, 2]
	else:
		return [1, 2, 3]


def do_move(position, move):
	#returns the child position after doing the move on the position
	return position - move


def value(position):
  #uncomment this and below to see all recursive calls
  #print(position)
  
  #returns the value of the position
  if primitive_value(position) != GAMENOTOVER:
    return primitive_value(position)
  else:
    moves = generate_moves(position)
    children = [do_move(position, move) for move in moves]
    values = [value(child) for child in children]
    
    #if a child is LOSE (so position 0), you win
    if LOSE in values:
      return WIN
    else:
      return LOSE

#uncomment this and above to see all recursive calls for just one game
#print(value(10))

for p in range(20,-1,-1):
	print(p, "has value", value(p))
	
```


---

## Getting info from Google Sheets

Project: [`6bbd06fce0`](https://trinket.strivemath.org/library/trinkets/6bbd06fce0)


### main.py

```python
import urllib.request
import json

#see this for how to publish the data and get URL
#https://www.freecodecamp.org/news/cjn-google-sheets-as-json-endpoint/

def makeAPICall(URL):
  req = urllib.request.urlopen(URL)
  html = req.read()
  values = json.loads(html)['feed']['entry'] #we only care about the feed / entry part
  return values


info = makeAPICall("https://spreadsheets.google.com/feeds/cells/1tdfqB_M-qZjpVsnSwscyD31Ti8-1w4bNTZUlMJTI45E/1/public/full?alt=json")
first = info[0]['content']['$t']
print("first: " + first)
second = info[1]['content']['$t']
print("second: " + second)
```


---

## Graphics demo starter

Project: [`0872428a92`](https://trinket.strivemath.org/library/trinkets/0872428a92)


### main.py

```python
from processing import *


#changing variables
class gameState:
  img = None
  img_x = 50
  img_y = 200
  img_size = 40
gs = gameState()


def mouseClicked():
  #YOUR CODE HERE
  #grow the image if clicked, use pointInRect
  pass


#collison detection function
def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


def mickeyEars(x, y, size):
  fill(0, 0, 0)
  ellipse(x, y, size, size)
  
  #YOUR CODE HERE
  #add the ears in the right spot and right size


def setup():
  size(500, 500)
  frameRate(30)
  #load images in setup
  gs.img = loadImage("https://www.fnasafety.com/wp-content/uploads/2018/10/chipotle-mexican-grill-logo-png-transparent.png")


def draw():
  background(50, 50, 250)
  mickeyEars(100, 100, 100)
  mickeyEars(350, 350, 20)
  
  image(gs.img, gs.img_x, gs.img_y, gs.img_size, gs.img_size)    #resized


run()
  
```


---

## Happy face example

Project: [`a24fc5e9fc`](https://trinket.strivemath.org/library/trinkets/a24fc5e9fc)


### main.py

```python


# Lesson
from processing import *

def setup():
  size(400, 400)
  background(0)
  frameRate(30)

def draw():
  #UNCOMMENT BELOW TO RUN FUNCTION
  happyFace(200, 200, 200)
  happyFace(50, 50, 50)
  happyFace(250, 350, 20)
  
def happyFace(x, y, size):
  fill(255, 255, 0)
  ellipse(x, y, size, size)
  
  #eyes
  fill(255, 0, 0)
  x_offset = .15*size
  y_offset = .1*size
  ellipse(x + x_offset, y - y_offset, size*.2, size*.2)
  ellipse(x - x_offset, y - y_offset, size*.2, size*.2)
  
  #mouth
  fill(150, 0, 0)
  
  y_offset = .12*size
  #Parameters: x, y, stretch in x (like ellipse), stretch in y, start of arc, end of arc (clockwise)
  arc(x, y + y_offset, size*.5, size*.4, 0, PI)
  #triangle(x*.9, y*1.1, x*1.1, y*1.1, x, y*1.2)


run()
```


---

## Image example

Project: [`2b1216561d`](https://trinket.strivemath.org/library/trinkets/2b1216561d)


### main.py

```python
from processing import *

def setup():
  frameRate(20)
  size(600, 400)
  noStroke()
  #image variables
  url = "https://media1.giphy.com/media/5aCiXMnPl1cli/giphy.gif?cid=e1bb72ffa17ca9746b7e9871ea213d26bcd5c1c2f6aef192&rid=giphy.gif"
  global img
  img = loadImage(url)


def draw():
  background(33, 150, 120)
    
  image(img, 10, 10)              #default size
  image(img, 300, 320, 40, 40)    #resized
   
  
run()
```


---

## Image filter - red, white, blue

Project: [`7a8f129cc4`](https://trinket.strivemath.org/library/trinkets/7a8f129cc4)


### main.py

```python
from processing import *
"""
BE SURE TO UPLOAD YOUR OWN IMAGE 
on the far right almost next to the output
and update the name when you loadImage()
it appears loading directly from URL won't work
"""

width = 450
height = 300

def setup():
  # For recoloring.
  darkBlue = color(0, 51, 76)
  reddish = color(217, 26, 33)
  lightBlue = color(112, 150, 158)
  yellow = color(252, 227, 166)
  
  size(width, height)
  
  #can get image from uploads or use a URL 
  img = loadImage("beach.jpg")
  image(img, 0, 0, width, height)
  
  loadPixels() #https://py.processing.org/reference/loadPixels.html
  for i in range(width * height): 
    r = red(pixels[i])
    g = green(pixels[i])
    b = blue(pixels[i])
    
    intensity = r + g + b
    if intensity < 182:
         pixels[i] = darkBlue

    elif intensity >= 182 and intensity < 364:
        pixels[i] = reddish

    elif intensity >= 364 and intensity < 546:
        pixels[i] = lightBlue

    elif intensity >=546:
        pixels[i] = yellow
    
  updatePixels()
  
run()
```


---

## Juggler sequence

Project: [`ae59378d7b`](https://trinket.strivemath.org/library/trinkets/ae59378d7b)


### main.py

```python
import math


def juggler(number):
  print(number)
  while number != 1:
    if number%2 == 0:
      number = int(math.pow(number, .5))
    else:
      number = int(math.pow(number, 1.5))
    print(number)
  

print(juggler(5))
```


---

## Jump Game

Project: [`9ff495bca1`](https://trinket.strivemath.org/library/trinkets/9ff495bca1)


### main.py

```python
from processing import *


def setup():
  frameRate(30)
  size(600, 400)
  noStroke()
  #image variables
  global mario_img, goomba_img
  mario_url = "https://s-media-cache-ak0.pinimg.com/originals/ae/4e/18/ae4e18b06e042a7661ddb0bd288a6738.png"
  mario_img = loadImage(mario_url)
  goomba_url = "https://i.ibb.co/k006c2Z/pngguru-com.png"
  goomba_img = loadImage(goomba_url)
    
    
def keyPressed():
  print keyboard.key
  #what should happen when you hit spacebar?


#changing variables
class gameState:
  mario_x = 50
  mario_y = 255
  mario_state = "ground"
  goomba_x = 500
  goomba_speed = 6
  score = 0
gs = gameState()





def moveEverything():
  #move goomba
  gs.goomba_x = gs.goomba_x - gs.goomba_speed 
  #when should the goomba restart?

  #move mario up until stop point

  #move mario down until stop point

  #check for collisions



def draw():
  moveEverything()
  
  bg_color = color(181, 237, 254)
  background(bg_color)
  
  #draw ground
  ground_color = color(97, 68, 59)
  fill(ground_color)
  rect(0, 350, 600, 100)
  
  #draw mario
  image(mario_img, gs.mario_x, gs.mario_y, 75, 110)
  
  #draw goomba
  image(goomba_img, gs.goomba_x, 302, 50, 50)

  #draw score label
  textSize(20)
  fill(0, 0, 0)
  text("Score: " + str(gs.score), 5, 20)


def marioHitGoomba():
  #check if either corners hit goomba rect
  corner1 = pointInRect(gs.mario_x + 5, gs.mario_y + 90, gs.goomba_x, 315, 50, 50)
  corner2 = pointInRect(gs.mario_x + 55, gs.mario_y + 90, gs.goomba_x, 315, 50, 50)
  return corner1 or corner2


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


run()
```


---

## Keep the Ball Up Game

Project: [`25f46d8c06`](https://trinket.strivemath.org/library/trinkets/25f46d8c06)


### main.py

```python
from processing import *
from processing import color

def setup():
  frameRate(30)
  size(600, 400)
  noStroke()


#changing variables
class gameState:
  ball_x = 50
  ball_y = 100
  paddle_x = 250
gs = gameState()


#non-changing variables
ball_size = 25
paddle_y = 370
paddle_width = 80
paddle_height = 20


def moveEverything():
  #move in x and y
  gs.ball_x = gs.ball_x + 5
  
  #bounce off edges
  if gs.ball_x > 600:
    gs.ball_x = 0
    
  #move paddle
  
  #bounce off paddle


def draw():
  moveEverything()
  
  bg_color = color(250, 250, 150)
  background(bg_color)
  
  #draw ball
  ball_color = color(200, 20, 60)
  fill(ball_color)
  ellipse(gs.ball_x, gs.ball_y, ball_size, ball_size)
  
  #draw paddle
  paddle_color = color(0, 0, 0)
  fill(paddle_color)
  rect(gs.paddle_x, paddle_y, paddle_width, paddle_height)



def ballHitPaddle():
  #check if the point directly below the ball is inside the paddle rectangle

  #just returns False for now, call the pointInRect function with the correct inputs
  return False


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False
    
run()
```


---

## Largest prime factor

Project: [`ffcc958996`](https://trinket.strivemath.org/library/trinkets/ffcc958996)


### main.py

```python
#https://projecteuler.net/problem=3 (smaller number for browser speed constraints)

#The prime factors of 13195 are 5, 7, 13 and 29.
#What is the largest prime factor of the number 6008514? 


def isPrime(num):
  #check if divides by two first
  if num%2 == 0:
    return False
  
  #then check all odds from 3 up to num/2
  for i in range(3, int(num/2), 2):
    if num%i == 0:
      return False

  return True


def getFactors(num):
  #ignore 1 and the number
  factors = []
  for i in range(2, int(num/2)+1):
    if num%i == 0:
      factors.append(i)
      
  return factors
  
  
def largestPrimeFactor(num):
  factors = getFactors(num)
  #go backwards to find the largest
  for i in range(len(factors)):
    index = len(factors) - i - 1
    if isPrime(factors[index]):
      return factors[index]
      

print(largestPrimeFactor(6008514))
```


---

## List comprehension example

Project: [`74cd81d38a`](https://trinket.strivemath.org/library/trinkets/74cd81d38a)


### main.py

```python
#create a list of numbers 0-9
values = range(10)
print(values)

#list comprehension - define a new list for each value in old list
sq_values = [number*number for number in values]
print(sq_values)

#do the same thing, but call a function this time
def squared(num):
  return num*num
sq_values2 = [squared(number) for number in values]
print(sq_values2)

#do the same thing, but only IF a certain condition for initial value
sq_values3 = [squared(number) for number in values if number % 2 == 0]
print(sq_values3)
```


---

## Many images from API call

Project: [`9038de425c`](https://trinket.strivemath.org/library/trinkets/9038de425c)


### main.py

```python
from processing import *
import urllib.request
import json

images = []

def setup():
  size(600, 600)
  background(0, 0, 0)
  frameRate(1)
  loadCats()


def makeAPICall(URL):
  req = urllib.request.urlopen(URL)
  html = req.read()
  values = json.loads(html)['data'] #we only care about the data part
  return values


def loadCats():
  #you can open this url in a web browser to see what it returns
  url = "https://api.giphy.com/v1/gifs/search?q=cat+funny&limit=100&api_key=dc6zaTOxFJmzC&rating=pg"
  allImages = makeAPICall(url)

  #let's just take square images
  for imageData in allImages:
    image = imageData['images']['original']
    width = int(image['width'])
    height = int(image['height'])

    #only use if it is roughly square
    if width > .8*height and width < 1.2*height:
      #use the processing loadImage function
      img = loadImage(image['url'])
      images.append(img)


def draw():
  #start off drawing at (0, 0)
  x = 0
  y = 0
  
  for index in range(len(images)):
    img = images[index]
    image(img, x, y, 200, 200)
    
    #for the next image move x over, reset to 0 when reach edge
    x += 200
    if x == 600:
      x = 0
      
    #skip down to the next row if needed
    if index == 2 or index == 5:
      y += 200
    
    #stop once reached 9 images
    if index == 8:
      break


run()
```


---

## Mapping Earthquakes

Project: [`02e8b1f0ff`](https://trinket.strivemath.org/library/trinkets/02e8b1f0ff)


### main.py

```python
import urllib.request
import json
from processing import *


def setup():
  size(700, 550)
  background(255, 255, 255)
  frameRate(1)
  data = loadEarthquakeData()
  loadMapImage(data)


def processData(data):
  north_american_quakes = []
  for quake in data:
    if quake['properties']['mag'] > 4:
      lat = quake['geometry']['coordinates'][1]
      long = quake['geometry']['coordinates'][0]
      #if north american lat and long
      if lat > 14 and lat < 72 and long > -167 and long < -59:
        north_american_quakes.append(quake)
  return north_american_quakes


def loadMapImage(data):
  global img #so can use outside of this function
  
  map_url = "https://maps.googleapis.com/maps/api/staticmap?size=700x550&zoom=3&visible=14,-167|72,-167|72,-59|14,-59&maptype=roadmap&key=AIzaSyCU14MgXL6k8x1W2BKzJ_Fp4YEs9WfQt_I&amp;format=png&visual_refresh=true&markers="
  
  #convert the lat and long to a string for the url
  latLongString = ""
  for quake in data:
    lat = quake['geometry']['coordinates'][1]
    long = quake['geometry']['coordinates'][0]  
    latLongString += str(lat) + "," + str(long) + "|"
  map_url += latLongString
  
  #use the processing loadImage function
  img = loadImage(map_url)
  

def makeAPICall(URL):
  req = urllib.request.urlopen(URL)
  html = req.read()
  values = json.loads(html)['features']
  return values


def loadEarthquakeData():
  all_data = makeAPICall("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/2.5_month.geojson")
  filtered_data = processData(all_data)
  return filtered_data


def draw():
  image(img, 0, 0, 700, 550)
  

run()
```


---

## Mario moves tree

Project: [`2b0603d822`](https://trinket.strivemath.org/library/trinkets/2b0603d822)


### main.py

```python
#Mario needs to jump over a sequence of Piranha plants, represented as a string of
#dashes (no plant) and P’s (plant!).  He only moves forward, and he can either step
#(move forward one place) or jump (move forward two places) from each position.
#How many different ways can Mario traverse a level without stepping or jumping
#into a Piranha plant? Assume that every level begins with a dash (where Mario
#starts) and ends with a dash (where Mario must end up), and there are never two 
#plants in a row (which would be impossible to pass)

#Generate a tree with all the valid moves.

#mario_tree('-P-P-')   # jump, jump
#mario_tree('---P-P-') # step, step, jump, jump OR jump, jump, jump
#mario_tree('----')  # step, jump OR jump, step OR step, step, step


#!/bin/python3
from processing import *
import random


# Node Class
class MarioNode:
  
  def __init__(self, obj):
    self.level = obj
    self.step = None
    self.jump = None


# MarioTree Class
class MarioTree:
  
  def __init__(self, level):
    self.root = MarioNode(level)
    self.insertHelper(self.root)

  def insertHelper(self, node):
    #check for a valid step
    if len(node.level) > 1 and node.level[1] == '-':
      node.step = MarioNode(node.level[1:])
      self.insertHelper(node.step)
      
    #check for a valid jump
    if len(node.level) > 2 and node.level[2] == '-':
      node.jump = MarioNode(node.level[2:])
      self.insertHelper(node.jump)

  def drawTree(self, x, y):
    self.drawNodeHelper(x, y, self.root, 0)
    
  def drawNodeHelper(self, x, y, node, depth):
    if node == self.root:
      text("start", x, y)
  
    new_y = y + 50
    if node.step:
      new_x = x - (220 / (depth * depth * 0.6 + 1))
      text("step", new_x, new_y)
      line(x + 12, y + 5, new_x + 5, new_y - 12)
      self.drawNodeHelper(new_x, new_y, node.step, depth + 1)
  
    if node.jump:
      new_x = x + (220 / (depth * depth * 0.6 + 1))
      text("jump", new_x, new_y)
      line(x + 12, y + 5, new_x + 5, new_y - 12)
      self.drawNodeHelper(new_x, new_y, node.jump, depth + 1)
      
  def numPaths(self):
    return self.leafHelper(self.root)
    
  def leafHelper(self, node):
    if node.step == None and node.jump == None:
      #is a leaf, return 1
      return 1
    else:
      #only want to call if there is a child
      stepLeaves = 0
      if node.step:
        stepLeaves = self.leafHelper(node.step)
      jumpLeaves = 0
      if node.jump:
        jumpLeaves = self.leafHelper(node.jump)
      return stepLeaves + jumpLeaves


starting_level = '----'
mario_tree = MarioTree(starting_level)

def setup():
  size(1000, 500)

def draw():
  background(200, 200, 10)
  mario_tree.drawTree(500, 20)
  text("Possible paths: " + str(mario_tree.numPaths()), 10, 20)

run()
```


---

## Memory card game

Project: [`366dfda3bc`](https://trinket.strivemath.org/library/trinkets/366dfda3bc)


### main.py

```python
from processing import*
import random

cardH = 144
cardW = 100
w = cardW*6
h = cardH*3
num_pairs = 9
cards = []
images = []

#changing variables
class gameState:
  selected = -1
  selected2 = -1
  game_pause = 0
gs = gameState()


def setup():
  frameRate(30)
  size(w, h)
  noStroke()
  initializeDeck()


def initializeDeck():
  #load images from https://github.com/notpeter/Vector-Playing-Cards/tree/master/cards-svg
  images.append(loadImage("https://i.ibb.co/qFx62Zp/cardback.png"))
  
  for i in range(num_pairs):
    #add twice for pairs
    cards.append(i+1)
    cards.append(i+1)
    
    #load image
    cardNum = str(i+1)
    if i+1==1:
      cardNum = "A"
    url = "https://raw.githubusercontent.com/notpeter/Vector-Playing-Cards/master/cards-svg/"+cardNum+"S.svg"
    images.append(loadImage(url))
  random.shuffle(cards)
  
  
def mouseClicked():
  if gs.game_pause > 0:
    #don't allow clicking while paused
    return
  x_loc = int(mouse.x/100)
  y_loc = int(mouse.y/144)
  selected_location = x_loc + y_loc*6
  if cards[selected_location] == -1:
    #don't allow clicking a blank space
    return
  #first card selected
  if gs.selected == -1:
    gs.selected = selected_location
  else:
    if gs.selected == selected_location:
      #don't allow selecting the same card
      return
    gs.selected2 = x_loc + y_loc*6
    gs.game_pause = 50
    #draw to flip the card
    drawCards()
    #determine if found a pair
    if cards[gs.selected] == cards[gs.selected2]:
      cards[gs.selected] = -1
      cards[gs.selected2] = -1
    #reset selected values
    gs.selected = -1
    gs.selected2 = -1


def drawCards():
  for i in range(len(cards)):
    #if has already been removed, skip
    if cards[i] == -1:
      continue
    x_loc = (i%6)*100
    y_loc = int(i/6)*144
    value = 0 #defaults to the card back
    if gs.selected == i or gs.selected2 == i:
      value = cards[i]
    image(images[value], x_loc, y_loc, cardW, cardH)


def draw():
  if gs.game_pause == 0:
    background(220, 255, 220)
    drawCards()
  else:
    gs.game_pause -= 1 

    
run()
```


---

## Memory game

Project: [`be34970f47`](https://trinket.strivemath.org/library/trinkets/be34970f47)


### main.py

```python
import random
import time
import os

def addNewDigit():
  new_number = random.randint(1, 9)
  digits.append(new_number)
  
def printPattern():
  for digit in digits:
    print digit
    time.sleep(.5)
    os.system('cls') #clear the text
    time.sleep(.1)

def welcomeMessage():
  print "Memorize the numbers and type them back!"
  time.sleep(1)
  print "Ready?"
  time.sleep(1)

digits = []

welcomeMessage()

while True:
  os.system('cls') #clear the text
  addNewDigit()
  printPattern()

  guess = input("What are the numbers?")
  if guess == str(digits).strip('[]').replace(', ', ''):
    print("You got it! Adding another number...")
    time.sleep(1)
  else:
    print("Wrong, let's start over...")
    time.sleep(1)
    digits = []

  
```


---

## Memory game starter

Project: [`0c9109a119`](https://trinket.strivemath.org/library/trinkets/0c9109a119)


### main.py

```python
import random
import time
import os


def printPattern():
  #YOUR CODE HERE
  #go through each digit
  
  time.sleep(.5)
  os.system('cls') #clear the text
  time.sleep(.1)


def welcomeMessage():
  print "Memorize the numbers and type them back!"
  time.sleep(1)
  print "Ready?"
  time.sleep(1)


digits = []

welcomeMessage()

while True:
  #YOUR CODE HERE
  #add to digits, printPattern, clear screen, get the user input
  guess = input("What are the numbers?")

  #compare their answer to digits like this
  if guess == str(digits).strip('[]').replace(', ', ''):
    pass
  
  #if they get it wrong, start over
  
  
```


---

## Monte Carlo pi approximation

Project: [`fc329c9584`](https://trinket.strivemath.org/library/trinkets/fc329c9584)


### main.py

```python
#http://interactivepython.org/courselib/static/thinkcspy/Labs/montepi.html

import random
import math

#imagine drawing a graph with a circle at the origin with a radius of 1
#we are just going to look at the wedge of the circle for positive x and y
#the area of a circle is pi*r*r
#for our wedge the area is pi*r*r/4, where r=1 so pi/4
#the area of the square from 0 to 1 x and y is just 1*1 = 1
#pick a random point in the square, the odds it will be in the circle is pi/4
#we will use this fact to approximate pi

#throw a bunch of darts randomly in the square
#(hint: random.random() gives a random decimal between 0 and 1)
#count how many are in the circle 
#(hint: it is in the circle if the distance from the origin to the point is <= 1)
#pi equals 4 times the number in the circle divided by the total thrown


def newPointInCircle():
  #pick a random x and y between 0 and 1
  x = random.random()
  y = random.random()
  
  #get the distance from the center of the circle to the point
  distance = math.sqrt(x*x + y*y)

  if distance <= 1:
    return True
  else:
    return False


inCirlce = 0
total = 100000
for x in range(total):
  if newPointInCircle():
    inCirlce += 1

pi = 4 * inCirlce / total
print(pi)
```


---

## Mouse Maze (Finished)

Project: [`3dd04511fff2`](https://trinket.strivemath.org/library/trinkets/3dd04511fff2) (formerly `01ad6f3b2a`)


### main.py

```python
from processing import *

def setup(): # Runs once at the beginning
  size(400, 400) # Create screen size with width, height
  frameRate(60) # Set frame rate
  noStroke() # Remove outlines (optional)

class Game: # Class to store simple variables
  started = False # Whether or not to start maze
  won = False
  startPos = (0, 0) # Coordinates for start square and end square
  endPos = (340, 360)

class Path: # Class for paths
  x = 0
  y = 0
  length = 0
  width = 0

def makePath(x, y, length, width): # External constructor for paths
  tempPath = Path()
  tempPath.x = x
  tempPath.y = y
  tempPath.length = length
  tempPath.width = width
  pathList.append(tempPath)

def drawMaze(): # The actual drawing of each path of the maze
  background(210, 10, 10)
  
  # This is for debugging and checking where the mouse is
  # print("X, Y: (" + str(mouse.x) + ", " + str(mouse.y) + ")")
  
  if Game.started: # Only show path if hovered over start square first
    fill(255, 255, 255)
    for path in pathList:
      rect(path.x, path.y, path.length, path.width)
    
    # Draw finish square
    fill(50, 30, 110)
    rect(Game.endPos[0], Game.endPos[1], 40, 40)
    fill(255, 255, 255)
    textSize(12)
    text("Finish!", Game.endPos[0] + 2, Game.endPos[1] + 24)

def drawStart(): # Draw the start square only if not showing actual maze
  if not Game.started:
    fill(50, 200, 100)
    rect(Game.startPos[0], Game.startPos[1], 40, 40)
    fill(255, 255, 255)
    textSize(12)
    text("Start!", Game.startPos[0] + 5, Game.startPos[1] + 24)
    text("<-- Move mouse over here!", Game.startPos[0] + 50, Game.startPos[1] + 24)

def checkMouse(): # Checks mouse placement
  # Checking if mouse over start square
  if (not Game.started and mouse.x > Game.startPos[0] and mouse.x <= Game.startPos[0] + 40 and mouse.y > Game.startPos[1] and mouse.y <= Game.startPos[1] + 40):
    Game.started = True
  # Checking if mouse over finish square
  if (Game.started and mouse.x >= Game.endPos[0] and mouse.x <= Game.endPos[0] + 40 and mouse.y >= Game.endPos[1] and mouse.y <= Game.endPos[1] + 40):
    Game.won = True
  # Checking if mouse inside one of the paths
  for path in pathList:
    if (mouse.x >= path.x and mouse.x <= path.x+path.length):
      if (mouse.y >= path.y and mouse.y <= path.y+path.width):
        return True
  Game.started = False
  return False

def win(): # What to do when win
  if Game.won:
    fill(0, 0, 0)
    textSize(120)
    text("Won!", 60, 250)

def draw(): # Happens every frame
  drawMaze()
  drawStart()
  checkMouse()
  win()

# Creating a path by hand
pathList = []
makePath(0, 0, 100, 40)
makePath(100, 0, 40, 100)
makePath(100, 100, 300, 40)
makePath(200, 0, 100, 40)
makePath(300, 0, 40, 100)
makePath(140, 100, 40, 200)
makePath(0, 300, 180, 40)
makePath(140, 200, 200, 40)
makePath(340, 200, 40, 200)

run() # Calls setup() once at the beginning, then calls draw() every frame continuously
```


---

## Multiplication game starter

Project: [`b91ab2bbc5`](https://trinket.strivemath.org/library/trinkets/b91ab2bbc5)


### main.py

```python
import random

#print your welcome message here


#pick the numbers to multiply
number1 = 1
number2 = 1
answer = number1 * number2

guess = 0
print "What is", number1, "x", number2, "?"

while guess != answer:
    guess = 1                 #how do you ask the user to enter a NUMBER (think about variable type)
    if guess != answer:
        print "No, try again"

print "You got it!"
```


---

## Name that Movie

Project: [`a3d79f648c`](https://trinket.strivemath.org/library/trinkets/a3d79f648c)


### main.py

```python
import random as rd
import urllib.request
from datetime import datetime
import json
from processing import *


gameData = []
posterImages = []

def setup():
  size(900, 150)
  background(255, 255, 255)
  frameRate(1)
  loadMovieData()
  
  global selectedOverview
  #gameData[0][0] is the answer
  selectedOverview = gameData[0][0]['overview']
  print(selectedOverview)

  getPosterImages()
  


def getPosterImages():
  #first flatten the list, then shuffle
  for x in range(3):
    for y in range(3):
      posterImages.append({'overview': gameData[x][y]['overview'], 'image': loadImage("https://image.tmdb.org/t/p/w200" + gameData[x][y]['poster'])})
  rd.shuffle(posterImages)


def processData(data):
  thisYearData = []

  for i in range(len(data)):
      movie = data[i]
      title = movie["title"]
      overview = movie["overview"]
      poster = movie["poster_path"]
      thisYearData.append({"title": title, "overview": overview, "poster": poster})

      if i == 10:
          # just do top 10
          break

  return thisYearData


def makeAPICall(URL):
  req = urllib.request.urlopen(URL)
  html = req.read()
  
  values = json.loads(html)['results']
  return processData(values)


def select3Random(data):
  selected = []
  
  while len(selected) < 3:
    nextIndex = rd.randint(0, len(data) - 1)
    # use if it isn't one we've already selected
    if nextIndex not in selected:
      selected.append(nextIndex)

  return [data[selected[0]], data[selected[1]], data[selected[2]]]


def loadMovieData():
  yearBorn = 2007 # replace with your birth year
  yearNow = datetime.now().year
  randomYear = rd.randint(yearBorn, yearNow)
  moviesURL = "https://api.themoviedb.org/3/discover/movie?primary_release_year=" + str(randomYear) + "&sort_by=revenue.desc&api_key=af76f7d4b75e58e9a235d4905d42a81b";
  moviesFollowingYearURL = "https://api.themoviedb.org/3/discover/movie?primary_release_year=" + str(randomYear+1) + "&sort_by=revenue.desc&api_key=af76f7d4b75e58e9a235d4905d42a81b";
  moviesPreviousYearURL = "https://api.themoviedb.org/3/discover/movie?primary_release_year=" + str(randomYear-1) + "&sort_by=revenue.desc&api_key=af76f7d4b75e58e9a235d4905d42a81b";

  data1 = makeAPICall(moviesURL)
  gameData.append(select3Random(data1))
  data2 = makeAPICall(moviesPreviousYearURL)
  gameData.append(select3Random(data2))
  data3 = makeAPICall(moviesFollowingYearURL)
  gameData.append(select3Random(data3))



def draw():
  for i in range(len(posterImages)):
    image(posterImages[i]['image'], i*100, 0, 100, 150)
  
  
def mouseClicked():
  clickIndex = int(mouse.x / 100) #will round down
  if(posterImages[clickIndex]['overview'] == selectedOverview):
    print("You Win!")
  else:
    print("Wrong, try again...")


run()
```


---

## Number guessing game

Project: [`920e5a0a8d`](https://trinket.strivemath.org/library/trinkets/920e5a0a8d)


### main.py

```python
import random

secret = random.randint(1, 99)     # pick a secret number
guess = 0
tries = 0

print "AHOY!  I'm the Dread Pirate Roberts, and I have a secret!"
print "It is a number from 1 to 99.  I'll give you 6 tries. "

# try until they guess it or run out of turns
while guess != secret and tries < 6:
    guess = int(input("What's yer guess?"))    # get the player's guess
    if guess < secret:
        print "Too low, ye scurvy dog!"
    if guess > secret:
        print "Too high, landlubber!"
    tries = tries + 1                           # used up one try

# print message at end of game    
if guess == secret:
    print "Avast! Ye got it!  Found my secret, ye did!"
else:
    print "No more guesses!  Better luck next time, matey!"
    print "The secret number was", secret
```


---

## Number Guessing Game Tree

Project: [`cd1e79cc92d2`](https://trinket.strivemath.org/library/trinkets/cd1e79cc92d2) (formerly `4b175d57d7`)


### main.py

```python
#!/bin/python3
from processing import *
import random


# Node Class
class Node:
  
  def __init__(self, obj):
    self.value = obj
    self.left = None
    self.right = None


# BinaryTree Class
class BinaryTree:
  
  def __init__(self):
    self.root = None
    
  def insert(self, value):
    self.root = self.insertHelper(self.root, value)
    
  def insertHelper(self, node, value):
    if node is None:
      return Node(value)
    elif node.value > value:
      node.left = self.insertHelper(node.left, value)
    elif node.value < value:
      node.right = self.insertHelper(node.right, value)
    
    return node
  
  def contains(self, value):
    return self.containsHelper(self.root, value)
    
  def containsHelper(self, node, value):
    if node == None:
      return False
    elif node.value == value:
      return True
    elif node.value > value:
      return self.containsHelper(node.left, value)
    elif node.value < value:
      return self.containsHelper(node.right, value)
      
    return False
      
  def drawTree(self, x, y):
    self.drawNodeHelper(x, y, self.root, 0)
    
  def drawNodeHelper(self, x, y, node, depth):
    text(str(node.value), x, y)
  
    new_y = y + 50
    if node.left:
      new_x = x - (220 / (depth * depth * 0.6 + 1))
      line(x + 5, y + 5, new_x + 5, new_y - 12)
      self.drawNodeHelper(new_x, new_y, node.left, depth + 1)
  
    if node.right:
      new_x = x + (220 / (depth * depth * 0.6 + 1))
      line(x + 5, y + 5, new_x + 5, new_y - 12)
      self.drawNodeHelper(new_x, new_y, node.right, depth + 1)


#create variables and start the game
print("I'm thinking of a number between 1-100")
num_to_guess = random.randint(1, 100)
num_guesses_left = 10
binary_tree = BinaryTree()

def setup():
  size(1000, 500)

def draw():
  global num_guesses_left
  
  if num_guesses_left == 0:
    return
  
  background(200, 0, 255)
  guess = int(input("What is your guess?"))
  
  if not binary_tree.contains(guess):
    if guess == num_to_guess:
      print("Correct!")
      num_guesses_left = 1 #so it will subtract to 0 below
    elif guess > num_to_guess:
      print("Too high!")
    elif guess < num_to_guess:
      print("Too low!")
    
    #add to the tree
    binary_tree.insert(guess)
  else:
    print("You already guessed that number!")
    
  binary_tree.drawTree(500, 20)
  num_guesses_left = num_guesses_left - 1

run()
```


---

## Paint Application Remix

Project: [`f083bab647`](https://trinket.strivemath.org/library/trinkets/f083bab647)


### main.py

```python
from processing import *



def setup():
  size(600, 400)
  background(255,255,255)
  noStroke()
  
  global currentColor, penSize, isRect
  currentColor = color(0,0,0)
  penSize = 10
  isRect = False

def mouseClicked():
  global penSize
  if (mouse.x > 300 and mouse.x <= 400 and mouse.y > 300):
    penSize += 5
  if (mouse.x > 400 and mouse.x <=500 and mouse.y > 300):
    penSize -= 5
    
    
def draw():
  global currentColor
  global penSize
  global isRect
  
  line(0, 300, 600, 300)
  
  fill(255,0,0)
  rect(0, 300, 100, 100)
  fill(0,255,0)
  rect(100,300,100,100)
  fill(0,0,255)
  rect(200,300,100,100)
  stroke(5)
  fill(0,0,0)
  line(325, 350, 375, 350)
  line(350,325, 350, 375)
  line(425, 350, 475, 350)
  textSize(15)
  text("Eraser", 520, 350)
  rect(5,5,20,20)
  noStroke()
  
  if(mouse.pressed):
    if(mouse.y < 300):
      if(mouse.x <= 25 and mouse.y <= 25):
        isRect = True
      else:
        if (isRect == True):
          fill(currentColor)
          rect(mouse.x - penSize/2, mouse.y - penSize/2, penSize, penSize)
        else:
          fill(currentColor)
          ellipse(mouse.x, mouse.y, penSize,penSize)
    elif(mouse.x <= 100):
      currentColor = color(255,0,0)
    elif(mouse.x > 100 and mouse.x <= 200):
      currentColor = color(0,255,0)
    elif (mouse.x > 200 and mouse.x <= 300):
      currentColor = color(0, 0 ,255)
    elif(mouse.x > 500):
      currentColor = color(255,255,255)
    
run()
```


---

## PaintReflect

Project: [`f18d348940a4`](https://trinket.strivemath.org/library/trinkets/f18d348940a4) (formerly `9252c5936a`)


### main.py

```python
from processing import *

class Game:
  lastX = 0
  lastY = 0
  redVal = 0
  grnVal = 0
  bluVal = 0
  color = 0

def mousePressed():
  Game.lastX = mouse.x
  Game.lastY = mouse.y

def mouseClicked():
  if 410 <= mouse.x <= 430 and 100 <= mouse.y <= 120:
    if Game.redVal > 0:
      Game.redVal -= 10
  if 410 <= mouse.x <= 430 and 130 <= mouse.y <= 150:
    if Game.grnVal > 0:
      Game.grnVal -= 10
  if 410 <= mouse.x <= 430 and 160 <= mouse.y <= 180:
    if Game.bluVal > 0:
      Game.bluVal -= 10
      
  if 470 <= mouse.x <= 490 and 100 <= mouse.y <= 120:
    if Game.redVal < 255:
      Game.redVal += 10
  if 470 <= mouse.x <= 490 and 130 <= mouse.y <= 150:
    if Game.grnVal < 255:
      Game.grnVal += 10
  if 470 <= mouse.x <= 490 and 160 <= mouse.y <= 180:
    if Game.bluVal < 255:
      Game.bluVal += 10
  
  Game.color = color(Game.redVal, Game.grnVal, Game.bluVal)

def mouseDragged():
  if mouse.x <= 400:
    strokeWeight(4)
    stroke(Game.color)
    
    line(mouse.x, mouse.y, Game.lastX, Game.lastY)
    line(400 - mouse.x, mouse.y, 400 - Game.lastX, Game.lastY)
    line(mouse.x, 400 - mouse.y, Game.lastX, 400 - Game.lastY)
    line(400 - mouse.x, 400 - mouse.y, 400 - Game.lastX, 400 - Game.lastY)
  
    Game.lastX = mouse.x
    Game.lastY = mouse.y
    
def drawPalette():
  strokeWeight(4)
  stroke(80, 80, 80)
  line(400, 0, 400, 400)
  
  noStroke()
  # Color
  fill(Game.color)
  rect(420, 20, 60, 60)
  
  # Color Blocks
  fill(255, 0, 0)
  rect(430, 100, 40, 20)
  fill(0, 255, 0)
  rect(430, 130, 40, 20)
  fill(0, 0, 255)
  rect(430, 160, 40, 20)

  # Color Buttons
  strokeWeight(3)
  stroke(120, 120, 120)
  fill(160, 160, 160)
  
  rect(410, 100, 20, 20)
  rect(470, 100, 20, 20)
  line(415, 110, 425, 110)
  line(475, 110, 485, 110)
  line(480, 105, 480, 115)
  
  rect(410, 130, 20, 20)
  rect(470, 130, 20, 20)
  line(415, 140, 425, 140)
  line(475, 140, 485, 140)
  line(480, 135, 480, 145)
  
  rect(410, 160, 20, 20)
  rect(470, 160, 20, 20)
  line(415, 170, 425, 170)
  line(475, 170, 485, 170)
  line(480, 165, 480, 175)

def drawDividers():
  strokeWeight(3)
  stroke(200, 200, 200, 100)
  line(200, 0, 200, 400)
  line(0, 200, 400, 200)

def setup():
  size(500, 400)
  frameRate(120)
  background(220, 220, 220)
  Game.color = color(0, 0, 0)
  
def draw():
  drawDividers()
  drawPalette()

run()
```


---

## Pascal's triangle

Project: [`a0ddedf2e5`](https://trinket.strivemath.org/library/trinkets/a0ddedf2e5)


### main.py

```python
from processing import *

#https://en.wikipedia.org/wiki/Pascal%27s_triangle

data = [[1], [1, 1], [1, 2, 1]]


def generateData():
  #YOUR CODE HERE
  #add enough rows
  pass


def addNewRow():
  #YOUR CODE HERE
  #add based on the row above it
  pass


def setup():
  size(500, 500)
  frameRate(1)
  generateData()


def draw():
  background(200, 50, 40)
  for i in range(len(data)):
    numbers = data[i]
    x_start = 250 - (len(numbers)-1)*7.5
    for j in range(len(numbers)):
      number = numbers[j]
      text(str(number), x_start + j*15, i*15 + 10)


run()
```


---

## Pascal's triangle finished

Project: [`3c263b6d81`](https://trinket.strivemath.org/library/trinkets/3c263b6d81)


### main.py

```python
from processing import *

#https://en.wikipedia.org/wiki/Pascal%27s_triangle

data = [[1], [1, 1], [1, 2, 1]]


def generateData():
  for i in range(30):
    addNewRow()


def addNewRow():
  last_row = data[len(data)-1]
  
  #always start and end with 1
  new_row = [1]
  
  for i in range(len(last_row)-1):
    new_number = last_row[i] + last_row[i+1]
    new_row.append(new_number)
  
  new_row.append(1)
  data.append(new_row)


def setup():
  size(500, 500)
  frameRate(1)
  generateData()


def draw():
  background(200, 50, 40)
  for i in range(len(data)):
    textSize(11 - int(i*.3))
    numbers = data[i]
    x_start = 250 - (len(numbers)-1)*10
    for j in range(len(numbers)):
      number = numbers[j]
      width = textWidth(str(number))
      text(str(number), x_start + j*20 - width/2, i*20 + 15)


run()
```


---

## Random art

Project: [`3a0ad9e3a6`](https://trinket.strivemath.org/library/trinkets/3a0ad9e3a6)


### main.py

```python
from processing import *
import random


def setup():
  #create a screen 600 pixels wide by 400 pixels tall
  size(600, 400)
  background(20, 240, 20)
  frameRate(1)
  
  
def random_color():
  r = random.randint(0, 255)
  g = random.randint(0, 255)
  b = random.randint(0, 255)
  return color(r, g, b)


def random_coordinates():
  x = random.randint(0, 600)
  y = random.randint(0, 400)
  return (x, y)


def starburst():
  my_color = random_color()
  stroke(my_color)
  
  #pick the set end point
  p1 = random_coordinates()
  for x in range(6):
    #pick the other endpoint
    p2 = random_coordinates()
    line(p1[0], p1[1], p2[0], p2[1])


def draw():
  starburst()
  

run()
```


---

## Random binary tree with count

Project: [`69d9c700cb`](https://trinket.strivemath.org/library/trinkets/69d9c700cb)


### main.py

```python
#!/bin/python3
from processing import *
import random


# Node Class
class Node:
  
  def __init__(self, obj):
    self.value = obj
    self.left = None
    self.right = None


# BinaryTree Class
class BinaryTree:
  
  def __init__(self):
    self.root = None
    
  def insert(self, value):
    self.root = self.insertHelper(self.root, value)
    
  def insertHelper(self, node, value):
    if node is None:
      return Node(value)
    elif node.value > value:
      node.left = self.insertHelper(node.left, value)
    elif node.value < value:
      node.right = self.insertHelper(node.right, value)
    
    return node
  
  def contains(self, value):
    return self.containsHelper(self.root, value)
    
  def containsHelper(self, node, value):
    if node == None:
      return False
    elif node.value == value:
      return True
    elif node.value > value:
      return self.containsHelper(node.left, value)
    elif node.value < value:
      return self.containsHelper(node.right, value)
      
    return False
      
  def drawTree(self, x, y):
    self.drawNodeHelper(x, y, self.root, 0)
    
  def drawNodeHelper(self, x, y, node, depth):
    text(str(node.value), x, y)
  
    new_y = y + 50
    if node.left:
      new_x = x - (220 / (depth * depth * 0.6 + 1))
      line(x + 5, y + 5, new_x + 5, new_y - 12)
      self.drawNodeHelper(new_x, new_y, node.left, depth + 1)
  
    if node.right:
      new_x = x + (220 / (depth * depth * 0.6 + 1))
      line(x + 5, y + 5, new_x + 5, new_y - 12)
      self.drawNodeHelper(new_x, new_y, node.right, depth + 1)

  def count(self):
    return self.countHelper(self.root)
    
  def countHelper(self, node):
    leftValue = 0
    if node.left:
      leftValue = self.countHelper(node.left)
    rightValue = 0
    if node.right:
      rightValue = self.countHelper(node.right)
    return 1 + leftValue + rightValue


random_tree = BinaryTree()
for i in range(50):
  num = random.randint(1, 50)
  if not random_tree.contains(num):
    random_tree.insert(num)

def setup():
  size(1000, 500)

def draw():
  background(200, 110, 100)
  random_tree.drawTree(500, 20)
  text("Count: "+str(random_tree.count()), 10, 20)

run()
```


---

## Random Fish Remix

Project: [`de55801c91`](https://trinket.strivemath.org/library/trinkets/de55801c91)


### main.py

```python
from processing import *
import random

# CLICK TO ADD A FISH
# How can you make this cooler?
# Can you make the fish sizes random?
# Can you add fins?

screen_w = 600
screen_h = 400

def setup():
  size(screen_w, screen_h)
  background(80, 140, 250)

def mouseClicked():
  x = mouse.px
  y = mouse.py
  drawFish(x, y, 20, 20)
  
def drawFish(x, y, width, height):
  fill(randomColor())
  ellipse(x, y, width, height)
  fill(randomColor())
  triangle(x, y, x + width, y - height*0.8, x + width, y + height*0.8)

def randomColor():
  r = random.randint(0,255)
  g = random.randint(0,255)
  b = random.randint(0,255)
  c1 = color(r,g,b)
  return c1
  
run()
```


---

## Saving to a file

Project: [`375638b539`](https://trinket.strivemath.org/library/trinkets/375638b539)


### main.py

```python
import json

# open a "file" with the same name as the tab
f = open('scores.txt', 'a') #a means will append

high_score=0

#read in the data one line at a time to find the high score
line = '0'
while line:
  line = f.readline()
  if line > high_score:
    high_score = line

print("High score: " + str(high_score))

#write a new score to the file
f.write("\n7") #\n will drop to a new line
```


---

## Shakespeare finished

Project: [`4547f8110a42`](https://trinket.strivemath.org/library/trinkets/4547f8110a42) (formerly `42f790558a`)


### main.py

```python
#Start reading from Shakespeare and Dictionaries
#https://inst.eecs.berkeley.edu/~cs61a/fa12/labs/lab06/lab06.php

import random


def shakespeare_play_words():
  #Return the words of Shakespeare's plays as a list
  file = open('shakespeare.txt') #a means will append
  return file.read().split()


#EXAMPLE INPUT
#text = ['We', 'came', 'to', 'investigate', ',', 'catch', 'bad', 'guys', 'and', 'to', 'eat', 'pie', '.']
#EXAMPLE OUTPUT
#table = build_successors_table(text)
#{'and': ['to'], 'We': ['came'], 'bad': ['guys'], 'pie': ['.'], ',': ['catch'], '.': ['We'], 'to': ['investigate', 'eat'], 'investigate': [','], 'catch': ['bad'], 'guys': ['and'], 'eat': ['pie'], 'came': ['to']}
def build_successors_table(word_list):
  table = {} #an empty dictionary
  prev = '.' #start the "previous" word as a period
  for word in word_list:
    if prev in table:
      #check if in list then add
      successor_list = table[prev]
      if word not in successor_list:
        successor_list.append(word)
    else:
      #create a new list with that word
      table[prev] = [word]
    prev = word
  return table


#Pass in a starting word and your successors table
#It will select a random next word until it reaches a . ! ?
def construct_sentence(word, table):
  result = word #output string starts with just the first word
  while word not in ['.', '!', '?']:
    #find the right list of successor words
    successor_list = table[word]
    word = random.choice(successor_list)
    result += ' ' + word
  return result
  

#create the word list
word_list = shakespeare_play_words()
#print("Total words:", len(word_list))
word_list = word_list[:10000] #just do the first ten thousand words, otherwise it may break your web browser
#print("Using words:", len(word_list))

#YOUR CODE HERE
#How do you build the table?
table = build_successors_table(word_list)
#How do you generate and print a random sentence?
sentence = construct_sentence("The", table)
#How do you fix the extra space before punctuation using replace?
sentence = sentence.replace(" ,", ",")
sentence = sentence.replace(" .", ".")
sentence = sentence.replace(" !", "!")
sentence = sentence.replace(" ?", "?")
print(sentence)
```


---

## Shakespeare words

Project: [`c670211e7dd4`](https://trinket.strivemath.org/library/trinkets/c670211e7dd4) (formerly `111bae66a7`)


### main.py

```python
#!/bin/python3

#Start reading from Shakespeare and Dictionaries
#https://inst.eecs.berkeley.edu/~cs61a/fa12/labs/lab06/lab06.php

import random


def shakespeare_play_words():
  #Return the words of Shakespeare's plays as a list
  file = open('shakespeare.txt') #a means will append
  return file.read().split()


#EXAMPLE INPUT
#text = ['We', 'came', 'to', 'investigate', ',', 'catch', 'bad', 'guys', 'and', 'to', 'eat', 'pie', '.']
#EXAMPLE OUTPUT
#table = build_successors_table(text)
#{'and': ['to'], 'We': ['came'], 'bad': ['guys'], 'pie': ['.'], ',': ['catch'], '.': ['We'], 'to': ['investigate', 'eat'], 'investigate': [','], 'catch': ['bad'], 'guys': ['and'], 'eat': ['pie'], 'came': ['to']}
def build_successors_table(word_list):
  table = {} #an empty dictionary
  prev = '.' #start the "previous" word as a period
  for word in word_list:
    if prev in table:
      #YOUR CODE HERE
      #hint: set a variable for the list following that word,
      #then check if the new word is already in the list before adding
      pass
    else:
      #YOUR CODE HERE
      #hint: no key-value exists, but remember the value needs to be a list
      pass
    prev = word
  return table


#Pass in a starting word and your successors table
#It will select a random next word until it reaches a . ! ?
def construct_sentence(word, table):
  result = word #output string starts with just the first word
  while word not in ['.', '!', '?']:
    #YOUR CODE HERE
    #hint: set a variable for the list following the word,
    #then use random.choice function to add a new word to the result
    pass
  return result
  

#create the word list
word_list = shakespeare_play_words()
print("Total words:", len(word_list))
word_list = word_list[:10000] #just do the first ten thousand words, otherwise it may break your web browser
print("Using words:", len(word_list))

#YOUR CODE HERE
#How do you build the table?
#How do you generate and print a random sentence?
#How do you fix the extra space before punctuation using replace?
```


---

## sierpinski carpet

Project: [`f2f68a18ee`](https://trinket.strivemath.org/library/trinkets/f2f68a18ee)


### main.py

```python
from processing import *


def setup():
  size(600, 600)
  noStroke()
    
#https://en.wikipedia.org/wiki/Sierpinski_carpet#/media/File:Sierpinski_carpet_1.svg
def sierpinski(x, y, size):
  new_size = size/3
  if new_size < 1:
    return
  
  rect(x + new_size, y + new_size, new_size, new_size)
  
  sierpinski(x, y, new_size)
  sierpinski(x + new_size, y, new_size)
  sierpinski(x + 2*new_size, y, new_size)
  sierpinski(x, y + new_size, new_size)
  sierpinski(x, y + 2*new_size, new_size)
  sierpinski(x + 2*new_size, y + new_size, new_size)
  sierpinski(x + new_size, y + 2*new_size, new_size)
  sierpinski(x + 2*new_size, y + 2*new_size, new_size)
  

def draw():

  background(0, 0, 0)
    
  fill(255, 255, 255)
  
  sierpinski(0, 0, 600)
    
run()
```


---

## Silly Sentence Generator

Project: [`8eada859d8`](https://trinket.strivemath.org/library/trinkets/8eada859d8)


### main.py

```python
import random

#create a list of all your verbs
verb_list = ["runs", "jumps", "climbs"]

#this function is given a list of words and selects one at random
def random_word(list_of_words):
  #get the length of the words list
  number_of_words = len(list_of_words)
  
  #pick a random number up to the end of the list
  random_word_number = random.randint(0, number_of_words - 1)
  
  #select the word at the number spot
  selected_word = list_of_words[random_word_number]
  
  #hand it back
  return selected_word 

#a simple sentence structure:
#The <adjective> <noun> <verb> the <adjective> <noun>.

#call the function and remember the result
verb = random_word(verb_list)
print verb
```


---

## SlingShot

Project: [`2998fd476a70`](https://trinket.strivemath.org/library/trinkets/2998fd476a70) (formerly `332349ead6`)


### main.py

```python
from processing import *
import math, random

class game:
  score = 0

class ball:
  x = 200
  y = 330
  angle = 0
  dist = 0
  xVel = 0
  yVel = 0
  radius = 20
  inBox = False
  onString = True
  dragging = False

class Target:
  x = 0
  y = 0
  radius = 50

targetList = []

# Create Functions
def createTarget():
  tempTarget = Target()
  tempTarget.x = random.randint(50, 350)
  tempTarget.y = random.randint(50, 300)
  targetList.append(tempTarget)

def clearTargets():
  for target in targetList:
    targetList.remove(target)

# Collision Functions
def targetCollision():
  for target in targetList:
    if circCollision(target.x, target.y, target.radius - 15, ball.x, ball.y, ball.radius-5):
      targetList.remove(target)
      game.score += 1

def circCollision(x, y, r, x1, y1, r1):
  if pointInCirc(x, y, r, x1-r1, y1): # LEFT
    return True
  if pointInCirc(x, y, r, x1+r1, y1): # RIGHT
    return True
  if pointInCirc(x, y, r, x1, y1-r1): # TOP
    return True
  if pointInCirc(x, y, r, x1, y1+r1): # BOT
    return True
  return False

def pointInCirc(x, y, r, px, py):
  if x - r <= px <= x + r:
    if y - r <= py <= y + r:
      return True
  return False

# Draw Functions
def drawRope():
  stroke(70, 70, 70, 150)
  strokeWeight(6)
  # If ball is on string
  if (ball.onString or not ball.inBox):
    line(120, 330, ball.x-9, ball.y + 10)
    line(280, 330, ball.x+9, ball.y + 10)
    stroke(135, 76, 0, 200)
    line(ball.x-9, ball.y+10, ball.x+8, ball.y + 10)
  else:
    line(120, 330, 280, 330)
    stroke(135, 76, 0, 200)
    line(191, 330, 208, 330)

def drawSling():
  noStroke()
  fill(100, 100, 100)
  rect(0, 325, 120, 10)
  rect(280, 325, 120, 10)

def drawBall():
  # Dragging ball
  if ball.dragging:
    ball.x = mouse.x
    ball.y = mouse.y
    # Limit dragging direction
    if ball.x <= 120:
      ball.x = 120
    elif ball.x >= 280:
      ball.x = 280
    if ball.y <= 335:
      ball.y = 335
    elif ball.y >= 425:
      ball.y = 425
  
  # Wall bounce
  if ball.x <= 10 or ball.x >= 390:
    ball.xVel *= -1
  
  # Reset ball, reset and create targets
  if ball.y <= -100:
    ball.x = 200
    ball.y = 330
    ball.xVel = 0
    ball.yVel = 0
    ball.onString = True
    ball.inBox = False
    clearTargets()
    createTarget()
    if random.randint(0, 4) == 0:
      createTarget()
    if random.randint(0, 4) == 0:
      createTarget()
  
  # Allow string to stick during shot
  if ball.y <= 310:
    ball.inBox = True
  
  # Move ball
  ball.x += ball.xVel
  ball.y += ball.yVel
  
  noStroke()
  fill(120, 140, 220)
  ellipse(ball.x, ball.y, ball.radius, ball.radius)

def drawTargets():
  for target in targetList:
    for i in range(3):
      if i % 2 == 0:
        fill(240, 30, 30)
      else:
        fill(255, 255, 255)
      ellipse(target.x, target.y, target.radius - 50/3*i, target.radius - 50/3*i)

def drawScore():
  fill(30, 30, 30)
  textSize(32)
  text(game.score, 5, 490)

# Mouse Functions
def mouseDragged():
  if ball.onString:
    if ball.x - 40 <= mouse.x <= ball.x + 40:
      if ball.y - 40 <= mouse.y <= ball.y + 40:
        ball.dragging = True

def mouseReleased():
  if ball.dragging:
    ball.onString = False
    ball.dragging = False
    if ball.x == 200:
      ball.angle = -math.pi / 2
      ball.dist = 5*(ball.y - 330)
    else:
      ball.angle = math.atan((ball.y-330)/(ball.x-200))
      ball.dist = 5*(330 - ball.y)/math.sin(ball.angle)
    ball.xVel = ball.dist*math.cos(ball.angle)/30
    ball.yVel = ball.dist*math.sin(ball.angle)/30

# Processing Functions
def setup():
  size(400, 500)
  frameRate(60)
  createTarget()
  createTarget()
  
def draw():
  background(230)
  drawRope()
  drawSling()
  drawTargets()
  drawBall()
  targetCollision()
  drawScore()


run()
```


---

## Snake Game

Project: [`524f2e1137`](https://trinket.strivemath.org/library/trinkets/524f2e1137)


### main.py

```python
from processing import *
import random
from classes import GameBoard
from classes import Point


#non changing variables
squares_wide = 15
squares_high = 12
pixel_width = 555
pixel_height = 445
square_pixels = pixel_width / squares_wide


#changing variables
snake = []
class gameState():
  direction = "East"
  lastDirection = "East"
  gameOver = False
  apple = Point(random.randint(0, squares_wide), random.randint(0, squares_high))
gs = gameState()


#create a 2D grid of zeros
def makePieces(width, height):
  pieces = []
  for col in range(width):
    pieces.append([])
    for row in range(height):
      pieces[col].append(0)
  return pieces


boardPieces = makePieces(squares_wide, squares_high)
gb = GameBoard(pixel_width, pixel_height, squares_wide, squares_high, boardPieces)


def keyPressed():
  if(keyCode == UP and gs.lastDirection is not "South"):
    gs.direction = "North"
  elif (keyCode == DOWN and gs.lastDirection is not "North"):
    gs.direction = "South"
  elif (keyCode == LEFT and gs.lastDirection is not "East"):
    gs.direction = "West"
  elif (keyCode == RIGHT and gs.lastDirection is not "West"):
    gs.direction = "East"


def clearSnake():
  for i in range(squares_wide):
    for j in range(squares_high):
				boardPieces[i][j] = 0
	#put the apple back
  boardPieces[gs.apple.x][gs.apple.y] = 2
		
		
def snakeToGrid():
  if not gs.gameOver:
		clearSnake()
		for i in range(len(snake)):
			boardPieces[snake[i].x][snake[i].y] = 1


def checkOverlap():
  for i in range(1, len(snake)):
    if(snake[0].x == snake[i].x and snake[0].y == snake[i].y):
      gs.gameOver = True
      break


def checkBounds():
  if snake[0].x < 0 or snake[0].x == squares_wide or snake[0].y < 0 or snake[0].y == squares_high:
		gs.gameOver = True


def isInvalidAppleLocation():
		for i in range(len(snake)):
			if gs.apple.x == snake[i].x and gs.apple.y == snake[i].y:
				return True
		return False


def generateApple():
  gs.apple = Point(random.randint(0, squares_wide - 1), random.randint(0, squares_high - 1))
  
  while isInvalidAppleLocation():
    #regenerate somewhere else
		gs.apple = Point(random.randint(0, squares_wide - 1), random.randint(0, squares_high - 1))

	
def moveSnakeCheckHitApple():
  #remember the original head location
  previousLocation = snake[0]

  #moves the head
  if gs.direction == "North":
	  snake[0] = Point(snake[0].x, snake[0].y-1)
	  gs.lastDirection = "North"
  elif gs.direction == "East":							
	  snake[0] = Point(snake[0].x + 1, snake[0].y)
	  gs.lastDirection = "East"
  elif gs.direction == "West":
	  snake[0] = Point(snake[0].x - 1, snake[0].y)
	  gs.lastDirection = "West"
  else:											  					
	  snake[0] = Point(snake[0].x, snake[0].y+1)
	  gs.lastDirection = "South"
		
	#Sets every segment in the body of the snake to be the location of the segment before it
  for i in range(1, len(snake)):
		nextLocation = snake[i]
		snake[i] = previousLocation
		previousLocation = nextLocation

  #check if you hit apple, if so add an extra segment at the end
  if snake[0].x == gs.apple.x and snake[0].y == gs.apple.y:
		generateApple()
		snake.append(previousLocation)

	
def playGame():
  moveSnakeCheckHitApple()
  checkBounds()
  checkOverlap()
  if not gs.gameOver:
    snakeToGrid()
  
  
def setupGame():
  snake.append(Point(5,6))
  snake.append(Point(4,6))
  snake.append(Point(3,6))
  generateApple()
  
  
def setup():
  size(pixel_width,pixel_height)
  frameRate(5)
  setupGame()
  #load apple image here so it just happens once (but after processing initiates)
  gb.loadImg()


def draw():
  gb.drawBoard()
  if not gs.gameOver:
    playGame()
  else:
    textSize(35)
    text("Game Over", 195, 220) 

  
run()
  
```


### classes.py

```python
from processing import *


class Point:
  def __init__(self, x, y):
    self.x = x
    self.y = y
    

class GameBoard:
  def __init__(self, width, height, squares_wide, squares_high, boardPieces):
    self.pixel_width = width
    self.pixel_height = height
    self.squares_wide = squares_wide
    self.squares_high = squares_high
    self.boardPieces = boardPieces
    self.square_pixels = self.pixel_width / self.squares_wide
    self.apple_image_string = "apple.png"
    
  def loadImg(self):
    self.img = loadImage(self.apple_image_string)
  
  def drawPieces(self):
    for i in range(self.squares_wide):
      for j in range(self.squares_high):
        piece = self.boardPieces[i][j]
        if piece != 0:
          if piece == 1:
            fill(0, 0, 0)
            rect(i * self.square_pixels, j*self.square_pixels, self.square_pixels, self.square_pixels)
          elif piece == 2:
            image(self.img, i * self.square_pixels, j*self.square_pixels, self.square_pixels, self.square_pixels)
    
  def drawBackground(self):
    fill(75, 255, 179)
    for i in range(self.squares_wide):
			for j in range(self.squares_high):
				rect(i * self.square_pixels, j * self.square_pixels, self.square_pixels, self.square_pixels)
				
  def drawBoard(self):
    self.drawBackground()
    self.drawPieces()
```


---

## Snake Game

Project: [`55bff16f67`](https://trinket.strivemath.org/library/trinkets/55bff16f67)


### main.py

```python
from processing import *


def setup():
    frameRate(30)
    size(600, 400)
    noStroke()


#keyPressed has to go before run()
def keyPressed():
  #keyboard.key doesn't work for arrow keys, have to use keyCode
  if keyboard.keyCode == 37:
    gs.direction = "L"
    
  #how can you find out the other keyCode values?
  #add additional direction changes



#changing variables
class gameState:
  snake_x = 20
  snake_y = 200
  direction = "R"
  target_x = 360
  target_y = 360
  speed = 5
gs = gameState()


#non-changing variables
snake_size = 20
target_size = 20


def moveEverything():
  #move the snake the correct direction
  if gs.direction == "R":
    gs.snake_x = gs.snake_x + gs.speed
  
  #end game if you hit the edge
  
  
def draw():
  moveEverything()
  
  background(150, 250, 250)
  
  #draw snake
  snake_color = color(250, 20, 30)
  fill(snake_color)
  rect(gs.snake_x, gs.snake_y, snake_size, snake_size)
  
  #draw target
  
  
def snakeHitTarget():
  #check if any of the 4 corners of the snake are inside the target
  
  #top left corner
  if pointInRect(gs.snake_x, gs.snake_y, gs.target_x, gs.target_y, target_size, target_size):
    return True
  #top right corner
  elif pointInRect(gs.snake_x, gs.snake_y + snake_size, gs.target_x, gs.target_y, target_size, target_size):
    return True
  #bottom left corner
  elif pointInRect(gs.snake_x + snake_size, gs.snake_y, gs.target_x, gs.target_y, target_size, target_size):
    return True
  #bottom right corner
  elif pointInRect(gs.snake_x + snake_size, gs.snake_y + snake_size, gs.target_x, gs.target_y, target_size, target_size):
    return True
  else:
    return False

  
def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


run()
```


---

## Snake Game - starter

Project: [`47c866779c`](https://trinket.strivemath.org/library/trinkets/47c866779c)


### main.py

```python
from processing import *
import random
from classes import GameBoard
from classes import Point


#non changing variables
squares_wide = 15
squares_high = 12
pixel_width = 555
pixel_height = 445
square_pixels = pixel_width / squares_wide


#changing variables
snake = []
class gameState():
  direction = "East"
  gameOver = False
  apple = Point(random.randint(0, squares_wide), random.randint(0, squares_high))
gs = gameState()


#create a 2D grid of zeros
def makePieces(width, height):
  pieces = []
  for col in range(width):
    pieces.append([])
    for row in range(height):
      pieces[col].append(0)
  return pieces


boardPieces = makePieces(squares_wide, squares_high)
gb = GameBoard(pixel_width, pixel_height, squares_wide, squares_high, boardPieces)


def keyPressed():
  if(keyCode == UP and gs.direction is not "South"):
    gs.direction = "North"
  elif (keyCode == DOWN and gs.direction is not "North"):
    gs.direction = "South"
  elif (keyCode == LEFT and gs.direction is not "East"):
    gs.direction = "West"
  elif (keyCode == RIGHT and gs.direction is not "West"):
    gs.direction = "East"


def clearSnake():
  for i in range(squares_wide):
    for j in range(squares_high):
				boardPieces[i][j] = 0
	#put the apple back
  boardPieces[gs.apple.x][gs.apple.y] = 2
		
		
def snakeToGrid():
  if not gs.gameOver:
    clearSnake()
    
    #YOUR CODE HERE
    #convert the snake array to the 2D grid boardPieces


def checkOverlap():
  #YOUR CODE HERE
  #if the head of the snake overlaps with any other segment of the snake, you lose
  pass


def checkBounds():
  #YOUR CODE HERE
  #if the head of the snake has gone off the screen, you lose
  pass


def isInvalidAppleLocation():
		for i in range(len(snake)):
			if gs.apple.x == snake[i].x and gs.apple.y == snake[i].y:
				return True
		return False


def generateApple():
  gs.apple = Point(random.randint(0, squares_wide - 1), random.randint(0, squares_high - 1))
  
  while isInvalidAppleLocation():
    #regenerate somewhere else
		gs.apple = Point(random.randint(0, squares_wide - 1), random.randint(0, squares_high - 1))


def moveSnakeCheckHitApple():
  #YOUR CODE HERE
  #move the head of the snake based on the direction it is pointing
  #move every other segment to the previous location
  #(note: may have to remember the previous location of the segment before you move it)
  #if you hit the apple, add an extra segment to the end and generate new apple
  pass

	
def playGame():
  moveSnakeCheckHitApple()
  checkBounds()
  checkOverlap()
  if not gs.gameOver:
    snakeToGrid()
  
  
def setupGame():
  snake.append(Point(5,6))
  snake.append(Point(4,6))
  snake.append(Point(3,6))
  generateApple()
  
  
def setup():
  size(pixel_width,pixel_height)
  frameRate(5)
  setupGame()
  #load apple image here so it just happens once (but after processing initiates)
  gb.loadImg()


def draw():
  gb.drawBoard()
  if not gs.gameOver:
    playGame()
  else:
    textSize(35)
    text("Game Over", 195, 220) 

  
run()
  
```


### classes.py

```python
from processing import *


class Point:
  def __init__(self, x, y):
    self.x = x
    self.y = y


class GameBoard:
  def __init__(self, width, height, squares_wide, squares_high, boardPieces):
    self.pixel_width = width
    self.pixel_height = height
    self.squares_wide = squares_wide
    self.squares_high = squares_high
    self.boardPieces = boardPieces
    self.square_pixels = self.pixel_width / self.squares_wide
    self.apple_image_string = "apple.png"
    
  def loadImg(self):
    self.img = loadImage(self.apple_image_string)
  
  def drawPieces(self):
    for i in range(self.squares_wide):
      for j in range(self.squares_high):
        piece = self.boardPieces[i][j]
        if piece != 0:
          if piece == 1:
            fill(0, 0, 0)
            rect(i * self.square_pixels, j*self.square_pixels, self.square_pixels, self.square_pixels)
          elif piece == 2:
            image(self.img, i * self.square_pixels, j*self.square_pixels, self.square_pixels, self.square_pixels)
    
  def drawBackground(self):
    fill(75, 255, 179)
    for i in range(self.squares_wide):
			for j in range(self.squares_high):
				rect(i * self.square_pixels, j * self.square_pixels, self.square_pixels, self.square_pixels)
				
  def drawBoard(self):
    self.drawBackground()
    self.drawPieces()
```


---

## Space Invaders

Project: [`2e42030c16`](https://trinket.strivemath.org/library/trinkets/2e42030c16)


### main.py

```python
from processing import *
from bullet import Bullet
from ship import Ship
from enemyShip import EnemyShip
import random

#non changing variables
width = 550
height = 450
enemies_per_row = 7
enemy_rows = 2
enemy_sep = 15

#changing variables
class gameState:
  gameOver = False
gs = gameState()
boss = EnemyShip(250, 10, "BOSS")
player = Ship(350, 400)
enemies = []
enemyBullets = []
bullets = []


def createEnemies():
  enemyY = 135
  for i in range(enemy_rows):
    enemyX = 100
    for j in range(enemies_per_row):
      enemy = EnemyShip(enemyX, enemyY, "REGULAR")
      enemies.append(enemy)
      
      #move over for the next ship
      enemyX = enemyX + enemy_sep + enemy.width
    #move down for the next row
    enemyY = enemyY + enemy_sep + 60

def enemiesShoot():
  #1 in 50 a regular ship will shoot
  randomNumber = random.randint(0, 50)
  if randomNumber == 34:
    if len(enemies) != 0:
      #select a random ship
      randomIndex = random.randint(0, len(enemies)-1)
      currShip = enemies[randomIndex]
      
      newBullet = Bullet(currShip.x + currShip.width/2, currShip.y + currShip.height, "enemy")
      enemyBullets.append(newBullet)
  
  #boss will shoot twice as often as all other ships
  if randomNumber == 21 or randomNumber == 22:
    if boss.isAlive():
      newBullet = Bullet(boss.x + boss.width/2, boss.y + boss.height, "enemy")
      enemyBullets.append(newBullet)
    

def moveEverything():
  player.move()
  boss.moveShip()
  
  for i in range(len(bullets)):
    if i < len(bullets):
      currBullet = bullets[i]
      currBullet.move()
    
      if currBullet.y <= 0:
        bullets.remove(currBullet)
  
  for i in range(len(enemies)):
    currentEnemy = enemies[i]
    currentEnemy.moveShip()
    
  for i in range(len(enemyBullets)):
    currEnBullet = enemyBullets[i]
    currEnBullet.move()


def drawEverything():
  player.draw()
  if boss.isAlive():
    boss.draw()
    
  for i in range(len(bullets)):
    currBullet = bullets[i]
    currBullet.draw()
    
  for i in range(len(enemies)):
    currentEnemy = enemies[i]
    currentEnemy.draw()
    
  for i in range(len(enemyBullets)):
    currEnBullet = enemyBullets[i]
    currEnBullet.draw()
  

def getEnemyAt(x, y):
  for i in range(len(enemies)):
    if pointInRect(x, y, enemies[i].x, enemies[i].y, enemies[i].width, enemies[i].height):
      return enemies[i]
      
  if boss.isAlive() and pointInRect(x, y, boss.x, boss.y, boss.width, boss.height):
      return boss
      
  return None
  
  
def checkBulletCollisions():
  for i in range(len(bullets)):
    if i < len(bullets):
      currBullet = bullets[i]
    
      collObj = getEnemyAt(currBullet.x + 2, currBullet.y + 2)
    
      if collObj is not None:
        collObj.takeDamage()
        if not collObj.isAlive():
          if collObj is not boss:
            enemies.remove(collObj)
        
        bullets.remove(currBullet)
    
  #check if enemy bullets hit the player
  for i in range(len(enemyBullets)):
    currBullet = enemyBullets[i]
    if pointInRect(currBullet.x + 2, currBullet.y + 2, player.x, player.y, player.width, player.height):
      gs.gameOver = True


#if an ememy ship collided with the player ship
def checkTouchingShip():
  for i in range(len(enemies)):
    if pointInRect(player.x + player.width/2, player.y + player.height/2, enemies[i].x, enemies[i].y, enemies[i].width, enemies[i].height):
      gs.gameOver = True


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


def mousePressed():
  #not game over
  if not gs.gameOver:
    #last bullet has moved 30 pixels
    if bullets == [] or bullets[len(bullets)-1].y < player.y - 30:
      newBullet = Bullet(player.x + player.width/2, player.y, "mine")
      bullets.append(newBullet)


def setup():
  size(width, height)
  createEnemies()
  frameRate(30)
  #load all the images here so it just happens once (but after processing initiates)
  player.loadImg()
  boss.loadImg()
  for i in range(len(enemies)):
    currentEnemy = enemies[i]
    currentEnemy.loadImg()


def draw():
  background(0, 0, 0)

  if not gs.gameOver:
    enemiesShoot()
    moveEverything()
    checkBulletCollisions()
    checkTouchingShip()
  else:
    fill(255, 0, 0)
    textSize(35)
    text("Game Over", 195, 220) 
    
  drawEverything()

  
run()
```


### bullet.py

```python
from processing import *


class Bullet:
	def __init__(self, spaceshipX, spaceshipY, type):
	  self.x = spaceshipX
	  self.y = spaceshipY
	  self.type = type

	  if type == "enemy":
	    self.speed = 6
	  else:
	    self.speed = -8
	
	def move(self):
	  self.y = self.y + self.speed
	  
	def draw(self):
	  if self.type == "enemy":
	    fill(255, 0, 0)
	  else:
	    fill(255, 255, 255)
	  noStroke()
	  ellipse(self.x, self.y, 5, 5)
	  
```


### enemyShip.py

```python
from processing import *


class EnemyShip:
  def __init__(self, x, y, type):
    self.x = x
    self.y = y
    self.speed = -2
    self.type = type
    if type is "REGULAR":
      self.hitPoints = 3
      self.height = 25
      self.width = 45
      self.imageName = "spaceship-small.png"
    else:
      self.hitPoints = 15
      self.height = 80
      self.width = 100
      self.imageName = "spaceship6.png"

  def loadImg(self):
    self.img = loadImage(self.imageName)

  def takeDamage(self):
    self.hitPoints =  self.hitPoints - 1

  def isAlive(self):
    return self.hitPoints > 0
	
  def draw(self):
    image(self.img, self.x, self.y, self.width,self.height)

  def moveShip(self):
    self.x = self.x + self.speed
    if self.x <= 0 or self.x >= 550 - self.width:
      self.moveDown()
      self.flipDirection()

  def moveDown(self):
    self.y = self.y + 40

  def flipDirection(self):
    self.speed = -self.speed
```


### ship.py

```python
from processing import *


class Ship:
  def __init__(self, x, y):
    self.x = x
    self.y = y
    self.width = 40
    self.height = 40
    self.imageName = "ship5.png"
    
  def loadImg(self):
    self.img = loadImage(self.imageName)
  
  def move(self):
    self.x = mouseX
    #don't allow to go too far
    if self.x > 510:
      self.x = 510
    
  def draw(self):
    image(self.img, self.x, self.y, self.width, self.height)
```


---

## Space Invaders starter

Project: [`e8c17737b7`](https://trinket.strivemath.org/library/trinkets/e8c17737b7)


### main.py

```python
from processing import *
from bullet import Bullet
from ship import Ship
from enemyShip import EnemyShip
import random


#non changing variables
width = 550
height = 450
enemies_per_row = 7
enemy_rows = 2
enemy_sep = 15


#changing variables
class gameState:
  gameOver = False
gs = gameState()
boss = EnemyShip(250, 10, "BOSS")
player = Ship(350, 400)
enemies = []
enemyBullets = []
bullets = []


def createEnemies():
  enemy = EnemyShip(100, 135, "REGULAR")
  enemies.append(enemy)
  
  #YOUR CODE HERE
  #create rows of enemies using the variables enemies_per_row, enemy_rows, enemy_sep

def enemiesShoot():
  #YOUR CODE HERE
  #randomly decide if each spaceship should shoot
  #try 1 in 50 odds a regular ship shoots, 1 in 25 for boss
  pass


def moveEverything():
  player.move()
  boss.moveShip()
  
  for i in range(len(bullets)):
    if i < len(bullets):
      currBullet = bullets[i]
      currBullet.move()
    
      if currBullet.y <= 0:
        bullets.remove(currBullet)
  
  for i in range(len(enemies)):
    currentEnemy = enemies[i]
    currentEnemy.moveShip()

  #YOUR CODE HERE
  #move the enemy bullets and remove if they go off the screen


def drawEverything():
  player.draw()
  if boss.isAlive():
    boss.draw()
    
  for i in range(len(bullets)):
    currBullet = bullets[i]
    currBullet.draw()
    
  for i in range(len(enemies)):
    currentEnemy = enemies[i]
    currentEnemy.draw()
    
  for i in range(len(enemyBullets)):
    currEnBullet = enemyBullets[i]
    currEnBullet.draw()
  

def getEnemyAt(x, y):
  for i in range(len(enemies)):
    if pointInRect(x, y, enemies[i].x, enemies[i].y, enemies[i].width, enemies[i].height):
      return enemies[i]
      
  if boss.isAlive() and pointInRect(x, y, boss.x, boss.y, boss.width, boss.height):
      return boss
      
  return None
  
  
def checkBulletCollisions():
  for i in range(len(bullets)):
    if i < len(bullets):
      currBullet = bullets[i]
    
      collObj = getEnemyAt(currBullet.x + 2, currBullet.y + 2)
    
      if collObj is not None:
        collObj.takeDamage()
        if not collObj.isAlive():
          if collObj is not boss:
            enemies.remove(collObj)
        
        bullets.remove(currBullet)
    
  #YOUR CODE HERE
  #check if enemy bullets hit the player (hint: use pointInRect)


#if an ememy ship collided with the player ship
def checkTouchingShip():
  for i in range(len(enemies)):
    if pointInRect(player.x + player.width/2, player.y + player.height/2, enemies[i].x, enemies[i].y, enemies[i].width, enemies[i].height):
      gs.gameOver = True


def pointInRect(pt_x, pt_y, rect_x, rect_y, rect_w, rect_h):
  if (pt_x > rect_x) and (pt_x < rect_x + rect_w) and (pt_y > rect_y) and (pt_y < rect_y + rect_h):
    return True
  else:
    return False


def mousePressed():
  #not game over
  if not gs.gameOver:
    #last bullet has moved 30 pixels
    if bullets == [] or bullets[len(bullets)-1].y < player.y - 30:
      newBullet = Bullet(player.x + player.width/2, player.y, "mine")
      bullets.append(newBullet)


def setup():
  size(width, height)
  createEnemies()
  frameRate(30)
  #load all the images here so it just happens once (but after processing initiates)
  player.loadImg()
  boss.loadImg()
  for i in range(len(enemies)):
    currentEnemy = enemies[i]
    currentEnemy.loadImg()


def draw():
  background(0, 0, 0)

  if not gs.gameOver:
    enemiesShoot()
    moveEverything()
    checkBulletCollisions()
    checkTouchingShip()
  else:
    fill(255, 0, 0)
    textSize(35)
    text("Game Over", 195, 220) 
    
  drawEverything()

  
run()
```


### bullet.py

```python
from processing import *


class Bullet:
	def __init__(self, spaceshipX, spaceshipY, type):
	  self.x = spaceshipX
	  self.y = spaceshipY
	  self.type = type

	  if type == "enemy":
	    self.speed = 6
	  else:
	    self.speed = -8
	
	def move(self):
	  self.y = self.y + self.speed
	  
	def draw(self):
	  if self.type == "enemy":
	    fill(255, 0, 0)
	  else:
	    fill(255, 255, 255)
	  noStroke()
	  ellipse(self.x, self.y, 5, 5)
	  
```


### enemyShip.py

```python
from processing import *


class EnemyShip:
  def __init__(self, x, y, type):
    self.x = x
    self.y = y
    self.speed = -2
    self.type = type
    if type is "REGULAR":
      self.hitPoints = 3
      self.height = 25
      self.width = 45
      self.imageName = "spaceship-small.png"
    else:
      self.hitPoints = 15
      self.height = 80
      self.width = 100
      self.imageName = "spaceship6.png"

  def loadImg(self):
    self.img = loadImage(self.imageName)

  def takeDamage(self):
    self.hitPoints =  self.hitPoints - 1

  def isAlive(self):
    return self.hitPoints > 0
	
  def draw(self):
    image(self.img, self.x, self.y, self.width,self.height)

  def moveShip(self):
    self.x = self.x + self.speed
    
    #YOUR CODE HERE
    #make the space ships bounce off the side and go back the other way
```


### ship.py

```python
from processing import *


class Ship:
  def __init__(self, x, y):
    self.x = x
    self.y = y
    self.width = 40
    self.height = 40
    self.imageName = "ship5.png"
    
  def loadImg(self):
    self.img = loadImage(self.imageName)
  
  def move(self):
    self.x = mouseX
    #don't allow to go too far
    if self.x > 510:
      self.x = 510
    
  def draw(self):
    image(self.img, self.x, self.y, self.width, self.height)
```


---

## Space Race Remix

Project: [`2fe1bbf6b9`](https://trinket.strivemath.org/library/trinkets/2fe1bbf6b9)


### main.py

```python
#player 1 is w-s
#player 2 is up-down
#first to 10 wins

from processing import *
import random

w = 600
h = 400
dia = 5
num_balls = 30
rocket_h = 20
rocket_w = 10

def setup():
  frameRate(30)
  size(w, h)
  noStroke()

class spaceComets:
  x = []
  y = []
  speed = 2
comet = spaceComets()

# populate arrays with random coordinates of a bunch of comets
for i in range(num_balls):
    comet.x.append(random.random() * w)
    comet.y.append(random.random() * (h-rocket_h))

class rockets:
  rocket_x = 3*w/4
  rocket_y = h - rocket_h
  score = 0
r1 = rockets()
r2 = rockets()

r2.rocket_x = 100  #Place second rocket at a different position

def drawRocket(x,y):
  rect(x, y, rocket_w, rocket_h)

#reset the rocket to start
def collision(rocket): 
  for i in range(num_balls):
    rx = rocket.rocket_x 
    ry = rocket.rocket_y
    cx = comet.x[i]
    cy = comet.y[i]
    if cx > rx and cx < rx + rocket_w and cy > ry and cy < ry + rocket_h:
      rocket.rocket_y = h -20

def keyPressed(): # Map space bar to jump
  speed = 10
  if (keyboard.keyCode == UP): #up key pressed #38
    r1.rocket_y = r1.rocket_y - speed
  if (keyboard.keyCode == DOWN): #down key pressed #40
    r1.rocket_y = r1.rocket_y + speed
  if (keyboard.keyCode == 87): # W
    r2.rocket_y = r2.rocket_y - speed
  if (keyboard.keyCode == 83): # S
    r2.rocket_y = r2.rocket_y + speed

def moveEverything():
  number_of_balls = len(comet.x)
  for index in range(number_of_balls):
    #move each ball
    comet.x[index] = comet.x[index] + comet.speed
    # Reset the balls to the start if they move off screen
    if comet.x[index] > w + dia:
      comet.x[index] = 0
      comet.y[index] =  random.random() * (h - rocket_h)

def scoreUp():
  if r1.rocket_y <= 0:
    r1.score = r1.score + 1
    r1.rockey_y = 380 #reset rocket position
    r2.rockey_y = 380 #reset rocket position
  elif r2.rocket_y <= 0:
    r2.score = r2.score + 1
    r1.rockey_y = 380 #reset rocket position
    r2.rockey_y = 380 #reset rocket position

def draw():
  moveEverything()
  # Check both rockets for collisions:
  collision(r1)
  collision(r2)

  if r1.rocket_y <= 0 or r2.rocket_y <= 0:
    if r1.rocket_y <= 0:
      r1.score = r1.score + 1
    if r2.rocket_y <= 0:
      r2.score = r2.score + 1
    r1.rocket_y = 380
    r2.rocket_y = 380
    
  # Creation of all visuals that show up on screen !!
  background(0, 0, 50)
  fill(255, 255, 255)
  text("Score: " + str(r1.score), 500, 50)
  text("Score: " + str(r2.score), 50, 50)
  drawRocket(r1.rocket_x,r1.rocket_y)
  drawRocket(r2.rocket_x, r2.rocket_y)
  #draw each ball
  for index in range(num_balls):
    ellipse(comet.x[index], comet.y[index], dia, dia)
  
  if  r1.score >= 10:
    background(100, 240, 150)
    textSize(90)
    text("Player 1 wins!!!", 5, 200)
  elif r2.score >= 10:
    background(100, 240, 150)
    textSize(90)
    text("Player 2 wins!!!", 5, 200)
    
run()
```


---

## Squidward Drawing (Finished)

Project: [`681bf93b156a`](https://trinket.strivemath.org/library/trinkets/681bf93b156a) (formerly `58b4d9d057`)


### main.py

```python
# Graphics Library
from processing import *

# Function that happens one time at the beginning
def setup():
  # Size actually makes the screen, (width, height)
  size(400, 400)
  noStroke()

# Function that happens continuously
def draw():
  noStroke()
  background(90)
  
  fill(139, 190, 224) # Shape of head
  ellipse(200, 130, 250, 180)
  ellipse(200, 220, 120, 160)
  ellipse(200, 280, 180, 50)
  
  fill(0, 0, 0) # Mouth
  rect(130, 280, 140, 2)
  
  fill(232, 221, 171) # Eyes
  ellipse(165, 180, 50, 80)
  ellipse(235, 180, 50, 80)
  
  fill(132, 28, 25) # Pupils
  ellipse(165, 180, 12, 25)
  ellipse(235, 180, 12, 25)
  
  fill(0, 0, 0) # Nose outline
  ellipse(200, 250, 50, 120)
  
  fill(139, 190, 224) # Nose
  ellipse(200, 250, 49, 119)
  
  fill(30, 91, 140) # Freckles
  ellipse(180, 50, 5, 5)
  ellipse(160, 80, 5, 5)
  ellipse(128, 75, 5, 5)
  ellipse(220, 90, 5, 5)
  ellipse(230, 55, 5, 5)
  ellipse(270, 70, 5, 5)
  
  noFill() # Forehead lines
  stroke(30, 91, 140)
  arc(200, 105, 80, 15, -3.14159, 0)
  arc(200, 130, 170, 30, -3.14159, 0)
  
  
run()
```


---

## Star project - finished

Project: [`5feab16dff`](https://trinket.strivemath.org/library/trinkets/5feab16dff)


### main.py

```python
#!/bin/python3
from processing import *
import urllib.request


# Translate Coordinates
def coords_to_pixel(orig_x, orig_y, size):
  origin_val = size / 2
  pixel_x = orig_x * origin_val
  pixel_y = orig_y * origin_val
  return (origin_val + pixel_x, origin_val - pixel_y)


# Part 1: Plotting Star Charts
# Reads coordinates from the files, then returns three dictionaries, which follow the spec
def read_coords(file):
  stars_list = []
  line = ' '
  
  # Add all stars to stars_lst
  while line: # Uses truthy value of strings to terminate
    line = file.readline()
    stars_list.append(line.split()) # splits strings by spaces
  
  # Remove empty list caused by endline
  stars_list = stars_list[0:len(stars_list) - 1]
  
  # Convert all string numbers to floats
  for i in range(len(stars_list)):
    for j in range(6): # 6 because the format states that the first 6 numbers are floats
      stars_list[i][j] = float(stars_list[i][j])
  
  
  # Returns 3 dictionaries
  # dictionary 1 - key: henry draper #, value: tuples of coordinates (x, y)
  
  dict1 = {}
  for i in range(len(stars_list)):
    dict1[stars_list[i][3]] = (stars_list[i][0], stars_list[i][1])
  
  # dictionary 2 - key: henry draper #, value: magnitude (float) of stars
  dict2 = {}
  for i in range(len(stars_list)):
    dict2[stars_list[i][3]] = stars_list[i][4]
    
  # dictionary 3 - key: star name, value: henry draper #
  dict3 = {}
  for i in range(len(stars_list)):
    name_str = ''
    if len(stars_list[i]) > 6:
      for j in range(6, len(stars_list[i])):
        name_str += stars_list[i][j] + " "
    
    star_names = name_str.split(";")
    for name in star_names: # Remove spaces at the beginning or end of the word. Otherwise dictionary key/value pairs become problematic for constellations
      if name == "":
        continue
      if name[-1] == ' ': # If there is a space at the end, remove it
        name = name[0:len(name) - 1]
      if name[0] == ' ': # If there is a space at the beginning, remove it
        name = name[1:]
       
      dict3[name] = stars_list[i][3]
      
  return dict1, dict2, dict3


# Reads a constellation file, returning (starname, starname) pairs
def read_constellation(file):
  const_lines = []
  line = ' '
  while line:
    line = file.readline()
    pair = line.split(",")
    if len(pair) > 1:
      pair[1] = pair[1][0:len(pair[1]) - 1] # Minus one to remove new line items
    const_lines.append(pair)
  
  return const_lines;


# open stars.txt file
f = open('stars.txt', 'a')
dict1, dict2, dict3 = read_coords(f) # Get the dictionaries from read_coords for the star file and store them in dict1, dict2, and dict3 respectively.


# Get constellation lines to draw
def load_constellations():
# Create a list of all constellation files.
  all_constellation_files = []
  all_constellation_files.append(open('Cas_lines.txt', 'a'))
  all_constellation_files.append(open('Cyg_lines.txt', 'a'))
  all_constellation_files.append(open('Big_lines.txt', 'a'))
  all_constellation_files.append(open('Uminor_lines.txt', 'a'))
  all_constellation_files.append(open('Gemini_lines.txt', 'a'))
  
  lines = []
  for file in all_constellation_files: # Loops through the list of files.
    const_lines = read_constellation(file) # Read the file.
    for pairs in const_lines: # Loop through all the pairs of stars and find their coordinate pairs.
      if pairs == ['']: # Skip any null pairs in the const lines
        continue
      star1 = dict1[dict3[pairs[0]]] # Star Name (pair) -> Henry Draper # (dict3) -> Coordinates (dict1) 
      star2 = dict1[dict3[pairs[1]]] # Star Name (pair) -> Henry Draper # (dict3) -> Coordinates (dict1)
    
      star1_pixels = coords_to_pixel(star1[0], star1[1], 600) # Cartesian to pixel conversion.
      star2_pixels = coords_to_pixel(star2[0], star2[1], 600) # Cartesian to pixel conversion.
      lines.append((star1_pixels, star2_pixels))
  return lines


lines_to_draw = load_constellations()


def setup():
  size(600, 600)
  frameRate(1)
  
  
def draw():
  background(0, 0, 0)
  
  # Draw the stars
  stroke(255, 250, 250)
  noStroke()
  # Loop through the dictionary keys
  for key in dict1.keys():
    # Use to Cartesian -> Pixel function to determine where they should be graphed
    pixel_vals = coords_to_pixel(dict1[key][0], dict1[key][1], 600) 
    # Create ellipse at the given values (magnitude divided by 3 to look better)
    ellipse(pixel_vals[0], pixel_vals[1], dict2[key]/3, dict2[key]/3) 
  
  # Draw the constellation lines
  stroke(255, 255, 0)
  # Loop through all the points (which are pairs of points) in the lines_to_draw and plot them
  for points in lines_to_draw: 
    line(int(points[0][0]), int(points[0][1]), int(points[1][0]), int(points[1][1]))
  
  
run()

  

  
```


---

## Star project - starter

Project: [`35352da3a3`](https://trinket.strivemath.org/library/trinkets/35352da3a3)


### main.py

```python
#!/bin/python3
from processing import *
import urllib.request


# Translate Coordinates
def coords_to_pixel(orig_x, orig_y, size):
  origin_val = size / 2
  pixel_x = orig_x * origin_val
  pixel_y = orig_y * origin_val
  return (origin_val + pixel_x, origin_val - pixel_y)


# Part 1: Plotting Star Charts
# Reads coordinates from the files, then returns three dictionaries, which follow the spec
def read_coords(file):
  stars_list = []
  line = ' '
  
  # Add all stars to stars_lst
  while line: # Uses truthy value of strings to terminate
    line = file.readline()
    stars_list.append(line.split()) # splits strings by spaces
  
  # Remove empty list caused by endline
  stars_list = stars_list[0:len(stars_list) - 1]
  
  # Convert all string numbers to floats
  for i in range(len(stars_list)):
    for j in range(6): # 6 because the format states that the first 6 numbers are floats
      stars_list[i][j] = float(stars_list[i][j])
  
  
  # Returns 3 dictionaries
  
  # dictionary 1 - key: henry draper #, value: tuples of coordinates (x, y)
  dict1 = {}
  # YOUR CODE HERE
  
  # dictionary 2 - key: henry draper #, value: magnitude (float) of stars
  dict2 = {}
  # YOUR CODE HERE

    
  # dictionary 3 - key: star name, value: henry draper #
  dict3 = {}
  # YOUR CODE HERE
  # Hint: check if there is a star name, based on the length of the array
  # If there is more than one name, there will be a trailing ; to remove, 
  # Along with removing any spaces from the dictionary key

  return dict1, dict2, dict3


# Reads a constellation file, returning (starname, starname) pairs
def read_constellation(file):
  const_lines = []
  line = ' '
  while line:
    line = file.readline()
    pair = line.split(",")
    if len(pair) > 1:
      pair[1] = pair[1][0:len(pair[1]) - 1] # Minus one to remove new line items
    const_lines.append(pair)
  
  return const_lines;


# open stars.txt file
f = open('stars.txt', 'a')
dict1, dict2, dict3 = read_coords(f) # Get the dictionaries from read_coords for the star file and store them in dict1, dict2, and dict3 respectively.


# Get constellation lines to draw
def load_constellations():
# Create a list of all constellation files.
  all_constellation_files = []
  all_constellation_files.append(open('Cas_lines.txt', 'a'))
  all_constellation_files.append(open('Cyg_lines.txt', 'a'))
  all_constellation_files.append(open('Big_lines.txt', 'a'))
  all_constellation_files.append(open('Uminor_lines.txt', 'a'))
  all_constellation_files.append(open('Gemini_lines.txt', 'a'))

  lines = []  
  for file in all_constellation_files: # Loops through the list of files.
    const_lines = read_constellation(file) # Read the file.
    for pairs in const_lines: # Loop through all the pairs of stars and find their coordinate pairs.
      if pairs == ['']: # Skip any null pairs in the const lines
        continue
      
      #YOUR CODE HERE
      # Hint: set a variable for star1 and star2 in the pair
      # It should use your previous dictionaries to go from
      # Name to Henry Draper # to coordinates
      
      # Then use coords_to_pixel
      # Then add coordinate pair to lines
    return lines


lines_to_draw = load_constellations()


def setup():
  size(600, 600)
  frameRate(1)
  
  
def draw():
  background(0, 0, 0)
  
  # Draw the stars
  stroke(255, 250, 250)
  noStroke()
  # Loop through the dictionary keys
  for key in dict1.keys():
    # Use to Cartesian -> Pixel function to determine where they should be graphed
    pixel_vals = coords_to_pixel(dict1[key][0], dict1[key][1], 600) 
    # Create ellipse at the given values (magnitude divided by 3 to look better)
    ellipse(pixel_vals[0], pixel_vals[1], dict2[key]/3, dict2[key]/3) 
  
  # Draw the constellation lines
  stroke(255, 255, 0)
  # Loop through all the points (which are pairs of points) in the lines_to_draw and plot them
  for points in lines_to_draw: 
    line(int(points[0][0]), int(points[0][1]), int(points[1][0]), int(points[1][1]))
  
  
run()

  

  
```


---

## String together API calls

Project: [`345d72912c`](https://trinket.strivemath.org/library/trinkets/345d72912c)


### main.py

```python
import urllib.request
import json


def makeAPICall(URL):
  req = urllib.request.urlopen(URL)
  html = req.read()
  return json.loads(html)


#look up the artist
artist_name = "Taylor Swift"
response = makeAPICall("http://musicbrainz.org/ws/2/artist/?query=artist:" + artist_name +"&fmt=json")
first_artist = response['artists'][0]

#use the artist id to look up all the recordings
recording_url = "http://musicbrainz.org/ws/2/recording?&limit=100&offset=0&fmt=json&artist=" + first_artist['id']
response = makeAPICall(recording_url)
recordings = response['recordings']
for recording in recordings:
  print(recording['title'])
```


---

## Subtract game solver

Project: [`f99f83edff`](https://trinket.strivemath.org/library/trinkets/f99f83edff)


### main.py

```python
#!/bin/python3
#python 3 for print format

#10 pieces on the table to start
#On your move subtract 1 or 2
#The winner is the player who removes the last one
#https://drive.google.com/file/d/1nQY67Q1S3jXWGzR8sNrKNdDoe45tMiml/view

#Strong solution --
#For every position, assuming alternating play
#For player whose turn it is:
#  Win if there exists a losing child
#  Lose if all children win


WIN = "Win"
LOSE = "Lose"
GAMENOTOVER = "Game NOT Over!"


def primitive_value(position):
	#returns WIN/LOSE if primitive (game over), otherwise GAMENOTOVER
	if position == 0:
		return LOSE
	else:
		return GAMENOTOVER


def generate_moves(position):
	#returns all the moves from position (not a primitive)
	if position == 1:
		return [1]
	else:
		return [1, 2]


def do_move(position, move):
	#returns the child position after doing the move on the position
	return position - move


def value(position):
  #uncomment this and below to see all recursive calls
  #print(position)
  
  #returns the value of the position
  if primitive_value(position) != GAMENOTOVER:
    return primitive_value(position)
  else:
    moves = generate_moves(position)
    children = [do_move(position, move) for move in moves]
    values = [value(child) for child in children]
    
    #if a child is LOSE (so position 0), you win
    if LOSE in values:
      return WIN
    else:
      return LOSE

#uncomment this and above to see all recursive calls for just one game
#print(value(10))

for p in range(10,-1,-1):
	print(p, "has value", value(p))
	
#notice that if the position on your turn is a multiple of 3, you 
#will lose if the opponent plays optimally. The optimal play for 
#you is also to make sure your opponent's position is a multiple of 3
```


---

## TicTacToe finished

Project: [`23b62e488c`](https://trinket.strivemath.org/library/trinkets/23b62e488c)


### main.py

```python
#!/bin/python3
#python 3 needed for board printing

# Tic-Tac-Toe
import os

board = [['E', 'E', 'E'], ['E', 'E', 'E'], ['E', 'E', 'E']] 
turn = True # True indicates 'X' turn, while False indicates 'O' turn
move_count = 0

# Checks if a player has won the game of TicTacToe
def check_win(player):
  
  for i in range(3):
    if board[i][0] == player and board[i][1] == player and board[i][2] == player:
      return True

  for j in range(3):
    if board[0][j] == player and board[1][j] == player and board[2][j] == player:
      return True
  
  if board[0][0] == player and board[1][1] == player and board[2][2] == player:
    return True
  
  if board[0][2] == player and board[1][1] == player and board[2][0] == player:
    return True
  
  return None
  
# Prints out the current state of the TicTacToe Board
def print_board():
    print('   ', '0', '   ', '1', '   ', '2', ' ')
    for i in range(len(board)):
        print(i, ' ', board[i][0] , ' | ', board[i][1], ' | ', board[i][2], ' ')
        if i != 2:
            print(' ', '-', '-', '-', '-', '-', '-', '-', '-', '-')

# Game Logic
while move_count <= 9:

  os.system('cls') #clear the text
  print_board()
  
  if move_count == 9:
    break
  
  if check_win('X'):
    print('X Wins!')
    break
  elif check_win('O'):
    print('O Wins!')
    break
  
  if turn:
    print("X's turn!")
  else:
    print("O's turn!")
    
  x_pos = int(input("Input X position: "))
  y_pos = int(input("Input Y position: "))
  
  if board[y_pos][x_pos] != 'E':
    print("Invalid Position")
  else:
    if turn: 
      board[y_pos][x_pos] = 'X'
    else:
      board[y_pos][x_pos] = 'O'
    move_count += 1
    turn = not turn
  
```


---

## TicTacToe starter

Project: [`f0c209915d`](https://trinket.strivemath.org/library/trinkets/f0c209915d)


### main.py

```python
#!/bin/python3
#python 3 needed for board printing

# Tic-Tac-Toe
import os

board = [['E', 'E', 'E'], ['E', 'E', 'E'], ['E', 'E', 'E']] 
turn = True # True indicates 'X' turn, while False indicates 'O' turn
move_count = 0

# Checks if a player has won the game of TicTacToe
def check_win(player):
  
  for i in range(3):
    if board[i][0] == player and board[i][1] == player and board[i][2] == player:
      return True

  for j in range(3):
    if board[0][j] == player and board[1][j] == player and board[2][j] == player:
      return True
  
  #YOUR CODE HERE
  #add two if statements to check both diagonals
  
  return None
  
# Prints out the current state of the TicTacToe Board
def print_board():
    print('   ', '0', '   ', '1', '   ', '2', ' ')
    for i in range(len(board)):
        print(i, ' ', board[i][0] , ' | ', board[i][1], ' | ', board[i][2], ' ')
        if i != 2:
            print(' ', '-', '-', '-', '-', '-', '-', '-', '-', '-')

# Game Logic
while move_count <= 9:

  os.system('cls') #clear the text
  print_board()
  
  if move_count == 9:
    break
  
  if check_win('X'):
    print('X Wins!')
    break
  elif check_win('O'):
    print('O Wins!')
    break
  
  #YOUR CODE HERE
  #print which player's turn, then get input for x and y position
  #make sure it is valid, then add move to the board
  #what other variables need to change to finish the turn?
  
  
```


---

## Timer example

Project: [`fc03a2d5b8`](https://trinket.strivemath.org/library/trinkets/fc03a2d5b8)


### main.py

```python
import time

before = time.time()

print "Adding all numbers from 1 to 1,000,000..."
total = 0
for x in range(1000000):
  total = total + x
print "=", total

after = time.time()

diff = after - before
print "It took", diff, "seconds to run"
```


---

## Towers of Hanoi - finished

Project: [`fc5fd4efe0`](https://trinket.strivemath.org/library/trinkets/fc5fd4efe0)


### main.py

```python
#!/bin/python3
#python 3 for print format

from stack import Stack
import os
import time

num_discs = 5
pause_time = .5

#setup
column_a = Stack()
column_b = Stack()
column_c = Stack()
for i in reversed(range(num_discs)):
  column_a.push(i)


def print_game():
  os.system('cls') #clear the text
  
  #use lists for easier printing
  list_a = column_a.toList()
  list_b = column_b.toList()
  list_c = column_c.toList()
  
  for i in reversed(range(num_discs)):
    #uses an inline if to make sure in the list range, else prints blank
    print('   ', list_a[i] if i<len(list_a) else ' ', '   ', list_b[i] if i<len(list_b) else ' ', '   ', list_c[i] if i<len(list_c) else ' ')

  print('   ', '-', '   ', '-', '   ', '-')
  print('   ', 'A', '   ', 'B', '   ', 'C')
  
  #pause
  time.sleep(pause_time)


def move_tower(disc, source, destination, spare):
  if disc == 0:
    destination.push(source.pop())
    print_game()
  else:
    move_tower(disc - 1, source, spare, destination)
    destination.push(source.pop())
    print_game()
    move_tower(disc - 1, spare, destination, source)


print_game()
move_tower(num_discs - 1, column_a, column_b, column_c)
```


### stack.py

```python
class Stack:
  def __init__(self):
    self.stack = []
  
  def toList(self):
    return self.stack
  
  def push(self, elem):
    self.stack.append(elem)
    
  def pop(self):
    return self.stack.pop()
    
  def size(self):
    return len(self.stack)
    
  def peek(self):
    return self.stack[self.size()- 1]
```


---

## Towers of Hanoi - starter

Project: [`6e1fc5f079`](https://trinket.strivemath.org/library/trinkets/6e1fc5f079)


### main.py

```python
#!/bin/python3
#python 3 for print format

from stack import Stack
import os
import time

num_discs = 5
pause_time = .5

#setup
column_a = Stack()
column_b = Stack()
column_c = Stack()
for i in reversed(range(num_discs)):
  column_a.push(i)


def print_game():
  os.system('cls') #clear the text
  
  #use lists for easier printing
  list_a = column_a.toList()
  list_b = column_b.toList()
  list_c = column_c.toList()
  
  for i in reversed(range(num_discs)):
    #uses an inline if to make sure in the list range, else prints blank
    print('   ', list_a[i] if i<len(list_a) else ' ', '   ', list_b[i] if i<len(list_b) else ' ', '   ', list_c[i] if i<len(list_c) else ' ')

  print('   ', '-', '   ', '-', '   ', '-')
  print('   ', 'A', '   ', 'B', '   ', 'C')
  
  #pause
  time.sleep(pause_time)


def move_tower(disc, source, destination, spare):
  #YOUR CODE HERE
  #be sure to call print_game every time you move a disc
  pass


print_game()
#call the recursive function
```


### stack.py

```python
class Stack:
  def __init__(self):
    self.stack = []
  
  def toList(self):
    return self.stack
  
  def push(self, elem):
    self.stack.append(elem)
    
  def pop(self):
    return self.stack.pop()
    
  def size(self):
    return len(self.stack)
    
  def peek(self):
    return self.stack[self.size()- 1]
```


---

## Turtle graphics examples

Project: [`e9439f4079`](https://trinket.strivemath.org/library/trinkets/e9439f4079)


### main.py

```python
import turtle 
t = turtle.Turtle()
t.shape('turtle')
t.speed(10) #0 to 10
t.pensize(3)

t.up() #pen up
t.goto(200, 200) #grid is 400x400 with (0, 0) in the center. (200, 200) is upper right

t.down() #pen down
t.goto(0, 0)

t.forward(50)
t.right(90) #turn
t.forward(50)
t.left(90) #turn
t.forward(50)
t.up()

t.goto(-60, -60)
t.down()
for i in range(360):
  t.forward(1)
  t.right(1)
  
#many more commands are in the documentation, but that is enough for now
```


---

## Turtle with colors

Project: [`d53a2b5046`](https://trinket.strivemath.org/library/trinkets/d53a2b5046)


### main.py

```python
import turtle
t = turtle.Turtle()
t.shape('turtle')
t.speed(10) #0 to 10
t.pensize(3)

t.color((0, 255, 0)) #RGB color
t.begin_fill()
for i in range(9):
  t.forward(50)
  t.right(80)
t.end_fill()
```


---

## Using Firebase to save info

Project: [`2a90e9feed`](https://trinket.strivemath.org/library/trinkets/2a90e9feed)


### main.py

```python
from urllib import request
import requests
import json

#NOTE: THIS DOESN'T WORK IN TRINKET, DOWNLOAD TO YOUR COMPUTER

#https://firebase.google.com/
#setup an account, new project
#Click database, then new Realtime Database
#use test mode which will allow all reads and writes (public on web for anyone with the URL)
#type in {"high_score": 5} for the data (it will create )

base_url = "https://exampledb-7e657.firebaseio.com"

def makeAPICall(URL):
  req = request.urlopen(URL)
  html = req.read()
  values = json.loads(html.decode('utf-8'))
  return values

#can get info like this
high_score = makeAPICall(base_url + "/high_score.json")
print("Before: " + str(high_score))


#can update like this
new_high_score = {'high_score': 11}
#patch is like update, can't use do with urllib.request on trinket
requests.patch(base_url + '/.json', json=new_high_score)
```
