---
title: LiOS Operating System
period: "2024-present"
date: "2024-01-01"
---

*[Github](https://github.com/glolichen/lios)*

A 64-bit operating system for x86. I worked on this on and off since 2024, and here are some features I have implemented so far (copiped from README):
 - Bootstrapping to long mode
 - Interrupts (only keyboard implemented)
 - Printing to VGA graphical mode and serial
 - Minimum viable memory manager/allocator
   - Uses paging (obviously)
   - Linked list based page frame and virtual address allocator
   - Kernel heap allocator (using linked lists and bitmap)
 - Supports UEFI and runs on real hardware (at least it did ~late 2025, I should test again soon)
 - Basic file system functionality:
   - reading and writing from NVMe SSD volume
   - FAT32 file system
   - Can read and create new files, but not write data
   - No directory support for now
 - User mode support and system calls (IO only for now)
 - Basic ELF/program loading functionality
   - C runtime (`crt0` only)

I am currently (well, whenever I have time in college) working on implementing processes. Right now we *can* run *a* user process (from a binary on file system), but that's it... The next priorities are getting better (not full) C support (I don't want to keep writing programs in assembly, but a full C library is quite hard and unnecessary for now), and using that to write a shell from where we can hopefully start other processes.

There's some more details in the repository itself but of note is I (try my best to) keep a journal of changes and features [here](https://github.com/glolichen/lios/blob/main/journal.md).

