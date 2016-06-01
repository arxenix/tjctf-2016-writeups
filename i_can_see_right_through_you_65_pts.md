# I can see right through you! (65 pts)

---

## Problem
Bet you can't find the tasty flag I put into [this image](https://static.tjctf.org/i_can_see_right_through_you_ce76e115558c75229b06eb050a3e1755d108dc7f364e17b212c8fd0bccff1dd1.png)! Don't worry, I fried it first. 

---

## Solution

Based on the problem statement, we are given 2 very important clues: fried foods, and the alpha (transparency) channel. Inspecting it, we see that there are only 2 values present: 240 and 255. And there's a cipher we know with only 2 values: the Baconian Cipher! Bacon's a fried food too, so we know we're on the right track. We write a quick program to solve the problem (the baconian cipher part is copy+pasted):
```python
from PIL import Image
from unicodedata import normalize
import optparse
import codecs
import sys
import re
def generate_dict():

    """
    Create Bacon dictionary.
    a   AAAAA   g     AABBA   n    ABBAA   t     BAABA
    b   AAAAB   h     AABBB   o    ABBAB   u-v   BAABB
    c   AAABA   i-j   ABAAA   p    ABBBA   w     BABAA
    d   AAABB   k     ABAAB   q    ABBBB   x     BABAB
    e   AABAA   l     ABABA   r    BAAAA   y     BABBA
    f   AABAB   m     ABABB   s    BAAAB   z     BABBB
    :return: Bacon dict
    """

    bacon_dict = {}

    for i in xrange(0, 26):
        tmp = bin(i)[2:].zfill(5)
        tmp = tmp.replace('0', 'a')
        tmp = tmp.replace('1', 'b')
        bacon_dict[tmp] = chr(65 + i)

    return bacon_dict


def encode(words, bacon_dict):

    """
    Encrypt text to Bacon's cipher.
    :param words: string to encrypt
    :param bacon_dict: Bacon dict
    :return: encrypted string
    """

    cipher = ''
    bacon_dict = {v: k for k, v in bacon_dict.items()}  # hack to get key from value - reverse dict
    words = normalize('NFKD', words).encode('ascii', 'ignore')  # replace national characters to ASCII equivalents
    words = words.upper()
    words = re.sub(r'[^A-Z]+', '', words)

    for i in words:
            cipher += bacon_dict.get(i).upper()
    return cipher


def decode(words, bacon_dict):

    """
    Decrypt Bacon's cipher to text.
    :param words: string to decrypt
    :param bacon_dict: Bacon dict
    :return: decrypted string
    """

    cipher = ''
    words = words.lower()
    words = re.sub(r'[^ab]+', '', words)

    for i in xrange(0, len(words) / 5):
        cipher += bacon_dict.get(words[i * 5:i * 5 + 5], ' ')
    return cipher
im = Image.open("i_can_see_right_through_you.png")
width, height = im.size
out = ''
count = 0
#a=open('outshade8.txt','w')
for pixel in im.getdata():
    if pixel[3]==240:
        out += 'B'
    else:
        out += 'A'
print(decode(out,generate_dict()))
```
Running it, we get a really long text, and inside we find this:
`THEFLAGISTJCTFOPENBRACEBACONMMMMMMTASTYCLOSEBRACE`

---

## Flag
`tjctf{baconmmmmmmtasty}`
