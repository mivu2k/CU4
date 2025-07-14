
Branching in assembly language allows the program's execution flow to change based on conditions or to jump to a different part of the code unconditionally. This is crucial for implementing control structures like if-else statements, loops, and function calls.

### 1. Conditional Jumps

Conditional jumps alter the program flow based on the state of the CPU's flags, which are set by previous arithmetic or logical operations. There are over 30 variations of conditional jump instructions.

#### 1.1. `CMP` vs. `SUB` Instructions

Both `CMP` (Compare) and `SUB` (Subtract) perform a subtraction operation, but their effects on operands differ:

- **`CMP Operand1, Operand2`**: Subtracts `Operand2` from `Operand1` but **does not change the contents** of either operand. Its primary purpose is to 
    
    **affect the flags** (AF, CF, OF, PF, SF, ZF) based on the result of the subtraction. The comparison relation is from the destination to the source. For example, 
    
    `CMP AL, BL` will set `ZF=1` if `AL` and `BL` are equal.
    
- **`SUB Operand1, Operand2`**: Subtracts `Operand2` from `Operand1` and **stores the result in `Operand1`**. For example, 
    
    `SUB AL, 1` would change the value of `AL`.
    

#### 1.2. Common Conditional Jump Instructions

- **`JNZ` (Jump if Not Zero)**: Jumps if the Zero Flag (ZF) is clear (0). This means the result of the previous operation was non-zero.
    
- **`JZ` (Jump if Zero)**: Jumps if the Zero Flag (ZF) is set (1).
    
- **`JC` (Jump if Carry)**: Jumps if the Carry Flag (CF) is set (1). This indicates a previous operation caused a carry.
    
- **`JNC` (Jump if No Carry)**: Jumps if the Carry Flag (CF) is clear (0).
    
- **`JS` (Jump if Sign)**: Jumps if the Sign Flag (SF) is set (1) (result is negative).
    
- **`JNS` (Jump if Not Sign)**: Jumps if the Sign Flag (SF) is clear (0) (result is positive).
    
- **`JO` (Jump if Overflow)**: Jumps if the Overflow Flag (OF) is set (1).
    
- **`JNO` (Jump if Not Overflow)**: Jumps if the Overflow Flag (OF) is clear (0).
    
- **`JP` (Jump if Parity)**: Jumps if the Parity Flag (PF) is set (1) (even parity).
    
- **`JNP` / `JPO` (Jump if No Parity / Jump if Parity Odd)**: Jumps if the Parity Flag (PF) is clear (0) (odd parity).
    

#### 1.3. Conditional Jumps for Comparisons (Signed vs. Unsigned)

For comparisons, different jump instructions are used based on whether the numbers are signed or unsigned.

- **For Unsigned Numbers:**
    
    - `JA` (Jump if Above): Jumps if destination > source (unsigned).
        
    - `JAE` (Jump if Above or Equal): Jumps if destination >= source (unsigned).
        
    - `JB` (Jump if Below): Jumps if destination < source (unsigned).
        
    - `JBE` (Jump if Below or Equal): Jumps if destination <= source (unsigned).
        
- **For Signed Numbers:**
    
    - `JG` (Jump if Greater): Jumps if destination > source (signed).
        
    - `JGE` (Jump if Greater or Equal): Jumps if destination >= source (signed).
        
    - `JL` (Jump if Less): Jumps if destination < source (signed).
        
    - `JLE` (Jump if Less or Equal): Jumps if destination <= source (signed).
        

#### 1.4. Range of Conditional Jumps

All conditional jumps are "SHORT" jumps, meaning their target address must be within -128 to +127 bytes of the current instruction pointer. To overcome this limitation for larger distances, a conditional jump can be combined with an unconditional jump.

### 2. Unconditional Jumps

An unconditional jump (

`JMP`) causes an immediate transfer of control to a specified `destination_label`. Labels are symbolic names defined to be an address in the code segment. Labels typically end with a colon (e.g., 

`a_Label:`).

#### 2.1. Types of Unconditional Jumps based on Reach

- **Short Jump**: The destination label is within -128 to +127 bytes of the current instruction pointer. This is common for conditional jumps and can also be explicit with 
    
    `jmp short label`.
    
- **Near Jump**: The destination label is within the same segment. The jump address is specified as an offset relative to the current instruction pointer.
    
- **Far Jump**: The destination label is in a different code segment. Both the segment address and the offset address must be specified.
    

#### 2.2. Relative Addressing in Jumps

The processor often calculates jump addresses using 

