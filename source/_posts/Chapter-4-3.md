---
title: Chapter-4-3
date: 2020-07-17 22:47:40
tags:
categories: CSAPP
---
 ### Practice Problem 4.13
Fill in the right-hand column of the following table to describe the processing of the irmovq instruction on line 4 of the object code in Figure 4.17:
<!-- more -->
***Solution to Problem 4.13***
Implementing conditional moves requires only minor changes from register-to-register moves. We simply condition the write-back step on the outcome of the conditional test:
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595522622/Solution4-13_aldi7i.png)
This instruction sets register %rsp to 128 and increments the PC by 10.

---------------------------------------------
### Practice Problem 4.14
Fill in the right-hand column of the following table to describe the processing of the popq instruction on line 7 of the object code in Figure 4.17.
***Solution to Problem 4.14***
We can see that the instruction is located at address 0x02c and consists of 2 bytes with values 0xb0 and 0x00f. Register %rsp was set to 120 by the pushq instruction (line 6), which also stored 9 at this memory location.
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595522728/Solution4-14_bxsvr9.png)
The instruction sets %rax to 9, sets %rsp to 128, and increments the PC by 2.

----------------------------------------------
### Practice Problem 4.15
What would be the effect of the instruction pushq %rsp according to the steps listed in Figure 4.20? Does this conform to the desired behavior for Y86-64, as determined in Problem 4.7?
***Solution to Problem 4.15***
Tracing the steps listed in Figure 4.20 with rA equal to %rsp, we can see that in the memory stage the instruction will store valA, the original value of the stack pointer, to memory, just as we found for x86-64.

-----------------------------------------------
### Practice Problem 4.16
Assume the two register writes in the write-back stage for popq occur in the order listed in Figure 4.20. What would be the effect of executing popq %rsp? Does this conform to the desired behavior for Y86-64, as determined in Problem 4.8?
***Solution to Problem 4.16***
Tracing the steps listed in Figure 4.20 with rA equal to %rsp, we can see that both of the write-back operations will update %rsp. Since the one writing valM would occur last, the net effect of the instruction will be to write the value read from memory to %rsp, just as we saw for x86-64.

-----------------------------------------------
### Practice Problem 4.17
We can see by the instruction encodings (Figures 4.2 and 4.3) that the rrmovq instruction is the unconditional version of a more general class of instructions that include the conditional moves. Show how you would modify the steps for the rrmovq instruction below to also handle the six conditional move instructions.You may find it useful to see how the implementation of the jXX instructions (Figure 4.21) handles conditional behavior.
***Solution to problem 4.17***
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595522170/Solution4-17-1_ncpxzb.png)
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595522170/Solution4-17-2_s65soy.png)

----------------------------------------------
### Practice Problem 4.18
Fill in the right-hand column of the following table to describe the processing of the call instruction on line 9 of the object code in Figure 4.17:
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595687631/Solution4-18-1_yzcq7n.png)
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595687632/Solution4-18-2_doxliy.png)
What effect would this instruction execution have on the registers, the PC,and the memory?
***Solution to Problem 4.18***
We can see that this instruction is located at address 0x037 and is 9 bytes long. The first byte has value 0x80, while the last 8 bytes are a byte-reversed version of 0x0000000000000041, the call target. The stack pointer was set to 128 by the popq instruction (line 7).
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595687958/Solution4-18-3_i2vaxg.png)
The effect of this instruction is to set %rsp to 120, to store 0x040 (the return address) at this memory address, and to set the PC to 0x041 (the call target).

---------------------------------------------
### Practice Problem 4.19
Write HCL code for the signal need_valC in the SEQ implementation.
***Solution to Problem 4.19***
All of the HCL code in this and other practice problems is straightforward, but trying to generate it yourself will help you think about the different instructions and how they are processed. For this problem, we can simply look at the set of Y86-64 instructions (Figure 4.2) and determine which have a constant field.
bool need_valC =
    icode in { IIRMOVQ, IRMMOVQ, IMRMOVQ, IJXX, ICALL };

