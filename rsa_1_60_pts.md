# RSA 1 (60 pts)

---

## Problem

---

## Solution
1) The N's have common factors (re-used p or q), which is why they're all so similar.
2) Take gcd() of 2 different N's to get one of its factors, p. N/p to get the other factor q.
3) Do a standard RSA decryption to get each message.
4) Concatenate all the decimal plaintexts, and convert to hex.
5) Convert hex to ascii for flag.
---

## Flag


