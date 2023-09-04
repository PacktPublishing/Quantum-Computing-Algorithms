### Creating and running a quantum circuit


```python
from qiskit import QuantumRegister, \
    ClassicalRegister, QuantumCircuit

qReg = QuantumRegister(1, 'q')
cReg = ClassicalRegister(1, 'c')
circuit = QuantumCircuit(qReg, cReg)
circuit.h(qReg[0])
circuit.measure(qReg[0], cReg[0])
display(circuit.draw('latex'))
```


```python
from qiskit import Aer
device = Aer.get_backend('qasm_simulator')
```


```python
from qiskit import execute
from qiskit.visualization import plot_histogram

job = execute(circuit, backend=device, shots=1000)
print(job.job_id())

result = job.result()
counts = result.get_counts(circuit)

print(counts)
display(plot_histogram(counts))
```

### Finding a backend


```python
from qiskit_ibm_provider import IBMProvider  

provider = IBMProvider()
IBM_cloud_backends = provider.backends(operational=True, 
                                       min_num_qubits=5) 
for i in IBM_cloud_backends: 
    print(i)   
```

### Drawing circuits


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

### Creating a circuit with very little code


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
