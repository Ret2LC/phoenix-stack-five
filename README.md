# Details:
    - Auther: Totem
    - Discord: Totem#0001
    - Challenge: Phoenix/stack-five from https://exploit.education
---------------
# Information Gathering:
```
    1. Check Security On Binary:
        -    cmd: checksec ./stack-five
             results:
                    Arch:     amd64-64-little
                    RELRO:    No RELRO
                    Stack:    No canary found
                    NX:       NX disabled
                    PIE:      No PIE (0x400000)
                    RWX:      Has RWX segments
                    RPATH:    '/opt/phoenix/x86_64-linux-musl/lib'
                    
    3. Getting Source Code:
        -   Source Code Located @: https://exploit.education/phoenix/stack-five
    
    2. Static Code Analysis:
        -   char:   char buffer[128];
            + char itself is not necessarily vulnerable, however; the developer is
            statically assigning a buffer size where the user has input.
            
        -   gets:   gets(buffer);
            + gets does not have any way of telling how many bytes the user will input
            so this can lead to a plethera of many memory curruption attacks.
```
---------------
# Exploitation:
+ We will first go to https://wiremask.eu/tools/buffer-overflow-pattern-generator and generate a payload
of 200 characters, then feed that payload to the program via gdb.
  ![alt text](https://i.imgur.com/kuw0khP.png)
+ We see that the program has seg-faulted, we see that we have over-written the RIP, we can give the
current value of RIP to the website and get the offset, after doing, so we figure out that our offset is 136
---------------

# Exploit Development:
+ Now that we know what the offset is lets create a small exploit.
![alt text](https://i.imgur.com/189vaP4.png)
+ Further, Exploit Details:
    - our nop slide will slide us to the location on the stack we can execute code on.
    - our shellcode being used is a simple sys_execve system call that will execute /bin/sh
    - the 'rip' variable in our exploit HOLDS THE LOCATION on the stack where we control
    - we found the location on the stack that we can control by sending our payload and inspecting the stack, from there we see that we were able to control the stack @ 0x00007fffffffe5f0
  
---------------
# Using The Exploit Created:
![alt text](https://i.imgur.com/nS0TlPA.png)
- Hurray! we got a shell on the system!
---------------
# Keywords:
+ RIP - x64 register that points to the instruction being executed.
---------------
+ NOP-SLIDE - series of no operations that will slide to our shellcode.
