###  I'll provide a simple example of an assembly program for the individual assignment that uses conditional branching and loops to solve a mathematical problem. We'll create a program that calculates the sum of the first N natural numbers, where N is taken as input.

``` shell assembly
section .data
    prompt db 'Enter a number: $'
    sum_msg db 'Sum of first N natural numbers: $'
    nl db 10

section .bss
    n resb 1     ; Input: N
    sum resb 2    ; Variable to store the sum

section .text
    global _start

_start:
    ; Display prompt and read input
    mov eax, 4       ; syscall: sys_write
    mov ebx, 1       ; file descriptor: stdout
    mov ecx, prompt  ; pointer to the message
    mov edx, 17      ; message length
    int 0x80         ; invoke syscall

    mov eax, 3       ; syscall: sys_read
    mov ebx, 0       ; file descriptor: stdin
    mov ecx, n       ; buffer to store input
    mov edx, 1       ; number of bytes to read
    int 0x80         ; invoke syscall

    ; Convert ASCII to integer
    movzx eax, byte [n]
    sub eax, '0'

    ; Initialize sum to 0
    mov word [sum], 0

    ; Loop to calculate the sum of first N natural numbers
    mov ecx, eax     ; ECX will act as the counter
    mov eax, 0       ; Clear EAX for summing

sum_loop:
    add eax, ecx     ; Add current value of ECX to EAX (sum)
    loop sum_loop     ; Repeat until ECX becomes 0

    ; Display the sum
    mov eax, 4       ; syscall: sys_write
    mov ebx, 1       ; file descriptor: stdout
    mov ecx, sum_msg ; pointer to the message
    mov edx, 32      ; message length
    int 0x80         ; invoke syscall

    ; Convert sum (integer) to ASCII for display
    movzx eax, word [sum]
    add al, '0'
    mov [sum], ax    ; Store the ASCII representation of sum

    ; Display the calculated sum
    mov eax, 4       ; syscall: sys_write
    mov ebx, 1       ; file descriptor: stdout
    mov ecx, sum     ; pointer to the sum
    mov edx, 2       ; message length (2 bytes for the sum)
    int 0x80         ; invoke syscall

    ; Exit the program
    mov eax, 1       ; syscall: sys_exit
    xor ebx, ebx     ; exit code 0
    int 0x80         ; invoke syscall
```

This program calculates the sum of the first N natural numbers by taking N as input from the user and using a loop to perform the summation. It utilizes conditional branching (looping) to achieve this.

For the group assignment, I'll provide a flowchart and assembly program to read 10 consecutive numbers from RAM, check if each number is a multiple of 5, and then store them accordingly as required. Let's start with the flowchart:

Flowchart for the program to read 10 consecutive numbers and handle multiples of 5:

``` shell
Start
|
v
Initialize counter (i = 0)
|
v
If i < 10
|
v
Read a number from RAM at address 47H + i
|
v
Check if the number is a multiple of 5
|
v
If yes, move the number to the stack
|
v
If no, round the number to the nearest multiple of 5 and move to the stack
|
v
Increment counter (i)
|
v
Loop back to "If i < 10"
|
v
End
```

Now let's create an assembly program based on this flowchart:

