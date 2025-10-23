# Environment
Our environment for compiling and executing code:
- **Ubuntu** (OS)
- **GCC** (Compiler)
- **VSCode** (IDE)
- **IDA Pro** (Disassembler)

We begin by considering a simple C code. Printing line of text:
```C
#include <stdio.h>

int main()
{
    printf("Hi Clarson!");
    return 0;
}
```
It can be compiled with:
```bash
gcc SimpleLineCode.c -o SimpleLineCode
```
When we run it:
```bash
>>> ./SimpleLineCode
Hi Clarson!
```

So it's very well, but is very high-level code. What happens under it? "IDA Pro" comes as a superstar! Say machine language for us:
```Assembly
; Attributes: bp-based frame

; int __fastcall main(int argc, const char **argv, const char **envp)
public main
main proc near
; __unwind {
push    rbp
mov     rbp, rsp
lea     rax, format     ; "Hi Clarson!"
mov     rdi, rax        ; format
mov     eax, 0
call    _printf
mov     eax, 0
pop     rbp
retn
; } 
main endp

_text ends
```
It's `main` function that always exists in each executable program written in C. 
This part of function is `prologue` and makes function ready for execution (search for C calling convention):
```Assembly
push    rbp
mov     rbp, rsp
```
Then, `_printf` will be use to print our string. It's very clear that `rdi` is input register for `_printf` function. **Format string** is in it!
At the end, `epilogue` comes up. It's like `prologue` but it ends our function with clearing stack, and say goodbye! (search for stack in virtual memory)

Congratulations! Here is new "C programmer" and "reverse engineer" for our planet!
