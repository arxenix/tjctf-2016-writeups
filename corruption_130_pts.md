# corruption (130 pts)

---

## Problem
Have a free flag! It might have gotten corrupted in transmission, but that shouldn't be a problem for you. Right? 

---

## Solution
1. Run `pngcheck corrupted.png`.<br>
2. We see that the file is corrupted.<br>
3. Open the file in a hex editor.<br>
4. We see that every chunk length and checksum is messed up, as well as the IHDR being blank.<br>
5. Fix all the chunk lengths and checksums. We used [pngcsum](http://schaik.com/png/pngcsum.html) to fix the checksums, and the following code to fix the lengths:<br>
```java
public static void fixLengths() throws IOException {
    byte[] bytes = Files.readAllBytes(Paths.get("corrupted_.png")); // read the image
    int idx = -1;
    int sum = 0;
    int total = 0;
    for(int i = 0; i < bytes.length; i++) {
        // if IDAT or IEND
        if(bytes[i] == (byte)73 && bytes[i+1] == (byte)68 && bytes[i+2] == (byte)65 && bytes[i+3] == (byte)84 ||
                bytes[i] == (byte)73 && bytes[i+1] == (byte)69 && bytes[i+2] == (byte)78 && bytes[i+3] == (byte)68) {
            // if first IDAT continue
            if(idx == -1) {
                idx = i;
                continue;
            }

            // calc difference between this chunk and last one
            int diff = i - idx - 12;
            sum += diff;

            // set appropriate lengths
            bytes[idx-4] = (byte)(diff >> 24);
            bytes[idx-3] = (byte)(diff >> 16);
            bytes[idx-2] = (byte)(diff >> 8);
            bytes[idx-1] = (byte)diff;

            idx = i;
            total++;
            System.out.println("dif " + diff);
        }

        // manually set IEND length
        if(bytes[i] == (byte)73 && bytes[i+1] == (byte)69 && bytes[i+2] == (byte)78 && bytes[i+3] == (byte)68) {
            bytes[i-4] = (byte)0;
            bytes[i-3] = (byte)0;
            bytes[i-2] = (byte)0;
            bytes[i-1] = (byte)0;
        }
    }
    FileOutputStream fos = new FileOutputStream("anktrash_new.png");
    fos.write(bytes);
    fos.close();
    System.out.println(sum);
    System.out.println(total);
}
```
6. Check the top row of every possible bit depth/color type combo to find the ones that look most legitimate.<br>
7. Grayscale with alpha channel seemed most legitimate.<br>
8. Brute force create images of widths up to 1000 with an arbitrary set height (also need to fix checksums of each image). 
```java
public static void genAll() throws IOException {
    // read file
    byte[] bytes = Files.readAllBytes(Paths.get("corrupted_new_fixed.png"));
    // brute force widths
    for(int i = 200; i <= 1000; i+=5)
        // brute force colors (in this example, we are just brute forcing grayscale with alpha channel)
        for(int colour : Arrays.asList(4)) {
            // init bit depths that always exist
            ArrayList<Integer> bitDepths = new ArrayList<>();
            bitDepths.add(8);
            bitDepths.add(16);

            // add bit depths that only exist for pure grayscale
            switch(colour) {
                case 0:
                    bitDepths.add(1);
                    bitDepths.add(2);
                    bitDepths.add(4);
            }

            // brute force each bit depth
            for(int bD : bitDepths) {
                // create new file
                FileOutputStream fos = new FileOutputStream("image-out/" + i + "_" + colour + "_" + bD + ".png");
                bytes[18] = (byte)(i >> 8);
                bytes[19] = (byte)i;
                bytes[22] = (byte)1;
                bytes[23] = (byte)0x90;
                bytes[24] = (byte)bD;
                bytes[25] = (byte)colour;
                fos.write(bytes);
                fos.close();

            }
        }
    }
}
```
9. Scroll through these images.<br>
10. We see the flag appears in one of the images.<br>
![flag image][image]


[image]: fix-600_4_16.png

---

## Flag
`tjctf{c0rruption? wh4t corrupt10n?}`