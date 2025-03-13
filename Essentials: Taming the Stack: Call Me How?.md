# FLAGS-UPB-cyberedu-2025
# #1: Essentials: Taming the Stack: Call Me How?
secarea@D1040H:~/cyber$ ./main
SSS{there_can_be_no_wrong_arguments}



## **Steps to Solve the Challenge**


### ** This was my Initial Issue: Permission Denied**
When trying to execute `get_flag.o`, we got:
```bash
secarea@D1040H:~/cyber$ ./get_flag.o
-bash: ./get_flag.o: Permission denied
```
Then:
```bash
-bash: ./get_flag.o: cannot execute binary file: Exec format error
```
This indicates we cannot execute the binary directly.

### ** Therefore you have to install required dependencies**
We need 32-bit support for proper compilation. Install with:
```bash
sudo apt install gcc-multilib libc6-dev-i386
```

### ** Now compile the program**
Clean and build the project:
```bash
make clean
make
```

### ** We have to convert `main.c` to UNIX Format**
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

### ** Then modify `main.c` to call the function as it should**
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

### ** Analyze the Binary for Security Checks**
Analyse:


 strings get_flag.o 
 
objdump -d get_flag.o 


## hexedit get_flag.o

### ** After we have to modify `main.c` to bypass the Check**
Change `main.c` to pass the correct arguments:
```c
#include <stdio.h>

void check_before_flag(int a, int b);  // Declare function correctly

int main() {
    check_before_flag(0xDEAD, 0xBEEF);  // Pass correct values
    return 0;
}
```

### ** Now we will have to change the Binary to Remove the Security Check**
Use `hexedit` to modify `get_flag.o`:
```bash
hexedit get_flag.o
```
Find the `jne` (`75 XX`) instruction near: "You Came to the Wrong Neighborhood!"

Change:
```bash
75 07 (JNE) → 90 90 (NOP NOP)
```
### Save and exit.

Then Recompile and Run the Exploit
```bash
make clean
make
./main
```

###  Capture the Flag**
```bash
SSS{there_can_be_no_wrong_arguments}
```




   
