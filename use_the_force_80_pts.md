# Use the Force (80 pts)

---

## Problem
Find the flag. usetheforce.p.tjctf.org 

---

## Solution
1. Our goal is to get the check() function to return true
2. We need to de-obfuscate this...
3. its calling eval() and passing in some globals and locals
  4. We add some print statements to see what exactly its evaluating, and what those globals and locals are
  5. It's trying to evaluate `A[20]`, which is `d(t(n,p(r(l(m(o,x))),t(b,p(m(o,c(y)(a(s))),m(o,c(z)(a(s))))))))`
  6. We look in the globals and locals to see what each of those functions is. The globals dict is `D`, and the locals dict is `dict(zip(A[19], A[:3]), s=text)`
  7. We attempt to simplify the function:
  8. `return not all(itertools.starmap(operator.contains,zip(itertools.repeat([0, 32]),itertools.starmap(operator.sub,zip(map(ord,str(s).lower()),map(ord,str(s).upper()))))))`
  9. The part that confused us the most was the "contains" and "sub" functions. We didn't realize they were from operator at first.
4. Essentially, what this function is doing, is subtracting s.upper() from s.lower(), and checking if the character differences are all either 0 or 32
5. You would think this would always be true, since s.lower() on a non-letter returns the same thing, and the difference between uppercase and lowercase characters is 32 in ASCII
6. Realize that s.upper() and lower() also works on unicode characters, such as `à`, and the difference wouldn't be 32. Input this to the website, and it will give you the flag

---

## Flag
`tjctf{99f47fbbc74e814a9a00a6458d4e5c12}`