# RSA 1 (60 pts)

---

## Problem

---

## Solution

1. The N's have common factors (re-used p or q), which is why they're all so similar.
2. Take gcd() of 2 different N's to get one of its factors, p. N/p to get the other factor q.
3. Do a standard RSA decryption to get each message.
4. Concatenate all messages to get the flag

Code below:
```
f = open('rsa1.txt').readlines()

import random


def rabinMiller(num):
    # Returns True if num is a prime number.

    s = num - 1
    t = 0
    while s % 2 == 0:
        # keep halving s while it is even (and use t
        # to count how many times we halve s)
        s = s // 2
        t += 1

    for trials in range(5): # try to falsify num's primality 5 times
        a = random.randrange(2, num - 1)
        v = pow(a, s, num)
        if v != 1: # this test does not apply if v is 1.
            i = 0
            while v != (num - 1):
                if i == t - 1:
                    return False
                else:
                    i = i + 1
                    v = (v ** 2) % num
    return True

def egcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        g, y, x = egcd(b % a, a)
        return (g, x - (b // a) * y, y)

def modinv(a, m):
    g, x, y = egcd(a, m)
    if g != 1:
        raise Exception('modular inverse does not exist')
    else:
        return x % m

ns=[]
cs=[]
for ln in f:
	if ln.startswith("n = "):
		ns.append(int(ln.replace("n = ","")))
	elif ln.startswith("c = "):
		cs.append(int(ln.replace("c = ","")))

ps = []
qs = []

for n in ns:
	p = egcd(ns[0], n)[0]
	ps.append(p)
	qs.append(n/p)

# fix incorrect ones manually
ps[0] = egcd(ns[0], ns[1])[0]
qs[0] = ns[0]/ps[0]

ps[-1] = egcd(ns[-1], ns[-3])[0]
qs[-1] = ns[-1]/ps[-1]

ps[-3] = egcd(ns[-3], ns[-1])[0]
qs[-3] = ns[-3]/ps[-3]

ps[-2] = egcd(ns[-2], ns[-1])[0]
qs[-2] = ns[-2]/ps[-2]

#check to see if factored correctly
for i in range(len(qs)):
	print "factored successfully:"
	print qs[i]*ps[i]==ns[i]
	print "factors prime?"
	print rabinMiller(qs[i]) and rabinMiller(ps[i])


# decrypt everything..
ms = []
for i in range(len(qs)):
	phi = (qs[i]-1)*(ps[i]-1)
	d = modinv(65537, phi)
	ms.append(pow(cs[i],d,ns[i]))


print "check decryptions..."
#check decryptions..
for i in range(len(qs)):
	c = pow(ms[i], 65537, ns[i])
	print c==cs[i]

print "tostring..."
for m in ms:
	print (hex(m)[2:])[:-1].decode('hex')
```

---

## Flag

`tjctf{pp6nh3uqjh2a4karaze0kt70yiq22hkpcdzutyoo4aand40l3or73kehrb8n4ft1zh9g7i00lxss83p083dlwxa70brhl6qagx1s6mq8edozln7urps6cbd6kd01g0kvtzbg5e6nhev394777lhs427cpcmi96dc226hrpqwcl6uji1346jh4cfuz3w9i0m25s5ulkilatyjg7nsbi6jka2mocchqf8jxpfypbocqq1fj4cqgdydys0bt7j3h572bgfq6rt3rdgy2q33zyhgqoj3u3il8gchqg1bof6kmt1fpo1ragzxlx6baqt2u7baw0r77c8k2cb92qmto3kgagb45vmooyz9482btindgjuq7tgiwr10iau1icv1hhtll5zrz4oejyk0h26kfbglx25nvu5m1b1d9pn3x9reev55oaaqqoauep3h3bvh011pxgxfm09kbixxurghbw3r4pntdcqha2g287stdy3tev7op}`
