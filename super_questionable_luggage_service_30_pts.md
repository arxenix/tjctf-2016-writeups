# Super Questionable Luggage Service (30 pts)

---

## Problem

---

## Solution
It seems that the "retrieve luggage" function is vulnerable. Plugging in `' OR ''='`, we are able to dump the whole database. Scrolling down, we see something interesting:
![](eJwNyFEOgyAMANC7cABqg0DxNgQJmmlLoGYfy-6-vc_3Mc-4zGYO1T43gP2cRcZup8rIrdom0q6a-zltkRuyai7HXVknIHlMMUQMtFIiwvCv4H1afMK4EjnnFnj4xfJm27mZ7w8MliMA.jXJK0kDD6eburM62x1HJ7Vo5MqU.png)
We reverse this string for the flag.

---

## Flag
`tjctf{th1s_m4d3_1t_e4s1er}`