``` sh 
section .data
    msg db 'Number: $'
    stack_size equ 10   ; Stack size for 10 numbers

section .bss
    stack resb stack_size  ; Stack to store the numbers
    i resb 1              ; Counter variable i

section .text
    global _start

_start:
    ; Initialize counter i to 0
    mov byte [i], 0

    ; Loop to read 10 consecutive numbers
read_loop:
    ; Check if i < 10
    cmp byte [i], 10
    jge end_program  ; If i >= 10, exit the loop

    ; Read a number from RAM (address 47H + i)
    movzx eax, byte [0x47 + i]
    
    ; Display "Number: "
    mov eax, 4       ; syscall: sys_write
    mov ebx, 1       ; file descriptor: stdout
    mov ecx, msg     ; pointer to the message
    mov edx, 8       ; message length
    int 0x80         ; invoke syscall

    ; Check if the number is a multiple of 5
    mov edx, 5
    mov eax, 0
    div edx           ; Divide eax by edx (5)
    test edx, edx     ; Check the remainder
    jz multiple_of_5  ; Jump if zero (multiple of 5)

    ; Round the number to the nearest multiple of 5
    imul eax, eax, 5  ; Multiply eax by 5
    add eax, 5        ; Add 5
    shr eax, 3        ; Divide by 8 (equivalent to rounding to nearest multiple of 5)

multiple_of_5:
    ; Move the number to the stack
    movzx ebx, byte [i]   ; Load i into ebx
    add ebx, 0x50         ; Calculate the stack address (50H + i)
    movzx ecx, ax         ; Move the rounded number to ecx
    mov [ebx], cl         ; Store the rounded number in the stack

    ; Increment counter i
    inc byte [i]

    ; Repeat the loop
    jmp read_loop

end_program:
    ; Exit the program
    mov eax, 1       ; syscall: sys_exit
    xor ebx, ebx     ; exit code 0
    int 0x80         ; invoke syscall
```

### In this program, we read 10 consecutive numbers from RAM starting at address 47H, check if each number is a multiple of 5, and then either move the number to the stack or round it to the nearest multiple of 5 before moving to the stack. The stack is used to store these numbers. The program utilizes loops and conditional branching to achieve the desired functionality.

Sure, here's a simple example of assembly code in a Git README format:

```markdown
# Assembly Language Example

This is a simple assembly language program that displays "Hello, World!" using system calls.

## Code

```assembly
section .data
    hello db 'Hello, World!', 10 ; Message to be printed, with newline character

section .text
    global _start

_start:
    ; Write the message to stdout
    mov eax, 4       ; syscall: sys_write
    mov ebx, 1       ; file descriptor: stdout
    mov ecx, hello   ; pointer to the message
    mov edx, 13      ; message length
    int 0x80         ; invoke syscall

    ; Exit the program
    mov eax, 1       ; syscall: sys_exit
    xor ebx, ebx     ; exit code 0
    int 0x80         ; invoke syscall
```

## Explanation

## This program uses x86 assembly language and Linux syscalls to print "Hello, World!" to the standard output. The `mov` instruction is used to move values into registers, and `int 0x80` is used to invoke syscalls.

1. **Data Section**:
   - `hello` stores the message "Hello, World!" along with a newline character.

2. **Text Section**:
   - `global _start` declares the entry point of the program.
   - `mov` instructions set up the syscall parameters: file descriptor (stdout), pointer to the message, message length.
   - `int 0x80` invokes the sys_write syscall to print the message.
   - `mov` instructions and `int 0x80` are used to exit the program.


## How to Run

1. Assemble the code using an assembler like NASM:
   ```sh
   nasm -f elf program.asm -o program.o
   ```

2. Link the object file to create an executable:
   ```sh
   ld -m elf_i386 program.o -o program
   ```

3. Run the executable:
   ```sh
   ./program
   ```

This will print "Hello, World!" to the terminal.


### This example demonstrates a simple "Hello, World!" program in assembly language using the `nasm` assembler and `ld` linker. It includes explanations and instructions on how to assemble, link, and run the program.

# Assembly Language Example - Factorial Calculation

This assembly program calculates the factorial of a given number using a loop and conditional branching.

## Code

```sh
section .data
    n db 5          ; Input: N for factorial calculation

section .text
    global _start

_start:
    ; Load N into a register
    movzx ecx, byte [n]

    ; Initialize result to 1 (factorial of 0 is 1)
    mov eax, 1

    ; Check if N is 0 (edge case)
    cmp ecx, 0
    je end_calculation

    ; Loop to calculate factorial
    ; for(i = N; i > 1; i--)
    calculation_loop:
        ; Multiply result by i
        imul eax, eax, ecx

        ; Decrement i
        dec ecx

        ; Check if i > 1
        cmp ecx, 1
        jg calculation_loop

    ; End of calculation
    end_calculation:

    ; Display the result (factorial)
    ; You can add code to display the result here

    ; Exit the program
    mov eax, 1       ; syscall: sys_exit
    xor ebx, ebx     ; exit code 0
    int 0x80         ; invoke syscall
