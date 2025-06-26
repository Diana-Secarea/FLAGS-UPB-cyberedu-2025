### Name: Matryoshka
Description
There's something more in the executable, isn't there?
Hint 1: It's and ELF inside and ELF.
Vulnerability
There is a global variable storing an ELF file. The participant will retrieve it, find out what the XOR key is by matching the ELF header, extract the ELF file and run it. The executable is stripped to make things a little bit difficult for the participant.
Exploit
Script in ./sol/exploit.py

cel mai simplu cu binwalk cu 'binwalk -D='.*' matryoshka' si se executa executabilul 2020. Se putea face si cu ghidra dar era mai complicat.
