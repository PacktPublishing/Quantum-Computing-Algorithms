```python
from qiskit import QuantumRegister, \
    ClassicalRegister, QuantumCircuit

qReg = QuantumRegister(2, 'q')
qRegNew = QuantumRegister(2, 'qNew')
cReg = ClassicalRegister(2, 'c')
cRegNew = ClassicalRegister(1, 'cNew')
circuit = QuantumCircuit(qReg, qRegNew, cReg, cRegNew)
display(circuit.draw('latex'))
```


```python
from qiskit import QuantumCircuit

circuit = QuantumCircuit(1, 1)
circuit.h(0)
circuit.measure([0], [0])
display(circuit.draw('latex'))
```


```python
from qiskit import QuantumCircuit

circuit = QuantumCircuit(2, 4)
circuit.h(0)
circuit.barrier()
circuit.measure([1, 0], [2, 3])
display(circuit.draw('latex'))
```


```python
from qiskit import QuantumCircuit

circuit = QuantumCircuit(2)
circuit.h([0, 1])
circuit.measure_all()
display(circuit.draw('latex'))
```


```python

```
