```python
from qiskit import QuantumRegister, QuantumCircuit
reg = QuantumRegister(1)
circuit = QuantumCircuit(reg)
circuit.x(reg[0])
circuit.h(reg[0])
display(circuit.draw('latex', initial_state=True))
```


```python
from qiskit.quantum_info import Statevector
from qiskit.visualization \
    import plot_bloch_multivector 

vector = Statevector.from_label('0')           # 1
vector = vector.evolve(circuit)                # 2
display(plot_bloch_multivector(vector.data))   # 3 
```


```python
from qiskit import IBMQ
from qiskit import execute

provider = IBMQ.load_account()
device = provider.get_backend('ibmq_qasm_simulator')

circuit.measure_all()
job = execute(circuit, backend=device, shots=1000)
print(job.job_id())

result = job.result()
counts = result.get_counts(circuit)
print(counts)
```


```python
from qiskit import QuantumRegister, QuantumCircuit
from math import pi
reg = QuantumRegister(1)
circuit = QuantumCircuit(reg)
circuit.ry(pi/2, reg[0])
display(circuit.draw('latex'))
```


```python

```
