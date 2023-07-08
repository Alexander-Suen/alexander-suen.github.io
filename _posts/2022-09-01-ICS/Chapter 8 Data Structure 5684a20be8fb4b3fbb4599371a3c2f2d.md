# Chapter 8 Data Structure

## Subroutines

subroutines / functions / procedures

### Call / Return Mechanism

Two instructions:

- JSR:  loads R7 with the next address immediately after the address (return linkage) of JSR; Loads the PC with the starting address of subroutine (PCoffset11). (R7 first!!!!!!)
- JSRR: loads R7 with the next address immediately after JSRR. Loads the PC with the address contained in R3 (BaseR, jump to that address).
- JMP R7 (RET): the last instruction in subroutine, returning back to the “return linkage” of the caller.

![Untitled](Chapter%208%20Data%20Structure%205684a20be8fb4b3fbb4599371a3c2f2d/Untitled.png)

Call instruction (in caller) → code A (callee) → return instruction

### Saving & Restoring Registers

Previous contents of registers would be stored in memory locations (are set aside for saving those register values).

- Callee save: the subroutine (the callee) saves and restores the registers.
- Caller save: the caller program save all the registers before JSR (jump to subroutine). E.g., R7.

- ST: save the register values
- LD: restore the register values
- Pseudo-op: .BLKW, set blocks to store the register values.

### Library Routines

Hide implementation details of some complex functions.

Pseudo-op .EXTERNAL tells the assembler that the label (e.g., SQRT in MATH library) will be supplied by some other program fragment (module).  That module will be linked together to create the executable image.

### Linking of executable image

source → assemble → 1.obj + symbol table → link as executable image ← module for library.obj + symbol table for library.

Libraries are pre-assembled. 

![Untitled](Chapter%208%20Data%20Structure%205684a20be8fb4b3fbb4599371a3c2f2d/Untitled%201.png)

## Stack

LIFO, Last in, first out.

### Push & Pop

