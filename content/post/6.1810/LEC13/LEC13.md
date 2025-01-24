---
title: "xv6 LECTURE13"
date: 2025-01-24T08:33:35+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 13

files, bit arrays, human-readable names  
fs APIs  
fd: listed to the file even if it changes  
a file can have multiple links  
file must have info stored other than directory  

inode: i-number(initial version), link count, count of open FDs  
inode deallocation deferred until last link and FD are gone  

data stays on disk without power  
historically, disks were read/write usually in 512-byte units, called sectors  

in HDD, sub-sector operation are expensive(read-modify-write)  
flash must be erased before written  
xv6 uses 2-sector blocks  

xv6 treats disk as an array of sectors  
metadata: everything on disk other than file content  
on-disk inode: type, nlink, size, addrs[12+1]  

content is array of dirents  
dirent: inum, 14-byte filename  

view file system as an on-disk data structure  
facing concurrency challenges  
look at block cache  
bread->bget, watch the bcache  
bcache.lock: description of what's in the cache  
b->lock: protects just the one buffer  

bcache replacement: bget, brelse  
namex() ilock()  
getting a reference separately from locking  

## Slides 13

0: syscalls  
1: names+fd  
2: inodes  
3: inode cache & buffer cache  
4: Log  
5: virtio & disk driver  
FD: stdin(0), stdout(1), stderr(2)  

mkfs: generate layout for new FS, the layout will stay static for the lifetime of fs  
indirect block: inode is fixed size, but file could be large  
tree-structure: dir tree layer; inode layer; block layer  

## Book Chapter 8

file system must have crash recovery  

block 0: boot sector  
block 1: superblock, holds metadata  
block start at 2 hold the log  
inode, bitmap, data  

bcache: only one kernel thread is using the copy, cache popular blocks  
bcache is a doubly linked-list, main->binit  
bread->bget->sleeplock, scan the bcache  
at most one cached buffer per disk sector  
brelse to release the buffer  

block allocator: maintains a free bitmap  
balloc: find the free block, refresh the bitmap, return the block  
bfree: clear the block  

on-disk inode: struct dinode  
itable, struct inode to copy dinode into memory  
iget, iput  
there can be many same pointers point to same inode  
hold ilock and release by iunlock  
Code that modifies an in-memory inode writes it to disk with iupdate  
iput() doesn’t truncate a file immediately when the link count for the file drops to zero  

direct/indirect block  
bmap: returns the nth data block for inode ip, or allocate one if not exists  
itrunc: frees blocks and sets inode to 0  

directory: has type T_DIR, dirlookup  
namex -> skipelem  
namex locks each directory in the path separately  
avoid racing by using sleeplocks  

opened file is symboled as struct file  
kept in ftable, filealloc->filedup->fileclose->fileread/filewrite  
file has no concept of offset  

sys_link, sys_unlink  
sys_link creates a new name for the inode  
using create, it is easy to implement sys_open, sys_mkdir, and sys_mknod  

## Lecture 13

user-friendly, concurrency, persistence  
the abstraction itself is useful  
crash safety  
disk layout  
performance problems, as disk are always slow  
pathname is human-readable, no explicit offset in write() syscall  

file system structure: inode  
link count & fd count = 0, miantain an offset  
sync/async read/write option? yes, same way as console driver  
max file size in xv6: (256+12)*1024  
make files bigger: add indirect portions  
why must 256 bytes a block? block address has only 4 digits  

how to compute block index?  divide into 1024  
get index from inode, read out 8000 bytes  
how to find root inode index? scan root block, scan subdir's block  

create a file:  
33, write into the free inode  
33, fill in the inode  
46, write the first block  
32, size changed  
33, return back the inode  

write "hi" into x:  
45, bitmap refresh  
595: the block allocated for x  
33: updating the size of inode again  

bget: get the bcache lock, scan the buffer  
xv6 follows a rule: any modification on bcache needs bcache lock, any modification on block 33 need to hold sleeplock  

shortage of spinlock: must turn off interrupt  
sleeplock will return the CPU resources  

bcache: a copy of block in mem, sleeplock, LRU, 2-level-blocking  