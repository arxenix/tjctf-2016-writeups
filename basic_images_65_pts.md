# Basic Images (65 pts)

---

## Problem
Check out my [shades](https://static.tjctf.org/shades_94e8895ce768575f55dd91819c8e5db7d9d7a913062dd8f2d4bc1742c0c46ecc.png)! If you want the flag, I guess you should be basic like me. Good luck! 

---

## Solution

We take the RGB value (any work) of the gray pixels, and convert to ascii. This re convert from base64 to hex -> stick into new png image. repeat. I wrote a PIL program to do this, which took in the picture, and outputted into another file the base64 data.
The images we get:<br>
original:<br>
![](shades.png)
<br>
Iteration 1:<br>
![1](base64.png)
<br>
Iteration 2:<br>
![2](base642.png)
<br>
Iteration 3:<br>
![3](base643.png)
<br>
Iteration 4:<br>
![4](base645.png)
<br>
Iteration 5:<br>
![6](base646.png)
<br>
Iteration 7:<br>
![7](base647.png)
<br>
Finally we convert this data from base64 to ascii for the flag.

---

## Flag
`tjctf{asc11_c0l0r_inc3pt1on}`