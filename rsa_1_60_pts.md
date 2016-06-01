# RSA 1 (60 pts)

---

## Problem

---

## Solution

1. The N's have common factors (re-used p or q), which is why they're all so similar.
2. Take gcd() of 2 different N's to get one of its factors, p. N/p to get the other factor q.
3. Do a standard RSA decryption to get each message.
4. Concatenate all messages to get the flag
5. Convert hex to ascii for flag.

---

## Flag

`tjctf{pp6nh3uqjh2a4karaze0kt70yiq22hkpcdzutyoo4aand40l3or73kehrb8n4ft1zh9g7i00lxss83p083dlwxa70brhl6qagx1s6mq8edozln7urps6cbd6kd01g0kvtzbg5e6nhev394777lhs427cpcmi96dc226hrpqwcl6uji1346jh4cfuz3w9i0m25s5ulkilatyjg7nsbi6jka2mocchqf8jxpfypbocqq1fj4cqgdydys0bt7j3h572bgfq6rt3rdgy2q33zyhgqoj3u3il8gchqg1bof6kmt1fpo1ragzxlx6baqt2u7baw0r77c8k2cb92qmto3kgagb45vmooyz9482btindgjuq7tgiwr10iau1icv1hhtll5zrz4oejyk0h26kfbglx25nvu5m1b1d9pn3x9reev55oaaqqoauep3h3bvh011pxgxfm09kbixxurghbw3r4pntdcqha2g287stdy3tev7op}`
