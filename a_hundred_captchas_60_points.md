# A Hundred Captchas (60 points)

---


## Problem
I heard that this server will give you a flag if you solve a few captchas for them. 
`nc p.tjctf.org 8008`

## Solution
We first access the server using the given command, and find that we have to solve 100 captchas within 30 seconds. Realizing this is infeasible to do by hand, we must write a program to do it for us. 

We can see that each character in the captcha is generated using `figlet`. Thus, we write a python script to compare each character to the possible figlet outputs, solving the captcha. This program also handles the connection with the server, and gets the flag when finished. 

```
import socket
from pprint import pprint
import subprocess

#create an INET, STREAMing socket
s = socket.socket(
    socket.AF_INET, socket.SOCK_STREAM)
#now connect to the web server on port 80
# - the normal http port
s.connect(("p.tjctf.org", 8008))

possibles = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 
'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', 
'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z']

# generate a list of all possible characters and their corresponding figlets
figs = {}
for char in possibles:
    sw = subprocess.Popen("figlet "+char, shell=True, stdout=subprocess.PIPE).stdout.read()
    figs[char] = sw

# classify a single character by comparing it with the outputs above
def name_char(char):
    char = '\n'.join(char)+'\n'
    print char
    # print repr(char)
    for poss in possibles:
        works = figs[poss]
        if char == works:
            # print poss, repr(works)
            return poss
        
    return raw_input().strip()

# solve the given captcha
def solve(prob):
    print '\n'.join(prob)
    
    tokens = [] # array (8) of arrays of arrays
    i = 0
    while i < len(prob[2]):
        # all spaces
        spaces = True
        for h in range(len(prob)):
            # print prob[h], i
            if prob[h][i] != ' ':
                spaces = False
                break
        
        if spaces:
            # print "Found space row"
            character = []
            for h in range(len(prob)):
                character.append(prob[h][:i])
                prob[h] = prob[h][i+1:]
            tokens.append(character)
            i=0
        else:
            i = i+1
    tokens.append(prob) # leftovers
    
    ans = ""
    for c in tokens:
        if c[0] == '':
            continue
        this = name_char(c)
        ans += this
    
    return ans
    # return raw_input()

# fetch and solve 100 captchas
solved = 0
while solved < 100:
    prob = s.recv(10000)
    
    # print prob
    prob = prob.split('\n')
    prob = prob[3:-2]
    
    ans = solve(prob)
    
    s.send(ans + "\n")
    
    solved += 1
    # break
    
# output the flag that is given at the end
fin = s.recv(10000)
print fin
```
