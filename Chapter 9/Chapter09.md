### Unitary matrices


```python
from qiskit import QuantumCircuit
from qiskit.visualization import visualize_transition

circ = QuantumCircuit(1)
circ.h(0)
circ.s(0)

display(circ.draw('latex', scale=2.5))
visualize_transition(circ, trace=True)
```

### Illustrating Shor's algorithm with Qiskit code


```python
from qiskit import QuantumCircuit, Aer, execute
from qiskit.circuit.library import QFT
from qiskit.tools.visualization import plot_histogram
import numpy as np
```


```python
public_key = 15
coprime = 7
#coprime = 11

vector = []
for i in range(8):
    vector.append(coprime**i % public_key)

norm = np.linalg.norm(vector)
statevector = vector / norm

print('vector:')
print(vector)
print()
print('statevector:')
print(statevector)
```


```python
circ = QuantumCircuit(3)
circ.initialize(statevector)
```


```python
circ.append(QFT(3), [0, 1, 2])
circ.measure_all()
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

### Another implementation of Shor's algorithm


```python
from qiskit import QuantumCircuit, Aer, execute
from qiskit.circuit.library import QFT
from qiskit.visualization import plot_histogram
```


```python
_7k_mod15 = QuantumCircuit(4)
_7k_mod15.x([0, 1, 2, 3])
_7k_mod15.swap(1, 2)
_7k_mod15.swap(2, 3)
_7k_mod15.swap(0, 3)
display(_7k_mod15.draw('latex'))
```


```python
def _7EPXn_mod15(n):
    circ = QuantumCircuit(4)
    for k in range(n):
        circ = circ.compose(_7k_mod15, qubits=[0, 1, 2, 3])       
      
    gate = circ.to_gate(label='(7^' + str(n) + ') mod 15')   
    return gate.control(1, ctrl_state='1')
```


```python
circ = QuantumCircuit(7, 3)
circ.h([0, 1, 2])
circ.x(3)
circ.barrier()

circ.append(_7EPXn_mod15(1), [0, 3, 4, 5, 6])
circ.append(_7EPXn_mod15(2), [1, 3, 4, 5, 6])
circ.append(_7EPXn_mod15(4), [2, 3, 4, 5, 6]) # NOT NEEDED

circ.append(QFT(3).inverse(), [0, 1, 2])
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

### Question 10


```python
phase = None
circ = QuantumCircuit(2, 1)
circ.h([0, 1])
circ.rz(phase, 1)
circ.cnot(0, 1)
circ.h(0)
circ.measure([0], [0])
display(circ.draw('latex'))

provider = IBMProvider()
device = provider.get_backend('ibmq_qasm_simulator')
job = execute(circ, device, shots=1000)
result = job.result()
counts = result.get_counts(circ) 
print(counts)
```
