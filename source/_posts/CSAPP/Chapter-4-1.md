---
title: Chapter 4:Processor Architecture(4.1)
date: 2020-07-07 00:05:02
tags:
categories: CSAPP
---
### Practice Problem 4.1
Determine the byte encoding of the Y86-64 instruction sequence that follows. The line .pos 0x100 indicates that the starting address of the object code should be 0x100.
```x86asm
.pos 0x100 # Start code at address 0x100
    irmovq $15, %rbx
    rrmovq %rbx, %rcx
loop:
    rmmovq %rcx, -3(%rbx)
    addq   %rbx, %rcx
    jmp loop
```
<!-- more -->
**Solution to Problem 4.1**
Encoding instructions by hand is rather tedious, but it will solidify your understanding of the idea that assembly code gets turned into byte sequences by the assembler. In the following output from our Y86-64 assembler, each line shows an address and a byte sequence that starts at that address:
```x86asm
    0x100:                                | .pos 0x100  # Start code at address 0x100
    0x100: 30f30f00000000000000           |      irmovq $15,%rbx
    0x10a: 2031                           |      rrmovq %rbx,%rcx
    0x10c:                                | loop:
    0x10c: 4013fdffffffffffffff           |      rmmovq %rcx,-3(%rbx)
    0x116: 6031                           |      addq   %rbx,%rcx
    0x118: 700c01000000000000             |      jmp  loop
```
Several features of this encoding are worth noting:
+ Decimal 15 (line 2) has hex representation 0x000000000000000f. Writing the bytes in reverse order gives 0f 00 00 00 00 00 00 00.
+ Decimal −3 (line 5) has hex representation 0xfffffffffffffffd. Writing the bytes in reverse order gives fd ff ff ff ff ff ff ff.
+ The code starts at address 0x100. The first instruction requires 10 bytes, while the second requires 2. Thus, the loop target will be 0x0000010c. Writing these bytes in reverse order gives 0c 01 00 00 00 00 00 00.
---------------------------------------
### Practice Problem 4.2  
For each byte sequence listed, determine the Y86-64 instruction sequence it en-codes. If there is some invalid byte in the sequence, show the instruction sequence up to that point and indicate where the invalid value occurs. For each sequence, we show the starting address, then a colon, and then the byte sequence.
A. 0x100: 30f3fcffffffffffffff40630008000000000000
B. 0x200: a06f800c020000000000000030f30a00000000000000
C. 0x300: 5054070000000000000010f0b01f
D. 0x400: 611373000400000000000000
E. 0x500: 6362a0f0
**Solution to Problem 4.2**
Decoding a byte sequence by hand helps you understand the task faced by a processor. It must read byte sequences and determine what instructions are to be executed. In the following, we show the assembly code used to generate each of the byte sequences. To the left of the assembly code, you can see the address and byte sequence for each instruction.
A. Some operations with immediate data and address displacements:
```x86asm
0x100: 30f3fcffffffffffffff     |     irmovq $-4,%rbx
0x10a: 40630008000000000000     |     rmmovq %rsi,0x800(%rbx)
0x114: 00                       |     halt
```
B. Code including a function call:
```x86asm
0x200: a06f                     |     pushq %rsi
0x202: 800c02000000000000       |     call proc
0x20b: 00                       |     halt
0x20c:                          | proc:
0x20c: 30f30a00000000000000     |     irmovq $10,%rbx
0x216: 90                       |     ret
```
C. Code containing illegal instruction specifier byte 0xf0:
```x86asm
0x300: 50540700000000000000     |     mrmovq 7(%rsp),%rbp
0x30a: 10                       |     nop
0x30b: f0                       | .byte 0xf0 # Invalid instruction code
0x30c: b01f                     |     popq %rcx
```
D. Code containing a jump operation:
```x86asm
0x400:                          | loop:
0x400: 6113                     |     subq %rcx, %rbx
0x402: 730004000000000000       |     je loop
0x40b: 00                       |     halt
```
E. Code containing an invalid second byte in a pushq instruction:
```x86asm
x500: 6362                      |     xorq %rsi,%rdx
0x502: a0                       | .byte 0xa0 # pushq instruction
code                            
0x503: f0                       | .byte 0xf0 # Invalid register
specifier byte
```
---------------------------------------
### Practice Problem 4.3  
One common pattern in machine-level programs is to add a constant value to a register. With the Y86-64 instructions presented thus far, this requires first using an irmovq instruction to set a register to the constant, and then an addq instruction to add this value to the destination register. Suppose we want to add a new instruction iaddq with the following format:
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1594454732/4.3_zitvkl.png)
This instruction adds the constant value V to register rB. Rewrite the Y86-64 sum function of Figure 4.6 to make use of the iaddq instruction. In the original version, we dedicated registers %r8 and %r9 to hold constant values. Now, we can avoid using those registers altogether.
**Solution to Problem 4.3**
Using the iaddq instruction, we can rewrite the sum function as
```x86asm
# long sum(long *start, long count)
# start in %rdi, count in %rsi
sum:
    xorq    %rax,%rax         #sum = 0
    andq    %rsi,%rsi         #Set condition codes
    jmp     test
loop:
    mrmovq  (%rdi),%r10      # Get *start
    addq    %r10,%rax        # Add to sum
    iaddq   $8,%rdi          # start++
    iaddq   $-1,%rsi         # count--
test:
    jne    loop              # Stop when 0
    ret
```
----------------------------------------
### Practice Problem 4.4
Write Y86-64 code to implement a recursive product function rproduct, based on the following C code:
```c
long rproduct(long *start, long count)
{
    if (count <= 1)
        return 1;
    return *start * rproduct(start+1, count-1);
}
```
Use the same argument passing and register saving conventions as x86-64 code does. You might find it helpful to compile the C code on an x86-64 machine and then translate the instructions to Y86-64.
***Solution to Problem 4.4***
Gcc, running on an x86-64 machine, produces the following code for rproduct:
```x86asm
long rproduct(long *start, long count)
start in %rdi, count in %rsi
rproduct:
    movl     $1, %eax
    testq    %rsi, %rsi
    jle     .L9
    pushq   %rbx
    movq    (%rdi), %rbx
    subq    $1, %rsi
    addq    $8, %rdi
    call    rproduct
    imulq   %rbx, %rax
    popq    %rbx
.L9:
    rep; ret
```
This can easily be adapted to produce Y86-64 code
```x86asm
# long rproduct(long *start, long
# start in %rdi, count in %rsi
rproduct:
    xorq    %rax,%rax    #Set return value to 1
    andq    %rsi,%rsi    #Set condition codes
    je      return       #If count <= 0, return 1
    pushq   %rbx         #Save callee-saved register
    mrmovq  (%rdi),%rbx  # Get *start
    irmovq  $-1,%r10
    addq    %r10,%rsi    # count--
    irmovq  $8,%r10
    addq    %r10,%rdi    # start++
    call    rproduct
    imulq   %rbx,%rax    # Multiply *start to product
    popq    %rbx         # Restore callee-saved register
return:
    ret
```
-----------------------------------
### Practice Problem 4.5
Modify the Y86-64 code for the sum function (Figure 4.6) to implement a function absSum that computes the sum of absolute values of an array. Use a conditional jump instruction within your inner loop.
***Solution to Problem 4.5***
This problem gives you a chance to try your hand at writing assembly code.
```X86asm
# long absSum(long *start, long count)
# start in %rdi, count in %rsi
absSum:
        irmovq  $8,%r8           # Constant 8
        irmovq  $1,%r9           # Constant 1
        xorq    %rax,%rax        # sum = 0
        andq    %rsi,%rsi        # Set condition codes
        jmp     test
loop:
        mrmovq (%rdi),%r10      # x = *start
        xorq   %r11,%r11        # Constant 0
        subq   %r10,%r11        # -x
        jle    pos              # Skip if -x <= 0
        rrmovq %r11,%r10        # x = -x
pos:
        addq   %r10,%rax        # Add to sum
        addq   %r8,%rdi         # start++
        subq   %r9,%rsi         # count--
test:
        jne  loop               # Stop when 0
        ret
```
-------------------------------
### Practice Problem 4.6
Modify the Y86-64 code for the sum function (Figure 4.6) to implement a function absSum that computes the sum of absolute values of an array. Use a conditional move instruction within your inner loop.
***Solution to Problem 4.6***
his problem gives you a chance to try your hand at writing assembly code with conditional moves. We show only the code for the loop. The rest is the same as for Problem 4.5:
```x86asm
loop:
        mrmovq  (%rdi),%r10     # x = *start
        xorq    %r11,%r11       # Constant 0
        subq    %r10,%r11       # -x
        cmovg   %r11,%r10       # If -x > 0 then x = -x
        addq    %r10,%rax       # Add to sum
        addq    %r8,%rdi        # start++
        subq    %r9,%rsi        # count--
test:
        jne     loop            # Stop when 0
```
-------------------------------
### Practice Problem 4.7
Let us determine the behavior of the instruction pushq %rsp for an x86-64 processor. We could try reading the Intel documentation on this instruction, but a simpler approach is to conduct an experiment on an actual machine. The C compiler would not normally generate this instruction, so we must use hand-generated assembly code for this task. Here is a test function we have written (Web Aside asm:easm on page 214 describes how to write programs that combine C code with handwritten assembly code):
```x86asm
.text
.globl pushtest
pushtest:
    movq
    %rsp, %rax          #Copy stack pointer
    pushq %rsp          #Push stack pointer
    popq  %rdx          #Pop it back
    subq  %rdx, %rax    #Return 0 or 4
    ret
```
In our experiments, we find that function pushtest always returns 0. What does this imply about the behavior of the instruction pushq %rsp under x86-64?
***Solution to Problem 4.7***
Although it is hard to imagine any practical use for this particular instruction, it is important when designing a system to avoid any ambiguities in the specification. We want to determine a reasonable convention for the instruction’s behavior and to make sure each of our implementations adheres to this convention. The subq instruction in this test compares the starting value of %rsp to the value pushed onto the stack. The fact that the result of this subtraction is zero implies that the old value of %rsp gets pushed. 

----------------------------------
### Practice Problem 4.8
The following assembly-code function lets us determine the behavior of the instruction popq %rsp for x86-64:
```
.text
.globl poptest
poptest:
    movq  %rsp, %rdi    #Save stack pointer
    pushq $0xabcd       #Push test value
    popq  %rsp          #Pop to stack pointer
    movq  %rsp, %rax    #Set popped value as return value
    movq  %rdi, %rsp    #Restore stack pointer
    ret
```
We find this function always returns 0xabcd. What does this imply about the behavior of popq %rsp? What other Y86-64 instruction would have the exact same behavior?
***Solution to Problem 4.8***
It is even more difficult to imagine why anyone would want to pop to the stack pointer. Still, we should decide on a convention and stick with it. This code sequence pushes 0xabcd onto the stack, pops to %rsp, and returns the popped value. Since the result equals 0xabcd, we can deduce that popq %rsp sets the stack pointer to the value read from memory. It is therefore equivalent to the instruction mrmovq (%rsp),%rsp.