**relative addressing**. Instead of specifying an absolute memory address, the jump instruction contains an offset (displacement) from the current Instruction Pointer (IP).

- If the displacement is positive, it means jumping forward.
    
- If the displacement is negative, it means jumping backward. This makes the code position-independent, meaning it can be loaded anywhere in memory and still execute correctly.
    

### 3. Bubble Sort Implementation in Assembly Language

Sorting is the process of arranging array values in ascending or descending order. Bubble sort works by repeatedly stepping through the array, comparing adjacent items, and swapping them if they are in the wrong order until no swaps are needed.

#### 3.1. Algorithm Logic

1. **Outer Loop**: Controls the number of passes through the array. For an array of N elements, N-1 passes are typically sufficient to sort.
    
2. **Inner Loop**: Compares adjacent elements and swaps them if they are in the incorrect order (e.g., for ascending sort, if the current element is greater than the next).
    
3. **Swap Flag**: A flag (e.g., `swap db 1` initially) can be used to optimize the sort. If a full pass completes with no swaps, the array is sorted, and the algorithm can terminate early.
    

#### 3.2. Assembly Code Structure for Bubble Sort (Ascending Order)

A basic structure for bubble sort in assembly using flags and jumps:

Code snippet

```
.DATA
    data1 dw 3,9,8,1,12,5,13,2,6,7 ; Array to be sorted
    swap db 1                   ; Flag to check if any swap occurred (1 = swapped, 0 = no swap)

.CODE
   MAIN PROC
      MOV AX, @DATA                ; Initialize DS
      MOV DS, AX

  mov cl,0                         ; Outer loop counter (e.g., for number of passes)
start:                             ; Outer loop label
  mov ch, [swap]                   ; Check the swap flag
  cmp ch,0
  jnz init                         ; If swap occurred in previous pass, continue
  jz end                           ; If no swap, array is sorted, exit

init:
 mov bx,0                         ; Inner loop index, reset for each pass
 mov [swap], 0                    ; Reset swap flag for the current pass
 add cl,1                         ; Increment outer loop counter
 cmp cl,10                        ; Check if all passes completed (for N elements, N-1 passes)
 jz end                           ; If outer loop counter reaches N, exit

comparison:
mov ax, [data1+bx]               ; Get current element
cmp ax, [data1+bx+2]             ; Compare with next element (2 bytes for DW)
jbe noswap                       ; If (current <= next), no swap needed (for ascending)

; Swapping logic
mov dx, [data1+bx+2]             ; Store next element in DX
mov [data1+bx+2], ax             ; Move current element to next position
mov [data1+bx], dx               ; Move original next element to current position
mov [swap], 1                    ; Set swap flag to 1, indicating a swap occurred

noswap:
add bx, 2                        ; Move to the next pair (increment by 2 for words)
cmp bx, (SIZE of data1 in bytes - 2) ; Check if end of inner loop
jl comparison                    ; If not end, continue inner loop
jmp start                        ; Go to start of outer loop for next pass

end:
; Program termination
mov ax, 0x4c00
int 0x21
```

- **`data1 dw ...`**: Declares an array of words (2 bytes each).
    
- **`swap db 1`**: A byte-sized variable initialized to 1. It acts as a flag to indicate whether any swaps occurred during a pass. If `swap` remains 0 after a full pass, the array is sorted.
    
- **Outer Loop (`start` label)**: Manages the passes. It checks the `swap` flag. If no swaps happened in the previous pass (`cmp ch, 0`, then `jz end`), the sort is complete. Otherwise, it resets `swap` to 0 for the new pass and initializes `BX` for the inner loop.
    
- **Inner Loop (`comparison` label)**: Iterates through the array, comparing adjacent elements `[data1+bx]` and `[data1+bx+2]`.
    
- **`jbe noswap`**: For ascending sort, `jbe` (jump if below or equal) means if the current element is less than or equal to the next, no swap is needed, and it jumps to `noswap`. If the current element is _greater_, it falls through to the swap logic.
    
- **Swapping**: Involves using a temporary register (`DX`) to hold one value while the two values are exchanged in memory. After a swap, the `swap` flag is set to 1.
    
- **Loop Continuation**: `add bx, 2` moves to the next pair of elements. `cmp bx, (SIZE of data1 in bytes - 2)`checks if the inner loop has processed all necessary pairs. `jl comparison` continues the inner loop. `jmp start`initiates the next pass of the outer loop.
    
- **`jmp start`**: After each inner loop completion, control jumps back to `start` to begin the next pass.
    
- **`mov ax, 0x4c00; int 0x21`**: Standard DOS exit sequence.