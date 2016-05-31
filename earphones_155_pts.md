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
t = 9
```


```
#!/usr/bin/python

from z3 import *

lines = [line.rstrip('\n').split(' ') for line in open('testz3.in')]
s = Solver()
dict = {}
for line in lines:
    expr = '(((((((((('
    for elem in line:
        multiplier = 1
        if elem == '&' or elem == '^' or elem == '|' or elem == '=' or elem == '+' or elem == '-':
            expr = expr[:-1] + ')' + elem
            if elem == '=': expr += '='
            continue
        for letter in elem[::-1]:
            if not dict.has_key(letter):
                dict[letter] = BitVec(letter, 32)
                print letter
                s.add(0 <= dict[letter] <= 9)
            expr += str(multiplier) + '*dict[\'' + str(letter) + '\']+'
            multiplier*=10
    expr = expr[:-1]
    exec("s.add(" + expr + ")")
s.add(dict['d'] == 1)
#s.add(not all(x == 0 for x in dict.values()))
print(s.check())
print(s.model())
```
2) Get each letter value from the z3 model.
```
flag = 'gWT ZYv za gdT WbR ZSX ZZa Ghr WWZ gbQ ZZJ te GRd Nz dnF gjI gdk gYH GYz dAA GrQ ZZb ZnC GWT Ggg Zbv dnc Wqq qrh WqO gbU grQ aa gVP dGT GnL gZE gQl dbE qZH ay Wqj dqM tu gZB gdo gds zi WYq qji Gbm qjt GWx qZF Zrn dnT gQN qQa gVd WGu qGX dgq ZQQ ZVf ZZs ZVt ty aK aO gQz dbO gYo ZWJ qgi dQD gjZ qVs dGY drd qWh dhd qrQ GGF zD Wnv qbW qhA gRZ dby ZqW gQB dGg Gqj WZl ZYY dGK WQo WAW ZQU qYL Zde ZWn zm dVL dVE Zgo ZSP'
replace = """[w = 4,
 e = 8,
 o = 5,
 Y = 0,
 D = 7,
 q = 1,
 f = 5,
 P = 5,
 E = 6,
 M = 4,
 z = 9,
 S = 2,
 H = 4,
 K = 8,
 t = 9,
 G = 1,
 J = 8,
 u = 7,
 j = 0,
 X = 3,
 b = 0,
 m = 8,
 B = 6,
 O = 7,
 F = 3,
 C = 9,
 N = 9,
 n = 0,
 V = 0,
 U = 4,
 i = 7,
 s = 3,
 R = 2,
 I = 8,
 g = 1,
 T = 6,
 A = 2,
 p = 6,
 y = 7,
 k = 4,
 h = 2,
 Q = 0,
 a = 9,
 l = 3,
 L = 5,
 Z = 1,
 v = 6,
 c = 5,
 W = 1,
 x = 3,
 r = 2,
 d = 1
"""
for map in replace.replace(" ", "").replace("\n", "").replace("[","").replace("]","").split(','):
    flag = flag.replace(map[0], map[2])
print flag
```
3) Convert the flag numbers to ascii to get the flag.

---

## Flag
`tjctf{wzodvbycglrhmzxnmtojiozuhxcititgjranratsqaeklmqqxjmmeuqodiqmabamkivukegnypyxqajezykojonqdviyhivnbijs}`