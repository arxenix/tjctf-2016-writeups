# corruption (130 pts)

---

## Problem

---

## Solution
1) Run `pngcheck corrupted.png`.<br>
2) We see that the file is corrupted.<br>
3) Open the file in a hex editor.<br>
4) We see that every chunk length and checksum is messed up, as well as the IHDR being blank.<br>
5) Fix all the chunk lengths and checksums. We used [pngcsum](http://schaik.com/png/pngcsum.html) to fix the checksums and the following code to fix the lengths:<br>
```
import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.Arrays;

/**
 * Created by aashish on 5/28/16.
 */
public class Main {
    public static void fixLengths() throws IOException {
        byte[] bytes = Files.readAllBytes(Paths.get("corrupted_.png"));
        int idx = -1;
        int sum = 0;
        int total = 0;
        for(int i = 0; i < bytes.length; i++) {
            if(bytes[i] == (byte)73 && bytes[i+1] == (byte)68 && bytes[i+2] == (byte)65 && bytes[i+3] == (byte)84 ||
                    bytes[i] == (byte)73 && bytes[i+1] == (byte)69 && bytes[i+2] == (byte)78 && bytes[i+3] == (byte)68) {
                if(idx == -1) {
                    idx = i;
                    continue;
                }
                int diff = i - idx - 12;
                sum += diff;
                bytes[idx-4] = (byte)(diff >> 24);
                bytes[idx-3] = (byte)(diff >> 16);
                bytes[idx-2] = (byte)(diff >> 8);
                bytes[idx-1] = (byte)diff;

                idx = i;
                total++;
                System.out.println("dif " + diff);
            }
            if(bytes[i] == (byte)73 && bytes[i+1] == (byte)69 && bytes[i+2] == (byte)78 && bytes[i+3] == (byte)68) {
                bytes[i-4] = (byte)0;
                bytes[i-3] = (byte)0;
                bytes[i-2] = (byte)0;
                bytes[i-1] = (byte)0;
            }
        }
//        FileOutputStream fos = new FileOutputStream("anktrash_new.png");
//        fos.write(bytes);
//        fos.close();
        System.out.println(sum);
        System.out.println(total);
    }

    public static void genAll() throws IOException {
        byte[] bytes = Files.readAllBytes(Paths.get("corrupted_new_fixed.png"));
        for(int i = 200; i <= 1000; i+=5)
            for(int colour : Arrays.asList(4)) {
                ArrayList<Integer> bitDepths = new ArrayList<>();
                bitDepths.add(8);
                bitDepths.add(16);
                switch(colour) {
                    case 0:
                        bitDepths.add(1);
                        bitDepths.add(2);
                        bitDepths.add(4);
                }
                for(int bD : bitDepths) {
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

    public static void subtract(int row) throws IOException {
        BufferedImage image = ImageIO.read(new File("corrupted_new_fixed.png"));
        for(int i = 1; i < image.getWidth(); i++) {
            for(int j = 0; j < image.getHeight(); j++) {
                Color sub = new Color(180,180,180);
                int r = sub.getRed();
                int g = sub.getGreen();
                int b = sub.getBlue();
                Color c = new Color(image.getRGB(i, j));
                int newR = c.getRed() - r;
                int newG = c.getGreen() - g;
                int newB = c.getBlue() - b;
                Color newC = new Color(f(newR), f(newG), f(newB));
                image.setRGB(i, j, newC.getRGB());
            }
        }
        //ImageIO.write(image, "png", new File("image-out/" + row + ".png"));
        ImageIO.write(image, "png", new File("corrupted_new_fixed_subtracted.png"));
    }

    private static int f(int i) {
//        return (i + 256) % 256;
        return i < 0 ? 255 : i > 255 ? 0 : i;
    }
}

```
6) Check the top row of every possible bit depth/color type combo to find the ones that look most legitimate.<br>
7) Grayscale with alpha channel seemed most legitimate.<br>
8) Brute force create images of widths up to 1000 with an arbitrary set height (also need to fix checksums of each image).<br>
9) Scroll through these images.<br>
10) We see the flag appears in one of the images.<br>
![flag image][image]


[image]: http://filebin.ca/2izgk9c68hW4/fix-600_4_16.png

---

## Flag
`tjctf{c0rruption? wh4t corrupt10n?}`