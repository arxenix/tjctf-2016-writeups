# Super Questionable Luggage Service (30 pts)

---

## Problem
I was using this great luggage storing service, but I accidentally stored my flag with them. Fortunately, my flag went in the wrong way, so it shouldn't be difficult for you to find. Right? super-questionable-luggage.p.tjctf.org 

---

## Solution
It seems that the "retrieve luggage" function is vulnerable to SQL injection. Plugging in `' OR ''='`, we are able to dump the whole database. Scrolling down, we see something interesting:
![](luggage_service.png)
We reverse this string for the flag.

---

## Flag
`tjctf{th1s_m4d3_1t_e4s1er}`