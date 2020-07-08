# Trees

The tree data structure is very useful for hierarchies of information. Think a family tree. 

![tree image](https://cdn-media-1.freecodecamp.org/images/1*V_EUgNXVc8Wy9H1-JoqT3g.jpeg)

The information stored is a node. This might be a person's name. They are connected to other nodes by edges. Down the tree is a called child relationship, up the tree is a parent relationship. The top most node is called the root. [Here is a great article for further reading](https://www.freecodecamp.org/news/all-you-need-to-know-about-tree-data-structures-bceacb85490c/)

A tree data structure is usually implemented on your own, as opposed to a feature of the coding language like a list/array or dictionary/map. It sets up really well for using recursion - for example searching the node and a recursive call to search all its children.



## Subtract Game

This is a simple two player game:
```
Start with 10 pieces of candy on the table
Alternate turns removing either 1 or 2 pieces of candy
The winner is the player who removes the last one
```

The gameplay can be stored as a tree: 
![tree image](https://user-images.githubusercontent.com/1643783/86969814-38d94280-c123-11ea-8f37-008429b77166.png)

The code for it is a little hard to understand, but [here it is](https://trinket.io/library/trinkets/f99f83edff) from a [UC Berkeley Computer Science course](https://drive.google.com/drive/folders/1JDzC1WS13oQlsgLhUznfk-U-65GiZ8rK). The example code uses list comprehension, a cool Python feature. [See some simple examples here](https://trinket.io/library/trinkets/74cd81d38a)

The gist of a "strong solution" is in [this slide deck](https://drive.google.com/file/d/1nQY67Q1S3jXWGzR8sNrKNdDoe45tMiml/view) from the course. On your turn the game is at a certain state. Each possible move you can make results in a new gamestate that are all the children nodes. You win if there exists a node where the other player loses. You lose if all the children result in the other player winning. 

![strong solution](https://user-images.githubusercontent.com/1643783/86971445-0a109b80-c126-11ea-9f34-447c3c16b8a5.png)

This game is pretty simple, but the same idea can be used for much more complex games - with many possible moves and ways to win or lose. Obviously in this game there are only two possible moves, so two children nodes. And the only way the game ends is when zero pieces are left. In the code this is called the "primitive", and says if it is your turn and there are zero pieces left, return LOSE (this would mean the other player removed the final pieces on their turn).


