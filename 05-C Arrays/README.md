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
An array's indexing are handled by this pattern: `rbp+rax*4+var_30`.

```C
#include <stdio.h>
#define RESPONSES_SIZE 40
#define FREQUENCY_SIZE 11

int main()
{
    int frequency[FREQUENCY_SIZE] = {0};
    int responses[RESPONSES_SIZE] = {1, 2, 6, 4, 8, 5, 9, 7, 8, 10, 1, 6, 3, 8, 6, 10, 3, 8, 2, 7, 6, 5, 7, 6, 8, 6, 7, 5, 6, 6, 5, 6, 7, 5, 6, 4, 8, 6, 8, 10};
    for(size_t answer=0; answer < RESPONSES_SIZE; answer++)
    {
        ++frequency[responses[answer]];
        //printf("%d", responses[answer]);
    }
    printf("%s%17s\n", "Rating", "Frequency");
    for (size_t rating = 1; rating < FREQUENCY_SIZE; ++rating)
    {
        printf("%6d%17d\n", rating, frequency[rating]);
    }
}
```
Output:
```bash
Rating        Frequency
     1                2
     2                2
     3                2
     4                2
     5                5
     6               11
     7                5
     8                7
     9                1
    10                3
```

> There is an important point. **No boundary checking!**

## Passing array and individual array elements to functions
```C
#include <stdio.h>
#define SIZE 5

void modifyArray(int b[], size_t size);
void modifyElement(int e);

int main()
{
    int a[SIZE] = {0, 1, 2, 3, 4};
    puts("Effecting of passing entire array by reference:\n\nThe values of the original array are:");
    for (size_t i = 0; i < SIZE; ++i)
    {
        printf("%3d", a[i]);
    }
    puts("");
    modifyArray(a, SIZE);
    puts("The values of the modified array are:");
    for (size_t i = 0; i < SIZE; ++i)
    {
        printf("%3d", a[i]);
    }
    printf("\n\n\nEffects of passing array element by value:\n\nThe value of a[3] is %d\n", a[3]);
    modifyElement(a[3]);
    printf("The value of a[3] is %d\n", a[3]);
}

void modifyArray(int b[], size_t size)
{
    for (size_t j = 0; j < size; ++j)
    {
        b[j] *= 2;
    }
}

void modifyElement(int e)
{
    printf("Value in modifyElement is %d\n", e *= 2);
}
```

Output:
```bash
Effecting of passing entire array by reference:

The values of the original array are:
  0  1  2  3  4
The values of the modified array are:
  0  2  4  6  8


Effects of passing array element by value:

The value of a[3] is 6
Value in modifyElement is 12
The value of a[3] is 6
```
