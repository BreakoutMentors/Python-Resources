The hailstone sequence is a cool number pattern that for any positive whole number, it will always end in one. It repeats the following steps until it gets to one: 
* if the number is even, it will divide by two
* if the number is odd, it will multiply by three and add one.

Here is an example sequence that starts with the number 7:

7
22
11
34
17
52
26
13
40
20
10
5
16
8
4
2
1

It took 16 steps!

How can you make this into a program? It should ask the user to enter a number, output the number at every step, and count how many steps it took to finish.

This will require one thing that you likely haven't seen before: mod (%). Mod can be used to determine if a number is even or odd. Mod is similar to division, but just worries about the remainder. So if you do 7 mod 2, it is asking what the remainder is when you divide 7 by 2. The answer is 1. This is how you can check if a variable called 'number' is even:

```python
if number % 2 == 0:
```

What happens when you enter a really big number? How many steps does it take?
