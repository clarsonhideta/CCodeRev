Pointers are variables whose values are **memory addresses**. Normally, a variable directly contains a specific value. A pointer, however, contains an **address** of a variable that contains a specific value. In the sense, a variable name **directly** references a value, and a pointer **indirectly** references a value.
## Declaring Pointers
Pointers must be defined before they can be used. The definition:
```C
int *countPtr, count;
```
We can say, `countPtr` is pointer to an object of type `int`. What is difference in assembly of code?
Compile code:
```C
#include <stdio.h>

int main()
{
    int *countPtr, count;
    printf("%p %d", countPtr, count);
}
```
Disassembly:
```Assembly
; int __fastcall main(int argc, const char **argv, const char **envp)
public main
main proc near

var_C= dword ptr -0Ch
var_8= qword ptr -8

; __unwind {
push    rbp
mov     rbp, rsp
sub     rsp, 10h
mov     edx, [rbp+var_C]
mov     rax, [rbp+var_8]
lea     rcx, format     ; "%p %d"
mov     rsi, rax
mov     rdi, rcx        ; format
mov     eax, 0
call    _printf
mov     eax, 0
leave
retn
; } // starts at 1139
main endp

_text ends
```
It shows that `var_C` is from 0x08 to 0x0C and it's size is 4 bytes. Because it's type is `int`, but var_8 is 8 bytes. Why? It is pointer of `int` value but it's length is 8 bytes!
Because it is pointer to `int` variable and is not as `int` type! Each pointer in x86-64 architecture has 8 bytes length! In x86, each pointer has 4 bytes length!
## Initializing and Assigning Values to Pointers
Pointers should be initialized when they're defined, or they can be assigned a value. A pointer may be initialized to `NULL`, 0 or an address. A pointer with value `NULL` points to *nothing*. `NULL` is symbolic constant defined in the `<stddef.h>` header or `<stdio.h>` header. 

# Pointer Operators
What are `&` and `*` in pointer operation?
## The Address (&) Operator
`&` is unary operator that returns the address of its operand.
```C
int y = 5;
int *yPtr;

yptr = &y;
```
It assigns the address of the variable `y` to pointer variable `yPtr`.

## The Indirection Operator
The unary `*` operator, commonly referred to as the indirection operator or dereferencing operator, returns the value of the object.
```C
printf("%d", *yPtr);
```
It prints the value of variable `y`: 5
## Example
```C
#include <stdio.h>

int main()
{
    int a = 7;
    int *aPtr = &a;

    printf("The address of a is %p\nThe value of aPtr is %p\n", &a, aPtr);
    printf("\nThe value of a is %d\nThe value of *aPtr is %d\n", a, *aPtr);
}
```
Output:
```bash
The address of a is *************
The value of aPtr is *************

The value of a is 7
The value of *aPtr is 7
```
And assembly:
```Assembly
; int __fastcall main(int argc, const char **argv, const char **envp)
public main
main proc near

var_C= dword ptr -0Ch
var_8= qword ptr -8

; __unwind {
push    rbp
mov     rbp, rsp
sub     rsp, 10h
mov     [rbp+var_C], 7
lea     rax, [rbp+var_C]
mov     [rbp+var_8], rax
mov     rdx, [rbp+var_8]
lea     rax, [rbp+var_C]
lea     rcx, format     ; "The address of a is %p\nThe value of aP"...
mov     rsi, rax
mov     rdi, rcx        ; format
mov     eax, 0
call    _printf
mov     rax, [rbp+var_8]
mov     edx, [rax]
mov     eax, [rbp+var_C]
lea     rcx, aTheValueOfAIsD ; "\nThe value of a is %d\nThe value of *a"...
mov     esi, eax
mov     rdi, rcx        ; format
mov     eax, 0
call    _printf
mov     eax, 0
leave
retn
; } // starts at 1139
main endp

_text ends
```
It's clear that pointer is saved at `rbp+var_8` and variable is saved at `rbp+var_C`:
1. `mov [rbp+var_C], 7` stores 7 in the local variable `var_C`. 
2. `lea rax, [rbp+var_C]` loads the address of `var_C` into `RAX`. 
3. Then `mov [rbp+var_8], rax`, stores the address in `var_8`.
`lea` has key role in dereferencing and pointer handling. 

# Passing Arguments to Functions by Reference
There are two ways to pass arguments to a function:
1. pass-by-value
2. pass-by-reference

