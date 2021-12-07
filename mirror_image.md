### 去壳

upx壳，可以通过[ELF64手脱UPX壳实战](https://bbs.pediy.com/thread-255519.htm)去除

```c
#include <idc.idc>
#define PT_LOAD              1
#define PT_DYNAMIC           2
static main(void)
{
         auto ImageBase,StartImg,EndImg;
         auto e_phoff;
         auto e_phnum,p_offset;
         auto i,dumpfile;
         ImageBase=0x400000;
         StartImg=0x400000;
         EndImg=0x0;
         if (Dword(ImageBase)==0x7f454c46 || Dword(ImageBase)==0x464c457f )
  {
    if(dumpfile=fopen("G:\\dumpfile","wb"))
    {
      e_phoff=ImageBase+Qword(ImageBase+0x20);
      Message("e_phoff = 0x%x\n", e_phoff);
      e_phnum=Word(ImageBase+0x38);
      Message("e_phnum = 0x%x\n", e_phnum);
      for(i=0;i<e_phnum;i++)
      {
         if (Dword(e_phoff)==PT_LOAD || Dword(e_phoff)==PT_DYNAMIC)
                         {
                                 p_offset=Qword(e_phoff+0x8);
                                 StartImg=Qword(e_phoff+0x10);
                                 EndImg=StartImg+Qword(e_phoff+0x28);
                                 Message("start = 0x%x, end = 0x%x, offset = 0x%x\n", StartImg, EndImg, p_offset);
                                 dump(dumpfile,StartImg,EndImg,p_offset);
                                 Message("dump segment %d ok.\n",i);
                         }   
         e_phoff=e_phoff+0x38;
      }
 
      fseek(dumpfile,0x3c,0);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
 
      fseek(dumpfile,0x28,0);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
      fputc(0x00,dumpfile);
 
      fclose(dumpfile);
        }else Message("dump err.");
 }
}
static dump(dumpfile,startimg,endimg,offset)
{
        auto i;
        auto size;
        size=endimg-startimg;
        fseek(dumpfile,offset,0);
        for ( i=0; i < size; i=i+1 )
        {
        fputc(Byte(startimg+i),dumpfile);
        }
}
```

也可以添加`UPX`的特征使用`UPX-3.96`工具去除。

### 去花

去除后IDA打开一片混乱，调试时可以看到有花指令。

`Trace`进行花指令分析。

![](https://gitee.com/Emtanling/image/raw/master/img/20210908090301.png)

![](https://gitee.com/Emtanling/image/raw/master/img/20210908090402.png)

可以看到很多类似与此的结构，特征为`test`指令，隔几段之后为两个`jno`指令

所以可以写出`ida python`脚本去除

```python
import time
startAddress = 0x401000
endAddress   = 0x408000
currentLoc = startAddress
instructions = [0] * 7
m = 5 # 此处为指令间隔距离 3 ~ 6

print("\t"+"="*15+"--开始位置--"+"="*15)
def getAsm(Addr):
	try:
		temp = Addr
		while idc.MakeUnkn(Addr,idc.GetFlags(Addr)) != True or idc.MakeCode(temp) == 0:
			Addr += 1
	except:
		print("Except Addr: ",hex(Addr))

while currentLoc <= endAddress:
	instructions[0] = currentLoc
	getAsm(instructions[0])
	instructions[1] = instructions[0] + idaapi.decode_insn(instructions[0])
	getAsm(instructions[1])
	instructions[2] = instructions[1] + idaapi.decode_insn(instructions[1])
	getAsm(instructions[2])
	instructions[3] = instructions[2] + idaapi.decode_insn(instructions[2])
	getAsm(instructions[3])
	instructions[4] = instructions[3] + idaapi.decode_insn(instructions[3])
	getAsm(instructions[4])
	instructions[5] = instructions[4] + idaapi.decode_insn(instructions[4])
	getAsm(instructions[5])
	instructions[6] = instructions[5] + idaapi.decode_insn(instructions[5])
	getAsm(instructions[6])
	if idc.GetDisasm(instructions[0]).find('test    ') != -1 and idc.GetDisasm(instructions[m]).find('jno     ') != -1:
		print("Find Patch Start Addr: ",hex(currentLoc))
		lo = instructions[m] - (0xFF - idaapi.get_byte(instructions[m] + 1)) + 1
		con = idaapi.get_byte(lo + 1) + lo + 2
		while instructions[0] < con:
			patch_byte(instructions[0],0x90)
			# print("Patch Addr: ",hex(instructions[0]))
			instructions[0] += 1
		time.sleep(0.5)
		currentLoc = instructions[m] 
	else:
		currentLoc += 1
print("\t"+"="*15+"--结束位置--"+"="*15)
```

不足之处

patch后发现，之后还有结构，可以使用脚本对之后数据进行进一步的处理，`nop`到`jnb`的跳转地址处。

![](https://gitee.com/Emtanling/image/raw/master/img/20210908090927.png)

### 分析

由于之前的花指令没有去除干净，之后的话手动去除吧🤣

![](https://gitee.com/Emtanling/image/raw/master/img/20210907184600.png)

![](https://gitee.com/Emtanling/image/raw/master/img/20210907185028.png)

输入长度为48

向下寻找此处有`int 3`触发异常处理

![](https://gitee.com/Emtanling/image/raw/master/img/20210907185925.png)

此处有判断函数位置
![](https://gitee.com/Emtanling/image/raw/master/img/20211207165220.png)
