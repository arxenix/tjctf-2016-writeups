# Guess (120 pts)

---

## Problem
Can you guess the numbers? <a href="|guess.c|">guess.c</a> <a href="|guess|">guess</a><br/>`nc p.tjctf.org 8007`

---

## Solution
Looking at the code, we can make a critical initial observation: we see that the random number generator is seeded with the current time. Thus, we know that we can generate the same random numbers that the server does, and thus "guess" the right answer all 100 times. 

One challenge in this approach is that the server may not have the exact same time as our local system. To solve this, we generated all different random numbers that we could have given different time offsets between the local system and the server. We had an offset of about 150 seconds, but it is likely different for each machine. 

Knowing the offset allows us to generate the exact same random numbers as the server. Because of network latency and other problems, we found (through trial and error) that the optimal solution would send a number of guesses together (all the same random number, as they would be processed at the same time and seed), and wait a set amount of time between these batch sends. 

This is the code we used to solve the problem:
```python
BRET THIS IS FOR YOU
```

---

## Flag
`tjctf{n0t_so_r4ndom_4nymor3}`
