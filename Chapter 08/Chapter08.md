### Grover's algorithm with matrices


```python
from qiskit import QuantumCircuit, execute
from qiskit_ibm_provider import IBMProvider
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
circ = QuantumCircuit(4, 3) # We use the fourth 
                            #   qubit later in this chapter.
circ.h([0, 1, 2])
circ.barrier()
circ = circ.compose(grover_iterate).compose(grover_iterate)
circ.measure([0, 1, 2], [0, 1, 2])                         
display(circ.draw('latex'))
```


```python
provider = IBMProvider()
device = provider.get_backend('ibmq_qasm_simulator') 
job = execute(circ,backend = device,shots = 1000)
print(job.job_id())

result = job.result()
counts = result.get_counts(circ)

print(counts)
display(plot_histogram(counts))
```

### Grover's algorithm with high-level functions


```python
pip install tweedledum
```


```python
from qiskit.circuit.library.phase_oracle import PhaseOracle
from qiskit.algorithms import AmplificationProblem
from qiskit.algorithms import Grover
from qiskit.tools.visualization import plot_histogram
```


```python
expression = \
    '(sausage & ~anchovies & pineapple) & (mushrooms | anchovies)'
oracle = PhaseOracle(expression)
problem = AmplificationProblem(oracle)
grover = Grover(iterations=2) 
circ = grover.construct_circuit(problem)
circ.measure_all()
display(circ.draw('latex'))
```


```python
from qiskit import execute
from qiskit_ibm_provider import IBMProvider
from qiskit.visualization import plot_histogram

provider = IBMProvider()
device = provider.get_backend('ibmq_qasm_simulator') 

job = execute(circ,backend = device, shots = 1000)
print(job.job_id())

result = job.result()
counts = result.get_counts(circ)

print(counts)
display(plot_histogram(counts))
```

### Grover's algorithm with quantum gates


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

### Exercise 3


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
oracle.unitary(oracle_matrix, 
               qubits=[0, 1, 2], label='oracle')
oracle.barrier()
display(oracle.draw('latex'))
```

### Exercise 5


```python
from qiskit import QuantumCircuit
from qiskit import Aer
from numpy import real

def matrix_reals(m):
    return list(map(lambda x: list(map(real, x)), m))

circ = QuantumCircuit(3)
circ.x(2)
circ.h(2)
circ.barrier()
circ.toffoli(0, 1, 2)
display(circ.draw('latex'))
device = Aer.get_backend('unitary_simulator')
job = execute(circ,backend = device,shots = 1)
matrix = job.result().get_unitary(circ, decimals=1)
print('[')
for row in matrix.data:
    print(list(map(real, row)))
print(']')
```

### Exercise 7


```python
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
