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

