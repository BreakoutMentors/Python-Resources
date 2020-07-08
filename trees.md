# Trees

The tree data structure is very useful for hierarchies of information. Think a family tree. 

![tree image](https://cdn-media-1.freecodecamp.org/images/1*V_EUgNXVc8Wy9H1-JoqT3g.jpeg)

The information stored is a node. This might be a person's name. They are connected to other nodes by edges. Down the tree is a called child relationship, up the tree is a parent relationship. The top most node is called the root. [Here is a great article for further reading (and where a couple of these images are from)](https://www.freecodecamp.org/news/all-you-need-to-know-about-tree-data-structures-bceacb85490c/)

A tree data structure is usually implemented on your own, as opposed to a feature of the coding language like a list/array or dictionary/map. It sets up really well for using recursion - for example searching the node and a recursive call to search all its children.


## Binary Tree with Numbers

Binary just means that there are a maximum of two children for a given node. A very common binary tree use case is to store numbers - the left child for smaller and the right child for larger.

![binary tree](https://cdn-media-1.freecodecamp.org/images/1*ofbwuz4inpf2OlB-l9gtHw.jpeg)

With such a small example it may seem more complex than just storing the numbers in an array. But when there are a ton of numbers, it is much faster to find one when stored in a binary tree. Let's look at a quick example.

Say you were in charge of giving out Social Security Numbers to newborns. Your strategy is to pick 9 random digits, then look up to see if it has been used already. There are hundreds of millions of people with a SSN - do you want to look through a list / array one by one to see if a given number is already used? If you stored them as a binary tree though, you can take advantage of the smaller / larger comparision to cut your search in half each time. The tree gets exponentially smaller as you go.

This is show in the [number guessing game tree code](https://trinket.io/library/trinkets/4b175d57d7). The tree is implemented with a very simple Node Class:
```python
class Node:
  
  def __init__(self, obj):
    self.value = obj
    self.left = None
    self.right = None
```

All it does is store its value and children called left and right. The children start as None and are added later.

The BinaryTree class is where the magic happens. All it stores is the root Node. When a new value is inserted into the tree, it calls a recursive helper function that will help find the right spot to create the new node.

```python
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
```
The contains and draw functions are similar - each calls a helper function with the root node parameter, which makes recursive calls for each child.

Read through the code, make sure you understand how it works. We are going to use the Node and BinaryTree classes as a starting point for a new project. Copy them over to a new Trinket file, along with the graphics setup, draw, and run (you don't need any of the number guessing game logic).

```
Create a new tree called random_tree
Loop 50 times, each time picking a random number 1-50
If the tree doesn't already contain the number, insert it
```

This makes use of the contains and insert functions that were already created for you. As a bonus challenge, see if you can add a count function that counts the total number of nodes in the tree (look at how the recursion was done for the others).

[Here is a finished example if you need it](https://trinket.io/library/trinkets/69d9c700cb)

## Subtract Game

This is a simple two player game:
```
Start with 10 pieces of candy on the table
Alternate turns removing either 1 or 2 pieces of candy
The winner is the player who removes the last one
```

The gameplay can be stored as a tree: 

![tree image](https://user-images.githubusercontent.com/1643783/86969814-38d94280-c123-11ea-8f37-008429b77166.png)

The code for it is a little hard to understand, but [here it is](https://trinket.io/library/trinkets/f99f83edff) from a [UC Berkeley Computer Science course](https://drive.google.com/drive/folders/1JDzC1WS13oQlsgLhUznfk-U-65GiZ8rK). (The code uses list comprehension, a cool Python feature, [see some simple examples here](https://trinket.io/library/trinkets/74cd81d38a))

The gist of a "strong solution" is in [this slide deck](https://drive.google.com/file/d/1nQY67Q1S3jXWGzR8sNrKNdDoe45tMiml/view) from the course. On your turn the game is at a certain state. Each possible move you can make results in a new gamestate that are all the children nodes. You win if there exists a node where the other player loses. You lose if all the children result in the other player winning. 

![strong solution](https://user-images.githubusercontent.com/1643783/86971445-0a109b80-c126-11ea-9f34-447c3c16b8a5.png)

This game is pretty simple, but the same idea can be used for much more complex games - with many possible moves and ways to win or lose. Obviously in this game there are only two possible moves, so two children nodes. And the only way the game ends is when zero pieces are left. In the code this is called the "primitive", and says if it is your turn and there are zero pieces left, return LOSE (this would mean the other player removed the final pieces on their turn).

Now let's apply the same idea to a slightly more complex game - [Game of Sticks](http://nifty.stanford.edu/2014/laaksonen-vihavainen-game-of-sticks/handout.html).

```
In the game of sticks there is a heap of sticks on a board. 
On their turn, each player picks up 1 to 3 sticks. 
The one who has to pick the final stick will be the loser.
```

Start a new project and copy over the code from the last game. What changes do you need to make for these new rules? Once you print out all the values, what is the optimal strategy?

[Here is a finished example if you need it](https://trinket.io/library/trinkets/f9a1a79b38)
