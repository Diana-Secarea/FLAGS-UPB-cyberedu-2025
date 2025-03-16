# Fibonacci
We check ./fibonacci in gdb ./fibonacii and realise that the execution of the program is not in the main, but in run. So we disassemble run and we discover the following: gets runs without a limitation of input. This will be the vulnerability.

The vulnerable buffer is: 
0x00000000004006cd <+23>:    lea    rax,[rbp-0x20]
0x00000000004006d1 <+27>:    mov    rdi,rax

 We have to overwrite return address with a buffer bigger than 23 bytes.
 Buffer overflow must be 32 + 8 = 40 bytes.

 Dupa gasim gadget-ul pop rdi : 
 secarea@D1040H:~/cyber$ ROPgadget --binary ./fibonacci | grep "pop rdi"
0x00000000004007b3 : pop rdi ; ret
secarea@D1040H:~/cyber$
