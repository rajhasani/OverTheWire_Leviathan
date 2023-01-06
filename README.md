This repo is a level-by-level write-up of OverTheWire's Leviathan wargame, which is intended to be a continuation on elementary Linux commands.

The guide and instructions for each level can be accessed at https://overthewire.org/wargames/leviathan/

You will need to connect to leviathan.labs.overthewire.org via SSH on port 2223. The username you will be using to access the first level is leviathan1; hence, the proper input will be:  
**`$ ssh leviathan0@leviathan.labs.overthewire.org -p 2223`**

When progressing to the next levels, it is not required you end your current SSH session; you can simply reference localhost, still utilizing port 2223. For example:  
**`$ ssh leviathan1@localhost -p 2223`**

### The filenames in this guide directly pertain to the level you are attempting to access, i.e. the intended solution. Example: 04_leviathan4.md is a guide for which you are currently on leviathan3, attempting to access leviathan4.

Syntax for command I/O is as follows:

* All input commands will be in **`bolded code span`**  
* All output from commands will be either in
  * *`italicized code span`* OR
  * 
  ``` 
  fenced
  code 
  blocks 
  ```

It is worth noting that items like passwords and RSA keys are subject to change at OverTheWire's sole discretion. The passwords displayed in this guide will be current as the time of publication. I will NOT be revisiting/revising the guide to update passwords and RSA keys; however, the overall process will remain unchanged.
