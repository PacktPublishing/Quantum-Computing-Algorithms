```python
import numpy as np
import random
from qiskit import QuantumCircuit, QuantumRegister, \
    ClassicalRegister, IBMQ, execute
from qiskit.result import marginal_counts
```


```python
def create_registers():
    alice_q = QuantumRegister(1, 'alice (q)')
    peter_alice_q = \
        QuantumRegister(1, 'peter/alice (q)')
    peter_bob_q = QuantumRegister(1, 'peter/bob (q)')
    bob_c = ClassicalRegister(3, 'bob (c)')
    circ = QuantumCircuit(alice_q, peter_alice_q,
                          peter_bob_q, bob_c)
    return circ
```


```python
def generate_amplitudes():
    alpha = np.sqrt(random.uniform(0, 1))
    beta = np.sqrt(1 - alpha**2)
    return alpha, beta
```


```python
def add_gates(circ, alpha, beta):
    circ.initialize([alpha, beta], 0)
    circ.barrier()
    circ.h(1)
    circ.cnot(1, 2)
    circ.barrier()
    circ.cnot(0, 1)
    circ.h(0)
    circ.barrier()
    circ.measure(0, 0)
    circ.measure(1, 1)
    circ.x(2).c_if(1, 1) 
    circ.z(2).c_if(0, 1)
    circ.measure(2, 2)
    return circ
```


```python
alpha, beta = generate_amplitudes()
circ = create_registers()
circ = add_gates(circ, alpha, beta)
display(circ.draw('latex', cregbundle=False))
```


```python
provider = IBMQ.load_account()
device = provider.get_backend('ibmq_qasm_simulator')
shots = 1000
job = execute(circ, backend=device, shots=shots)
print(job.job_id())

result = job.result()
counts = result.get_counts(circ)
counts_m = marginal_counts(counts, [2])
number_of_0s = counts_m.get('0') 
number_of_1s = counts_m.get('1') 
alpha = np.sqrt(number_of_0s / shots)
beta = np.sqrt(number_of_1s / shots)
print("|\u03C8> ({:.4f}, {:.4f})".format(alpha, beta))
```


```python

```
