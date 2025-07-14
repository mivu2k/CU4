## **1. Introduction to Subroutines**

Subroutines (also called **procedures** or **functions**) in assembly language allow code reuse and modular programming, similar to functions in high-level languages like C/C++.

### **Why Use Subroutines?**

- **Modularity**: Break down complex programs into smaller, manageable blocks.
    
- **Reusability**: Avoid rewriting the same code multiple times.
    
- **Maintainability**: Easier debugging and updating.
    

### **Key Instructions**

|Instruction|Description|
|---|---|
|`CALL`|Jumps to a subroutine and pushes the return address onto the stack.|
|`RET`|Returns from a subroutine by popping the return address from the stack.|

---

## **2. Basic Subroutine Structure**

A simple subroutine in x86 assembly:


```assembly

[org 0x100]    ; Set origin for .COM files (DOS executable)

jmp start      ; Skip subroutine definition

; --- Subroutine Definition ---
mySubroutine:
    mov ax, 8   ; Example operation
    ret         ; Return to caller

; --- Main Program ---
start:
    call mySubroutine  ; Call the subroutine

    ; Terminate program (DOS interrupt)
    mov ax, 0x4C00
    int 0x21
```


### **How It Works**

1. **`CALL mySubroutine`**
    
    - Pushes the **return address** (next instruction) onto the stack.
        
    - Jumps to `mySubroutine`.
        
2. **`RET`**
    
    - Pops the return address from the stack.
        
    - Returns execution to the instruction after `CALL`.
        

---

## **3. Passing Parameters & Returning Values**

### **Method 1: Using Registers (Simplest)**

```
```assembly

addNumbers:
    add ax, bx  ; AX = AX + BX
    ret

start:
    mov ax, 5   ; First parameter in AX
    mov bx, 3   ; Second parameter in BX
    call addNumbers
    ; Result is in AX
```


### **Method 2: Using the Stack (More Flexible)**

```assembly
addNumbers:
    push bp
    mov bp, sp
    mov ax, [bp+4]  ; First argument
    add ax, [bp+6]  ; Second argument
    pop bp
    ret 4           ; Clean up stack (remove 2 words)

start:
    push 3          ; Second argument
    push 5          ; First argument
    call addNumbers
    ; Result is in AX
```
---
## **4. Preserving Registers**

Subroutines should **save and restore** registers they modify (except those used for return values).

```assembly

mySubroutine:
    push bx     ; Save BX
    push cx     ; Save CX
    ; ... (subroutine code) ...
    pop cx      ; Restore CX
    pop bx      ; Restore BX
    ret
```
---

## **5. Practical Examples**

### **Example 1: Zeroing Registers**

```assembly

clearRegisters:
    xor ax, ax  ; Faster than MOV AX, 0
    xor bx, bx
    xor cx, cx
    xor dx, dx
    ret
```
### **Example 2: Swapping Two Variables**

```assembly

num1 db 5
num2 db 6

swap:
    mov al, [num1]
    mov bl, [num2]
    mov [num1], bl
    mov [num2], al
    ret
```