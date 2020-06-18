
```python
def factorial(num):
  if num == 1:
    return 1
  return num * factorial(num-1)
	    
value = factorial(5)
print(value)
```

http://pythontutor.com/visualize.html#code=def%20factorial%28num%29%3A%0A%20%20%20%20if%20num%20%3D%3D%201%3A%0A%20%20%20%20%20%20%20%20return%201%0A%20%20%20%20return%20num%20*%20factorial%28num-1%29%0A%20%20%20%20%0Avalue%20%3D%20factorial%285%29%0Aprint%28value%29&cumulative=false&curInstr=0&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false
