#### [GWCTF 2019]babyvm

![](https://gitee.com/Emtanling/image/raw/master/img/20210810090101.png)

只有三个函数。

sub_CD1函数

```c++
unsigned __int64 __fastcall sub_CD1(__int64 a1)
{
  unsigned __int64 v2; // [rsp+18h] [rbp-8h]

  v2 = __readfsqword(0x28u);
  *a1 = 0;
  *(a1 + 4) = 0x12;
  *(a1 + 8) = 0;
  *(a1 + 12) = 0;
  *(a1 + 16) = &unk_202060;
  *(a1 + 24) = 0xF1;
  *(a1 + 32) = sub_B5F;
  *(a1 + 40) = 0xF2;
  *(a1 + 48) = sub_A64;
  *(a1 + 56) = 0xF5;
  *(a1 + 64) = sub_AC5;
  *(a1 + 72) = 0xF4;
  *(a1 + 80) = sub_956;
  *(a1 + 88) = 0xF7;
  *(a1 + 96) = sub_A08;
  *(a1 + 104) = 0xF8;
  *(a1 + 112) = sub_8F0;
  *(a1 + 120) = 0xF6;
  *(a1 + 128) = sub_99C;
  qword_2022A8 = malloc(0x512uLL);
  memset(qword_2022A8, 0, 0x512uLL);
  return __readfsqword(0x28u) ^ v2;
}
```

`unk_202060`为值，应该是指令顺序。

```c++
unsigned __int64 __fastcall sub_B5F(__int64 a1)
{
  int *v2; // [rsp+28h] [rbp-18h]
  unsigned __int64 v3; // [rsp+38h] [rbp-8h]

  v3 = __readfsqword(0x28u);
  v2 = (*(a1 + 16) + 2LL);
  switch ( *(*(a1 + 16) + 1LL) )
  {
    case 0xE1:
      *a1 = *(qword_2022A8 + *v2);
      break;
    case 0xE2:
      *(a1 + 4) = *(qword_2022A8 + *v2);
      break;
    case 0xE3:
      *(a1 + 8) = *(qword_2022A8 + *v2);
      break;
    case 0xE4:
      *(qword_2022A8 + *v2) = *a1;
      break;
    case 0xE5:
      *(a1 + 12) = *(qword_2022A8 + *v2);
      break;
    case 0xE7:
      *(qword_2022A8 + *v2) = *(a1 + 4);
      break;
    default:
      break;
  }
  *(a1 + 16) += 6LL;
  return __readfsqword(0x28u) ^ v3;
}
```

`sub_B5F`函数，通过对内存地址qword_2022A8操作，将内存值放入a1之中，应该为mov指令。

```cpp
unsigned __int64 __fastcall sub_A64(__int64 a1)
{
  unsigned __int64 v2; // [rsp+18h] [rbp-8h]

  v2 = __readfsqword(0x28u);
  *a1 ^= *(a1 + 4);
  ++*(a1 + 16);
  return __readfsqword(0x28u) ^ v2;
}
```

`sub_A64`函数，进行了异或操作，应该是xor指令。

```cpp
unsigned __int64 __fastcall sub_AC5(__int64 a1)
{
  const char *buf; // [rsp+10h] [rbp-10h]
  unsigned __int64 v3; // [rsp+18h] [rbp-8h]

  v3 = __readfsqword(0x28u);
  buf = qword_2022A8;
  read(0, qword_2022A8, 0x20uLL);
  dword_2022A4 = strlen(buf);
  if ( dword_2022A4 != 21 )
  {
    puts("WRONG!");
    exit(0);
  }
  ++*(a1 + 16);
  return __readfsqword(0x28u) ^ v3;
}
```

`sub_AC5`函数读取字符串存入qword_2022A8。

所以qword_2022A8应为flag。

```c++
unsigned __int64 __fastcall sub_956(__int64 a1)
{
  unsigned __int64 v2; // [rsp+18h] [rbp-8h]

  v2 = __readfsqword(0x28u);
  ++*(a1 + 16);
  return __readfsqword(0x28u) ^ v2;
}
```

`sub_956`函数没有做操作，只是进入下一个指令，应为nop指令。

```cpp
unsigned __int64 __fastcall sub_A08(__int64 a1)
{
  unsigned __int64 v2; // [rsp+18h] [rbp-8h]

  v2 = __readfsqword(0x28u);
  *a1 *= *(a1 + 12);
  ++*(a1 + 16);
  return __readfsqword(0x28u) ^ v2;
}
```

`sub_A08`函数将两个数进行了乘法，操作数间隔为12，应为mul指令。

```cpp
unsigned __int64 __fastcall sub_8F0(int *a1)
{
  int v2; // [rsp+14h] [rbp-Ch]
  unsigned __int64 v3; // [rsp+18h] [rbp-8h]

  v3 = __readfsqword(0x28u);
  v2 = *a1;
  *a1 = a1[1];
  a1[1] = v2;
  ++*(a1 + 2);
  return __readfsqword(0x28u) ^ v3;
}
```

`sub_8F0`函数将两个数交换了位置，应该为swap。

```cpp
unsigned __int64 __fastcall sub_99C(__int64 a1)
{
  unsigned __int64 v2; // [rsp+18h] [rbp-8h]

  v2 = __readfsqword(0x28u);
  *a1 = *(a1 + 8) + 2 * *(a1 + 4) + 3 * *a1;
  ++*(a1 + 16);
  return __readfsqword(0x28u) ^ v2;
}
```

`sub_99C`函数，应该是一个运算函数。

![](https://gitee.com/Emtanling/image/raw/master/img/20210810093119.png)

`sub_E0B`函数中**(a1+16)等于0xF4就结束。

![](https://gitee.com/Emtanling/image/raw/master/img/20210810093916.png)

通过函数`sub_E6E`对指令的调用可得，对应关系。

| 码值 |  指令   |
| :--: | :-----: |
| 0xF1 |   mov   |
| 0xF2 |   xor   |
| 0xF4 |   nop   |
| 0xF5 |  input  |
| 0xF7 |   mul   |
| 0xF8 |  swap   |
| 0xF6 |   f()   |
| 0xE1 |   r1    |
| 0xE2 |   r2    |
| 0xE3 |   r3    |
| 0xE4 | flag r1 |
| 0xE5 |   r4    |
| 0xE7 | flag r2 |



```txt
0xF5, 									# input
0xF1, 0xE1, 0x00, 0x00, 0x00, 0x00,     # mov r1,flag[0]
0xF2, 									# xor r1,r2
0xF1, 0xE4, 0x20, 0x00, 0x00, 0x00, 	# mov flag[0x20],r1
0xF1, 0xE1, 0x01, 0x00, 0x00, 0x00, 	# mov r1,flag[1]
0xF2, 									# xor r1,r2
0xF1, 0xE4, 0x21, 0x00, 0x00, 0x00, 	# 以此循环，r2的初始值为18
0xF1, 0xE1, 0x02, 0x00, 0x00, 0x00, 	# 解出 This_is_not_flag_233
0xF2, 
0xF1, 0xE4, 0x22, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x03, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x23, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x04, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x24, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x05, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x25, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x06, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x26, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x07, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x27, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x08, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x28, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x09, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x29, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x0A, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x2A, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x0B, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x2B, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x0C, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x2C, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x0D, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x2D, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x0E, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x2E, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x0F, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x2F, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x10, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x30, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x11, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x31, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x12, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x32, 0x00, 0x00, 0x00, 
0xF1, 0xE1, 0x13, 0x00, 0x00, 0x00, 
0xF2, 
0xF1, 0xE4, 0x33, 0x00, 0x00, 0x00, 
0xF4, 
0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 

0xF5, 								# input
0xF1, 0xE1, 0x00, 0x00, 0x00, 0x00, # mov r1 flag[0]
0xF1, 0xE2, 0x01, 0x00, 0x00, 0x00, # mov r2 flag[1]
0xF2, 								# xor r1,r2
0xF1, 0xE4, 0x00, 0x00, 0x00, 0x00, # mov flag[0],r1
0xF1, 0xE1, 0x01, 0x00, 0x00, 0x00, # mov r1 flag[1]
0xF1, 0xE2, 0x02, 0x00, 0x00, 0x00, # mov r2 flag[2]
0xF2, 								# xor r1,r2
0xF1, 0xE4, 0x01, 0x00, 0x00, 0x00, # mov flag[1] r1
0xF1, 0xE1, 0x02, 0x00, 0x00, 0x00, # mov r1 flag[2]
0xF1, 0xE2, 0x03, 0x00, 0x00, 0x00, # mov r2 flag[3]
0xF2, 								# xor r1,r2
0xF1, 0xE4, 0x02, 0x00, 0x00, 0x00, # mov flag[2] r1
0xF1, 0xE1, 0x03, 0x00, 0x00, 0x00, # mov r1 flag[3]
0xF1, 0xE2, 0x04, 0x00, 0x00, 0x00, # mov r2 flag[4]
0xF2, 								# xor r1,r2
0xF1, 0xE4, 0x03, 0x00, 0x00, 0x00, # mov flag[3],r1
0xF1, 0xE1, 0x04, 0x00, 0x00, 0x00, # mov r1 flag[4]
0xF1, 0xE2, 0x05, 0x00, 0x00, 0x00, # mov r2 flag[5]
0xF2, 								# xor r1,r2
0xF1, 0xE4, 0x04, 0x00, 0x00, 0x00, # mov flag[4] r1
0xF1, 0xE1, 0x05, 0x00, 0x00, 0x00, # mov r1 flag[5]
0xF1, 0xE2, 0x06, 0x00, 0x00, 0x00, # mov r2 flag[6]
0xF2, 								# xor r1,r2
0xF1, 0xE4, 0x05, 0x00, 0x00, 0x00, # mov flag[5] r1
0xF1, 0xE1, 0x06, 0x00, 0x00, 0x00, # mov r1 flag[6]
0xF1, 0xE2, 0x07, 0x00, 0x00, 0x00, # mov r2 flag[7]
0xF1, 0xE3, 0x08, 0x00, 0x00, 0x00, # mov r3 flag[8]
0xF1, 0xE5, 0x0C, 0x00, 0x00, 0x00, # mov r4 flag[9]
0xF6, 								# r1 = f()
0xF7, 								# mul r1,r4
0xF1, 0xE4, 0x06, 0x00, 0x00, 0x00, # mov flag[6] r1
0xF1, 0xE1, 0x07, 0x00, 0x00, 0x00, # mov r1 flag[7]
0xF1, 0xE2, 0x08, 0x00, 0x00, 0x00, # mov r2 flag[8]
0xF1, 0xE3, 0x09, 0x00, 0x00, 0x00, # mov r3 flag[9]
0xF1, 0xE5, 0x0C, 0x00, 0x00, 0x00, # mov r4 flag[12]
0xF6, 								# r1 = f()
0xF7, 								# mul r1,r4
0xF1, 0xE4, 0x07, 0x00, 0x00, 0x00,	# mov flag[7] r1
0xF1, 0xE1, 0x08, 0x00, 0x00, 0x00, # mov r1 flag[8]
0xF1, 0xE2, 0x09, 0x00, 0x00, 0x00, # mov r2 flag[9]
0xF1, 0xE3, 0x0A, 0x00, 0x00, 0x00, # mov r3 flag[10]
0xF1, 0xE5, 0x0C, 0x00, 0x00, 0x00, # mov r4 flag[12]
0xF6, 								# r1 = f()
0xF7, 								# mul r1,r4
0xF1, 0xE4, 0x08, 0x00, 0x00, 0x00, # mov flag[8] r1
0xF1, 0xE1, 0x0D, 0x00, 0x00, 0x00, # mov r1 flag[13]
0xF1, 0xE2, 0x13, 0x00, 0x00, 0x00, # mov r2 flag[19]
0xF8, 								# swap(r1,r2)
0xF1, 0xE4, 0x0D, 0x00, 0x00, 0x00, # mov flag[13] r1
0xF1, 0xE7, 0x13, 0x00, 0x00, 0x00, # mov flag[19] r2
0xF1, 0xE1, 0x0E, 0x00, 0x00, 0x00, # mov r1 flag[14]
0xF1, 0xE2, 0x12, 0x00, 0x00, 0x00, # mov r2 flag[18]
0xF8, 								# swap(r1,r2)
0xF1, 0xE4, 0x0E, 0x00, 0x00, 0x00, # mov flag[14] r1
0xF1, 0xE7, 0x12, 0x00, 0x00, 0x00, # mov flag[18] r2
0xF1, 0xE1, 0x0F, 0x00, 0x00, 0x00, # mov r1 flag[15]
0xF1, 0xE2, 0x11, 0x00, 0x00, 0x00, # mov r2 flag[17]
0xF8,								# swap(r1,r2)
0xF1, 0xE4, 0x0F, 0x00, 0x00, 0x00,	# mov flag[15] r1
0xF1, 0xE7, 0x11, 0x00, 0x00, 0x00, # mov flag[17] r2
0xF4
```

对其进行翻译，得到伪代码。

```python
flag = []
code = 'Fz{aM{aM|}fMt~suM !!'
for i in range(6):
    flag[i] = flag[i]^flag[i+1]
flag[6]=(flag[8] + 2*flag[7] + 3*flag[6]) * flag[12]
flag[7]=(flag[9] + 2*flag[8] + 3*flag[7]) * flag[12]
flag[8]=(flag[10] + 2*flag[9] + 3*flag[8]) * flag[12]
swap(flag[13] ,flag[19]);
swap(flag[14] ,flag[18]);
swap(flag[15] ,flag[17]);
```

解密代码

```python
from z3 import *
x, y, z = Reals('x y z')
enc = [105, 69, 42, 55, 9, 23, 197, 11, 92, 114, 51, 118, 51, 114, 115, 51, 95, 49, 116, 33, 0]
solve((3*x+2*y+z)*0x33==0x6dc5,(3*y+2*z+0x72)*0x33==0x5b0b,(3*z+2*0x72+0x33)*0x33==0x705c)
enc[6] = 118
enc[7] = 51
enc[8] = 95

for i in range(5,-1,-1):
	enc[i] = enc[i]^enc[i+1]
flag=''
for i in range(len(enc)):
	flag+=chr(enc[i])
print(flag)
# Y0u_hav3_r3v3rs3_1t!
```

#### FuelVM.exe

从`GetDlgItemTextA`函数寻找。

![](https://gitee.com/Emtanling/image/raw/master/img/20210810162159.png)

找到读取字符串，分析函数`sub_401238`。

```cpp
int sub_401238()
{
  int result; // eax
  unsigned int v1; // ecx
  unsigned int v2; // ecx
  int v3; // ecx

  result = len(name);                           // 长度返回值在存入ecx中
  if ( v1 >= 7 )                                // name_len >= 7
  {
    *(_DWORD *)&byte_4031CD = v1;
    result = len(key);
    if ( v2 >= 7 )                              // key_len >= 7
    {
      v3 = 0;
      do
      {
        name[v3] ^= v3;
        ++v3;
      }
      while ( v3 <= *(int *)&byte_4031CD );
      unk_4031CE = v3;
      dword_4031C8 = dword_4035FF;
      sub_4011D8();
      sub_4011D8();
      __debugbreak();
    }
  }
  return result;
}
```

```asm
.text:004012B5                 push    offset byte_4012D7
.text:004012BA                 push    large dword ptr fs:0
.text:004012C1                 mov     large fs:0, esp
.text:004012C8                 call    sub_4011D8
.text:004012CD                 int     3  
```

这里注册了一个seh，通过int3 触发

![](https://gitee.com/Emtanling/image/raw/master/img/20210810164030.png)

又在`0x004012E1`处注册一个seh，通过div除0触发



![](https://gitee.com/Emtanling/image/raw/master/img/20210810164217.png)

又在`0x00401310 `处注册seh，通过访问`0x4035FF`无权限触发

![](https://gitee.com/Emtanling/image/raw/master/img/20210810170425.png)

int3触发

`sub_40135E`为最后一个函数，所以可以定义函数进行分析。

**0xA**

![](https://gitee.com/Emtanling/image/raw/master/img/20210810170608.png)

分析函数`sub_401812`

```
void __stdcall sub_401812(int *a1)
{
  int v1; // ecx

  dword_4035EF -= 4;                            // 可能对栈操作，是栈的偏移，为push
  if ( a1 )
  {
    v1 = *a1;
  }
  else
  {
    LOWORD(v1) = *(_WORD *)((char *)&dword_4031C8 + 1);
    dword_4035FF += 2;
  }
  *(_WORD *)((char *)&unk_4031CF + dword_4035EF) = v1;// 存放栈中
}
```

**0xB**

![](https://gitee.com/Emtanling/image/raw/master/img/20210810172359.png)

分析函数`sub_40184A`

```cpp
__int64 __stdcall sub_40184A(_DWORD *a1)
{
  _DWORD *v1; // ebx

  v1 = (_DWORD *)((char *)&stack_base + stack_offset);
  *a1 = *(_DWORD *)((char *)&stack_base + stack_offset);
  *v1 = 0;
  stack_offset += 4;                            // 对栈偏移操作，可能为pop
  return 0i64;
}
```

**0xC**

![](https://gitee.com/Emtanling/image/raw/master/img/20210810173005.png)

分析函数`sub_401871`

```cpp
__int64 __stdcall sub_401871(__int64 a1)
{
  __int64 result; // rax
  int v2; // ebx

  result = a1;
  if ( HIDWORD(a1) )
  {
    v2 = *(_DWORD *)HIDWORD(a1);
  }
  else
  {
    LOWORD(v2) = *(_WORD *)((char *)&code_now + 1);
    code_offset += 2;
  }
  *(_WORD *)a1 = v2;                            // 进行赋值，可能为mov
  return result;
}
```

**0xD**

![](https://gitee.com/Emtanling/image/raw/master/img/20210810173220.png)

分析函数`sub_401A03`

```cpp
int __stdcall sub_401A03(int a1, int a2)
{
  int result; // eax
  __int16 v3; // bx

  result = a1;
  v3 = a2;
  if ( !a2 )
  {
    v3 = *(_WORD *)((char *)&code_now + 1);     // a2 == 0
    ++code_offset;
    ++code_offset;
  }
  if ( (__int16)a1 < v3 )
    goto LABEL_6;
  if ( (__int16)a1 > v3 )
  {
    byte_40360F = 0;
    byte_403610 = 0;
  }
  else
  {
    if ( (_WORD)a1 != v3 )
    {
LABEL_6:
      byte_40360F = 1;
      byte_403610 = 0;
      return result;
    }
    byte_40360F = 0;
    byte_403610 = 1;
  }
  return result;                                // 这里应该为cmp
}
```

**0xE**

![](https://gitee.com/Emtanling/image/raw/master/img/20210810174211.png)

分析函数`sub_401898`

```cpp
_DWORD *__stdcall sub_401898(_DWORD *a1)
{
  _DWORD *result; // eax

  result = a1;
  ++*a1;
  return result;                                // 应为inc
}
```

**0xF**

![](https://gitee.com/Emtanling/image/raw/master/img/20210810174345.png)

分析函数`sub_4018A7`

```cpp
_DWORD *__stdcall sub_4018A7(_DWORD *a1)
{
  _DWORD *result; // eax

  result = a1;
  --*a1;
  return result;                                // 应为dec
}
```

**0x1B**

![](https://gitee.com/Emtanling/image/raw/master/img/20210810174511.png)

分析函数`sub_4018B6`

```cpp
_DWORD *__stdcall sub_4018B6(_DWORD *a1, int a2)
{
  _DWORD *result; // eax
  int v3; // ebx

  result = a1;
  v3 = *a1;
  code_offset += 2;
  *(_WORD *)a1 = *(_WORD *)((_BYTE *)&code_now + 1) & v3;// and
  return result;
}
```

**0x1C**

![](https://gitee.com/Emtanling/image/raw/master/img/20210810174705.png)

分析函数`sub_4018D5`

```cpp
_DWORD *__stdcall sub_4018D5(_DWORD *a1, int a2)
{
  _DWORD *result; // eax
  int v3; // ebx

  result = a1;
  v3 = *a1;
  code_offset += 2;
  LOWORD(v3) = *(_WORD *)((char *)&code_now + 1) | v3;// or
  *a1 = v3;
  return result;
}
```

**0x1D**

![](https://gitee.com/Emtanling/image/raw/master/img/20210810174827.png)

分析函数`sub_4018F3`

```cpp
__int32 __stdcall sub_4018F3(int a1, int a2)
{
  int v2; // ebx

  v2 = a2;
  if ( !a2 )
  {
    v2 = *(int *)((char *)&code_now + 1);
    code_offset += 2;
  }
  return _InterlockedExchange(&dword_4035CF, v2 ^ a1);// xor
}
```

可以得到结论

|    opcode    | value |
| :----------: | :---: |
|     push     |  0xA  |
|     pop      |  0xB  |
|     mov      |  0xC  |
|     cmp      |  0xD  |
|     inc      |  0xE  |
|     dec      |  0xF  |
|     and      | 0x1B  |
|      or      | 0x1C  |
|     xor      | 0x1D  |
| dword_4035CF |  r1   |
| dword_4035D7 |  r2   |
| dword_4035DF |  r3   |
| dword_4035E7 |  r4   |

对其复原[fuelvm.py](https://github.com/ctf-wiki/ctf-challenges/blob/master/reverse/vm/fuelvm/fuelvm_keygen.py)

```txt
push 0x9809
mov r1, R4      	# r1 = key[1,..,len(key)-1]
push r4				
or   r1, 0x0011		# key | r1
mov r3, 0x00b0		# r3 = 0xb0
xor  r1, 0x020b		# r1 = key ^ 0x20b
mov r1, r2			# r1 = 0
cmp  r1, 0x0088		# 
or   r1, 0x0009		# r1 = r1 | 9
and  r1, 0xffff		# r1 = r1 & 0xffff
xor  r1, 0x040f		# r1 = 0x406
dec  r4				# ord(key) - 1
push r4
inc  r1				# r1 = 0x407
pop  r3
dec  r1	
dec  r1				# r1 = 0x405
push 0x00ba
pop  r2				# r2 = 0xba 
inc  r2
inc  r2
inc  r2
dec  r2				# r2 = 0xbc
xor  r1, r2			# r1 = 0xbc ^ 0x405 = 0x4b9
pop  r3				# r3 = key
xor  r1, r2			# r1 = 0x4b9 ^ 0xbc = 0x405
or   r1, 0x0022		# r1 = 0x427
push r3
pop  r2				# r2 = key
xor  r1, r2			# r1 = (0x427 ^ key) & 0xff
```

解密脚本

```python
import random, string
Name = input("name: ")
name = ''
for i in range(len(Name)):
    name += chr(ord(Name[i])^i)
key = ''
key += random.choice(string.ascii_letters + string.digits)
key += random.choice(string.ascii_letters + string.digits)
for i in range(1,len(name)):
    x = (ord(name[i]) ^ 0x427) & 0xff 
    if x < 0x21:
         x += 0x21
    key += chr(x)
print ("key: " + key)
# name: Emtanling
# key: jJKQEMNHNH
```

成功注册

![](https://gitee.com/Emtanling/image/raw/master/img/20210810221714.png)