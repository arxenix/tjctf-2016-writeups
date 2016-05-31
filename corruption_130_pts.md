# corruption (130 pts)

---

## Problem

---

## Solution
1) Run `pngcheck corrupted.png`.
2) We see that the file is corrupted.
3) Open the file in a hex editor.
4) We see that every chunk length and checksum is messed up, as well as the IHDR being blank.
5) Fix all the chunk lengths and checksums
6) Check the top row of every possible bit depth/color type combo to find the ones that look most legitimate.
7) Grayscale with alpha channel seemed most legitimate.
8) Brute force create images of widths up to 1000 with an arbitrary set height (also need to fix checksums of each image).
9) Scroll through these images.
10) We see the flag appears in one of the images.

---

## Flag