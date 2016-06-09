# Guess (120 pts)

---

## Problem
Can you guess the numbers? <a href="|guess.c|">guess.c</a> <a href="|guess|">guess</a><br/>`nc p.tjctf.org 8007`

---

## Solution
Looking at the code, we can make a critical initial observation: we see that the random number generator is seeded with the current time. Thus, we know that we can generate the same random numbers that the server does, and thus "guess" the right answer all 100 times. 

One challenge in this approach is that the server may not have the exact same time as our local system. To solve this, we generated all different random numbers that we could have given different time offsets between the local system and the server. We had an offset of about 150 seconds, but it is likely different for each machine. 

Knowing the offset allows us to generate the exact same random numbers as the server. Because of network latency and other problems, we found (through trial and error) that the optimal solution would send a number of guesses together (all the same random number, as they would be processed at the same time and seed), and wait a set amount of time between these batch sends. 

This is the code we used to solve the problem:

```C
int main()
{
    int i,checknum;
    int a;
    int time_dank;
    time_dank = time(NULL)+5;
    //1464489575:2146966132
    printf("%d\n",time_dank);
    //1464489576:775630972
    for (i=time_dank;i<time_dank+101;i++)
    {
        srand(i+152);
        a=rand();
        printf("%d:%d\n",i,a);
    }
}
```

That script's output was redirected to a file for this next script to use as input.
```python
import calendar
import random
import subprocess
import time
from pwn import *
HOST,PORT = "p.tjctf.org", 8007
lis = [""]
g=open("guessfile","r").read().strip().split("\n")
time_dank=int(g[0])
g=g[1:len(g)]
g=[x.split(":") for x in g]
g={int(key): value for (key, value) in g}

print g
while (calendar.timegm(time.gmtime()) != time_dank): pass



time.sleep(0.25)
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST,PORT))
for x in range(100):
    print x
    print s.recv(300)
    s.send((g[calendar.timegm(time.gmtime())+1] + "\n")*10)

    #time.sleep(0.3)


print s.recv(1000)
```

---

## Flag
`tjctf{n0t_so_r4ndom_4nymor3}`
