# Chapter 10+

# Recursion

## Return address

### Is the return address for a recursive function always the same at each function call?

A: 

![Untitled](Chapter_10+/Untitled.png)

## Important

ACV: access control violation exception. ACV exception occurs if a process attempts to access a location in privileged memory (system space or I/O page)

P131, In phase FETCH OPERAND, the MAR ← results of EVALUATE ADDRESS, and MDR ← M[MAR].

MAR is not loaded in phase EAVLUATE ADDRESS.

# Microarchitecture of the LC-3

## Overview

### Data path

contains all the components that actually process the instructions.

### Control

all the components that generate the set of control signals, to control the processing.

### Clock Cycle

was provided with  J, COND, IRD

### Others

BEN: to indicate whether or not a BR should be taken. (Branch enable)

INT: .. higher PL than the current process

R: to indicate the end of a memory operation. E.g., MDR ← M[MAR], the state continues to execute until a ready signal from the memory (R) is asserted, indicating that the memory access has been completed. 

## State Machine

There are 52 signals specifying the state of the control structure / behaviors of the LC-3 microarchitecture by graph, called state machine.

One state for one clock. 

P702

## The Data Path

Data path: consists of all components that actually process the information during each clock cycle.