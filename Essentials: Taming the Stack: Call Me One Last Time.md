# Essentials: Taming the Stack: Call Me One Last Time
secarea@D1040H:~/cyber$ ./main

## SSS{i_told_you_strings_are_perilous}



## First I modified the MakeFile such that:
```

OBJS = get_flag.o main.o

CFLAGS = -Wno-implicit-function-declaration -m32

LDFLAGS = -m32


all: main

main: $(OBJS)

	gcc -o $@ $^ -no-pie $(LDFLAGS)
 

main.o: main.c

	gcc -c $(CFLAGS) main.c -o main.o



clean:

	rm -f main all.zip main.o

pack:

	zip all.zip Makefile run.sh main.c get_flag.o
 

.PHONY: all clean pack

```

## If you run the file you will get : When will you learn?
First, we analyze the binary to locate the check_before_flag() function and its security check.

objdump -d main | grep -A 10 "check_before_flag"

Key Findings

8049315:       75 07                   jne    804931e <check_before_flag+0x3b>
8049317:       e8 9a fe ff ff          call   80491b6 <get_flag>

75 07 → Conditional jump (jne)

We will replace jne (75 07) with jmp (EB 07) so execution always jumps to get_flag().
## Then Patch jne to jmp

## Modify the binary using a hex editor or sed. -> Using hexedit

Open the binary:

hexedit main

Press CTRL+S and search for:

75 07

Modify 75 → EB.
## Of course it did not work so we modified the Call to Directly Execute get_flag()
objdump -d main | grep "call"
Instead of calling check_before_flag(), we will redirect it to get_flag().

get_flag() is located at 0x080491b6.

## Calculate the Correct Offset : 
OFFSET = 0x080491b6 - (0x08049349 + 5)
OFFSET = 0x080491b6 - 0x0804934e
OFFSET = -0x198
## Which will be in little endian:  e8 68 fe ff ff  
## hexedit main
## we will search for e8 96 ff  ff ff and change it to  e8 68 fe ff ff 
And save it.
## ./main

 8049364:       e8 87 fd ff ff          call   80490f0 <__x86.get_pc_thunk.bx>
secarea@D1040H:~/cyber$ ./main

SSS{i_told_you_strings_are_perilous}

secarea@D1040H:~/cyber$
