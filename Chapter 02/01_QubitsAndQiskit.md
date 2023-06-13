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
from qiskit import IBMQ 

provider = IBMQ.load_account()
available_cloud_backends = provider.backends() 
print('\nCloud backends:')
for i in available_cloud_backends: print(i)
```


```python
from qiskit import execute
from qiskit.visualization import plot_histogram

device = provider.get_backend('ibmq_qasm_simulator')
job = execute(circuit, backend=device, shots=1000)
print(job.job_id())

result = job.result()
counts = result.get_counts(circuit)

print(counts)
display(plot_histogram(counts))
```


```python

```
