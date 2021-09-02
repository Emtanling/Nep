## RE
### [Re1](https://emtanling.lanzoui.com/iMOBBtflxxg)
花指令去除


![](https://gitee.com/Emtanling/image/raw/master/img/20210831105912.png#id=fQVpM&originHeight=562&originWidth=1185&originalType=binary&ratio=1&status=done&style=none)


![](https://gitee.com/Emtanling/image/raw/master/img/20210831145801.png#id=qEVzk&originHeight=200&originWidth=917&originalType=binary&ratio=1&status=done&style=none)


![](https://gitee.com/Emtanling/image/raw/master/img/20210831150244.png#id=yuxVz&originHeight=113&originWidth=1071&originalType=binary&ratio=1&status=done&style=none)


此处为花指令，nop即可


![](https://gitee.com/Emtanling/image/raw/master/img/20210831110408.png#id=NkB2c&originHeight=614&originWidth=770&originalType=binary&ratio=1&status=done&style=none)


输入长度为12到45，且为`WMCTF{}`


求出CRC产生的数据


![](https://gitee.com/Emtanling/image/raw/master/img/20210831173724.png#id=RDoxe&originHeight=123&originWidth=546&originalType=binary&ratio=1&status=done&style=none)


```python
from z3 import *
s = Solver()
k = [BitVec('%d'%i, 32) for i in range(4)]
s.add((k[0]+k[1]) == 0x11AB7A7A)
s.add(k[1]-k[2] == 0x1CD4F222)
s.add(k[2]+k[3] == 0xC940F021)
s.add(k[0]+k[2]-k[3] == 0x7C7D68D1)
if s.check() == sat:
    m = s.model()
    m = [m[k[i]].as_long() for i in range(4)]
    print(m)
# 0x78591F87  0x50E7D09A 0x6DBCC2BC 0xA3EEB7BE
```


获得前4项值


```python
def get_crc32_table():
	crc32_table = []
	for i in range(0x100):
		v3 = i
		for j in range(8):
			if (v3&1)!=0:
				v3 = (v3>>1)^0x8320EDB8
			else:
				v3 >>= 1
		crc32_table.append(v3)
	return crc32_table

def crc32_func(data,rounds,v,code):
	crc32_table = get_crc32_table()
	for c in range(128):
		block = []
		for j in range(rounds):
			block.append(c+j+v)
		v5 = 0
		v4 = data
		for i in range(rounds):
			v4 = ((v4&0xffffffff)>>8)^crc32_table[(block[i]^(v4&0xffffffff))&0xff]
		if ((data&0xffffffff) ^ (v4&0xffffffff)) == code:
			print("Get it",c)
			return c
if __name__ == "__main__":
	rounds = [0x100, 0x100, 0xf, 0x1c]
	enc = [0xA3EEB7BE,0x6DBCC2BC,0x50E7D09A,0x78591F87]
	m = []
	for i in range(len(enc)):
		if 0 == i:
			m.append(crc32_func(-2,rounds[i],i,enc[i]))
		else:
			m.append(crc32_func(enc[i-1],rounds[i],i,enc[i]))
	for i in range(len(m)):
		print(chr(m[i]),end="")
# Hah4
```


之后XTEA加密，需要爆破进行求出可见、可阅读字符


密文为![](https://gitee.com/Emtanling/image/raw/master/img/20210831154837.png#id=LeJz2&originHeight=203&originWidth=497&originalType=binary&ratio=1&status=done&style=none)


```data
enc1 = [0x1989FB2B,0x83F5A243]
enc2 = [0x556E2853,0x4393DF16]
```


密钥为


```data
0x78591FAD 0x6DBCC2BC 0xA3EEB7BE 0x50E7B79A
```


delta 为 0x9981ABCD


```python
def decrypt(rounds, v, k):
    v0 = v[0]
    v1 = v[1]
    delta = 0x9981ABCD
    x = delta * rounds
    for i in range(rounds):
        v1 -= (((v0 << 4) ^ (v0 >> 5)) + v0) ^ (x + k[(x >> 11) & 3])
        v1 = v1 & 0xFFFFFFFF
        x -= delta
        x = x & 0xFFFFFFFF
        v0 -= (((v1 << 4) ^ (v1 >> 5)) + v1) ^ (x + k[x & 3])
        v0 = v0 & 0xFFFFFFFF
    v[0] = v0
    v[1] = v1
    return v
if __name__ == '__main__':
    key1 = [0x78591FAD ,0x6DBCC2BC ,0xA3EEB7BE ,0x50E7B79A]
    key2 = [0xA3EEB7BE,0x50E7B79A,0x6DBCC2BC,0x78591FAD]
    enc1 = [0x1989FB2B,0x83F5A243]
    enc2 = [0x556E2853,0x4393DF16]
    decrypted1 = decrypt(32, enc1, key1)
    for i in decrypted1:
        print(hex(i))
    decrypted2 = decrypt(32, enc2, key2)
    for i in decrypted2:
        print(hex(i))
# _uOy	yOu_
# Ek1L	L1kE
# _0D_	_D0_
# !tI_	_It!
# _D0_yOu_L1kE_It!
```


爆破结果为0xb7ad，即将0xde变为0xb7


过程为`@FFFE#0F20-11B7`


sub_1400035E0函数更改值


![](https://gitee.com/Emtanling/image/raw/master/img/20210831233515.png#id=CpXco&originHeight=677&originWidth=1486&originalType=binary&ratio=1&status=done&style=none)


sub_1400033F0函数更改值


![](https://gitee.com/Emtanling/image/raw/master/img/20210831233753.png#id=tcbas&originHeight=677&originWidth=1486&originalType=binary&ratio=1&status=done&style=none)sub_140003390函数更改值


![](https://gitee.com/Emtanling/image/raw/master/img/20210831234016.png#id=Sdmiu&originHeight=677&originWidth=1486&originalType=binary&ratio=1&status=done&style=none)


所以flag


```
WMCTF{Hah4_D0_yOu_L1kE_It!@FFFE#0F20-11B7}
```


### [Re2](https://emtanling.lanzoui.com/iMQ8Etgxv3c)
sub_10BBC函数，通过检测`/data/local/su`文件是否存在判断是否为root


![](https://gitee.com/Emtanling/image/raw/master/img/20210901100030.png#id=hxAWl&originHeight=489&originWidth=931&originalType=binary&ratio=1&status=done&style=none)


先进行了aes加密，之后rc4加密，之后异或0x50


rc4的key为`0x46068`处的字符


```python
rc4_key = [0xA5, 0x88, 0x81, 0x81, 0x82, 0xCD, 0x8B, 0x9F, 0x82, 0x80, 0xCD, 0xAE, 0xC6, 0xC6, 0xED]
print("rc4_key = ",end='')
for i in range(len(rc4_key)):
	print(chr(rc4_key[i]^0xED),end='')
# rc4_key = Hello from C++
```


RCE解密异或0x50


```python
from Crypto.Cipher import ARC4
rc4_key = ''
rc4_key_table = [0xA5, 0x88, 0x81, 0x81, 0x82, 0xCD, 0x8B, 0x9F, 0x82, 0x80, 0xCD, 0xAE, 0xC6, 0xC6, 0xED]
for i in range(len(rc4_key_table)):
	rc4_key += chr(rc4_key_table[i]^0xED)
print(rc4_key.encode('utf-8'))
enc_table = [	0x18, 0x77, 0xE9, 0x84, 0x72, 0x3B, 0x71, 0x0F, 0xC8, 0x84, 0x5C, 0x2E, 0x92, 0x38,
				0x03, 0x19, 0x33, 0x74, 0x73, 0x79, 0x00, 0x88, 0x59, 0x0B, 0x7C, 0x38, 0x67, 0x63,
				0xA6, 0x4E, 0x8F, 0x3D
]
enc_xor = [	0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F,
			0x10, 0x11, 0x12, 0x13, 0x14, 0x15, 0x16, 0x17, 0x18, 0x19, 0x1A, 0x1B, 0x1C, 0x1D, 0x1E, 0x1F
]
enc = []
for i in range(len(enc_table)):
	enc.append(enc_table[i]^enc_xor[i]^0x50)
print(enc)
print(rc4_key.encode('utf-8')[:-1])
rc4 = ARC4.new(rc4_key.encode('utf-8')[:-1])
enc = list(rc4.decrypt(bytes(enc)))
```


sub_10E80函数为`tracepid`反调试。


aes加密的key为


```data
0x54, 0x72, 0x61, 0x63, 0x65, 0x72, 0x50, 0x69, 0x64, 0x3a, 0x9, 0x30, 0xa, 0x66, 0x6c, 0x67
```


aes加密的iv为


```data
0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F
0x10, 0x11, 0x12, 0x13, 0x14, 0x15, 0x16, 0x17, 0x18, 0x19, 0x1A, 0x1B, 0x1C, 0x1D, 0x1E, 0x1F
```


求逆S盒，[逆推逆S盒](https://bbs.pediy.com/thread-266410-1.htm)


```python
sbox = [
    0x7C, 0xF2, 0x63, 0x7B, 0x77, 0x6B, 0x6F, 0xC5, 0x30, 0x01,
    0x67, 0x2B, 0xFE, 0xD7, 0xAB, 0x76, 0xCA, 0x82, 0xC9, 0x7D,
    0xFA, 0x59, 0x47, 0xF0, 0xAD, 0xD4, 0xA2, 0xAF, 0x9C, 0xA4,
    0x72, 0xC0, 0xB7, 0xFD, 0x93, 0x26, 0x36, 0x3F, 0xF7, 0xCC,
    0x34, 0xA5, 0xE5, 0xF1, 0x71, 0xD8, 0x31, 0x15, 0x04, 0xC7,
    0x23, 0xC3, 0x18, 0x96, 0x05, 0x9A, 0x07, 0x12, 0x80, 0xE2,
    0xEB, 0x27, 0xB2, 0x75, 0x09, 0x83, 0x2C, 0x1A, 0x1B, 0x6E,
    0x5A, 0xA0, 0x52, 0x3B, 0xD6, 0xB3, 0x29, 0xE3, 0x2F, 0x84,
    0x53, 0xD1, 0x00, 0xED, 0x20, 0xFC, 0xB1, 0x5B, 0x6A, 0xCB,
    0xBE, 0x39, 0x4A, 0x4C, 0x58, 0xCF, 0xD0, 0xEF, 0xAA, 0xFB,
    0x43, 0x4D, 0x33, 0x85, 0x45, 0xF9, 0x02, 0x7F, 0x50, 0x3C,
    0x9F, 0xA8, 0x51, 0xA3, 0x40, 0x8F, 0x92, 0x9D, 0x38, 0xF5,
    0xBC, 0xB6, 0xDA, 0x21, 0x10, 0xFF, 0xF3, 0xD2, 0x0C, 0xCD,
    0x13, 0xEC, 0x5F, 0x97, 0x44, 0x17, 0xC4, 0xA7, 0x7E, 0x3D,
    0x64, 0x5D, 0x19, 0x73, 0x60, 0x81, 0x4F, 0xDC, 0x22, 0x2A,
    0x90, 0x88, 0x46, 0xEE, 0xB8, 0x14, 0xDE, 0x5E, 0x0B, 0xDB,
    0xE0, 0x32, 0x3A, 0x0A, 0x49, 0x06, 0x24, 0x5C, 0xC2, 0xD3,
    0xAC, 0x62, 0x91, 0x95, 0xE4, 0x79, 0xE7, 0xC8, 0x37, 0x6D,
    0x8D, 0xD5, 0x4E, 0xA9, 0x6C, 0x56, 0xF4, 0xEA, 0x65, 0x7A,
    0x08, 0xAE, 0xBA, 0x78, 0x25, 0x2E, 0x1C, 0xA6, 0xB4, 0xC6,
    0xE8, 0xDD, 0x74, 0x1F, 0x4B, 0xBD, 0x8B, 0x8A, 0x70, 0x3E,
    0xB5, 0x66, 0x48, 0x03, 0xF6, 0x0E, 0x61, 0x35, 0x57, 0xB9,
    0x86, 0xC1, 0x1D, 0x9E, 0xE1, 0xF8, 0x98, 0x11, 0x69, 0xD9,
    0x8E, 0x94, 0x9B, 0x1E, 0x87, 0xE9, 0xCE, 0x55, 0x28, 0xDF,
    0x8C, 0xA1, 0x89, 0x0D, 0xBF, 0xE6, 0x42, 0x68, 0x41, 0x99,
    0x2D, 0x0F, 0xB0, 0x54, 0xBB, 0x16
]
inv_sbox = [0] * 0x100
for i in range(256):
	line = (sbox[i]&0xf0)>>4
	rol = sbox[i]&0xf
	inv_sbox[(line*16)+rol] = i
```


求aes解密，[aes加密](https://www.cnblogs.com/starf/p/3657546.html)


```python
from Crypto.Cipher import ARC4
rc4_key = ''
rc4_key_table = [0xA5, 0x88, 0x81, 0x81, 0x82, 0xCD, 0x8B, 0x9F, 0x82, 0x80, 0xCD, 0xAE, 0xC6, 0xC6, 0xED]
for i in range(len(rc4_key_table)):
	rc4_key += chr(rc4_key_table[i]^0xED)
print(rc4_key.encode('utf-8'))
enc_table = [	0x18, 0x77, 0xE9, 0x84, 0x72, 0x3B, 0x71, 0x0F, 0xC8, 0x84, 0x5C, 0x2E, 0x92, 0x38,
				0x03, 0x19, 0x33, 0x74, 0x73, 0x79, 0x00, 0x88, 0x59, 0x0B, 0x7C, 0x38, 0x67, 0x63,
				0xA6, 0x4E, 0x8F, 0x3D
]
enc_xor = [	0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F,
			0x10, 0x11, 0x12, 0x13, 0x14, 0x15, 0x16, 0x17, 0x18, 0x19, 0x1A, 0x1B, 0x1C, 0x1D, 0x1E, 0x1F
]
enc = []
for i in range(len(enc_table)):
	enc.append(enc_table[i]^enc_xor[i]^0x50)
print(enc)
print(rc4_key.encode('utf-8')[:-1])
rc4 = ARC4.new(rc4_key.encode('utf-8')[:-1])
enc = list(rc4.decrypt(bytes(enc)))
sbox = [
    0x7C, 0xF2, 0x63, 0x7B, 0x77, 0x6B, 0x6F, 0xC5, 0x30, 0x01,
    0x67, 0x2B, 0xFE, 0xD7, 0xAB, 0x76, 0xCA, 0x82, 0xC9, 0x7D,
    0xFA, 0x59, 0x47, 0xF0, 0xAD, 0xD4, 0xA2, 0xAF, 0x9C, 0xA4,
    0x72, 0xC0, 0xB7, 0xFD, 0x93, 0x26, 0x36, 0x3F, 0xF7, 0xCC,
    0x34, 0xA5, 0xE5, 0xF1, 0x71, 0xD8, 0x31, 0x15, 0x04, 0xC7,
    0x23, 0xC3, 0x18, 0x96, 0x05, 0x9A, 0x07, 0x12, 0x80, 0xE2,
    0xEB, 0x27, 0xB2, 0x75, 0x09, 0x83, 0x2C, 0x1A, 0x1B, 0x6E,
    0x5A, 0xA0, 0x52, 0x3B, 0xD6, 0xB3, 0x29, 0xE3, 0x2F, 0x84,
    0x53, 0xD1, 0x00, 0xED, 0x20, 0xFC, 0xB1, 0x5B, 0x6A, 0xCB,
    0xBE, 0x39, 0x4A, 0x4C, 0x58, 0xCF, 0xD0, 0xEF, 0xAA, 0xFB,
    0x43, 0x4D, 0x33, 0x85, 0x45, 0xF9, 0x02, 0x7F, 0x50, 0x3C,
    0x9F, 0xA8, 0x51, 0xA3, 0x40, 0x8F, 0x92, 0x9D, 0x38, 0xF5,
    0xBC, 0xB6, 0xDA, 0x21, 0x10, 0xFF, 0xF3, 0xD2, 0x0C, 0xCD,
    0x13, 0xEC, 0x5F, 0x97, 0x44, 0x17, 0xC4, 0xA7, 0x7E, 0x3D,
    0x64, 0x5D, 0x19, 0x73, 0x60, 0x81, 0x4F, 0xDC, 0x22, 0x2A,
    0x90, 0x88, 0x46, 0xEE, 0xB8, 0x14, 0xDE, 0x5E, 0x0B, 0xDB,
    0xE0, 0x32, 0x3A, 0x0A, 0x49, 0x06, 0x24, 0x5C, 0xC2, 0xD3,
    0xAC, 0x62, 0x91, 0x95, 0xE4, 0x79, 0xE7, 0xC8, 0x37, 0x6D,
    0x8D, 0xD5, 0x4E, 0xA9, 0x6C, 0x56, 0xF4, 0xEA, 0x65, 0x7A,
    0x08, 0xAE, 0xBA, 0x78, 0x25, 0x2E, 0x1C, 0xA6, 0xB4, 0xC6,
    0xE8, 0xDD, 0x74, 0x1F, 0x4B, 0xBD, 0x8B, 0x8A, 0x70, 0x3E,
    0xB5, 0x66, 0x48, 0x03, 0xF6, 0x0E, 0x61, 0x35, 0x57, 0xB9,
    0x86, 0xC1, 0x1D, 0x9E, 0xE1, 0xF8, 0x98, 0x11, 0x69, 0xD9,
    0x8E, 0x94, 0x9B, 0x1E, 0x87, 0xE9, 0xCE, 0x55, 0x28, 0xDF,
    0x8C, 0xA1, 0x89, 0x0D, 0xBF, 0xE6, 0x42, 0x68, 0x41, 0x99,
    0x2D, 0x0F, 0xB0, 0x54, 0xBB, 0x16
]
inv_sbox = [0] * 0x100
for i in range(256):
	line = (sbox[i]&0xf0)>>4
	rol = sbox[i]&0xf
	inv_sbox[(line*16)+rol] = i

s = [[sbox[j * 16 + i] for i in range(16)] for j in range(16)]

#字节代换s盒
def s_box(input_matrix):
    output_matrix = [[0 for i in range(0, 4)] for _ in range(0, 4)]
    for i in range(0, 4):
        for j in range(0, 4):
            x = int(bin(input_matrix[i][j])[2:].zfill(8)[:4], 2)
            y = int(bin(input_matrix[i][j])[2:].zfill(8)[4:], 2)
            output_matrix[i][j] = s[x][y]
    return output_matrix


def inv_sbox_box(input_matrix):
    output_matrix = [[0 for i in range(0, 4)] for _ in range(0, 4)]

    for i in range(0,4):
        for j in range(0,4):
            # x = int(bin(input_matrix[i][j])[2:].zfill(8)[:4],2)
            # y = int(bin(input_matrix[i][j])[2:].zfill(8)[4:],2)
            output_matrix[i][j] = sbox.index(input_matrix[i][j])
    return output_matrix

#定义密钥扩展所需的T函数
def T(vector,time):
    Rcon = [0x01, 0x02, 0x04, 0x08, 0x10, 0x20, 0x40, 0x80, 0x1b, 0x36]
    vec = [0, 0, 0, 0]
    for i in range(0, 4):
        vec[i] = vector[(i+1)%4]

    for j in range(0, 4):
        x = int(bin(vec[j])[2:].zfill(8)[:4], 2)
        y = int(bin(vec[j])[2:].zfill(8)[4:], 2)
        vec[j] = s[x][y]
    vec[0] ^= Rcon[time-1]
    return vec

#密钥扩展,默认参数为字节类型
#此处将初始密钥的的每一列化作矩阵的“行”，即矩阵的每一行都是都密钥的列
def key_extend(init_key):
    w = [[0 for m in range(0, 4)] for _ in range(0, 44)]
    for i in range(0, 4):
        for j in range(0, 4):
            w[i][j] = init_key[i * 4 + j]
    time = 1
    for row in range(4, 44):
        if row % 4 != 0:
            for yuan in range(0,4):
                w[row][yuan] = w[row-4][yuan] ^ w[row-1][yuan]
        else:
            t_w = T(w[row-1], time)
            time += 1
            for yuan in range(0,4):
                w[row][yuan] = w[row-4][yuan] ^ t_w[yuan]
    return w


#行位移
def row_move(input_matrix):
    im = input_matrix
    output_matrix = [[0 for i in range(0, 4)] for _ in range(0, 4)]
    for i in range(0,4):
        for j in range(0,4):
            output_matrix[i][j] = input_matrix[i][(j+i)%4]
    return output_matrix


#逆行位移
def re_row_move(input_matrix):
    im = input_matrix
    output_matrix = [[0 for i in range(0, 4)] for _ in range(0, 4)]
    for i in range(0,4):
        for j in range(0,4):
            output_matrix[i][j] = input_matrix[i][(j-i)%4]
    return output_matrix


#有限域运算模块
def GF_calculate(num1,num2):
    sum = 0
    num1 = bin(num1)[2:].zfill(8)
    num2 = bin(num2)[2:].zfill(8)
    #0~14次方结果直接记录在表里
    table = [0b00000001, 0b00000010, 0b00000100, 0b00001000, 0b00010000, 0b00100000, 0b01000000, 0b10000000,
             0b11011, 0b110110, 0b1101100, 0b11011000, 0b10101101, 0b1000111, 0b10001110]


    for i in range(0,8):
        for j in range(0,8):
            if int(num1[i]) == 1 and int(num2[j]) == 1:
                index_value = (7-i) + (7-j)
                sum ^= table[index_value]
    return sum


#列混合
def col_mix(input_matrix):
    col_m = [[2,3,1,1],[1,2,3,1],[1,1,2,3],[3,1,1,2]]
    im = input_matrix
    output_matrix = [[0 for i in range(0,4)] for _ in range(0,4)]
    for i in range(0,4):
        for j in range(0,4):
            output_matrix[i][j] = GF_calculate(col_m[i][0],im[0][j])^GF_calculate(col_m[i][1],im[1][j])^\
                       GF_calculate(col_m[i][2],im[2][j])^GF_calculate(col_m[i][3],im[3][j])
    return output_matrix


#逆列混合
def re_col_mix(result_matrix):
    col_m = [[0x0e, 0x0b, 0x0d, 0x09],[0x09, 0x0e, 0x0b, 0x0d],[0x0d, 0x09, 0x0e, 0x0b],[0x0b, 0x0d, 0x09, 0x0e]]
    im = result_matrix
    output_matrix = [[0 for i in range(0, 4)] for _ in range(0, 4)]
    for i in range(0,4):
        for j in range(0,4):
            output_matrix[i][j] = GF_calculate(col_m[i][0],im[0][j])^GF_calculate(col_m[i][1],im[1][j])^\
                       GF_calculate(col_m[i][2],im[2][j])^GF_calculate(col_m[i][3],im[3][j])

    return output_matrix

def m_to_matrix(m):
    m_matrix = [[0 for i in range(0, 4)] for i in range(0, 4)]
    num = 0
    for j in range(0, 4):
        for i in range(0, 4):
            m_matrix[i][j] = m[num]
            num += 1
    return m_matrix

def matrix_to_m(matrix):
    m = []
    for j in range(0, 4):
        for i in range(0, 4):
            m.append(matrix[i][j])
    return bytes(m)

def matrix_print(m):
    for i in range(4):
        for j in range(4):
            print(hex(m[i][j])[2:].zfill(2), end=" ")
        print("")

# RoundKey
def add_roundkey(m, k):
    m_matrix = [[0 for i in range(0, 4)] for i in range(0, 4)]
    for i in range(4):
        for j in range(4):
            m_matrix = m[i][j] ^k[i][j]
    return m_matrix

# Transpose
def matrix_T(m):
    m_matrix = [[0 for i in range(0, 4)] for i in range(0, 4)]
    for i in range(4):
        for j in range(4):
            m_matrix[i][j] = m[j][i]
    return m_matrix


m = 'abcdefghijklmn-+'
key = b'1234567891234567'



def encrypt(key,m):
    w = key_extend(key)
    m_matrix = m_to_matrix(m)
    # print("init mat:")
    # matrix_print(m_matrix)

    for i in range(0, 4):
        for j in range(0, 4):
            #print("%x ^ %x = %x" % (m_matrix[j][i] , w[i][j], m_matrix[j][i] ^ w[i][j]))
            m_matrix[j][i] ^= w[i][j]
            

    # print("first round key:")
    # matrix_print(m_matrix)

    for loop_time in range(1, 10):
        # print("round:", loop_time)
        m_matrix = s_box(m_matrix)
        # print("sbox:")
        # matrix_print(m_matrix)

        m_matrix = row_move(m_matrix)
        # print("row_move:")
        # matrix_print(m_matrix)

        m_matrix = col_mix(m_matrix)
        # print("col_mix:")
        # matrix_print(m_matrix)

        for i in range(0, 4):
            for j in range(0, 4):
                m_matrix[j][i] ^= w[4 * loop_time + i][j]
               
        # print("round key:")
        # matrix_print(m_matrix)
        

    # 第10轮
    m_matrix = s_box(m_matrix)
    m_matrix = row_move(m_matrix)

    # print("last round key:")
    # matrix_print(m_matrix)



    for i in range(0, 4):
        for j in range(0, 4):
            m_matrix[j][i] ^= w[i+4*10][j]

    print("result:")
    matrix_print(m_matrix)
    return matrix_to_m(m_matrix)


def decrypt(cipher,key):
    cipher_matrix = m_to_matrix(cipher)
    w = key_extend(key)
    #初始轮密钥加
    for i in range(0, 4):
        for j in range(0, 4):
            cipher_matrix[i][j] ^= w[40+j][i]

    #9轮操作
    for looptime in range(1, 10):
        cipher_matrix = re_row_move(cipher_matrix)
        cipher_matrix = inv_sbox_box(cipher_matrix)
        for i in range(0, 4):
            for j in range(0, 4):
                cipher_matrix[i][j] ^= w[40 - looptime * 4 + j][i]
        cipher_matrix = re_col_mix(cipher_matrix)
    #第10轮操作
    cipher_matrix = re_row_move(cipher_matrix)
    cipher_matrix = inv_sbox_box(cipher_matrix)
    for i in range(0, 4):
        for j in range(0, 4):
            cipher_matrix[i][j] ^= w[j][i]
    msg = matrix_to_m(cipher_matrix)
    return msg

test_case = b'N@\x00l-S\xe6\xe9\xf9=\xe0c\x9a\x9c\x0bf'
print("明文为：", decrypt(test_case, key))
enc_data = b'1234567890qwertyuiopasdfghjklzxc'
enc1 = enc_data[:16]
iv = bytes([0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F])
iv_t = bytes([0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F])
#key = bytes([0x66, 0x6C, 0x67, 0x00, 0x09, 0x00, 0x00, 0x09, 0x00, 0x00, 0x00, 0x09, 0x00, 0x00, 0x00, 0x00])

key = bytes([0x54, 0x72, 0x61, 0x63, 0x65, 0x72, 0x50, 0x69, 0x64, 0x3A, 0x09, 0x30, 0x0A, 0x66, 0x6C, 0x67])


data = bytes(enc_data)

dec_data = bytes(enc)
import binascii
result = bytearray()
for i in range(len(data) // 16):
    tmp = bytearray(data[i * 16: i * 16 + 16])
    print(i, binascii.b2a_hex(iv))
    for x in range(16):
        tmp[x] ^= iv[x]

    res = encrypt(key, tmp)
    result += res
    iv = bytearray(res)

data = bytearray(dec_data)
result = bytearray()
for i in range(len(data) // 16 - 1, -1, -1):
    tmp = bytearray(data[i * 16: i * 16 + 16])
    if i != 0:
        iv = bytearray(data[i * 16 - 16: i * 16])
    else:
        iv = bytearray(iv_t)

    res = bytearray(decrypt(tmp, key))
    print(i, binascii.b2a_hex(iv))
    for x in range(16):
        res[x] ^= iv[x]
    result += res
print(result)
# bytearray(b'4be37a96e27e98c}wmctf{e78ce1a3ac')
# wmctf{e78ce1a3ac4be37a96e27e98c}
```




## MISC
### Car 
找V2x的配置文件
只有`v2x_misc.conf`打开不是明文，寻找其解密函数 (aes)，进行解密。
```python
from Crypto.Cipher import AES
import binascii
key = binascii.a2b_hex('89860918700319839632').ljust(32,b'\x00')
cip = AES.new(key,AES.MODE_CBC)
text = open('v2x_misc.conf','rb').read()
print(cip.decrypt(text))

# flag=wmctf{tb0x_s3curity_is_fun}
```