However all arguments in C are passed by value. Functions often require the capability to modify variables in the caller or receive a pointer to a large data object to avoid the overhead of receiving the object by value (which incurs the time and memory overheads of making a copy of the object).
Pass-by-reference also can be used to enable a function to "return" multiple values to its caller by modifying variables in the caller.
## Pass-by-value
Variable will be transferred by it's content and will be updated by new content.
```C
#include <stdio.h>

int CubeByValue(int n);

int main()
{
    int number = 5;
    printf("The original value of number is %d", number);
    number = CubeByValue(number);
    printf("\nThe new value of number is %d\n", number);
}

int CubeByValue(int n)
{
    return n*n*n;
}
```
Output:
```bash
The original value of number is 5
The new value of number is 125
```
## Pass-by-reference
Variable will be updated by it's address in memory!
```C
#include <stdio.h>

void cubeByReference(int *nPtr);

int main()
{
    int number = 5;
    printf("The original value of number is %d", number);
    cubeByReference(&number);
    printf("\nThe new value of number is %d\n", number);
}

void cubeByReference(int *nPtr)
{
    *nPtr = *nPtr * *nPtr * *nPtr;
}
```
Output:
```bash
The original value of number is 5
The new value of number is 125
```
# Pointers and Arrays; Relationship
```C
#include <stdio.h>
#define ARRAY_SIZE 4

int main()
{
    int b[] = {10, 20, 30, 40};
    int *bPtr = b;
    
    puts("Array b printed with:\nArray index notation");
    for (size_t i = 0; i < ARRAY_SIZE; ++i)
    {
        printf("b[%u] = %d\n", i, b[i]);
    }

    puts("\nPointer/offset notation where\nthe pointer is the array name");
    for (size_t offset = 0; offset < ARRAY_SIZE; ++offset)
    {
        printf("*(b + %u) = %d\n", offset, *(b + offset));
    }

    puts("\nPointer index notation");
    for (size_t i = 0; i < ARRAY_SIZE; ++i)
    {
        printf("bPtr[%u] = %d\n", i, bPtr[i]);
    }

    puts("\nPointer/offset notation");
    for(size_t offset = 0; offset < ARRAY_SIZE; ++offset)
    {
        printf("*(bPtr + %u) = %d\n", offset, *(bPtr + offset));
    }
}
```
Output:
```bash
Array b printed with:
Array index notation
b[0] = 10
b[1] = 20
b[2] = 30
b[3] = 40

Pointer/offset notation where
the pointer is the array name
*(b + 0) = 10
*(b + 1) = 20
*(b + 2) = 30
*(b + 3) = 40

Pointer index notation
bPtr[0] = 10
bPtr[1] = 20
bPtr[2] = 30
bPtr[3] = 40

Pointer/offset notation
*(bPtr + 0) = 10
*(bPtr + 1) = 20
*(bPtr + 2) = 30
*(bPtr + 3) = 40
```

# Double Pointer in C
A double pointer stores the address of another pointer.
```C
int a = 10;
int *p = &a;
int **pp = &p;
// int ***ppp = &pp;   3D pointer!
```

```C
#include <stdio.h>

int main() {
    int a = 10;

    int *p = &a;
    int **pp = &p;

    printf("Value of a       = %d\n", a);
    printf("Value of *p      = %d\n", *p);
    printf("Value of **pp    = %d\n", **pp);

    printf("Address of a     = %p\n", (void*)&a);
    printf("Value of p (address of a) = %p\n", (void*)p);
    printf("Value of *pp (value of p) = %p\n", (void*)*pp);
    printf("Address of p     = %p\n", (void*)&p);
    printf("Value of pp (address of p) = %p\n", (void*)pp);

    return 0;
}
```

Output:
```bash
Value of a       = 10
Value of *p      = 10
Value of **pp    = 10
Address of a     = 0x7ffd9b674494
Value of p (address of a) = 0x7ffd9b674494
Value of *pp (value of p) = 0x7ffd9b674494
Address of p     = 0x7ffd9b674488
Value of pp (address of p) = 0x7ffd9b674488
```

Decompiled in IDA Pro:
```C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  int *v4; // [rsp+8h] [rbp-18h] BYREF
  int v5; // [rsp+14h] [rbp-Ch] BYREF
  const void **v6; // [rsp+18h] [rbp-8h]

  v5 = 10;
  v4 = &v5;
  v6 = (const void **)&v4;
  printf("Value of a       = %d\n", 10);
  printf("Value of *p      = %d\n", *v4);
  printf("Value of **pp    = %d\n", *(_DWORD *)*v6);
  printf("Address of a     = %p\n", &v5);
  printf("Value of p (address of a) = %p\n", v4);
  printf("Value of *pp (value of p) = %p\n", *v6);
  printf("Address of p     = %p\n", &v4);
  printf("Value of pp (address of p) = %p\n", v6);
  return 0;
}
```

# Pointers to Functions
It seems function pointer!
```C
#include <stdio.h>

void greet()
{
    printf("Hello Greet!\n");
}

int main()
{
    void (*funcPtr)();
    funcPtr = greet;

    puts("Calling directly!\n");
    greet();
    puts("Calling indirectly\n");
    funcPtr();
}
```
Output:
```bash
Calling directly!

Hello Greet!
Calling indirectly

Hello Greet!
```
Assembly:
```Assembly
public main
main proc near

var_8= qword ptr -8

; __unwind {
push    rbp
mov     rbp, rsp
sub     rsp, 10h
lea     rax, greet
mov     [rbp+var_8], rax
lea     rax, aCallingDirectl ; "Calling directly!\n"
mov     rdi, rax        ; s
call    _puts
call    greet
lea     rax, aCallingIndirec ; "Calling indirectly\n"
mov     rdi, rax        ; s
call    _puts
mov     rax, [rbp+var_8]
call    rax
mov     eax, 0
leave
retn
; } // starts at 114F
main endp

_text ends
```
Function pointer and it's calling appears here:
```Assembly
call    rax
```
