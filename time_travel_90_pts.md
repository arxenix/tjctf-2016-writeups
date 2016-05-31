# Time Travel (90 pts)

---

## Problem


---

## Solution
From the problem statement, we are given the idea of time travel, which brings to mind the [Wayback Machine](http://archive.org/web/). Then, from the hint, we get that there is something about a `<meta>` tag, and since it's Google Apps we know we're supposed to look at `<meta name="google-site-verification">`.

I wrote a quick script to mine this data from the wayback machine site:

```python
from bs4 import BeautifulSoup

import requests

url = 'https://web.archive.org/web/*/tjctf.org'
baseurl = 'https://web.archive.org'
r = requests.get(url)

data = r.text

soup = BeautifulSoup(data)

for link in soup.find_all('a'):
    a = link.get('href')
    print(a)
    if 'tjctf' in a and 'web' in a:
        r2 = requests.get(baseurl+a)
        data2=r2.text
        ind = data2.find('google–site–verification')
        print(data2[ind:ind+100])
```

Although it takes a while to run, it's easier than manually clicking through all those links. We soon see that one of the "verification codes" is longer than the rest: `dGpjdGZ7dzFzaF9pX2MwdWxkX3R1cm5fYjRja190MW0zfSAg`. We conver t from base 64 to ascii for the flag.


---

## Flag

`tjctf{w1sh_i_c0uld_turn_b4ck_t1m3}`
