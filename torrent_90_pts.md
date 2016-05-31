# torrent (90 pts)

---

## Problem

---

## Solution
1) Read up on torrent file format, we see that the chunk sizes are only 2 bytes.
2) At the end of the torrent file, it consists of the sha1 hashes of each chunk.
3) Take the 40-character hashes and do reverse hash lookups on each one to reverse the flag piece-by-piece on [crackstation.net](https://crackstation.net/). Concatenate them for the flag.
---

## Flag
`tjctf{pls_2_n0t_fl4g_share}`