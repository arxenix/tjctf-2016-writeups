# Cipher Font (50 pts)

---

## Problem
This [webpage](http://cipherfont.p.tjctf.org/) contains a flag! 

---

## Solution
1. We notice that a cipher font is used to 'encrypt' the text.
2. Write a script that replaces the encrypted font letters with the right letters.


```java
static String in = "..."; // the really long ciphertext
static String out1 = in;
static String out2 = in;

static Map<Character, Character> replace = new HashMap<>();

public static void main(String[] args) throws FileNotFoundException {
    replaceFirst(
            "wLZPfhllL6S32DBiKkR8yKsZoTS0y64B1" +
            "M1peoqntMoUiHL3xvFXferhqCvn97sJvG" +
            "FKZfa7fYUfuhcPkpNVTymdwYZSxkr7LJX" +
            "OYI73bdDk50nsY8712y0cSFKxFTbtWbNV" +
            "SjOSX1ud5xdVtaTIxKd3ezNAtdDo901W" +
            "wGQ78SP1PGzj5dmwhOKTY1RiRsPMKPx3" +
            "mtJyrHvt90YmU3rfkoth0qn70DLb1Jlg" +
            "mtM8BH07qxNBy5tybz2IKlllp3oR3mS6a" +
            "djQhU8nN18l7Gc075ugeWW2WPAAkZuAfT" +
            "3LUqDZfns7dL89uJOExkhN3UZ3QmnEH9I" +
            "zEXvl0Fpy"
    );

    for(Character c : in.toCharArray())
        System.out.print(replace.get(c));
    System.out.println();
}

public static void replaceFirst(String n) {
    System.out.println("len replace: " + n.length());
    System.out.println("idx needed: " + in.indexOf('T'));
    for(int i = 0; i < n.length(); i++) {
        replace.put(in.charAt(i), n.charAt(i));
        //replace(in.charAt(i), n.charAt(i));
    }
}

public static void replace(char a, char b) {
    out1 = out1.replace(a, b);
    out2 = out2.replace(a + "", "");
}
```

Then compute the md5 hash of the decrypted text.

---

## Flag
`tjctf{232bd3180db2e7261ad2d94b725c9008}`