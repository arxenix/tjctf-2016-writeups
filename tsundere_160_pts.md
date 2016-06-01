# tsundere (160 pts)

---

## Problem

---

## Solution
1) Reverse some of the bytecode manually to try and figure out what its doing.<br>
2) The flag text must consist of only lowercase acsii characters and numbers.<br>
3) The regex splits up the flag string into chunks of 5 characters. It calls the ? function on each chunk of 5 characters, and concatenates the results, checking if the final result is equal to the String `TODO - Put right string here`<br>
4) Guess that the ? function is a hashing function. It contains a sin/cos call, 4 "secret" numbers, and a bit rotateLeft call, so we guess that it is md5, based on this source code for [md5 in java](https://rosettacode.org/wiki/MD5/Implementation#Java).<br>
5) Split the final string into chunks of 32 characters, and brute force md5 hashes of chunks of 5 lowercase characters and numbers, to reverse each hash. We can easily plug the hashes into [hashkiller](hashkiller.co.uk) for the individual hashes, which we concatenate into the flag.

---

## Flag
`tjctf{j4hbjllsqyndjonfn6czn40f322p2ttgvbtg1m9pi33o8gmu368dkzpk7nldb8ztcdi3nb6ovjtawtovmmna2a67rjjoz6oun1if}`