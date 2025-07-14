
### 1. Overview of Addressing Modes

Addressing modes define the allowed approaches to access data from RAM and registers. In Assembly, variables are typically declared at the end of the program.

The common addressing modes include:

1. **Immediate Mode**: The operand is part of the instruction itself. This is fast as no memory reference is needed to fetch the data, but it has a limited range.
    
    - Example: 
        
        `ADD 5`
        
2. **Direct Access Mode**: The address field in the instruction directly contains the address of the operand. The effective address (EA) is the address field (A).
    
    - Example: 
        
        `mov ax, [0x1234]` (word move) 
        
    - Example: 
        
        `mov ah, [0x1234]` (byte move) 
        
    - Default segment for direct access mode is 
        
        `DS`.
        
3. **Base Register Indirect Mode**: The operand's memory cell is pointed to by the contents of a base register (e.g., `BX`, `BP`). We can put brackets around the base registers to indicate indirect access.
    
    - Example: 
        
        `mov bx, var1` (moves address of var1 to BX) then `mov ax, [bx]` (moves value from address in BX to AX).
        
    - Default segment for `BX`, `SI`, `DI` is `DS`. For 
        
        `BP`, the default segment is `SS`.
        
4. **Base Register Indirect + Offset Mode**: 
    
5. **Base Register + Index Mode**: 
    
6. **Index Register Indirect Mode**: The operand is in the memory cell pointed to by the contents of an index register (e.g., `SI`, `DI`). The effective address (EA) is 
    
    `(R)`.
    
    - Example: 
        
        `mov ax, [si]`
        
7. **Index Register Indirect + Offset Mode**: 
    
8. **Base + Index + Offset Mode**: 
    

### 2. Variable and Array Declaration

Variables and arrays are fundamental for storing data in memory.

- **Declaring Variables**: In Assembly, variables are declared at the end of the program.
    
- **Declaring Arrays**: To create an array in Assembly, `DW` (Define Word) can be used for 2-byte elements, or `DB`(Define Byte) for 1-byte elements.
    
    - Example: 
        
        `num dw 5,10,15,20` would declare an array `num` starting at address `100` (for example), with `5` at `100`, `10` at `102`, `15` at `104`, and `20` at `106`.
        
    - Accessing array elements using base register indirect mode: 
        
        `add ax, [bx]` then `add ax, [bx+2]`, `add ax, [bx+4]`, etc. to access subsequent elements (assuming `BX`holds the base address and elements are 2 bytes).
        

### 3. Illegal and Ambiguous Instructions

Certain instructions are not allowed in Assembly Language due to various reasons, primarily concerning data size consistency and memory access limitations.

#### 3.1. 

Size Mismatch Errors 

- **Not Allowed**:
    
    - Moving 16-bit data into an 8-bit register (e.g., 
        
        `mov al, ax`) results in 8 bits of data being lost.
        
    - Moving 8-bit data into a 16-bit register (e.g., 
        
        `mov ax, bl`) results in 8 bits being used and 8 bits being wasted.
        
    - Moving data between registers of different implicit sizes (e.g., 
        
        `mov bh, cx`, `mov bx, ch`, `mov cl, dx`, `mov cx, dl`).
        
- **Allowed**:
    
    - Moving 8-bit data to 8-bit registers (e.g., 
        
        `mov al, ah`).
        
    - Moving 16-bit data to 16-bit registers (e.g., 
        
        `mov ax, bx`).
        

#### 3.2. 

Memory to Memory Moves 

- Directly moving data from one memory location to another (e.g., 
    
    `mov variable1, variable2`) is generally not allowed because it is a slow operation, as it requires generating two addresses before data can be moved.
    
- It is faster to move data from memory to a register (
    
    `mov variable1, ax`), and fastest to move data between registers (`mov ax, bx`) as no address generation is required.
    

#### 3.3. 

Ambiguous Moves 

- An instruction like 
    
    `mov [num1], 5` is ambiguous because the assembler doesn't know if `5` should be treated as a byte or a word.
    
- To resolve ambiguity, explicitly specify the size:
    
    - `mov byte [num1], 5` (moves 5 as a byte) 
        
    - `mov word [num1], 5` (moves 5 as a word) 
        

### 4. Segment Association and Segment Override Prefix

- **Default Segment Associations**:
    
    - `CS` (Code Segment) is tied to `IP` (Instruction Pointer).
        
    - `SS` (Stack Segment) is tied to `SP` (Stack Pointer) and `BP` (Base Pointer).
        
    - `DS` (Data Segment) is the default segment for `SI`, `DI`, and `BX` in register indirect memory access.
        
- **Segment Override Prefix**: This prefix is used to override the default segment associated with a register for a specific instruction.
    
    - Example: 
        
        `mov ax, [cs:bx]` tells the processor to use `BX` with the `CS` segment instead of its default `DS` for that instruction.
        

### 5. Address Wraparound

Address wraparound occurs when a calculated address exceeds the memory limits, causing the address to "wrap around" to the beginning of the memory space.

- **Within a Single Segment**: If an effective address calculation (e.g., `BX + offset`) generates a carry, this carry is dropped. This means that if you try to access memory beyond the segment limit, the address will wrap around to the first cell within that segment.
    
    - Example: If `BX = 9100`, `DS = 1500`, and the access is `[bx + 0x7000]`, the effective address `9100 + 7000 = 10100`. The carry is dropped, resulting in an effective address of `0100`. The physical address would then be 
        
        `15100`.
        
- **Inside the Whole Physical Memory**: A similar wraparound can occur during the physical address calculation. If the physical address generated is a 21-bit answer, but the address bus is only 20 bits wide, the carry is dropped. This causes the physical memory to wrap around at its very top.
    

This phenomenon can be visualized like a circle, where reaching the end brings you back to the beginning.