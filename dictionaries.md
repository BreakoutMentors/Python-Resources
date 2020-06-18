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

Challenge questions:
- How do you add more information to the dictionary after it has been created?
- How do you find out the length of the dictionary?
- How can you find out if a key is in the dictionary?

Fun fact: other programming languages use different terms for the same data structure. For example the Python list is called an array in Java and JavaScript. The general term for a Python dictionary is "associative arrays" because it associates info into key-value pairs. In Java and JavaScript they are called Maps because they map information from key to value.
