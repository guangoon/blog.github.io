---
title: Chapter 4:Processor Architecture(4.2)
date: 2020-07-10 00:08:35
tags:
categories: CSAPP
---
### Practice Problem 4.9
Write an HCL expression for a signal xor, equal to the exclusive-or of inputs a and b. What is the relation between the signals xor and eq defined above?
***Solution to Problem 4.9***
The exclusive-or function requires that the 2 bits have opposite values:
bool xor = (!a && b) || (a && !b);
In general, the signals eq and xor will be complements of each other. That is,one will equal 1 whenever the other is 0.

-------------------------------
### Practice Problem 4.10
Suppose you want to implement a word-level equality circuit using the exclusive-or circuits from Problem 4.9 rather than from bit-level equality circuits. Design such a circuit for a 64-bit word consisting of 64 bit-level exclusive-or circuits and two additional logic gates.
***Solution to Problem 4.10***
The outputs of the exclusive-or circuits will be the complements of the bit equality values. Using DeMorgan’s laws (Web Aside data:bool on page 88), we can implement and using or and not, yielding the circuit shown in Figure 4.71.
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1594915876/Solution4-11_m6f3bf.png)

-------------------------------
### Practice Problem 4.11
The HCL code given for computing the minimum of three words contains four comparison expressions of the form X <= Y . Rewrite the code to compute the same result, but using only three comparisons.
***Solution to Problem 4.11***
We can see that the second part of the case expression can be written as
B <= C       : B;
Since the first line will detect the case where A is the minimum element, the second line need only determine whether B or C is minimum.

-------------------------------
### Practice Problem 4.12
Write HCL code describing a circuit that for word inputs A, B, and C selects the median of the three values. That is, the output equals the word lying between the minimum and maximum of the three inputs.
***Solution to Problem 4.11***
```
word Med3 = {
        A <= B && B <= C : B;
        C <= B && B <= A : B;
        B <= A && A <= C : A;
        C <= A && A <= B : A;
        1                : C;
};
```
