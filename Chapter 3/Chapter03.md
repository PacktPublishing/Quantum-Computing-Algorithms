### Combining gates along a single wire


```python
from qiskit import QuantumRegister, QuantumCircuit
reg = QuantumRegister(1)
circuit = QuantumCircuit(reg)
circuit.x(reg[0])
circuit.h(reg[0])

#circuit.x(reg[0])

#circuit.h(reg[0])
#circuit.h(reg[0])

display(circuit.draw('latex', initial_state=True))
```


```python
from qiskit.quantum_info import Statevector
from qiskit.visualization \
    import plot_bloch_multivector 

vector = Statevector(circuit)  
display(plot_bloch_multivector(vector.data))
```


```python
from qiskit import Aer, execute
device = Aer.get_backend('qasm_simulator')

#from qiskit_ibm_provider import IBMProvider
#provider = IBMProvider()
#device = provider.get_backend('ibmq_lima')

circuit.measure_all()
job = execute(circuit, backend=device, shots=1000)
print(job.job_id())

result = job.result()
counts = result.get_counts(circuit)
print(counts)
```

### Experimenting with rotations


```python
from qiskit import QuantumRegister, QuantumCircuit
from math import pi
reg = QuantumRegister(1)
circuit = QuantumCircuit(reg)
circuit.ry(pi/2, reg[0])
#circuit.ry(pi/3, reg[0])
display(circuit.draw('latex'))
```


```python
from qiskit import Aer, execute
device = Aer.get_backend('qasm_simulator')

circuit.measure_all()
job = execute(circuit, backend=device, shots=1000)
print(job.job_id())

result = job.result()
counts = result.get_counts(circuit)
print(counts)
```


```python

```
