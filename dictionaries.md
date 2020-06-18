# Python Dictionaries

Data structure is the general term for how code stores information beyond simple variables. A list is an example you have seen before.

Now let's look at a Python dictionary, which is great for storing unordered information in key-value pairs:

```python
stateDictionary = {'CA': 'California', 'NV': 'Nevada', 'OR': 'Oregon'}
print(stateDictionary['NV'])
```

Let's say your program has a variable for the state abbreviation and you want to determine the state name. A dictionary is an excellent way to store that information for quick access:

```python
stateShort = 'CA'
stateLong = stateDictionary[stateShort]
print(stateLong)
```

Challenge questions (try your best guess first, if it doesn't work then search online):
- How do you add more information to the dictionary after it has been created? Add a new state.
- How do you find out the length of the dictionary? Print the length.
- How can you find out if a key is in the dictionary? Add an if statement for a certain state.

Fun fact: other programming languages use different terms for the same data structure. For example the Python list is called an array in Java and JavaScript. The general term for a Python dictionary is "associative arrays" because it associates info into key-value pairs. In Java and JavaScript they are called Maps because they map information from key to value.


## Write a Sentence Like Shakespeare

Data structures are more useful for projects that use a lot of data. So let's jump into an interesting project that analyzes all of Shakespeare's writings and generates hilarious new sentences in his own words!

Like these:
```
The sealing-day betwixt my manners and your haunts.
The seasons alter till morrow deep midnight.
The finch, rather, being mortal grossness so that they him silently.
```

From the [problem prompt](https://inst.eecs.berkeley.edu/~cs61a/fa12/labs/lab06/lab06.php): 
```
Here's the idea: We start with some word - we'll use "The" as an example. Then we look through all of the 
texts of Shakespeare and for every instance of "The" we record the word that follows "The" and add it to a
list, known as the successors of "The". Now do this for every word Shakespeare has used, ever.

Let's go back to "The". Now, we randomly choose a word from this list, say "cat". Then we look up the 
successors of "cat" and randomly choose a word from that list, and we continue this process. This eventually 
will terminate in a period (".") and we will have generated a Shakespearean sentence!

The object that we'll be looking things up in is called a 'sucessor table', although really it's just a 
dictionary. The keys in this dictionary are words, and the values are lists of successors to those words.

Here's an incomplete definition of the build_successors_table function. The input is a list of words 
(corresponding to a Shakespearean text), and the output is a successors table. (By default, the first word 
is a successor to '.'). See the example below:

>>> def build_successors_table(tokens):
        table = {}
        prev = '.'
        for word in tokens:
            if prev in table:
                "***FILL THIS IN***"
            else:
                "***FILL THIS IN***"
            prev = word
        return table
>>> text = ['We', 'came', 'to', 'investigate', ',', 'catch', 'bad', 'guys', 'and', 'to', 'eat', 'pie', '.']
>>> table = build_successors_table(text)
>>> table
{'and': ['to'], 'We': ['came'], 'bad': ['guys'], 'pie': ['.'], ',': ['catch'], '.': ['We'], 'to': ['investigate', 'eat'], 'investigate': [','], 'catch': ['bad'], 'guys': ['and'], 'eat': ['pie'], 'came': ['to']}
```

[Here is the starter code](https://trinket.io/library/trinkets/111bae66a7) (and a [finished example](https://trinket.io/library/trinkets/42f790558a) if you need it)
