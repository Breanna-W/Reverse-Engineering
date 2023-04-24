# Week 9 - Shellcode




This week we learned about how shellcode works and created our own shellcode with the challenge to make it as small as possible and with no null bytes.




---

## Questions

1) A code block containing your assembly instructions for your shellcode

```
movq $0x3B, %RAX
xor %RCX, %RCX
pushq %RCX
movq %RSP, %RSI
movq %RSP, %RDX
movq $0x68732F6E69622F11, %RCX
shr $8, %RCX
pushq %RCX
movq %RSP, %RDI
syscall
```

2) A step-by-step explanation of your assembly code and how it sets up the system call

My assembly sets up 4 registers for the system call. First it sets `%RAX` to 0x3B which sets which system call to use in this case execve. Then it points both `%RSI` and `%RDX` to null which are the arguments and environment variables respectively. Lastly, it sets `%RDI` to the byte sequence equivalent to the shell script location which is the address of the code for the execve systemcall to run. Then once all registers are set it makes the call.

3) Report how many bytes total are in your assembly, and include the whole thing in ascii

There are 37 bytes in my assembly. Here they are:

4) Grad Students only, explain what you did to ensure there were no NULL bytes in your code

There are two places null shows up, first when setting `%RSI` and `%RDX` and second the null terminator for the byte sequence equivalent to the shell script location. For the first one I removed the null by using the xor operator to generate a null register by xoring it with itself instead of hardcoding the value. For the second one I simply added some extra non zero values to the end and then shifted them off using the shift instruction to create the null terminator needed.