--------------------------------------------
### Practice Problem 4.20
The register signal srcB indicates which register should be read to generate the signal valB. The desired value is shown as the second step in the decode stage in Figures 4.18 to 4.21. Write HCL code for srcB.
***Solution to Problem 4.20***
This code is similar to the code for srcA.
```
    word srcB = [
        icode in { IOPQ, IRMMOVQ, IMRMOVQ } : rB;
        icode in { IPUSHQ, IPOPQ, ICALL, IRET } : RRSP;
        1 : RNONE; # Don’t need register
    ];
```

--------------------------------------------
### Practice Problem 4.21
Register ID dstM indicates the destination register for write port M, where valM, the value read from memory, is stored. This is shown in Figures 4.18 to 4.21 as the second step in the write-back stage. Write HCL code for dstM.
***Solution to Problem 4.21***
```
word dstM = [
    icode in { IMRMOVQ, IPOPQ } : rA;
    1 : RNONE; # Don’t write any register
];
```

--------------------------------------------
### Practice Problem 4.22
Only the popq instruction uses both register file write ports simultaneously. For the instruction popq %rsp, the same address will be used for both the E and M write ports, but with different data. To handle this conflict, we must establish a priority among the two write ports so that when both attempt to write the same register on the same cycle, only the write from the higher-priority port takes place. Which of the two ports should be given priority in order to implement the desired behavior, as determined in Practice Problem 4.8?
***Solution to Problem 4.22***
As we found in Practice Problem 4.16, we want the write via the M port to take priority over the write via the E port in order to store the value read from memory into %rsp.

-------------------------------------------
### Practice Problem 4.23
Based on the first operand of the first step of the execute stage in Figures 4.18 to 4.21, write an HCL description for the signal aluB in SEQ.
***Solution to Problem 4.23***
```
word aluB = [
    icode in { IRMMOVQ, IMRMOVQ, IOPQ, ICALL, IPUSHQ, IRET, IPOPQ } : valB;
    icode in { IRRMOVQ, IIRMOVQ } : 0;
    # Other instructions don’t need ALU
];
```
------------------------------------------
### Practice Problem 4.24
The conditional move instructions, abbreviated cmovXX, have instruction code IRRMOVQ. As Figure 4.28 shows, we can implement these instructions by making use of the Cnd signal, generated in the execute stage. Modify the HCL code for dstE to implement these instructions.
***Solution to Problem 4.24***
Implementing conditional moves is surprisingly simple: we disable writing to the register file by setting the destination register to RNONE when the condition does not hold.
```
word dstE = [
    icode in { IRRMOVQ } && Cnd : rB;
    icode in { IIRMOVQ, IOPQ} : rB;
    icode in { IPUSHQ, IPOPQ, ICALL, IRET } : RRSP;
    1 : RNONE; # Don’t write any register
];
```
------------------------------------------
### Practice Problem 4.25
Looking at the memory operations for the different instructions shown in Figures 4.18 to 4.21, we can see that the data for memory writes are always either valA or valP. Write HCL code for the signal mem_data in SEQ.
***Solution to problem 4.25***
```
word mem_data = [
    # Value from register
    icode in { IRMMOVQ, IPUSHQ } : valA;
    # Return PC
    icode == ICALL : valP;
    # Default: Don’t write anything
];
```
-------------------------------------------
### Practice Problem 4.26
We want to set the control signal mem_write only for instructions that write data to memory. Write HCL code for the signal mem_write in SEQ.
***Solution to problem 4.26***
bool mem_write = icode in { IRMMOVQ, IPUSHQ, ICALL };

-------------------------------------------
### Practice Problem 4.27
Write HCL code for Stat, generating the four status codes SAOK, SADR, SINS, and SHLT (see Figure 4.26).
***Solution to problem 4.27***
```
## Determine instruction status
word Stat = [
    imem_error || dmem_error : SADR;
    !instr_valid: SINS;
    icode == IHALT : SHLT;
    1 : SAOK;
];
```