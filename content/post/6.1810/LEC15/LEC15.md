---
title: "xv6 LECTURE15"
date: 2025-01-27T08:59:50+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 15

crashes may lead to problem that won't show immediately  
make syscalls atomic(all-or-nothing)  
ext3: less waiting, reduced writes, ordered data mode  

batch update blocks, commit the current open transaction  
performance: multiple syscalls, multiple transactions  

the crash may forget last few ops  
log is circular: SB T4 T5 T2 T3  
before re-use, must write log superblock with offset/seq of resulting now-oldest transaction in log  

logging could help with "fail-stop"  
reboot, look in log, find the end of log, re-write all logs  
another crash? just repeat the logging steps  
commit needs to write on-disk log from a snapshot of cached blocks? if not, repeat but not all  
efficient using COW fork  

in ext3, metadata and blocks are both written in log  
write content after metadata? no, crash may leave inode pointing to blocks from other file!  

correctness challenges  
no enough free blocks? free oldest ones  
use checksums  

## Paper

preservation, predictability, atomicity  
ex2fs, however, is not atomic  
synchronous metadata update has performance problems  
solution: defered-ordered write way, but may leat to a cyclic problem  
soft-update way: share same problem as the recovery process still need to scan the whole disk  

commit: mot make effect until final  
journaling file system: the old and new versions of incomplete updates are preserved by writing the new versions to a separate location on disk until such time as the update has be committed  

transactions are atomic: either undo or redo  
contains all changed metadata  
reading the existing file system  
before commiting, make sure all blocks are written in the disk  

fs has no transaction abort, transactions are short-lived  
create a new transaction so often  
less IO operations is needed for long term, but make fs time-predictless  

inodes, bitmaps, record new contents and maintain atomicity  

journal metadata block: contains entire component  
keep track of metadata buffers  
keep old blocks unsynced before commiting to avoid crash  
complete commit: close the transaction, flush transactions to disk, wait for outstanding operations, wait for outstanding transactions, update journal handle block, mark as pinned  
without syncing, maybe data will lost if crash happens when completing commits 

## Lecture 15

ext3 = ext2 + journal  
let operations in begin_op() and end_op() atomic  
write-ahead: all of refresh or write should be pre-declared  
free rule: only when only when synced the logging will delete the indexes  

ext3 didn't modify the bottom layer of ext2, but just added journalling system in it  
cache, action info layer(sequence numbers, block numbers; handles), fs tree  
ext3 log format:  
log super block: off, seq, desc, commit  
there's a bunch of closed older transactions  

async, batching, concurrency  
fsync(fd): do all writes to the file descripter  
batching: one open transaction  
write absorbtion, disk scheduling  

concurrency: syscalls in parallel, older transactions  
one open, commiting, writing, freed  
we can't have a transaction started since close  

sys_unlink()  
h = start()  
get(h, block#)  
stop()  

steps in commit:  
block new syscalls, wait for outstanding syscall, open a new transaction, write a descriptor block with block numbers, write blocks to log, wait for finish, write commit record to the block, wait for the commit point, write the transactions blocks to the home locations, re-use log  
lots of records to show the transactions  

if there's a crash?  
assuming the disk is still all right, when the power reboot, there's a bunch of old completed transactions  

every descriptor block starts with a magic number  
after SB, start with a magic number, it must be a descriptor block  

why not let T2 get syscall just after T1 finishes syscall?  
create()->unlink(), crash?  
the inode won't be deleted, y still exists in file system  
no possibility to see the refresh in the future transaction  
copy into cache, copy into disk  

logging let multi-step disk transaction into atomic ones  
the correctness of logging is depend on write-ahead rule  

why not put commit message right in the descriptor block?  
no problem is eliminated  
