For all of these levels, OverTheWire gives us no instructions or prompts. This, in my opinion, is better, as we're forced to explore the filesystem on our own.

Let's first start be taking a look at the home directory:

**`leviathan0@gibson:~$ ls -la`**  
```
total 24
drwxr-xr-x  3 root       root       4096 Dec  3 08:13 .
drwxr-xr-x 83 root       root       4096 Dec  3 08:14 ..
drwxr-x---  2 leviathan1 leviathan0 4096 Dec  3 08:13 .backup
-rw-r--r--  1 root       root        220 Jan  6  2022 .bash_logout
-rw-r--r--  1 root       root       3771 Jan  6  2022 .bashrc
-rw-r--r--  1 root       root        807 Jan  6  2022 .profile
```

Right off the bat, what stands out is a hidden directory that is owned by user `leviathan1`, so let's navigate into that directory and take a look:

**`leviathan0@gibson:~$ cd .backup/`**  
**`leviathan0@gibson:~/.backup$ ls -la`**  
```
total 140
drwxr-x--- 2 leviathan1 leviathan0   4096 Dec  3 08:13 .
drwxr-xr-x 3 root       root         4096 Dec  3 08:13 ..
-rw-r----- 1 leviathan1 leviathan0 133259 Dec  3 08:13 bookmarks.html
```
**`leviathan0@gibson:~/.backup$ cat bookmarks.html`**  

A very long list of hundreds of URL's renders. At this point, I wouldn't be surprised if the password was hidden within this file, but it's too long to sift through row-by-row:

**`leviathan0@gibson:~/.backup$ cat bookmarks.html | grep password`**  
```
<DT><A HREF="http://leviathan.labs.overthewire.org/passwordus.html | This will be fixed later, the password for leviathan1 is PPIfmI1qsA" ADD_DATE="1155384634" LAST_CHARSET="ISO-8859-1" ID="rdf:#$2wIU71">password to leviathan1</A>
```
