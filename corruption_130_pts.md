# corruption (130 pts)

---

## Problem

---

## Solution
1) Run `pngcheck corrupted.png`.<br>
2) We see that the file is corrupted.<br>
3) Open the file in a hex editor.<br>
4) We see that every chunk length and checksum is messed up, as well as the IHDR being blank.<br>
5) Fix all the chunk lengths and checksums<br>
6) Check the top row of every possible bit depth/color type combo to find the ones that look most legitimate.<br>
7) Grayscale with alpha channel seemed most legitimate.<br>
8) Brute force create images of widths up to 1000 with an arbitrary set height (also need to fix checksums of each image).<br>
9) Scroll through these images.<br>
10) We see the flag appears in one of the images.<br>
insert the image here pls

---

## Flag
`tjctf{c0rruption? wh4t corrupt10n?}`