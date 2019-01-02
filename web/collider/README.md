# Challenge collider

86 Points

## Challenge

Your task is pretty simple: Upload two PDF files. The first should contain the string "NO FLAG!" and the other one "GIVE FLAG!", but both should have the same MD5 hash!

http://35.207.133.246

## Solution

After shortly looking at the website, there are 2 file upload input field on it to upload files. Also the source code can be found at http://35.207.133.246/src.tgz. As the challenge says, the PDFs must have the same md5 hash sum and some given words in it. Maybe it's possible to get around the php code of the compare...

As md5 as a "bit" broken, let's just try to attack MD5. There is an existing tool that can manipulate 2 files to have the same md5 checksum: https://github.com/cr-marcstevens/hashclash

So we create 2 PDFs (with libreOffice Writer) with the words "NO FLAG!" and another with the words "GIVE FLAG!" in it. Then we run Hashclash for this 2 files.

Unlucky, Hashclash took way to long on our hardware, so lets try another approach: There is another POC around, that is way faster then Hashclash: https://github.com/corkami/pocs. After downloading it and running the pdf.py script, the generation was done in under a second. Wow.

```
root@blackbox:# python ./pocs/collisions/scripts/pdf.py giveflag.pdf noflag.pdf

KEEP CALM and IGNORE THE NEXT ERRORS
error: cannot recognize xref format
warning: trying to repair broken xref
warning: repairing PDF document

collision1.pdf:

PDF-1.3

Pages: 1

Retrieving info from pages 1-1...


collision2.pdf:

PDF-1.3

Pages: 1

Retrieving info from pages 1-1...

MD5: 97f5289a50a930d4fc4b66134d155bac
Success!
```

Checking the MD5 hash of the 2 new generated PDFs:
```
root@blackbox:# md5sum collision*
97f5289a50a930d4fc4b66134d155bac  collision1.pdf
97f5289a50a930d4fc4b66134d155bac  collision2.pdf
```

So we can upload this two files and there we get the flag: 35C3_N3v3r_TrusT_MD5