Stack pointer: keeps track of the top of the stack. (contain the address of the top of the stack.

![This stack grows towards zero. ](Chapter%208%20Data%20Structure%205684a20be8fb4b3fbb4599371a3c2f2d/Untitled%202.png)

This stack grows towards zero. 

```nasm
PUSH  ADD R6, R6, #-1   ; R6--
      STR R0, R6, #0    ; BaseR + #0, R0 -> mem[R5 + 0]
```

R6 contains the address of the top element so the value in R6 should be accessed by BaseR + #0 (STR for push, LDR for pop).

```nasm
POP  LDR R0, R6, #0   ; R0 <- mem[R6 + 0]
     ADD R6, R6, #1   
```

### Underflow & Overflow

- Underflow: try to pop values when the stack is empty.
- Overflow: we can’t store a value on top of the stack because there is no space.

As a default, R6 = the stack pointer

```nasm
; Test the underflow if the R6 contains x4000
POP     R1, EMPTY
        ADD R2, R6, R1 ; Compare stack
        BRnz UNDERFLOW ; points to x4000
        
        LDR R0, R6, #0 ; POP
        ADD R6, R6, #1
        
        RET
EMPTY   .FILL xC000 
; negative of x4000 (0100 0000 0000 0000) 
; = xC000 (1 + 1011 1111 1111 1111 = 1100 0000 0000 0000)
```

```nasm
; Test overflow if the stack has topped over x3FFB
PUSH        R1, FULL ; Negative of x3FFB -> R1
            ADD R2, R6, R1
            BRnz OVERFLOW
            
            ADD R6, R6, #-1
            STR R2, R6, #0
            
            RET
FULL        .FILL xC005
; x3FFB = 0011 1111 1111 1011
; Negative of x3FFB = 1100 0000 0000 0101 = xC005
```

### The Complete Picture

Note: success_exit - R5 positive

failure_exit - R5 negative

ST / LD value of R1, R2

```nasm
; SAVE
ST R1, SaveR1
ST R2, SaveR2

; RESTORE
LD R1, SaveR1
LD R2, SaveR2

; Memory for storage
SaveR1 .FILL x0000 ; the value x0000 will be overwritten
SaveR2 .FILL x0000
```

![Untitled](Chapter%208%20Data%20Structure%205684a20be8fb4b3fbb4599371a3c2f2d/Untitled%203.png)

## Recursion

See Chapter 17, P569

- Towers of Hanoi
- Maze
- Recursion vs Iteration

## Queue

FIFO, First in, first out.

FRONT (R3): the location before the first element (R3), the value in the address of R3 is removed. 

REAR (R4): the location containing the most recent element that was added to the queue.

![In (a), the first value is in x8002 (23) while the last value is 74 (in x8004). The value in FRONT has been removed from the queue.](Chapter%208%20Data%20Structure%205684a20be8fb4b3fbb4599371a3c2f2d/Untitled%204.png)

In (a), the first value is in x8002 (23) while the last value is 74 (in x8004). The value in FRONT has been removed from the queue.

### Remove from FRONT, Insert at REAR

Remove from FRONT

```nasm
REMOVE  ADD R3, R3, #1 ; R3 points to the position before the first element
				LDR R0, R3, #0

```

Insert at REAR

```nasm
INSERT  ADD R4, R4, #1
				STR R0, R4, #0
```

### Wrap-Around

```nasm
; If R4 (REAR) contains x8005, then wrap-around
REMOVE  LD R2, LAST     ; R2 <- x7FFB
        ADD R2, R3, R2  ; if R3 != x8005, Branch to SKIP_1, FRONT++
        BRnp SKIP_1
        
        LD R3, FIRST ; if R3 == x8005, Then set FRONT == x8000 (to the start)
        BR SKIP_2
SKIP_1  ADD R3, R3, #1
SKIP_2  LDR R0, R3, #0 ; R0 <- m[R3] = the value in x8000 (restart)
        RET
LAST    .FILL x7FFB ; negative of x8005
FIRST   .FILL x8000 ; the first position

; If the queue has been inserted over x8005, then wrap-around
INSERT  LD R2, LAST ; R2 <- x7FFB
        ADD R2, R4, R2 ; 
        BRnp SKIP_1
        
        ; REAR == x8005
        LD R4, FIRST ; Restart again
        BR SKIP_2
        
SKIP_1  ADD R4, R4, #1
SKIP_2  STR R0, R4, #0 ; R0 gets the front of the queue
        RET
LAST    .FILL x7FFB
FIRST   .FILL x8000
```

### Number of elements in a queue

We allow only n - 1 elements to be stored in a n-sized queue to avoid that the state of full and that of empty are identical.

![Untitled](Chapter%208%20Data%20Structure%205684a20be8fb4b3fbb4599371a3c2f2d/Untitled%205.png)

For a full queue, REAR + 1 == FRONT;

For a empty queue, REAR == FRONT;

### Tests for underflow, overflow

Underflow: if the queue is empty (FRONT == REAR), then you would fail to Remove elements. You should also set the R5 as 1 to indicate the error.

Overflow: test if REAR + 1 - FRONT == 0, if true, R5 ← #1 and RET to caller.

```nasm
; Test underflow
TESTU   AND R5, R5, #0 ; R5 to indicate it succeeds or not
        ; See if REAR == FRONT
        NOT R2, R3
        ADD R2, R2, #1 ; R2 <- -R3
        ADD R2, R2, R4 ; See if FRONT - REAR == 0
        BRz UNDERFLOW ; ERROR, FRONT == REAR
        
UNDERFLOW ADD R5, R5, #1 ; ERROR PROMPT
          RET

; Test Overflow
TESTO   AND R5, R5, #0
        NOT R2, R3
        ADD R2, R2, #1 ; R2 <- -R3 = -FRONT
        ADD R2, R2, R4 ; R4 = REAR
        ADD R2, R2, #1 ; Test if REAR + 1 - FRONT == 0
        
        BRz OVERFLOW
OVERFLOW  ADD R5, R5, #1
          RET
```

### Complete Picture

See P299

## Character Strings

A sequence of keyboard characters (letters, digits, and other symbols) is organized as a one-dimensional array of ASCII codes. Terminating with null character ‘\n’ (x0000).

Using strings to represent lone integers. “7982384”