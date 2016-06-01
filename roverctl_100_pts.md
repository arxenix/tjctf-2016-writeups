# roverctl (100 pts)

---
## Problem
Looks like the backdoor you installed on Pathfinder is working; you've captured [these packets](https://static.tjctf.org/rover_7c2be0252685607348be5d53e10fa9d7f2f01322f6dbd2ecd07e2d4e780fddbe.pcapng). But what do they all mean? ([maybe this will help](https://static.tjctf.org/roverctl_3b27c9f5ceed7ff2edf0640f47850b198966e69e3f097ae87d07bcc7773ba64f.pdf)) 

## Solution
The packets are a `pcapng` file, which can be opened by Wireshark. We use the follow stream command to see the TCP exchange. Looking at the given PDF, we see that the packets have a image transfer followed by a series of camera rotation commands. 

First, we assemble the image using the script below. The `img` file is a copy everything sent by the rover. 
```python
import numpy as np
# import scipy.misc.pilutil as smp
from scipy import misc as smp

with open('img') as d:
    rover = d.readlines()
rover = ''.join(rover)
rover = rover.replace(' ', '').replace('\n', '')

# Create a 800x600x3 array of 8 bit unsigned integers
img = np.zeros( (800,600,3), dtype=np.uint8 )

# data[512,512] = [254,0,0]       # Makes the middle pixel red
# data[512,513] = [0,0,255]       # Makes the next pixel blue
for x in range(800):
    for y in range(600):
        chars = rover[:2]
        rover = rover[2:]
        pixel = int('0x' + chars, 16)
        #print pixel
        img[x,y] = [pixel, pixel, pixel]

print "Rover", rover
print "generated data"

pic = smp.toimage( img )       # Create a PIL image
pic.show()                      # View in default viewer
```

We generate the following image: ![](rover_output.png)
We realize that this image is related to The Martian movie. These papers are surrounding the rover, and go from 0 to F. This gives 17 spots, including the sign in the middle. We use the camera rotation packets (where the `diff`s come from, after dividing by 360/17=21) to find some hex values:
```python
signs = ["S", "0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "A", "B", "C", "D", "E", "F"]

diffs = [8, -3, 2, 4, -4, -3, 4, -3, 2, 0, 1, 4, -5, 3, -4, 10, -9, 6, -6, 3, -3, 5, -8, 0, 2, 10, -8, -7, 6, 9, -8, -3, 2, -5, 6, -3, -1, -3, 3, 0, 4, -4, 4, 6]

idx = 0
for dif in diffs:
    idx += dif
    print signs[idx]
```

This produces `746a6374667b695f6c696b335f706f7461743033737d`. Converting to ASCII, we find the flag.

## Flag
Flag: `tjctf{i_lik3_potat03s}`
