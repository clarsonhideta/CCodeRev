An array is a group of contiguous locations that all have the same type. There is an example of array:
```C
int c[12];
# c[0] c[1] c[2] c[3] c[4] c[5] c[6] c[7] c[8] c[9] c[10] c[11]
```
This array contains 12 elements, from 0 to 11. Ay array starts from zero!
## Example of defining C array
```C
#include <stdio.h>

int main()
{
    int n[5];

    for (size_t i = 0; i<5; i++)
    {
        n[i] = 0;
    }
    printf("%s%13s\n", "Element", "Value");
    for (size_t i = 0; i<5;i++)
    {
        printf("%7u%13d\n", i, n[i]);
    }
}
```
Output:
```bash
Element        Value
      0            0
      1            0
      2            0
      3            0
      4            0
```
And come to IDA:
```Assembly
; int __fastcall main(int argc, const char **argv, const char **envp)
public main
main            proc near               ; DATA XREF: _start+14↑o
var_30          = dword ptr -30h
var_10          = qword ptr -10h
var_8           = qword ptr -8

; __unwind {
	push    rbp
	mov     rbp, rsp
	sub     rsp, 30h
	mov     [rbp+var_8], 0
	jmp     short loc_115C
loc_114B:                               ; CODE XREF: main+28↓j
	mov     rax, [rbp+var_8]
	mov     [rbp+rax*4+var_30], 0
	add     [rbp+var_8], 1
loc_115C:                               ; CODE XREF: main+10↑j
	cmp     [rbp+var_8], 4
	jbe     short loc_114B
	lea     rdx, aValue     ; "Value"
	lea     rcx, aElement   ; "Element"
	lea     rax, format     ; "%s%13s\n"
	mov     rsi, rcx
	mov     rdi, rax        ; format
	mov     eax, 0
	call    _printf
	mov     [rbp+var_10], 0
	jmp     short loc_11BA
loc_1192:                               ; CODE XREF: main+86↓j
	mov     rax, [rbp+var_10]
	mov     edx, [rbp+rax*4+var_30]
	mov     rax, [rbp+var_10]
	lea     rcx, a7u13d     ; "%7u%13d\n"
	mov     rsi, rax
	mov     rdi, rcx        ; format
	mov     eax, 0
	call    _printf
	add     [rbp+var_10], 1
loc_11BA:                               ; CODE XREF: main+57↑j
	cmp     [rbp+var_10], 4
	jbe     short loc_1192
	mov     eax, 0
	leave
	retn
; } // starts at 1139
main            endp
_text           ends
```
This part code code, shows array iterations to set zero:
```Assembly
loc_114B:                               ; CODE XREF: main+28↓j
	mov     rax, [rbp+var_8]
	mov     [rbp+rax*4+var_30], 0
	add     [rbp+var_8], 1
```
Immediately, condition checks to reach 4 elements:
```Assembly
loc_115C:                               ; CODE XREF: main+10↑j
	cmp     [rbp+var_8], 4
	jbe     short loc_114B
```
Next loop is here:
```Assembly
mov     [rbp+var_10], 0
	jmp     short loc_11BA
loc_1192:                               ; CODE XREF: main+86↓j
	mov     rax, [rbp+var_10]
	mov     edx, [rbp+rax*4+var_30]
	mov     rax, [rbp+var_10]
	lea     rcx, a7u13d     ; "%7u%13d\n"
	mov     rsi, rax
	mov     rdi, rcx        ; format
	mov     eax, 0
	call    _printf
	add     [rbp+var_10], 1
loc_11BA:                               ; CODE XREF: main+57↑j
	cmp     [rbp+var_10], 4
	jbe     short loc_1192
```
An array's indexing are handled by this pattern: `rbp+rax*4+var_30`
