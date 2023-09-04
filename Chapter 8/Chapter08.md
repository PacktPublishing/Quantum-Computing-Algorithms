### Coding Grover's algorithm with matrices


```python
from qiskit import QuantumCircuit, Aer, execute
from qiskit.visualization import plot_histogram
```


```python
oracle_matrix = [
    [1, 0,  0, 0, 0, 0, 0, 0],
    [0, 1,  0, 0, 0, 0, 0, 0],
    [0, 0,  1, 0, 0, 0, 0, 0],
    [0, 0,  0, 1, 0, 0, 0, 0],
    [0, 0,  0, 0, -1, 0, 0, 0],
    [0, 0,  0, 0, 0, 1, 0, 0],
    [0, 0,  0, 0, 0, 0, 1, 0],
    [0, 0,  0, 0, 0, 0, 0, 1]
]
oracle = QuantumCircuit(3)
oracle.unitary(oracle_matrix, qubits=[0, 1, 2], label='oracle')
oracle.barrier()
display(oracle.draw('latex'))
```


```python
flip_matrix = [
    [-1, 0, 0, 0, 0, 0, 0, 0],
    [ 0, 1, 0, 0, 0, 0, 0, 0],
    [ 0, 0, 1, 0, 0, 0, 0, 0],
    [ 0, 0, 0, 1, 0, 0, 0, 0],
    [ 0, 0, 0, 0, 1, 0, 0, 0],
    [ 0, 0, 0, 0, 0, 1, 0, 0],
    [ 0, 0, 0, 0, 0, 0, 1, 0],
    [ 0, 0, 0, 0, 0, 0, 0, 1]
]
flip = QuantumCircuit(3)
flip.unitary(flip_matrix, qubits=[0, 1, 2], label='flip')
h3 = QuantumCircuit(3)
h3.h([0, 1, 2])

diffuser = h3.compose(flip).compose(h3)
diffuser.barrier()
display(diffuser.draw('latex'))
```


```python
grover_iterate = oracle.compose(diffuser)
```


```python
circ = QuantumCircuit(4, 3) # We use the fourth qubit
                            #    later in this chapter.
circ.h([0, 1, 2])
circ.barrier()
circ = circ.compose(grover_iterate).compose(grover_iterate)
circ.measure([0, 1, 2], [0, 1, 2])                         
display(circ.draw('latex'))
```


```python
device = Aer.get_backend('qasm_simulator') 
job = execute(circ,backend = device,shots = 1000)
print(job.job_id())

result = job.result()
counts = result.get_counts(circ)

print(counts)
display(plot_histogram(counts))
```

### When to use Grover's algorithm


```python
x = float(input())
if x**5 - 2*(x**4) + 4*(x**3) - 8*(x**2) + 3*x - 6 == 0:
    print(1)
else:
    print(-1)
```

### Coding Grover's algorithm with high-level functions


```python
pip install tweedledum
```


```python
pip install qiskit_algorithms
```


```python
from qiskit.circuit.library.phase_oracle import PhaseOracle
from qiskit_algorithms import AmplificationProblem, Grover
from qiskit.tools.visualization import plot_histogram
```


```python
expression = ('(sausage & ~anchovies & pineapple)' \
              ' & (mushrooms | anchovies)')
print(expression)

oracle = PhaseOracle(expression)
problem = AmplificationProblem(oracle)
grover = Grover(iterations=2) 
circ = grover.construct_circuit(problem)
circ.measure_all()
display(circ.draw('latex'))
```


```python
from qiskit import Aer, execute
from qiskit.visualization import plot_histogram

device = Aer.get_backend('qasm_simulator') 

job = execute(circ,backend = device, shots = 1000)
print(job.job_id())

result = job.result()
counts = result.get_counts(circ)

print(counts)
display(plot_histogram(counts))
```

### Coding Grover's algorithm with quantum gates


```python
from qiskit.circuit.library.standard_gates import XGate
```


```python
oracle = QuantumCircuit(4)
oracle.x(3)
oracle.h(3)

ctrl = XGate().control(3, ctrl_state='100')
oracle.append(ctrl, qargs=[0, 1, 2, 3])

oracle.barrier()
display(oracle.draw('latex'))
```


```python
diffuser = QuantumCircuit(4)
diffuser.h([0, 1, 2]) 

ctrl =  XGate().control(3, ctrl_state='000')
diffuser.append(ctrl, qargs=[0, 1, 2, 3])

diffuser.h([0, 1, 2])

diffuser.barrier()
display(diffuser.draw('latex'))
```

### Question 3


```python
import random

oracle_matrix = [
    [1, 0,  0, 0, 0, 0, 0, 0],
    [0, 1,  0, 0, 0, 0, 0, 0],
    [0, 0,  1, 0, 0, 0, 0, 0],
    [0, 0,  0, 1, 0, 0, 0, 0],
    [0, 0,  0, 0, 1, 0, 0, 0],
    [0, 0,  0, 0, 0, 1, 0, 0],
    [0, 0,  0, 0, 0, 0, 1, 0],
    [0, 0,  0, 0, 0, 0, 0, 1]
]

entry = random.randint(0, 7)
print(entry)
oracle_matrix[entry][entry] = -1

oracle = QuantumCircuit(3)
oracle.unitary(oracle_matrix, qubits=[0, 1, 2], label='oracle')
oracle.barrier()
display(oracle.draw('latex'))
```

### Question 7


```python
# ((m | t) &  ~n) &   ((t | n)  & ~m)
# (~~(m | t)  & ~n) & (~~(t | n)  & ~m)
# (~(~m & ~t) & ~n) & (~(~t & ~n) & ~m)

from qiskit import QuantumRegister, QuantumCircuit
from qiskit.circuit.library.standard_gates import XGate 

m = QuantumRegister(1, 'm')
t = QuantumRegister(1, 't')
n = QuantumRegister(1, 'n')
mt = QuantumRegister(1, 'not m and not t')
mtn = QuantumRegister(1, 'alice')
tn = QuantumRegister(1, 'not t and not n')
tnm = QuantumRegister(1, 'bob')
exp = QuantumRegister(1, 'alice and bob')
circ = QuantumCircuit(m, t, n, mt, mtn, tn, tnm, exp)

circ.h([0, 1, 2])

ctrl = XGate().control(2, ctrl_state='00')
circ.append(ctrl, qargs=[0, 1, 3])
circ.append(ctrl, qargs=[2, 3, 4])
circ.append(ctrl, qargs=[1, 2, 5])
circ.append(ctrl, qargs=[0, 5, 6])

circ.append(ctrl, qargs=[4, 6, 7])

circ.append(ctrl, qargs=[0, 5, 6])
circ.append(ctrl, qargs=[1, 2, 5])
circ.append(ctrl, qargs=[2, 3, 4])
circ.append(ctrl, qargs=[0, 1, 3])

circ.h([0, 1, 2])

display(circ.draw('latex'))
```


```python

```
