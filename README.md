# FLAGS-UPB-cyberedu-2025
# #1: Essentials: Taming the Stack: Call Me How?
secarea@D1040H:~/cyber$ ./main
SSS{there_can_be_no_wrong_arguments}

```

## **Steps to Solve the Challenge**


### **1. Initial Issue: Permission Denied**
When trying to execute `get_flag.o`, we got:
```bash
secarea@D1040H:~/cyber$ ./get_flag.o
-bash: ./get_flag.o: Permission denied
```
Then:
```bash
-bash: ./get_flag.o: cannot execute binary file: Exec format error
```
This indicates we **cannot execute** the binary directly.

### **2. Install Required Dependencies**
We need 32-bit support for proper compilation. Install with:
```bash
sudo apt install gcc-multilib libc6-dev-i386
```

### **3. Compile the Program**
Clean and build the project:
```bash
make clean
make
```

### **4. Convert `main.c` to UNIX Format**
If you encounter:
```bash
./main.c: line 3: $'\r': command not found
./main.c: line 4: syntax error near unexpected token '('
```
Convert the file:
```bash
dos2unix main.c
```
Then recompile:
```bash
make clean
make
```

### **5. Modify `main.c` to Call the Function Properly**
Edit `main.c`:
```c
#include <stdio.h>
#include <stdlib.h>

void check_before_flag();

int main(void) 
{
    printf("Calling check_before_flag()...\n");
    check_before_flag(); // Call the function
    return 0;
}
```
Then recompile:
```bash
make clean
make
```

### **6. Analyze the Binary for Security Checks**
```bash
strings get_flag.o 
objdump -d get_flag.o
```

### **7. Modify `main.c` to Bypass the Check**
Change `main.c` to pass the correct arguments:
```c
#include <stdio.h>

void check_before_flag(int a, int b);  // Declare function correctly

int main() {
    check_before_flag(0xDEAD, 0xBEEF);  // Pass correct values
    return 0;
}
```

### **8. Patch the Binary to Remove the Security Check**
Use `hexedit` to modify `get_flag.o`:
```bash
hexedit get_flag.o
```
Find the `jne` (`75 XX`) instruction near:
```bash
"You Came to the Wrong Neighborhood!"
```
Change:
```bash
75 07 (JNE) → 90 90 (NOP NOP)
```
Save and exit.

### **9. Recompile and Run the Exploit**
```bash
make clean
make
./main
```

### **10. Capture the Flag**
```bash
SSS{there_can_be_no_wrong_arguments}


We have permission denied : secarea@D1040H:~/cyber$ ./get_flag.o

-bash: ./get_flag.o: Permission denied   -> -bash: ./get_flag.o: cannot execute binary file: Exec format error  -> we can not execute the binary file 

Must install : sudo ap install gcc-multilib libc6-dev-i386

Compile the program : make clean THEN make

make clean
make
chmod +x main.c
We need to modify the script:
   
#include <stdio.h>

#include <stdlib.h>

void check_before_flag();

int main(void) 

{

    printf("Calling check_before_flag()...\n);
    
    check_before_flag(); // Call the function

    return 0;
}

   
BUT secarea@D1040H:~/cyber$ ./main.c
./main.c: line 3: $'\r': command not found
./main.c: line 4: syntax error near unexpected token `('
'/main.c: line 4: `void check_before_flag();  -> we need to Convert main.c to UNIX Format -> dos2unix main.c

then make clean , make , ./main.c
Analyse:
# strings get_flag.o 
# objdump -d get_flag.o 
Modify main.o:
#include <stdio.h>

void check_before_flag(int a, int b);  // Declare function correctly

int main() {
    check_before_flag(0xDEAD, 0xBEEF);  // Pass correct values
    return 0;
}
Then,
## hexedit get_flag.o
Find the jne (75 XX)  instruction near "You Came to the Wrong Neighborhood!" and change it:
hexedit get_flag.o
## Press Ctrl+S and search for: 75 07
## Change 75 07 (JNE) → 90 90 (NOP NOP), which removes the conditional check.
Then, we will recompile : 
make clean
make
./main

secarea@D1040H:~/cyber$ make clean
rm -f main all.zip main.o
secarea@D1040H:~/cyber$ make
cc -Wno-implicit-function-declaration -m32   -c -o main.o main.c
gcc -o main get_flag.o main.o -no-pie -m32
secarea@D1040H:~/cyber$ ./main
SSS{there_can_be_no_wrong_arguments}
secarea@D1040H:~/cyber$


   
