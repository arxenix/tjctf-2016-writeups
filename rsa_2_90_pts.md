# RSA 2 (90 pts)

---

## Problem

---

## Solution
1) The patterns in the base 5 representation are rather interesting... It's somewhat similar to my RSA problem for CryptoCTF!
2) Realize that in order for the repeated string of 1s in the middle to occur, the 2 factors must be of the form 1...1 (a ones) and 1...1 (b ones), in base 5
3) a must be equal to the number of digits in the first repeating pattern, plus the number of repeated ones + 1
4) b must be equal to the number of digits in the first repeating pattern+1
5) Test to see if it works, then do a standard RSA decryption routine
6) [put code here? or at least what a and b are]

---

## Flag

`tjctf{7FtNg1Vu7bbAhszil8IBveu3jlxFhxsZDpV5AapNYQqnxz09VAHG4gOodNdj6SGcClq1hyVeRi27bdG2jO9Z50aoN6VbT6Z0UQhtoLwJZvRAHfoU7DVM5PXlbMFZFIbw3lUDe6KnnuhWclr5MrpDEAPDkpeuL3DY5WWcWJMOMY7sRflcK6l9UCFaofZqtyG2zmKZYqox}`
