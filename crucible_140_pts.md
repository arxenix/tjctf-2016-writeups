# Crucible (140 pts)

---

## Problem

crucible.py matrix.py encrypted.txt 

---

## Solution

1. Our goal is to decrypt the encrypted.txt file and get the plaintext flag
2. First, we must understand what the encryption process it:
  3. Sets `curr` equal to the integer array of the flag's ordinal values
  4. Generates some matrix, `matrix`, based on the flag length
  5. Sets `inv` equal to the matrix inverse of `matrix`
  6. Repeats 100 times:
    7. Some array transformation on `curr`:
    8. `temp = [curr[0]] + [Decimal(2) * curr[x + 1] + r * (curr[x] - Decimal(2) * curr[x + 1] + curr[x + 2]) for x in range(n - 2)] + [curr[n - 1]]`
    8. sets `curr` equal to the matrix multiplication of `inv` and `temp`
  7. Outputs `curr`
3. We can now decrypt with the following procedure:
  4. Let `curr` equal the ciphertext array
  5. Repeat 100 times:
    6. `temp`= Undo the array transformation on `curr`, it is reversible!
    7. set `curr` to the matrix multiplication of `matrix` and `temp`, to undo the matrix multiplication because `matrix` is the inverse of `inv`
  6. Round each element in `curr` to the nearest integer, then convert each integer to a character based on ordinal values

My implementation of this decryption procedure is below:
```python
from decimal import *
from matrix import *
from z3 import *

getcontext().prec = 500

rd = Decimal(0.5)

encrypted = open('encrypted.txt').read()
encrypted = encrypted.replace("[","").replace("]","").replace("Decimal(","").replace(")","").replace("'","").split(", ")

encrypted = [Decimal(x) for x in encrypted]
size = len(encrypted)

matrix = [[0 for x in range(size)] for y in range(size)]
for i in range(size):
    if i == 0:
        matrix[i] = [2 + 2 * rd, -rd] + [Decimal(0)] * (size - 2)
    elif i == size - 1:
        matrix[i] = [Decimal(0)] * (size - 2) + [-rd, 2 + 2 * rd]
    else:
        matrix[i] = [Decimal(0)] * (i - 1) + [-rd, 2 + 2 * rd, -rd] + [Decimal(0)] * (size - i - 2)

inv = invert(matrix)

alph = "abcdefghijklmnopqrstuvwxyz"
varlist = []
for char in alph:
	varlist.append(Real(char))

def undo_transform(curr):
	solver = Solver()
	solver.add(varlist[0] == curr[0])
	solver.add(varlist[-1] == curr[-1])
	for var in range(len(curr)-2):
		solver.add(Decimal(2) * varlist[var+1] + rd*(varlist[var] - Decimal(2) * varlist[var+1] + varlist[var+2])==curr[var+1])
	solver.check()
	model = solver.model()
	ret = [0]*26
	for decl in model.decls():
		name = decl.name()
		sol = model[decl]
		ret[alph.index(name)] = Decimal(sol.as_decimal(500)[:-1])
	return ret

for step in range(100):
    print step
    encrypted = mult(matrix, encrypted)
    encrypted = undo_transform(encrypted)

encrypted = [chr(int(x.to_integral_value())) for x in encrypted]
print encrypted
```