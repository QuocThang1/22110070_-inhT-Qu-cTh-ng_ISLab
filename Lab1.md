## Lab1
**#bof1.c**
-stack frame:
![alt text](image.png)
![alt text](image-1.png)
-In bof1.c we can see the gets(array) can receive without limit so it can exceeding the limit of array.

![alt text](image-2.png)
-Use gcc -g bof1.c -o bof1.out -fno-stack-protector -mpreferred-stack-boundary=2,This command compiles the bof1.c program into the executable bof1.out, adds debugging information, disables buffer overflow protection, and adjusts the stack alignment to facilitate buffer overflow attacks.

![alt text](image-3.png)
-We can find address of secretFunc:0804846b

![alt text](image-4.png)
-First we need to 204 byte by 204 ‘a’.
-Next we insert an address of secretFunc to override the return address of the vuln() function.When the vuln() function completes, instead of returning to main(), the program jumps to execute the secretFunc() function.
- Printing out the message "Congratulation!" =>Success full attack

**#bof2.c**
-Stack frame:
![alt text](image-5.png)
![alt text](image-6.png)
-The buf[] can receive 40 elements but we can enter 44 elements by fgets(buf,45,stdin).We rely on 4 last elements to modify the check.

![alt text](image-7.png)
-Run the bof2.c

![alt text](image-8.png)
-Fill 40 ‘a’ to buf[] and last 4 for check:0xdeadbeef
-Print “Yeah! You win!”=>Attack sucessfully

**#bof3.c**
-Stack frame:
![alt text](image-9.png)
![](image-10.png)
-Run bof3.c

![alt text](image-11.png)
-Find the address of shell function:0804845b

![alt text](image-12.png)
-After fill 128 byte, fill the address of shell function into func.
=>Sucessfully