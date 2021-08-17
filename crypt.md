## [the cryptopals crypto challenges](https://cryptopals.com/)

### Crypto Challenge Set 1

#### 1. Convert hex to base64

```python
import base64
import binascii
text = binascii.a2b_hex('49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d')
plant = base64.b64encode(text)
print(plant.decode('utf-8'))

```

#### 2. Fixed XOR

```python
import binascii
import struct
text = binascii.a2b_hex('1c0111001f010100061a024b53535009181c')
m = binascii.a2b_hex('686974207468652062756c6c277320657965')
q = b''
for i in range(len(m)):
	q += (text[i]^m[i]).to_bytes(1,'big')
print(q)
print(binascii.b2a_hex(q))
```

#### 3. Single-byte XOR cipher

```python
import binascii
m = '1b37373331363f78151b7f2b783431333d78397828372d363c78373e783a393b3736'

m = binascii.a2b_hex(m)
for i in range(128):
	q = ''
	for j in range(len(m)):
		q += chr(m[j]^i)
	print(q)
	print(i)
key = 88
p = ''
for j in range(len(m)):
	p += chr(m[j]^key)
print(p)
```

#### 4. Detect single-character XOR

```python
import binascii
def check_str(str):
	table = list(range(97, 122)) + [32] + list(range(65,91)) + [10]
	for i in range(len(str)):
		if not ord(str[i]) in table:
			return False
	return True
def decrypt(m):
	for i in range(128):
		k = ''
		for j in range(len(m)):
			k += chr(m[j]^i)
		if check_str(k):
			print(k)
			break
f = open("4.txt")
line = f.readline()
while line:
	# m = bytes(line,encoding = "utf-8")[:-1]
	m = line.strip('\n')
	m = binascii.a2b_hex(m)
	# print(m)
	decrypt(m)
	line = f.readline()
f.close()

```

#### 5. Implement repeating-key XOR

```python
'''
Description: 
Autor: Emtanling
Date: 2021-07-07 19:03:10
LastEditors: Emtanling
LastEditTime: 2021-08-15 10:43:24
'''
import binascii
def xor(a, b):
    return [ x^y for (x,y) in zip(a, b)]
m = b"Burning 'em, if you ain't quick and nimble\nI go crazy when I hear a cymbal"
key = b'ICE'
key = key*(len(m)//len(key) + 1)
# print(key)
m = xor(m,key)
print(m)
for i in m:
    print(hex(i) ,end=' ')

```

#### 6. Break repeating-key XOR

* 对于异或加密，密文与密文异或的结果等于明文与明文异或的结果。
* 空格与小写字母异或结果为大写字母，与大写字母以后结果为小写字母。
* 大写字母与大写字母异或结果不为字母（小写与小写一样）。
* 大写字母与小写字母异或结果仍不为字母。

可以通过分块异或，算计出其 `hammingDistance`，选中最小的几个进行，进一步密钥爆破。

```python
'''
Autor: Emtanling
Date: 2021-08-15 10:46:36
LastEditors: Emtanling
LastEditTime: 2021-08-15 19:05:16
'''
import base64
import string
from itertools import combinations
def hammingDistance(a, b):
    if len(a) != len(b):
        raise ValueError("长度需要相等")
    return sum(bin(ord(x)^ord(y)).count('1') for (x, y) in zip(a, b))

'''
description: 猜测key长度，正常字母hamming值为2~3，任意字符为平均为4，所以越小，越正确
'''
keylist = []
def key_size(enc_stream):
	for size in range(2,41):
		blocks = []
		c = 1
		for i in range(0,len(enc_stream),size):
			c += 1
			blocks.append(enc_stream[i:i+size])
			if 4 == c:
				break
		part = combinations(blocks,2)
		sum = 0
		for (x,y) in part:
			sum += hammingDistance(x.decode('utf-8'),y.decode('utf-8'))
		dis = (sum / 6) / size
		key = {
			'keysize':size,
			'distance': dis
		}
		keylist.append(key)
	guess_key = sorted(keylist,key=lambda z:z['distance'],reverse=False)[0:3]
	return guess_key
def check_key(enc):
	table = string.ascii_letters + string.digits + '\n' + ' ' + '\'' + '.' + ',' + '-' + '?' + '!'
	if chr(enc) in table:
		return True
	else:
		return False
def get_key(keysize,enc_stream):
	key_plaint = ''
	for i in range(keysize):
		for j in range(128):
			for k in range(int(len(enc_stream)/keysize)-1):
				if not check_key(j^enc_stream[k*keysize + i]):
					break
				if k == int(len(enc_stream)/keysize) -2:
					key_plaint += chr(j)
					break
	if len(key_plaint) != keysize:
		return "keysize != {}".format(keysize)
	return "keysize = {}, keyplaint = \"{}\"".format(keysize, key_plaint)
def key_context(key_lenth,enc_stream):
	for i in key_lenth:
		key = get_key(i['keysize'],enc_stream)
		print(key)
print(hammingDistance("this is a test","wokka wokka!!!"))

m = open('6.txt').read()
m = base64.b64decode(m)
key_len = key_size(m)
key_context(key_len,m)
```

![](https://gitee.com/Emtanling/image/raw/master/img/20210815190641.png)

#### 7. AES in ECB mode

```python
'''
Description: 
Autor: Emtanling
Date: 2021-08-15 19:09:22
LastEditors: Emtanling
LastEditTime: 2021-08-15 19:26:38
'''
"""
ECB没有偏移量
"""
import base64
from Crypto.Cipher import AES
with open('7.txt') as fh:
    ciphertext = base64.b64decode(fh.read())

key = b'YELLOW SUBMARINE'
cipher = AES.new(key, AES.MODE_ECB)
plaintext = cipher.decrypt(ciphertext)
print(plaintext)
```

#### 8. Detect AES in ECB mode

找到由 `AES-ECB`加密的字符

```python
'''
Description: 
Autor: Emtanling
Date: 2021-08-15 19:27:41
LastEditors: Emtanling
LastEditTime: 2021-08-15 19:42:37
'''

def detect_ebc(data):
    chunks = [data[i:i+16*2] for i in range(0, len(data), 16*2)]
    return not (len(set(chunks)) == len(chunks))

with open('8.txt', 'r') as f:
    data = [line.strip() for line in f]
for i in data:
    if detect_ebc(i):
        print(i)
```

### Crypto Challenge Set 2

#### 9. Implement PKCS#7 padding

```python
'''
Description: 
Autor: Emtanling
Date: 2021-08-16 08:51:43
LastEditors: Emtanling
LastEditTime: 2021-08-16 13:46:51
'''
def pkcs7_padding(message, block_size):
	if len(message) >= block_size:
		return message.encode('utf-8')
	else:
		return (message.encode('utf-8') + (block_size - len(message)) * chr(block_size-len(message)).encode('utf-8'))
m = "YELLOW SUBMARINE"
s = 20
print(pkcs7_padding(m,s))
```

#### 10. Implement CBC mode

```python

```

