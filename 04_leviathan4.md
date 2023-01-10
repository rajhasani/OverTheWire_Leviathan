**`leviathan3@gibson:~$ ls`**  
*`level3`*  

Another setuid binary. As before, let's check the library calls:

**`leviathan3@gibson:~$ ltrace ./level3`**  
```
__libc_start_main(0x80492bf, 1, 0xffffd5f4, 0 <unfinished ...>
strcmp("h0no33", "kakaka")                                                                                                                         = -1
printf("Enter the password> ")                                                                                                                     = 20
fgets(Enter the password> 12345
"12345\n", 256, 0xf7fab620)                                                                                                                  = 0xffffd3cc
strcmp("12345\n", "snlprintf\n")                                                                                                                   = -1
puts("bzzzzzzzzap. WRONG"bzzzzzzzzap. WRONG
)                                                                                                                         = 19
+++ exited (status 0) +++
```

So after it prompts us for a password, it runs another string comparison using `strcmp()`, which looks very similar to the solution for leviathan2. The `/n` is regex for a new line, so this effectively can be stripped:

**`leviathan3@gibson:~$ ./level3`**  
*`Enter the password> snlprintf`*  
*`[You've got shell]!`*  
**`$ whoami`**  
*`leviathan4`*  
**`$ cat /etc/leviathan_pass/leviathan4`**  
*`AgvropI4OA`*  

This was way easier than the previous level; I'm disappointed. 
