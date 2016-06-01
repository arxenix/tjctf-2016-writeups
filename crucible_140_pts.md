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

My shitty implementation of this decryption procedure is below (hardcoded for a length 26 ciphertext):
```python
from decimal import *
from matrix import *
from z3 import *

getcontext().prec = 500

size = 26
rd = Decimal(0.5)

encrypted = open('encrypted.txt').read()
encrypted = encrypted.replace("[","").replace("]","").replace("Decimal(","").replace(")","").replace("'","").split(", ")

encrypted = [Decimal(x) for x in encrypted]

matrix = [[0 for x in range(size)] for y in range(size)]
for i in range(size):
    if i == 0:
        matrix[i] = [2 + 2 * rd, -rd] + [Decimal(0)] * (size - 2)
    elif i == size - 1:
        matrix[i] = [Decimal(0)] * (size - 2) + [-rd, 2 + 2 * rd]
    else:
        matrix[i] = [Decimal(0)] * (i - 1) + [-rd, 2 + 2 * rd, -rd] + [Decimal(0)] * (size - i - 2)

inv = invert(matrix)

a = Real('a')
b = Real('b')
c = Real('c')
d = Real('d')
e = Real('e')
f = Real('f')
g = Real('g')
h = Real('h')
i = Real('i')
j = Real('j')
k = Real('k')
l = Real('l')
m = Real('m')
n = Real('n')
o = Real('o')
p = Real('p')
q = Real('q')
r = Real('r')
s = Real('s')
t = Real('t')
u = Real('u')
v = Real('v')
w = Real('w')
x = Real('x')
y = Real('y')
z = Real('z')
alph = 'abcdefghijklmnopqrstuvwxyz'

def unmix(curr):
	solver = Solver()
	solver.add(a == curr[0])
	solver.add(z == curr[25])
	for var in range(24):
		#Decimal(2) * curr[x + 1] + rd * (curr[x] - Decimal(2) * curr[x + 1] + curr[x + 2])
		evalline = 'solver.add(Decimal(2) * '+alph[var+1]+' + rd*('+alph[var]+' - Decimal(2) * '+alph[var+1] + ' + '+alph[var+2]+')== curr['+str(var+1)+'])'
		#print evalline
		eval(evalline)
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
    encrypted = unmix(encrypted)

encrypted = [chr(int(x.to_integral_value())) for x in encrypted]
print encrypted
```