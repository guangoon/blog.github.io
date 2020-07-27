---
title: Chapter 4:Processor Architecture(4.4)
date: 2020-07-26 23:33:41
tags:
categories: CSAPP
---
### Practice Problem 4.28
Suppose we analyze the combinational logic of Figure 4.32 and determine that it can be separated into a sequence of six blocks, named A to F, having delays of 80, 30, 60, 50, 70, and 10 ps, respectively illustrated as follows:
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595777868/Solution4-28_ghhd3p.png)
We can create pipelined versions of this design by inserting pipeline registers between pairs of these blocks. Different combinations of pipeline depth (how many stages) and maximum throughput arise, depending on where we insert the pipeline registers. Assume that a pipeline register has a delay of 20 ps.
A. Inserting a single register gives a two-stage pipeline. Where should the register be inserted to maximize throughput? What would be the throughput and latency?
B. Where should two registers be inserted to maximize the throughput of a three-stage pipeline? What would be the throughput and latency?
C. Where should three registers be inserted to maximize the throughput of a 4-stage pipeline? What would be the throughput and latency?
D. What is the minimum number of stages that would yield a design with the maximum achievable throughput? Describe this design, its throughput, and its latency.
<!-- more -->
***Solution to Problem 4.28***
This problem is an interesting exercise in trying to find the optimal balance among a set of partitions. It provides a number of opportunities to compute throughputs and latencies in pipelines.
A. For a two-stage pipeline, the best partition would be to have blocks A, B, and C in the first stage and D, E, and F in the second. The first stage has a delay of 170 ps, giving a total cycle time of 170 + 20 = 190 ps. We therefore have a throughput of 5.26 GIPS and a latency of 380 ps.
B. For a three-stage pipeline, we should have blocks A and B in the first stage, blocks C and D in the second, and blocks E and F in the third. The first two stages have a delay of 110 ps, giving a total cycle time of 130 ps and a throughput of 7.69 GIPS. The latency is 390 ps.
C. For a four-stage pipeline, we should have block A in the first stage, blocks B and C in the second, block D in the third, and blocks E and F in the fourth. The second stage requires 90 ps, giving a total cycle time of 110 ps and a throughput of 9.09 GIPS. The latency is 440 ps.
D. The optimal design would be a five-stage pipeline, with each block in its own stage, except that the fifth stage has blocks E and F. The cycle time is 80 + 20 = 100 ps, for a throughput of around 10.00 GIPS and a latency of 500 ps. Adding more stages would not help, since we cannot run the pipeline any faster than one cycle every 100 ps.

----------------------------
### Practice Problem 4.29
Suppose we could take the system of Figure 4.32 and divide it into an arbitrary number of pipeline stages k, each having a delay of 300/k, and with each pipeline register having a delay of 20 ps.
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595778492/Solution4-29_o0eiy9.png)
A. What would be the latency and the throughput of the system, as functions of k?
B. What would be the ultimate limit on the throughput?
***Solution to problem 4.29***
Each stage would have combinational logic requiring 300/k ps and a pipeline register requiring 20 ps.
A. The total latency would be 300 + 20k ps, while the throughput (in GIPS) would be
![](https://res.cloudinary.com/dbtdrt9af/image/upload/v1595778691/Solution4-29-1_bwwkrn.png)
B. As we let k go to infinity, the throughput becomes 1,000/20 = 50 GIPS. Of course, the latency would approach infinity as well.
This exercise quantifies the diminishing returns of deep pipelining. As we try to subdivide the logic into many stages, the latency of the pipeline registers becomes a limiting factor.