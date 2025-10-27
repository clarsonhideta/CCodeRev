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
