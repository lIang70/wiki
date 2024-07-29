---
title: Virtual Memory
description: 
published: true
date: 2024-07-29T13:49:18.463Z
tags: 
editor: markdown
dateCreated: 2024-07-29T13:49:18.463Z
---

# Virtual Memory

> virtual memory is the perfect interaction layer between hardware exception, hardware address translation, main memory, disk file and kernel software.

## Critical Capability

- Main memory was seem as a cache of address space which stores on disk, only the active region is retained on main memory; and data is also transferred back and forth between disk and main memory as needed.

- The same address space was offered to every process, simplify the memory manage.

- It protects each process's address space from being corrupted by other processes.

## TLB (translation lookaside buffer)

> It is a small cache about PTE in the MMU.

```bash
    +-------------+-----+
    |     VPN     | VPO | => VA
    +-------------+-----+

    +------+------+-----+
    | TLBT | TLBI | VPO | => TLB
    +------+------+-----+
```
