### Creating and displaying values


```python
x = 7
```


```python
print(x)
```


```python
x = 1024
```


```python
x = 7
x = 99
x = 1024
print(x)
print(y)
```


```python
import time

while True:
    print('Hello')
    time.sleep(1)
```

### Matrices in Python


```python
import numpy as np
```


```python
A = np.matrix( [[2, -3, 0],  
                [1, 5, 19]] )
print(np.dot(4, A))
```


```python
A = np.matrix( [[5, 1, 3]] )
B = np.matrix( [[2],  
                [4],  
                [6]] )
print(np.dot(A, B))
```


```python
A = np.matrix( [[1, 3, 2],   
                [4, 0, -1]] )
B = np.matrix( [[5, 10],     
                [2, -2],  
                [-3, 6]] )
print(np.dot(A, B)) 
```


```python
print(A * B)
```


```python
A = np.matrix( [[3], 
                [2]] )
B = np.matrix( [[5], 
                [4]] )
print(np.kron(A, B))
```

### Exercise 8


```python
import numpy as np

A = np.matrix( [[1, 2, 3, 0],
                [2, 1, -1, 3]] )
B = np.matrix( [[1, 1, -2],
                [3, 2, -1],
                [0, 4, 3],
                [3, -3, 5]] )
print(np.dot(A, B))
```


```python
import numpy as np

A = np.matrix( [[2],
                [3],
                [1]] )
B = np.matrix( [[8, 4, 0],
                [1, 3, 5]] )
print(np.kron(A, B))
```


```python

```
