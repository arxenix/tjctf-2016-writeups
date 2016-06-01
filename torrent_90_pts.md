# torrent (90 pts)

---

## Problem
Help, someone's sharing flags

---

## Solution
We first read up on torrent file format, and see that the chunk sizes are only 2 bytes. Through the file format specifications, we know that at the end of the torrent file, it consists of the sha1 hashes of each chunk. So, we take the 40-character hashes and do reverse hash lookups on each one to reverse the flag piece-by-piece on [crackstation.net](https://crackstation.net/). Concatenate them for the flag.

---

## Flag
`tjctf{pls_2_n0t_fl4g_share}`