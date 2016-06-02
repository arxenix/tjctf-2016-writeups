# One Time Subtraction (20 pts)

---

## Problem
I encrypted this flag with a one time pad, but my friend says it's not secure because my key is only one byte. Can you check if it is secure?

---


## Solution
This was a tough one as well, though not quite as tough as `R U Insane`. That one was insane.

We are given the following ciphertext: `241 231 224 241 227 248 173 235 176 220 223 246 241 176 220 174 240 220 235 173 241 220 176 235 173 242 228 229 250 135`. We know that all flags are in the form `tjctf{XXX}`. From the problem title, we predict that we should subtract a constant "key" from each value, and convert to a character using ASCII. 

This python script embodies this idea. We use a key of 125 because `241-125='t'`.

```python
key = 125
cipher = [int(x) for x in "241 231 224 241 227 248 173 235 176 220 223 246 241 176 220 174 240 220 235 173 241 220 176 235 173 242 228 229 250 135".split(' ')]
plain = [x-key for x in cipher]
print ''.join(chr(x) for x in plain)
```

## Flag
`tjctf{0n3_byt3_1s_n0t_3n0ugh}`