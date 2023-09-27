---
title: "RISC-V Microprocessor Based On RVI32"
excerpt: "A toy implementation of pipelined RVI32, course project of Computer Organization. <br/><img src='/images/riscv.png' width='500' height='600'>"
collection: portfolio
---
In the RV32I instruction set, there are four core formats: R-type, I-type, S-type, and U-type, as well as two additional formats, SB-type and UJ-type. All instructions have a fixed length of 32 bits. To enhance CPU stability and operational speed, a pipelined design is employed, dividing the instruction processing into five stages: IF (instruction fetch), ID (instruction decode), EX (execute), MEM (memory access), and WB (write back). The execution of each instruction requires five clock cycles. At the rising edge of each clock cycle, the data and information represented by the instruction are transferred to the next stage for processing.

This mini CPU can execute core RV32I instructions based on pipeline technology. I implemented this RISC-V CPU in Nexys Video and it had a running speed higher than 25MHz.

[Project Link](https://github.com/Alexander-Suen/LC-3_Executor)

