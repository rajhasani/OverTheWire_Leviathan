**`leviathan1@gibson:~$ ls`**  
*`check`*  

Nice, another setuid binary. We dealt with these in previous OTW wargames, so let's just try running it:

**`leviathan1@gibson:~$ ./check`**  
*`password:`*  

It's prompting us for a password. Putting something random like `12345` gave me a response of *`Wrong password, Good Bye ...`*; it'd be useful to see how the setuid binary is functioning, or more rigorously, what it is calling to compare the password input to. We can utilize a command called `ltrace`, a library call tracer, to intercept and record dynamic library calls from the executed process, as well as system calls executed by the program:

**`leviathan1@gibson:~$ ltrace ./check`**  
```
__libc_start_main(0x80491e6, 1, 0xffffd604, 0 <unfinished ...>
printf("password: ")                                                                                                                               = 10
getchar(0xf7fbe4a0, 0xf7fd6f80, 0x786573, 0x646f67password: 123
)                                                                                                = 49
getchar(0xf7fbe4a0, 0xf7fd6f31, 0x786573, 0x646f67)                                                                                                = 50
getchar(0xf7fbe4a0, 0xf7fd3231, 0x786573, 0x646f67)                                                                                                = 51
strcmp("123", "sex")                                                                                                                               = -1
puts("Wrong password, Good Bye ..."Wrong password, Good Bye ...
)                                                                                                               = 29
+++ exited (status 0) +++
```

Looks like the program calls the `strcmp` library function, which compares two strings. My input was "123", and it seems to be comparing that against "sex". Let's try using the latter when running the setuid binary this time:

**`leviathan1@gibson:~$ ./check`**  
*`password: sex`*  
*`$`*  

We seem to be in a new shell...

**`$ whoami`**  
*`leviathan2`*  

Nice, we seem to be in this shell as user `leviathan2`.

**`$ cat /etc/leviathan_pass/leviathan2`**  
*`mEh5PNl10e`*  
