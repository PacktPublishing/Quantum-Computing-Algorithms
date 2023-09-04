### Qiskit code for the BB84 algorithm


```python
import random
from qiskit import QuantumCircuit, QuantumRegister, \
    ClassicalRegister, Aer, execute

NUMBER_OF_CIRCUITS = 100
DOES_EVE_EXIST = False
CHECK_MARK = u'\u2713'
```


```python
def create_registers(eve_exists):
    alice_q = QuantumRegister(1, 'alice_q')
    bob_q = QuantumRegister(1, 'bob_q')
    bob_c = ClassicalRegister(1, 'bob_c')

    if eve_exists:
        eve_c = ClassicalRegister(1, 'eve_c')
        circ = QuantumCircuit(alice_q, bob_q, bob_c, eve_c)
    else:
        circ = QuantumCircuit(alice_q, bob_q, bob_c)
    return circ
```


```python
def setup_alice(circ):
    alice_q = circ.qubits[0]

    if random.getrandbits(1):
        circ.x(alice_q)

    if random.getrandbits(1):
        circ.h(alice_q)

    return circ


def setup_bob(circ):
    bob_q = circ.qubits[1]
    bob_c = circ.clbits[0]

    if random.getrandbits(1):
        circ.h(bob_q)

    circ.measure(bob_q, bob_c)
    return circ


def setup_eve(circ):
    bob_q = circ.qubits[1]
    eve_c = circ.clbits[1]

    circ.barrier()
    circ.measure(bob_q, eve_c)
    circ.barrier()
    return circ
```


```python
def make_new_circuit(eve_exists):
    circ = create_registers(eve_exists)
    alice_q = circ.qubits[0]
    bob_q = circ.qubits[1]
    bob_c = circ.clbits[0]

    circ = setup_alice(circ)

    circ.swap(alice_q, bob_q)

    if eve_exists:
        circ = setup_eve(circ)

    circ = setup_bob(circ)

    return circ
```


```python
def create_circuits(how_many, does_eve_exist):
    circuits = []
    for i in range(how_many):
        circuits.append(make_new_circuit(does_eve_exist)) 
    return circuits
```


```python
def run_the_job(circuits):
    device = Aer.get_backend('qasm_simulator')
    job = execute(circuits, backend=device, shots=1, memory=True)
    return job.result()
```


```python
def print_alice_bits(circuits):
    print('alice bits: ', end='')
    for circ in circuits:
        bit = 1 if 'x' in circ.count_ops() else 0
        print(bit, end='')
    print('')
```


```python
def bob_bit_value(circ, memory):
    return memory[0][0]
```


```python
def print_bob_bits(circuits, result):
    print('bob bits  : ', end='')
    for circ in circuits:
        memory = result.get_memory(circ)
        print(bob_bit_value(circ, memory), end='')
    print('')
```


```python
def had_agreement(circ):
    gate_counts = circ.count_ops()
    return not ('h' in gate_counts and gate_counts['h'] == 1)
```


```python
def print_had_agreements(circuits):
    number_of_agreements = 0
    print('hads agree? ', end='')
    for circ in circuits:
        if had_agreement(circ):
            print(CHECK_MARK, end='')
            number_of_agreements += 1
        else:
            print(' ', end='')
    print('')
    return number_of_agreements
```


```python
def alice_bit_value(circ):
    return 1 if 'x' in circ.count_ops() else 0
```


```python
def bit_value_agreement(circ, result):
    memory = result.get_memory(circ)
    return alice_bit_value(circ) == int(
        bob_bit_value(circ, memory))
```


```python
def print_bit_agreements(circuits, result,
                         number_of_agreements):
    number_tested = 0
    is_eve_detected = False
    i = 0

    print('bits agree? ', end='')
    while number_tested < number_of_agreements // 2:
        if had_agreement(circuits[i]):
            if bit_value_agreement(circuits[i], result):
                print(CHECK_MARK, end='')
                number_tested += 1
            else:
                is_eve_detected = True
                print('X')
                break
        else:
            print(' ', end='')
        i += 1

    print()

    return i, is_eve_detected
```


```python
def print_key(circuits, number_of_circuits, how_many_for_testing):
    print('key       :', end='')
    for i in range(how_many_for_testing + 1):
        print(' ', end='')
    for i in range(i, NUMBER_OF_CIRCUITS):
        if had_agreement(circuits[i]):
            print(alice_bit_value(circuits[i]), end='')
        else:
            print(' ', end='')
```


```python
circuits = create_circuits(NUMBER_OF_CIRCUITS,
                           DOES_EVE_EXIST)             # 1

result = run_the_job(circuits)                         # 2

print_alice_bits(circuits)                             # 3

print_bob_bits(circuits, result)                       # 4

number_of_agreements = print_had_agreements(circuits)  # 5

how_many_for_testing, is_eve_detected = \
    print_bit_agreements(circuits, result,
                         number_of_agreements)         # 6

if is_eve_detected:                                    # 7
    print('INTRUDER ALERT!')
else:
    print_key(circuits, NUMBER_OF_CIRCUITS,
              how_many_for_testing) 
```

### Question 6


```python
from qiskit import QuantumRegister, QuantumCircuit
from math import pi
import random
alice_q = QuantumRegister(1, 'alice_q')
bob_q = QuantumRegister(1, 'bob_q')
circ = QuantumCircuit(alice_q, bob_q)
circ.ry(pi/(random.uniform(2, 20)), alice_q[0])
circ.cnot(0, 1)
circ.measure_all()
display(circ.draw('latex'))
```

### Question 7


```python
def setup_eve(circ):
    bob_q = circ.qubits[1]
    eve_c = circ.clbits[1]
    
    has_had = random.getrandbits(1)
    circ.barrier()
    if has_had:
        circ.h(bob_q)
    circ.measure(bob_q, eve_c)
    if has_had:
        circ.h(bob_q)
    circ.barrier()
    return circ

def had_agreement(circ):
    gate_counts = circ.count_ops()
    return not ('h' in gate_counts and gate_counts['h'] % 2 == 1)
```
