# Challenge rare_mount

44 Points

## Challenge

Little or big, we do not care!

FS (link to a file)

Difficulty estimate: Easy

## Solution

After downloading the file and analyse it with binwalk, it was obvious that the file contains a JFFS2 image:

```
root@blackbox:# binwalk ffbde7acedff79aa36f0f5518aad92d3-rare-fs.bin

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JFFS2 filesystem, big endian


```

There are some tools around to extract JFFS2 images like https://github.com/sviehb/jefferson.git. Just extract the image with jefferson and look at the files:

```
root@blackbox:# jefferson ffbde7acedff79aa36f0f5518aad92d3-rare-fs.bin -d out
dumping fs #1 to /root/ctf/rare_mount/out/fs_1
Jffs2_raw_dirent count: 2
Jffs2_raw_inode count: 2155
Jffs2_raw_summary count: 0
Jffs2_raw_xattr count: 0
Jffs2_raw_xref count: 0
Endianness: Big
writing S_ISREG RickRoll_D-oHg5SJYRHA0.mkv
writing S_ISREG flag
----------
```

Take a look at the content of the flag file and there it is:

```
root@blackbox:# cat out/fs_1/flag
35C3_big_or_little_1_dont_give_a_shizzle

```
