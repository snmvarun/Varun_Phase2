# 1. GDB Baby Step 1
Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
Disassemble this.

## Solution:
```bash
snmvarun@DESKTOP-16J7ALL:/mnt/d/Cryptonite/PicoCTF$ gdb debugger0_a
GNU gdb (Ubuntu 15.0.50.20240403-0ubuntu1) 15.0.50.20240403-git
Copyright (C) 2024 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from debugger0_a...
(No debugging symbols found in debugger0_a)
(gdb) info functions
All defined functions:

Non-debugging symbols:
0x0000000000001000  _init
0x0000000000001030  __cxa_finalize@plt
0x0000000000001040  _start
0x0000000000001070  deregister_tm_clones
0x00000000000010a0  register_tm_clones
0x00000000000010e0  __do_global_dtors_aux
0x0000000000001120  frame_dummy
0x0000000000001129  main
0x0000000000001140  __libc_csu_init
0x00000000000011b0  __libc_csu_fini
0x00000000000011b8  _fini
(gdb) disas main
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   %rbp
   0x000000000000112e <+5>:     mov    %rsp,%rbp
   0x0000000000001131 <+8>:     mov    %edi,-0x4(%rbp)
   0x0000000000001134 <+11>:    mov    %rsi,-0x10(%rbp)
   0x0000000000001138 <+15>:    mov    $0x86342,%eax
   0x000000000000113d <+20>:    pop    %rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
(gdb) p/d 0x86342
$1 = 549698
```

First, upon analysing, I understood that it is a ELF executable file, so I opened GDB as mentioned in the first hint to debug the file provided, I then use the info command with an argument of functions in the gdb interface to display all the functions available in this file, and as mentioned in the challenge to specifically look for a "main" function, to open this function and display its contents, I used the disas command to disassemble the function code and display the translated assembly code, after this I found the %eax register with a certain hex value as the content of the register, therefore I converted the hex value to decimal to find the flag and used the command p/d(p symbolizing print and /d to print the argument given in decimal) with an argument of 0x86342 to print out the decimal value to find the flag.

## Flag:
```
picoCTF{549698}
```

## Concepts learnt:
How to use the GDB interface and its commands such as info, to inspect functions and display its information and more, and disas, to disassemble code and convert machine code to human-readable assembly language representation, and a special feature of gdb to be able to print decimal value of a hex value 

## Resources:
[GDB Beginners Masterclass](https://youtu.be/MTkDTjdDP3c?si=CcHYHUS_q-GgbOJS)


# 2. ARMssembly 1
For what argument does this program print `win` with variables 81, 0 and 3? File: chall_1.S Flag format: picoCTF{XXXXXXXX} -> (hex, lowercase, no 0x, and 32 bits. ex. 5614267 would be picoCTF{0055aabb})

## Solution:
I opened the Chall_1.S file in VS code, to understand that the contents are written in assembly language, then after analysing the assembly code by converting into C code, essentially I understood the following about the function "func":
- it took a value, assume x, as argument
- first it left shift 81 by 0 which is basically 81 itself and stored it in a temp variable
- then it divided 81 by 3 which is 27 and stored it in temp
- and then subtracted x from temp 
- and then returned the temp variable value.
and the main function decoded that result is the value returned from function "func" with the argument x. and finally if the value returned stored in result is equal to 0 it will give "You win!" text else it puts "you lose!" instead. Therefore, the value of val should be equal to 27 and this number in hex is 1b or 0000001b which is the content of the flag.

```C
#include <stdio.h>
#include <stdlib.h>

int func(int x) {
    int a = 81;
    int b = 0;
    int c = 3;
    int temp  = a << b;
    temp = temp / c;
    temp = temp - x;
    return temp;
}

int main(int argc, char *argv[]) {
    int val = atoi(argv[1]);
    int result = func(val);
    if (result == 0) {
        puts("You win!");
    } else {
        puts("You Lose :(");
    }
    return 0;
}
```

## Flag:

```
picoCTF{0000001b}
```

## Concepts learnt:
How assembly language works and reading it aswell as converting it into C language and analyse.

## Notes:
The C code doesn't run directly, its analysis is more important than its functioning.

## Resources:
[Online Assembly to C Converter](https://www.codeconvert.ai/assembly-to-c-converter)
[Hex to Decimal](https://www.rapidtables.com/convert/number/hex-to-decimal.html)

# 3. vault-door-3
This vault uses for-loops and byte arrays. The source code for this vault is here: VaultDoor3.java

## Solution:

```java
import java.util.*;

class VaultDoor3 {
    public static void main(String args[]) {
        VaultDoor3 vaultDoor = new VaultDoor3();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	    System.out.println("Access granted.");
	} else {
	    System.out.println("Access denied!");
        }
    }

    // Our security monitoring team has noticed some intrusions on some of the
    // less secure doors. Dr. Evil has asked me specifically to build a stronger
    // vault door to protect his Doomsday plans. I just *know* this door will
    // keep all of those nosy agents out of our business. Mwa ha!
    //
    // -Minion #2671
    public boolean checkPassword(String password) {
        if (password.length() != 32) {
            return false;
        }
        char[] buffer = new char[32];
        int i;
        for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
        }
        String s = new String(buffer);
        System.out.println(buffer);
        return s.equals("jU5t_a_sna_3lpm18gb41_u_4_mfr340");
    }
}
```

Output 1:
```java
Enter vault password: picoCTF{jU5t_a_sna_3lpm18gb41_u_4_mfr340}
jU5t_a_s1mpl3_an4gr4m_4_u_1fb380
Access denied!
```

Output 2:
```java
Enter vault password: picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_1fb380}
jU5t_a_sna_3lpm18gb41_u_4_mfr340
Access granted.
```

First I open the java file to analyse the code given, upon inspection, I concluded that the program inputs the flag from the user and then converts it so that only the flag content inside, and it is stored inside the string variable "input", then the checkPassword function is called to check the input variable, now after analysing this function I understood that the for loops are for scrambling and unscrambling the flag content, and at the end there is a string given and which is equated to check if the actual flag is put so that when it is scrambled, it equates and gives a true value so that Access is granted. Moreover, I additionally put System.out.println(buffer); before returning to provide the unscrambled flag which is the correct flag content. So, I run the program for the first time while inputting the scrambled flag given in the equals builtin string function, and the sopln(buffer) prints out the actual flag after unscrabling the scrambled flag. Now I copied that correct flag and passed it as input after running the program again and Access was granted proving that this is the correct flag.

## Flag:
```
picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_1fb380}
```

## Concepts learnt:
How to reverse engineer to decode an scrambled string.

## Notes:
- The code scrambles the flag and unscrambles it both.

## Resources:

- None

