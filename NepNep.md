## **栈溢出在RE应用**

#### **dig_the_way**

分析可得

```c
func_ptr = swap;
v15 = func1;                                  // abs(x1 + x2) - abs(x1) - abs(x2) + 2
v16 = func2;                                  // abs(x1) - abs(x1 + x2) + abs(x2) + 2

*(&v8 + v6) = (*(&func_ptr + i))((int)&v8, v12, v13); // i=0时为swap函数。 i=1时为func1函数。 i=2时为func2函数。
```

![](https://i.loli.net/2021/07/15/DF5ExoW3OQezRMP.png)

当v11等于0时，可以得到flag。 

![](https://i.loli.net/2021/07/15/F7UNWkPKua2DOHv.png)

根据栈分布可得v11的值为fun2的返回值，其值>=2无法得到结果。

![](https://i.loli.net/2021/07/15/HyRKUw9d7p3bnYz.png)

这里对文件进行读取时，并未对输入的长度进行检测，导致可以之后的数值进行覆盖。

可以利用swap函数对fun1和func2进行交换，使得v11的值等于func1的返回值。

构造长度为`0x48-0x24=36`，而func1和func2位于数组位置7、8

所以data文件可以构造为

```bin
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 07 00 00 00 08 00 00 00 
```

测试成功交换。

构造func1的运算参数位于数组位置7、8，所以构造如下，而当`x1=1，x2=-1`返回值为0，所以构造如下

```bin
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 01 00 00 00 
FF FF FF FF 07 00 00 00 08 00 00 00 
```

执行

![](https://i.loli.net/2021/07/15/6WXMPBYfx7otyq3.png)

#### **ctf2017_Fpc**

```asm
.text:0040100D 000                 mov     dword_41B034, 2   ; dword_41B034存入2
.text:00401017 000                 call    sub_401050        ; 输入函数
.text:0040101C 000                 call    sub_401090	     ; 第一个判断 成功时dword_41B034--
.text:00401021 000                 call    sub_4010E0	   	 ; 第二个判断 成功时dword_41B034--
.text:00401026 000                 mov     eax, dword_41B034 ; dword_41B034存入eax
.text:0040102B 000                 test    eax, eax			 ; 判断dword_41B034是否为0 
.text:0040102D 000                 jnz     short loc_40103F  ; 为0正确
```

在输入时由于没有检查scanf输入的长度导致这里造成栈溢出，最多输入0xB个字符，不会破坏ret

![](https://i.loli.net/2021/07/20/6r39GOmtigvHZCT.png)

![](https://i.loli.net/2021/07/20/mOfTyZuJHRk1Fxc.png)

判断函数中字符一共需要八个，所以这里输入8个字符，之后可以用z3求解

```python
from z3 import *
x = Int('x')
y = Int('y')
solve(
		y != 0, x != 0, y != x, 5*(y-x)+y == 0x8F503A42, 13 * (y - x) + x == 0xEF503A42,
	    17 * (y - x) + y == 0xF3A94883, 7 * (y - x) + x == 0x33A94883
		)
# no solution
```

证明此处只是虚晃一枪，那么有两种可能。

* 通过改变返回地址，让EIP直接执行到输出“You get it!”。

* 通过改变返回地址，跳到另一个校验程序处，对输入内容进行校验。

从可见字符来看，第一种不可能，只能是第二种

![](https://i.loli.net/2021/07/20/tycqjNL1dMGZnQR.png)

可以发现代码段中有无法分析之处，那么可能这里进行加密或花指令了。

这里的起始地址是`0x413131`正好是`A11`，可以尝试构造一下。

```text
1234567890ab11A
```

动态调试，加了花指令，通过trace，进行花指令去除

```asm
00413131   add    esp, -0x10
00413150   xor    eax, eax
00413184   mov    dword ptr [0x41B034], eax
004131BA   pop    eax
004131EB   mov    ecx, eax
0041321F   pop    eax
00413254   mov    ebx, eax
00413289   pop    eax
004132B5   mov    edx, eax
004132AD   mov    edx, eax
004132E2   mov    eax, ecx
00413316   sub    eax, ebx
00413349   shl    eax, 0x2
00413380   add    eax, ecx
004133B5   add    eax, edx
004133E9   sub    eax, 0xEAF917E2
00413455   add    eax, ecx
004134F3   shl    eax, 1
00413525   add    eax, ebx
00413559   add    eax, ecx
0041358F   mov    ecx, eax
004135C3   add    eax, edx
004135F7   sub    eax, 0xE8F508C8
00413665   mov    eax, ecx
0041365D   mov    eax, ecx
004136A7   sub    eax, edx
004136D8   sub    eax, 0xC0A3C68
00413747   pop    eax            ; ctf2017_.00413131
00413777   xor    eax, 0x8101   //eax=410b030
004137A9   mov    edi, eax       ; ctf2017_.0041B030
004137E2   xor    eax, eax       ; ctf2017_.0041B030
00413817   stos    dword ptr es:[edi]    //[410b030]=0
00413830   call   00413841
0041385C   pop    eax   //eax=00413835
0041388E   push   eax
004138BA   mov    edi, eax      ; ctf2017_.00413835
004138B2   mov    edi, eax      ; ctf2017_.00413835
004138E6   push   0x4E000969
0041391F   pop    eax             //eax=0x4E000969
00413950   xor    eax, edx     //edx=sn[9-12]  eax=0x4E000969
00413983   stos    dword ptr es:[edi]  //[00413835]=0x4E000969 xor sn[9-12]
004139B5   xor    eax, 0x10A3E
004139EB   stos    dword ptr es:[edi]   //[00413839]=eax xor 10A3E
00413A1C   xor    eax, ebx   //ebx=sn[1-4]-sn[5-8]
00413A4D   xor    eax, 0x22511E14
00413A82   stos    dword ptr es:[edi]  //[0041383D]=eax
00413AB6   xor    eax, 0x61642D
00413B83   xor    eax, dword ptr [0x41B034]  //[0041B034]=00000000
00413BBB   jmp    eax
```

![](https://i.loli.net/2021/07/20/aIRBPw2ZJjCG1Uy.png)

通过对寄存器的值变化可以得到三个式子

```math
(x-y)<<2+x+z == 0xEAF917E2
(x-y)<<1+(x-y)+x+z == 0xE8F508C8
(x-y)<<1+(x-y)+x-z == 0xC0A3C68
```

可以使用z3求值

```python
from z3 import *
import struct
x, y, z = BitVecs('x y z', 32)
solver = Solver()
solver.add(((x-y) << 2)+x+z == 0xEAF917E2)
solver.add(((x-y) << 1)+(x-y)+x+z == 0xE8F508C8)
solver.add(((x-y) << 1)+(x-y)+x-z == 0xC0A3C68)
if solver.check() == sat:
	m = solver.model()
	print((m[x]))
    print((m[y]))
    print((m[z]))
# [z = 1853187632, y = 1919903280, x = 1953723722]
# [z = 0x6e756630, y = 0x726f6630, x = 0x7473754A]
```

将其转化为16进制得

```text
Just0for0fun11A
```

![](https://i.loli.net/2021/07/20/wt1xT7nbjargZBH.png)



## **Unity3D 逆向**

#### **Who_is_he**

分析`Assembly-CSharp.dll`

![](https://i.loli.net/2021/07/16/vuPgbXIwLf4BxJl.png)

```python
import base64
from Crypto.Cipher import DES
cry = '1Tsy0ZGotyMinSpxqYzVBWnfMdUcqCMLu0MA+22Jnp+MNwLHvYuFToxRQr0c+ONZc6Q7L0EAmzbycqobZHh4H23U4WDTNmmXwusW4E+SZjygsntGkO2sGA=='
key = '1234'
cry = base64.b64decode(cry)
key = key.encode('utf-16-le')
print(key)
des = DES.new(key,mode=DES.MODE_CBC,iv=key)
crd = des.decrypt(cry)
print(crd.decode())
# He_P1ay_Basketball_Very_We11!Hahahahaha!
```

输入错误

使用CE附加，对`momo.dll`进行分析

![](https://i.loli.net/2021/07/16/R3qrzvZ5xTibWwk.png)

可以发现这个，所以`UmbraModule.dll`可能是真正的解密dll，使用dnspy无法打开可能文件加密了。

根据[文章](https://blog.csdn.net/weixin_39186306/article/details/104691632)，可以知道加密过程：先将主逻辑dll加密，再将解密代码添加到mono动态链接库中的导出函数`mono_image_open_from_data_with_name`，重新编译，在运行过程中解密。

分析函数，对三个字符串解密，写出解密脚本

```python
def dec(stre):
    pre = ''
    v6 = [6,5,5,3,5]
    for i in range(len(stre)):
        pre += chr(ord(stre[i]) ^ v6[i % 5])
    return pre
print(dec("Gvvfhdi|.FUmdqu(aio"))
print(dec("Sklw|Ckbjkc+PngtdHlasi`-aji"))
print(dec("`idd~RmlpZOvZWmcZCoda$$\"JYJ:~"))
# Assembly-CSharp.dll
# UnityEngine.UmbraModule.dll
# flag{This_Is_The_Flag!!!O_O?}
```

之后先截取`flag{This_Is_The_Flag!!!O_O?}` 前16个字符当做`key`，之后对`UnityEngine.UmbraModule.dll`文件进行`xxtea`解密。

```python
import xxtea
key = 'flag{This_Is_The_Flag!!!O_O?}'[:16]
print(key)
pathE = 'UnityEngine.UmbraModule.dll'
with open(pathE,'rb+') as fp:
    text = fp.read()
    text_stream = xxtea.decrypt(text,key,padding=False)
pathD ='DECRY_UnityEngine.UmbraModule.dll'
with open(pathD,'wb+') as fp:
    text = fp.write(text_stream)
fp.close()
```

解密后的`DECRY_UnityEngine.UmbraModule.dll`文件载入`dnspy`得到新的加密base64字符串

```text
q+w89Y22rObfzxgsquc5Qxbbh9ZIAHET/NncmiqEo67RrDvz34cdAk0BalKWhJGl2CBYMlr8pPA=
```

解密后得

```text
Oh no!This is a trick!!!
```

仍然错误

换种思路通过栈回溯找到关键 call

![](https://i.loli.net/2021/07/18/im5QeJa9LcZpo8k.png)

对其进行调试得到

![](https://i.loli.net/2021/07/18/79r8GFzDyNnEOdY.png)

此处进行比较，查看内存

![](https://i.loli.net/2021/07/18/8WdSklH3OiFpmUu.png)

rdi 为输入内容，那么rsi 即为flag

![](https://i.loli.net/2021/07/18/1Mn4GeY9DymhIN7.png)

```text
She_P1ay_Black_Hole_Very_Wel1!LOL!XD!
```

正确

也可以用ce找到正确的密钥和密文

```text
xZWDZaKEhWNMCbiGYPBIlY3+arozO9zonwrYLiVL4njSez2RYM2WwsGnsnjCDnHs7N43aFvNE54noSadP9F8eEpvTs5QPG+KL0TDE/40nbU=
test
```

```python
import base64
from Crypto.Cipher import DES
cry = 'xZWDZaKEhWNMCbiGYPBIlY3+arozO9zonwrYLiVL4njSez2RYM2WwsGnsnjCDnHs7N43aFvNE54noSadP9F8eEpvTs5QPG+KL0TDE/40nbU='
key = 'test'
cry = base64.b64decode(cry)
key = key.encode('utf-16-le')
print(key)
des = DES.new(key,mode=DES.MODE_CBC,iv=key)
crd = des.decrypt(cry)
print(crd.decode())
# She_P1ay_Black_Hole_Very_Wel1!LOL!XD!
```

#### **Snake**

载入`dnspy`

找到主函数`GameObject`，这里载入`Interface.dll`，在其中进行`check`

```c#
[DllImport("Interface", CallingConvention = CallingConvention.Cdecl)]
```

对其进行分析找到主函数

```c
signed __int64 __fastcall GameObject(int a1) // 在其中进行判断
```

![](https://i.loli.net/2021/07/20/pYjWTq7tfSwKvsF.png)

这里可以得到参数的范围大于0小于100

由于只有一个参数，且知道类型和范围所以可以，通过调用dll中的函数进行爆破

```cpp
#include <iostream>
#include <windows.h>
typedef int(*ptrSub)(int);
int main()
{
    HMODULE hMod = LoadLibrary(L"E:\\编程\\main\\Interface.dll"); // GameObject
    if (hMod != NULL)
    {
        ptrSub GameObject = (ptrSub)GetProcAddress(hMod, "GameObject");
        if (GameObject != NULL)
        {
            for (int i = 0; i <= 99; i++)
            {
                GameObject(i);
            }
        }
        else 
        {
            std::cout << "Error" << std::endl;
        }
        FreeLibrary(hMod);
    }
}
```

可以得到

![](https://i.loli.net/2021/07/19/PFz2E61CmSxT3qY.png)

