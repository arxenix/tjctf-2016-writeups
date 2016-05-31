# corruption (130 pts)

---

## Problem

---

## Solution
1) Write a z3 python script that takes in all the constraints. For example, we know that the string given to us is in format `tjctf{...}`, and that it's in ascii. The three digit numbers, then, will always start with 1, and the 2 digit numbers always start with 9 (assuming all lowercase). As such, we already know the values of a lot of the characters:<br>
```
T = 6
Y = 0
v = 6
z = 9
a = 9
b = 0
R = 2
S = 2
X = 3
P = 5
t = 9```

2) Get each letter value from the z3 model.<br>
3) Convert the flag numbers to ascii to get the flag.

---

## Flag
`tjctf{wzodvbycglrhmzxnmtojiozuhxcititgjranratsqaeklmqqxjmmeuqodiqmabamkivukegnypyxqajezykojonqdviyhivnbijs}`