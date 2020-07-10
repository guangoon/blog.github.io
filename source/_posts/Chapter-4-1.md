---
title: Chapter 4:Processor Architecture(4.1)
date: 2020-07-07 00:05:02
tags:
categories: 深入理解计算机系统
---
### Practice Problem 4.1
Determine the byte encoding of the Y86-64 instruction sequence that follows. The line .pos 0x100 indicates that the starting address of the object code should be 0x100.
```x86asm
.pos 0x100 # Start code at address 0x100
    irmovq $15,%rbx
    rrmovq %rbx,%rcx
loop:
    rmmovq %rcx,-3(%rbx)
    addq   %rbx,%rcx
    jmp loop
```
### Practice Problem 4.2
For each byte sequence listed, determine the Y86-64 instruction sequence it en-codes. If there is some invalid byte in the sequence, show the instruction sequence up to that point and indicate where the invalid value occurs. For each sequence, we show the starting address, then a colon, and then the byte sequence.

A. 0x100: 30f3fcffffffffffffff40630008000000000000
B. 0x200: a06f800c020000000000000030f30a00000000000000
C. 0x300: 5054070000000000000010f0b01f
D. 0x400: 611373000400000000000000
E. 0x500: 6362a0f0