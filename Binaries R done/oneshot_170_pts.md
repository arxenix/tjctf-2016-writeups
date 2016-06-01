# Oneshot (170 points)

###Problem Overview:
This problem is straightforward, yet so wonderfully devious. Essentially, you are given an arbitrary 8 byte read, and an arbitrary `jmp`. You can't control function parameters or register values besides `RIP/EIP`

###The Process:
  The first thing I did was to run `strings` on the binary to see if the libc info was still inside. Luckily for me, it was. The binary on the server uses `libc.so.6`, a fairly common and modern libc. Then, with the arbitrary read, I leaked a GOT pointer so that I could calculate the base address of the `libc` in the binary. Once I got to this point, I was stuck. This felt like a ret2libc attack, but without function parameters or good ROPgadgets, I didn't know what to do.However, I came across a stroke of luck. In finding the solution to [blag](blag_175
  _pts.md), I also found the solution to this problem in [this pdf](http://j00ru.vexillium.org/blog/24_03_15/dragons_ctf.pdf). The thing to Ctrl-F for is "One Gadget RCE". A one gadget RCE, sometimes referred to as a "magic shell" is an address in libc that will give you a shell. No arguments, no shellcode, no fancy ROP techniques. All you have to do is know the base address of libc and you can call it. Knowing this, I used IDA to find the gadget. By more google-fu, I finally found a reasonable explanation of how to do it. By finding `Xrefs` (one function calling another) to `execve()`, I was able to find a little snippet of assembly that loads a pointer to "/bin/sh" into `RDI`, then calls `execve()`, giving me a shell!
  
```
.text:00000000000E681D                 mov     rax, cs:environ_ptr_0
.text:00000000000E6824                 lea     rsi, [rsp+1D8h+var_168]
.text:00000000000E6829                 lea     rdi, aBinSh     ; "/bin/sh"
.text:00000000000E6830                 mov     rdx, [rax]
.text:00000000000E6833                 call    execve
.text:00000000000E6838                 call    abort
```

This means that all we have to do is add 0xE681D to our computed libc base adress, and send that as our jump location.

pwned.

##Flag
`tjctf{m4gic_And_m0re_Mag1cK}`