# Week 4 - x86 Assembly




This week we reviewed how programs work at a low level and x86 assembly instructions.




---

## Questions

1.	What is the difference between machine code and assembly?

Machine code is the binary numbers the CPU understands as instructions assembly is the human readable interpretation of those instructions.

2.	If the ESP register is pointing to memory address 0x001270A4 and I execute a `push eax` instruction, what address will ESP now be pointing to?

ESP will decrement by 4 giving us 0x001270A4.

3.	What is a stack frame?

A stack frame is a section of the stack used by a certain function that keeps track of local memory and return locations.

4.	What would you find in a data section?

Static/global variables.

5.	What is the heap used for?

Dynamic memory which can be allocated and freed as needed

6.	What is in the code section of a program's virtual memory space?

The instructions for the CPU to execute

7.	What does the `inc` instruction do, and how many operands does it take?

It increments the given register and takes one operand, the register to increment.

8.	If I perform a `div` instruction, where would I find the remainder of the binary division (modulo)?

The remainder is stored in the EDX register

9.	How does `jz` decide whether to jump or not?

`jz` jumps if ZF = 1 which means it will jump if the zero flag is set.

10.	How does `jne` decide whether to jump or not?

`jne` is equivalent to `jnz` it jumps when the zero flag is not set and is usually used after `cmp` to jump if the values are not equal.

11.	What does a `mov` instruction do?

It puts the value of the first operand into the second 

12.	What does the `TF` flag do and why is it useful for debugging?

The `TF` flag is called the trap flag and when it is set the CPU will only run one instruction at a time. This helps with debugging because it allows the debugger to track every instruction executed instead of missing some if the CPU takes shortcuts to optimize.

13.	Why would an attacker want to control the EIP register inside a program they want to take control of?

If they control the EIP register they control what instructions are being executed and this can allow them to run whichever instructions they want even if they are not in the code section for that program. One example would be hiding instructions in the data section and then moving the EIP to execute them.

14.	What is the AL register and how does it relate to EAX?

The AL register is used to reference the lowest byte of EAX.

15.	What is the result of the instruction `xor eax, eax` and where is it stored?

The result is 0 and is stored in EAX so it effectively clears the EAX register.

I.	What does the `leave` instruction do in terms of registers to leave a stack frame?

It sets ESP to EBP and then pops EBP

II.	What `pop` instruction is `retn` equivalent to?

`retn` is equivalent to `pop eip`

III.	What is a stack overflow?

A stack overflow occurs when a program tries to use more stack memory than allocated to it.

IV.	What is a segmentation fault (a.k.a. a segfault)?

A segmentation fault occurs when a program tries to access a section of memory that it doesn’t have permission to access.

V.	What are the ESI and EDI registers for?

EDI is for the destination index and ESI is for the source index and they are used to make copying data from one place to another faster.


## Crackme

To solve this weeks crackme I first opened the program in Ghidra and put it into the code browser to see the deassembled and decompiled code. I started with the `main` function to see where the program begins and I found a function `validate_key` that takes the user input and evaluates it. In this function it checks whether or not the modulus of the input and 1223 is 0. So I assumed the key was any value that is divisible by 1223. I then tested this hypothesis by running the program and entering keys to test it.
