**`leviathan6@gibson:~$ ls`**  
*`leviathan6`*  
**`leviathan6@gibson:~$ ltrace ./leviathan6`**  
```
__libc_start_main(0x80491d6, 1, 0xffffd5f4, 0 <unfinished ...>
printf("usage: %s <4 digit code>\n", "./leviathan6"usage: ./leviathan6 <4 digit code>
)                                                                                               = 35
exit(-1 <no return ...>
+++ exited (status 255) +++
```
**`leviathan6@gibson:~$ ./leviathan6`**  
*`usage: ./leviathan6 <4 digit code>`*  

Looks like we have to run the setuid binary with a 4-digit code:

**`leviathan6@gibson:~$ ./leviathan6 1234`**  
*`Wrong`*  

Eh. Time to bruteforce it. 

**`leviathan6@gibson:~$ vim /tmp/rajh/brute.sh`**  
```
#!/bin/bash

for i in {0000..9999}
do
        /home/leviathan6/leviathan6 $i
done
```
**`leviathan6@gibson:~$ chmod 777 /tmp/rajh/brute.sh`**  
**`leviathan6@gibson:~$ /tmp/rajh/brute.sh`**  
```
Wrong
Wrong
..
..
$
```

Well after a certain point, we reach some shell:

**`$ whoami`**  
*`leviathan7`*  
**`$ cat /etc/leviathan_pass/leviathan7`**  
*`8GpZ5f8Hze`*  
