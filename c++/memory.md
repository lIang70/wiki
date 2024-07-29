---
title: Memory
description: 
published: true
date: 2024-07-29T13:32:48.263Z
tags: 
editor: markdown
dateCreated: 2024-07-29T13:32:48.263Z
---

# Memory

## Memory Arrangement of Program

- ***Stack***: stores local variables, function parameters, return addresses and so on, which are automatically allocated and released by the compiler/system.

- ***Heap***: dynamically allocated memory, is allocated by `malloc` block of memory, the programmer control its allocation and release, if the program execution is not free, the operating system will automatically reclaim.

- ***Global/Static Storage***(`.bss` segment and `.data` segment): Stores global and static variables, which are automatically released by the operating system at the end of the program. In C, uninitialized is placed in the `.bss` segment, initialized in the `.data` segment, C++ no longer distinguishes.

- ***Constant Store***(`.data`segment): stores constants, which are not allowed to be modified. The compiler will release them automatically after the program runs.

- ***Code Area***(`.text` segment): stores code that cannot be modified but can be executed. The compiled binaries are stored here.
