# Challenge collider

86 Points

## Challenge

Your task is pretty simple: Upload two PDF files. The first should contain the string "NO FLAG!" and the other one "GIVE FLAG!", but both should have the same MD5 hash!

http://35.207.133.246

## Solution

The website allows to upload 2 pdf files. As the challenge says, the PDFs must have the same md5 hash sum and some given words in it.

As md5 as a "bit" broken, this attack is possible. There is even an existing tool that can manipulate 2 files to have the same md5 checksum: https://github.com/cr-marcstevens/hashclash

After downloading and compiling hashclash, the 2 PDFs with the given texts are created and given as input files to hashclash:

```
root@blackbox:# ./scripts/cpc.sh ../giveflag.pdf ../noflag.pdf 
Chosen-prefix file 1: ../giveflag.pdf
Chosen-prefix file 2: ../noflag.pdf
Birthday search for MD5 chosen-prefix collisions
Copyright (C) 2009 Marc Stevens
http://homepages.cwi.nl/~stevens/

IHV1 = {1755234546,4022845013,1581847193,3217075462}
IHV1 = f2c09e6855bec7ef9912495e06adc0bf

IHV2 = {961669933,172686064,584466100,324994096}
IHV2 = 2deb5139f0fa4a0ab43ed62230045f13

Maximum amount of memory in MB for trails: 100 (local: 100)
Estimated number of trails that will be stored:    1872457 (local: 1872457)
Estimated number of trails that will be generated: 1061342
Estimated complexity per trail: 2^(17)
Estimated complexity on trails: 2^(37.0175)
Estimated complexity on collisions: 2^(27.7053)

Thread 3 created.
Thread 1 created.
Thread 2 created.
Thread 4 created.

```

Now we have to wait... according to some papers this can take several hours. After nearly overheating the laptop, there was still no collision found... and the batterie goes empty :(

So we have not solved this challenge, but with some more computer power this would likely be possible with hashclash.
