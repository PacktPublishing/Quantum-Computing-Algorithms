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


```python

```
