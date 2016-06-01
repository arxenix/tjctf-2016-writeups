# curvature2 (200 pts)

---

## Problem
stuff

---

## Solution

We know it's an ECC problem, and we need to find the exponent they used. We look up past ecc problems, and we find this: http://mslc.ctf.su/wp/polictf-2012-crypto-500/

We see that the curve fits the criteria for this attack, since it’s anomalous – its order is equal to p. Thus we just copy and paste the code and substitute in our own values.

```python
P = 0xd3ceec4c84af8fa5f3e9af91e00cabacaaaecec3da619400e29a25abececfdc9bd678e2708a58acb1bd15370acc39c596807dab6229dca11fd3a217510258d1b
A = 0x95fc77eb3119991a0022168c83eee7178e6c3eeaf75e0fdf1853b8ef4cb97a9058c271ee193b8b27938a07052f918c35eccb027b0b168b4e2566b247b91dc07
Gx = 0xcf634030986cf41c1add87e71d638b9cc723c764059cf4c9b8ed2a0aaf5d51dc770372503ebfaad746ab9220e992c09822916978226465ad31d354a3efee51da
Gy = 0x65eaad8848b2787103fce02358b45d8a61420031989eb6b4b70d82fe20d85583ae542eb8f76749dc640b0f13f682228819b8b2f04bd7a5a17a4c675540fe1c90
B = 7668542654793784988436499086739239442915170287346121645884096222948338279165302213440060079141960679678526016348025029558335977042712382611197995002316466
p = P
E = EllipticCurve(GF(p),[0x95fc77eb3119991a0022168c83eee7178e6c3eeaf75e0fdf1853b8ef4cb97a9058c271ee193b8b27938a07052f918c35eccb027b0b168b4e2566b247b91dc07, 7668542654793784988436499086739239442915170287346121645884096222948338279165302213440060079141960679678526016348025029558335977042712382611197995002316466])
 
g = E(Gx, Gy)
v = E(10150325274093651859575658519947563789222194633356867789068177057343771571940302488270622886585658965620106459791565259790154958179860547267338437952379763, 6795014289013853849339410895464797184780777251924203530417684718894057583288011725702609805686960505075072642102076744937056900144377846048950215257629102)
 
def hensel_lift(curve, p, point):
    A, B = map(long, (E.a4(), E.a6()))
    x, y = map(long, point.xy())
 
    fr = y**2 - (x**3 + A*x + B)
    t = (- fr / p) % p 
    t *= inverse_mod(2 * y, p)  # (y**2)' = 2 * y
    t %= p
    new_y = y + p * t
    return x, new_y
 
# lift points
x1, y1 = hensel_lift(E, p, g)
x2, y2 = hensel_lift(E, p, v)
# calculate new A, B (actually, they will be the same here)
mod = p ** 2
 
A2 = y2**2 - y1**2 - (x2**3 - x1**3)
A2 = A2 * inverse_mod(x2 - x1, mod)
A2 %= mod
B2 = y1**2 - x1**3 - A2 * x1
B2 %= mod

# new curve
E2 = EllipticCurve(IntegerModRing(p**2), [A2, B2])
# calculate dlog
g2s = (p - 1) * E2(x1, y1)
v2s = (p - 1) * E2(x2, y2)
 
x1s, y1s = map(long, g2s.xy())
x2s, y2s = map(long, v2s.xy())
 
dx1 = (x1s - x1) / p
dx2 = (y1s - y1) / p
dy1 = (x2s - x2)
dy2 = (y2s - y2)
m = dy1 * inverse_mod(int(dx1), int(p)) * dx2 * inverse_mod(int(dy2), int(p))
m %= p
 
print(m)
```

We convert the printed message to hex and then ascii for the flag.

---

## Flag
`tjctf{oops_ell1pt1c_curves_R_h4rd}`
