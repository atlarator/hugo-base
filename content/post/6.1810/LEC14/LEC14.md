---
title: "xv6 LECTURE14"
date: 2025-01-26T09:37:33+08:00
draft: false
tags: ["XV6 OS"]
---

## Handout 14

fs write first 46, then 32, 33? dirent points to free inode  
correctness and performance are often conflict  
crash recovery is a recurring problem  
logging solution: goal: fast recovery, atomic syscalls  
log, commit, install  
write-ahead log: if "done" in log, replay all writes in log, if no "done", ignore log  
multiple data structures, lay on existing storage system, high performance  
buffer cache, in-memory log block # array, FS tree on disk, log header and blocks on disk  
write buffers, wait for disk to complete write, keep data in buffer cache  
begin_op()->bmap()->bread()->log_write()->i_update()->end_op()  

when crash happens, kernel calls recover_from_log(), it's OK to replay the log more than once  
challenge: prevent write-back from cache  
system's call data must fit in log  
allowing concurrent syscalls  
xv6's solution: wait  

avoid written multiple times: write absorbtion  

## Slides 14

33, 33, 46, 32 inode crashed and wasted  
46, 32, 33, 33 inode reallocated, causing a disaster  
we hope for: internal fs invariants must be held, all but last few options must be stored, no reordering of data writes  
xv6 assumes disk is fail-stop  

pin dirty blocks in buffer cache, unpin the block  
Logging makes file system transactions atomic  
write-ahead logging is key  

## Book Logging Sections

log writes, write a special commit, copy the writes, erase the log  
design: logged blocks  
write the header block with a transaction commit  
sequence of write must be atomic  
group commit: commit several transactions together  
absorption: achieve better performance  
end_op()->commit()->write_log()->write_head()->install_trans()  

## Lecture 14

crash will lead to on-disk incorrect state  
fs operation are all multi-step  
33, failure, 45: a block remain unallocated  
if block is shared between files, there will be a security problem  
solution: logging, atomic fs calls, fast recovery  

crash between log write? see commit records  
every fs-related syscall: begin_op() -> operations -> end_op()  
ialloc() -> bwrite() -> log_write()  
bwrite() could never used by itself but closed with log_write()  
we don't expect the process live after the crash  
log, create, install  
ext3: deal with the factor 2  

eviction: broken of atomicity, write-ahead rule violation  
solution: don't evict blocks in the log  

fs operation must fit in log  
max log size is 30, 30 as the max block size(operation writes)  
write fs->many transactions  

concurrent fs calls  
all concurrent ops must be fit, limit concurrent calls  
