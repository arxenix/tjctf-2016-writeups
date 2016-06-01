# R U Insane? (5 pts)

---

## Problem
Copy and paste the flag into the box below. Use this flag format for all problems unless specified otherwise. `tjctf{y4y_u_R_5aN3}`

---

## Solution
This problem stumped us for a while. We were given a strange, unreadable ciphertext: `tjctf{y4y_u_R_5aN3}`, and we wished to find the flag. We tried ROT-13, thinking that since it was an easy problem that would be the solution, but it didn't work. We then proceeded to try every cipher available on rumkin, but none of those returned anything intelligible either. Resigned, we decided to take a break from this problem for a while.

After solving a few more problems, we decided to try our hand at this problem again. This time, we were more lucky. We had the idea of chaining TWO ciphers together, but that didn't work. But as the saying goes, third time's the charm. We tried three ciphers, and we realized that the sequence double-xor and ROT-26 worked (in any order). The key for the double-xor, which we got from extensive recon, was `tjctf_izz_thr33_h4rd`.

TL;DR double-xor, rot-26

Alternatively you could just copy and paste the flag, but that's just cheating.

---

## Flag
`tjctf{y4y_u_R_5aN3}`