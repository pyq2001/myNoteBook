# 一、C基础知识

```c
// stdio.h 标准输入输出头文件
#include <stdio.h>  // 包含头文件 stdio.h   
int main() 
// c程序的执行入口，一个工程可以有多个源程序但有且只有一个main函数
{
	printf("%s\n","Hello World!"); // 格式化输出
	return 0;
}
```

<img src="../image/C语言基础/image-20220515184821987.png" alt="image-20220515184821987" style="zoom: 50%;" />



## 进制转换

### 二进制数转十进制数:

1、先将二进制数按位权展开，再对其乘积相加，转换过程如下所示。

(10110)<sub>2</sub> = (1 \* 2<sup>4</sup> + 0 \* 2<sup>3</sup> + 1 \* 2<sup>2</sup> + 1 \* 2<sup>1</sup>  + 0 \* 2<sup>0</sup>  =  22 )<sub>10</sub>

2、8421法( 128  64  32  16  8  4  2  1 )

(1011 1101)<sub>2</sub> ---> ( 128 + 0 + 32 + 16 + 8 + 4 + 0 + 1 = 189 )<sub>10</sub>

### 十进制转二进制

二进制：0 和 1

八进制：0 ~ 7

十进制：0 ~ 9

十六进制：0 ~ 9 A B C D E F （a b c d e f 大小写均可）

1、除于2逆向取余法：除于2，逆向取余，直到商为零结束

(37)<sub>10</sub> ---> (100101)<sub>2</sub> 

  <img src="../image/C语言基础/image-20220517123618237.png" alt="image-20220517123618237" style="zoom: 50%;" />

2、逆转8421法               (128  64  32  16  8  4  2  1)

(37 = 32 + 4 + 1)<sub>10</sub>  ---> ( 0      0    1    0    0  1  0  1  )<sub>2</sub>

### 八进制转二进制

每1个八进制位转换为3个二进制位，左边不足3个则补零

( 567 )<sub>8</sub>  --->  (101  110  111)<sub>2</sub>

( 234 )<sub>8</sub>  --> ( 010   011  100 )<sub>2</sub>

### 二进制转八进制

从右向左，每3个二进制位为一组，每一组转换成一个八进制位

( 011  010  111  100  110 )<sub>2</sub>   --->    ( 3 2 7 4 6 )<sub>8</sub>

( 011  010  111  101  110 )<sub>2</sub>   --->    ( 3 2 7 5 6 )<sub>8</sub>

( 011  101  110  101  011 )<sub>2</sub>   --->    ( 3 5 6 5 3 )<sub>8</sub>

### 十六进制转二进制

每1个十六进制转成4个二进制位，左边不足4个则补0

( 109CBE )<sub>16</sub>   --->    ( 0001  0000  1001  1100  1011  1110 )<sub>2</sub>

( 5A7DF8 )<sub>16</sub>   --->    ( 0101  1010  0111  1101  1111  1000 )<sub>2</sub>

## 原码、补码、反码

数据在计算机中都是以==补码==的形式存储

- 正数的原码、反码、补码都是一样的!
- 符号位: 最左边的位即最高位，1表示负数，0表示正数。
- 负数的反码: 在原码的基础上“符号位不变，数值位取反”
- 负数的补码: 在反码的基础上“符号位不变，数值位加1”

​            原码         -->      补码     -->       反码     

17：   00010001 --> 00010001 --> 00010001

-17 ： 10010001 --> 11101110 --> 11101111

# 二、变量及数据类型

变量的概念: ==值可以发生改变的量==。

C语言的数据类型分为: ==基本类型==和==构造类型==两种

<img src="../image/C语言基础/image-20220601215537117.png" alt="image-20220601215537117" style="zoom: 50%;" />

## 字符型

- 特殊的整型，占据==1个字节==(1byte---8bit)
- 有符号字符型==signed char==/==char==: 最高位（最左边）为符号位取值范围: 1000 0000(-128) ---- 0111 1111 (127 )
- 无符号字符型==unsigned char==:  没有符号位，都是数值位取值围:  0000 0000(0) --- 1111 1111(255)

## 整型

- 有符号的整型==signed  int==/==int==，占据==4byte== ( 32位)

1000 0000 0000 0000 0000 0000 0000 0000 --- 0111 1111 1111 1111 1111 1111 1111 1111

- 无符号的整型==unsigned  int==，占据==4byte==

0000 0000 0000 0000 0000 0000 0000 0000---1111 1111 1111 1111 1111 1111 1111 1111

- ==short  int==: 占据==2byte==

1000 0000 0000 0000(-32768)---0111 1111 1111 1111(32767)

- ==unsigned short int==: 占据==2byte==

00000 0000 0000 0000(0)---1111 1111 1111 1111(65535)

- ==long  int==: 占据==4byte==
- ==long long int== : 占据==8byte==

## 浮点型

没有无符号的浮点型数据

==float==: 单精度浮点型，占据==4byte==

==double==: 双精度浮点型，占据==8byte==
==long double==: 长双精度浮点型，占据8byte或16byte

## 取值范围

![image-20220601224112718](../image/C语言基础/image-20220601224112718.png)

## 变量的定义及使用

命名规则： 变量名以字母、数字、下划线构成且第一个字符不能是数字，要符合标识符的命名规范，且不能是系统占用的单词，如关键字int、预定义标识符printf等。

变量先定义后使用

定义变量会在内存中开辟出一块该类型大小的空间。若变量未赋值时输出该变量则会输出是该空间上的原本的未知数。

# 三、运算符

<table border="1" cellpadding="0" cellspacing="0" style="width:612px;"><tbody><tr><td> <p><strong>优先级</strong></p> </td><td> <p><strong>运算符</strong></p> </td><td> <p><strong>名称或含义</strong></p> </td><td> <p><strong>使用形式</strong></p> </td><td> <p><strong>结合方向</strong></p> </td><td> <p><strong>说明</strong></p> </td></tr><tr><td rowspan="4"> <p><strong>1</strong></p> </td><td> <p><strong>[]</strong></p> </td><td> <p>数组下标</p> </td><td> <p>数组名[常量表达式]</p> </td><td rowspan="4"> <p>左到右</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>()</strong></p> </td><td> <p>圆括号</p> </td><td> <p>(表达式）/函数名(形参表)</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>.</strong></p> </td><td> <p>成员选择（对象）</p> </td><td> <p>对象.成员名</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>-&gt;</strong></p> </td><td> <p>成员选择（指针）</p> </td><td> <p>对象指针-&gt;成员名</p> </td><td> <p>--</p> </td></tr><tr><td colspan="6"> <p></p> </td></tr><tr><td rowspan="9"> <p><strong>2</strong></p> </td><td> <p><strong>-</strong></p> </td><td> <p>负号<a href="https://so.csdn.net/so/search?q=%E8%BF%90%E7%AE%97%E7%AC%A6&amp;spm=1001.2101.3001.7020" title="运算符">运算符</a></p> </td><td> <p>-表达式</p> </td><td rowspan="9"> <p><strong>右到左</strong></p> </td><td rowspan="7"> <p>单目运算符</p> </td></tr><tr><td> <p><strong>~</strong></p> </td><td> <p>按位取反运算符</p> </td><td> <p>~表达式</p> </td></tr><tr><td> <p><strong>++</strong></p> </td><td> <p>自增运算符</p> </td><td> <p>++变量名/变量名++</p> </td></tr><tr><td> <p><strong>--</strong></p> </td><td> <p>自减运算符</p> </td><td> <p>--变量名/变量名--</p> </td></tr><tr><td> <p><strong>*</strong></p> </td><td> <p>取值运算符</p> </td><td> <p>*指针变量</p> </td></tr><tr><td> <p><strong>&amp;</strong></p> </td><td> <p>取地址运算符</p> </td><td> <p>&amp;变量名</p> </td></tr><tr><td> <p><strong>!</strong></p> </td><td> <p>逻辑非运算符</p> </td><td> <p>!表达式</p> </td></tr><tr><td> <p><strong>(类型)</strong></p> </td><td> <p>强制类型转换</p> </td><td> <p>(数据类型)表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>sizeof</strong></p> </td><td> <p>长度运算符</p> </td><td> <p>sizeof(表达式)</p> </td><td> <p>--</p> </td></tr><tr><td colspan="6"> <p></p> </td></tr><tr><td rowspan="3"> <p><strong>3</strong></p> </td><td> <p><strong>/</strong></p> </td><td> <p>除</p> </td><td> <p>表达式/表达式</p> </td><td rowspan="3"> <p>左到右</p> </td><td rowspan="3"> <p>双目运算符</p> </td></tr><tr><td> <p><strong>*</strong></p> </td><td> <p>乘</p> </td><td> <p>表达式*表达式</p> </td></tr><tr><td> <p><strong>%</strong></p> </td><td> <p>余数（取模）</p> </td><td> <p>整型表达式%整型表达式</p> </td></tr><tr><td rowspan="2"> <p><strong>4</strong></p> </td><td> <p><strong>+</strong></p> </td><td> <p>加</p> </td><td> <p>表达式+表达式</p> </td><td rowspan="2"> <p>左到右</p> </td><td rowspan="2"> <p>双目运算符</p> </td></tr><tr><td> <p><strong>-</strong></p> </td><td> <p>减</p> </td><td> <p>表达式-表达式</p> </td></tr><tr><td rowspan="2"> <p><strong>5</strong></p> </td><td> <p><strong>&lt;&lt;&nbsp;</strong></p> </td><td> <p>左移</p> </td><td> <p>变量&lt;&lt;表达式</p> </td><td rowspan="2"> <p>左到右</p> </td><td rowspan="2"> <p>双目运算符</p> </td></tr><tr><td> <p><strong>&gt;&gt;&nbsp;</strong></p> </td><td> <p>右移</p> </td><td> <p>变量&gt;&gt;表达式</p> </td></tr><tr><td colspan="6"> <p></p> </td></tr><tr><td rowspan="4"> <p><strong>6</strong></p> </td><td> <p><strong>&gt;&nbsp;</strong></p> </td><td> <p>大于</p> </td><td> <p>表达式&gt;表达式</p> </td><td rowspan="4"> <p>左到右</p> </td><td rowspan="4"> <p>双目运算符</p> </td></tr><tr><td> <p><strong>&gt;=</strong></p> </td><td> <p>大于等于</p> </td><td> <p>表达式&gt;=表达式</p> </td></tr><tr><td> <p><strong>&lt;&nbsp;</strong></p> </td><td> <p>小于</p> </td><td> <p>表达式&lt;表达式</p> </td></tr><tr><td> <p><strong>&lt;=</strong></p> </td><td> <p>小于等于</p> </td><td> <p>表达式&lt;=表达式</p> </td></tr><tr><td rowspan="2"> <p><strong>7</strong></p> </td><td> <p><strong>==</strong></p> </td><td> <p>等于</p> </td><td> <p>表达式==表达式</p> </td><td rowspan="2"> <p>左到右</p> </td><td rowspan="2"> <p>双目运算符</p> </td></tr><tr><td> <p><strong>！=</strong></p> </td><td> <p>不等于</p> </td><td> <p>表达式!= 表达式</p> </td></tr><tr><td colspan="6"> <p></p> </td></tr><tr><td> <p><strong>8</strong></p> </td><td> <p><strong>&amp;</strong></p> </td><td> <p>按位与</p> </td><td> <p>表达式&amp;表达式</p> </td><td> <p>左到右</p> </td><td> <p>双目运算符</p> </td></tr><tr><td> <p><strong>9</strong></p> </td><td> <p><strong>^</strong></p> </td><td> <p>按位异或</p> </td><td> <p>表达式^表达式</p> </td><td> <p>左到右</p> </td><td> <p>双目运算符</p> </td></tr><tr><td> <p><strong>10</strong></p> </td><td> <p><strong>|</strong></p> </td><td> <p>按位或</p> </td><td> <p>表达式|表达式</p> </td><td> <p>左到右</p> </td><td> <p>双目运算符</p> </td></tr><tr><td> <p><strong>11</strong></p> </td><td> <p><strong>&amp;&amp;</strong></p> </td><td> <p>逻辑与</p> </td><td> <p>表达式&amp;&amp;表达式</p> </td><td> <p>左到右</p> </td><td> <p>双目运算符</p> </td></tr><tr><td> <p><strong>12</strong></p> </td><td> <p><strong>||</strong></p> </td><td> <p>逻辑或</p> </td><td> <p>表达式||表达式</p> </td><td> <p>左到右</p> </td><td> <p>双目运算符</p> </td></tr><tr><td colspan="6"> <p></p> </td></tr><tr><td> <p><strong>13</strong></p> </td><td> <p><strong>?:</strong></p> </td><td> <p>条件运算符</p> </td><td> <p>表达式1?</p> <p>表达式2: 表达式3</p> </td><td> <p><strong>右到左</strong></p> </td><td> <p>三目运算符</p> </td></tr><tr><td colspan="6"> <p></p> </td></tr><tr><td rowspan="11"> <p><strong>14</strong></p> </td><td> <p><strong>=</strong></p> </td><td> <p>赋值运算符</p> </td><td> <p>变量=表达式</p> </td><td rowspan="11"> <p><strong>右到左</strong></p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>/=</strong></p> </td><td> <p>除后赋值</p> </td><td> <p>变量/=表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>*=</strong></p> </td><td> <p>乘后赋值</p> </td><td> <p>变量*=表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>%=</strong></p> </td><td> <p>取模后赋值</p> </td><td> <p>变量%=表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>+=</strong></p> </td><td> <p>加后赋值</p> </td><td> <p>变量+=表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>-=</strong></p> </td><td> <p>减后赋值</p> </td><td> <p>变量-=表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>&lt;&lt;=</strong></p> </td><td> <p>左移后赋值</p> </td><td> <p>变量&lt;&lt;=表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>&gt;&gt;=</strong></p> </td><td> <p>右移后赋值</p> </td><td> <p>变量&gt;&gt;=表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>&amp;=</strong></p> </td><td> <p>按位与后赋值</p> </td><td> <p>变量&amp;=表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>^=</strong></p> </td><td> <p>按位异或后赋值</p> </td><td> <p>变量^=表达式</p> </td><td> <p>--</p> </td></tr><tr><td> <p><strong>|=</strong></p> </td><td> <p>按位或后赋值</p> </td><td> <p>变量|=表达式</p> </td><td> <p>--</p> </td></tr><tr><td colspan="6"> <p></p> </td></tr><tr><td> <p><strong>15</strong></p> </td><td> <p><strong>，</strong></p> </td><td> <p>逗号运算符</p> </td><td> <p>表达式,表达式,…</p> </td><td> <p>左到右</p> </td><td> <p>--</p> </td></tr></tbody></table>

# 四、算法的基本结构

## 顺序结构

按语句书写的先后顺序依次执行的结构。

复合语句: 由大括号中的0个或多个声明和语句列表共同构成。

```c
int main()
{
	int a = 5;
	int b = 2;
	{
		int a = 10;
		printf("%d\n",a); // 10
		b++;
		printf("%d\n",b); // 3
	}
	printf("%d\n",a); // 5
	printf("%d\n",b);  // 3
	return 0;
}
```



## 选择结构（分支结构）

### if  /  else if  / else语句

```c
if(条件表达式1)
{
    语句1;
} 
else if(条件表达式2)
{
    语句2;
}
else if(条件表达式3)
{
    语句3;
}
else:
{
    语句4;
}
// 执行过程:依次判断条件表达式的值，当某个值为真时，则执行相应的语句，然后跳出整个if语句之外，继续执行后面的程序。如果所有的表达式都为假，则执行else语句。
```

### switch语句

```c
switch(表达式)
{
    case 常量表达式1: 语句1(集合);
    case 常量表达式2: 语句2(集合);
    ...
    case 常量表达式n: 语句n(集合);
    default: 语句n+1;
}
// 执行过程:首先计算表达式的值，与常量表达式i进行比较，如果与其中一个常量表达式i的值相等，就执行其后的语句直到遇到break语句才结束switch 语句，如果case后无 break语句，则继续执行随后所有的case后的语句。如果没有找到与表达式的值相匹配的常量表达式，则执行default后的语句n+1。
```

### 条件运算符和条件运算表达式

`表达式1 ? 表达式2  : 表达式3;`

求值过程: 如果表达式1的值为真，则以表达式2的值作为条件表达式的值否则以表达式3的值作为条件表达式的值.

##  循环结构

### while 语句

```c
while(循环条件表达式)
{
    循环体语句;
}
// 执行过程:只有循环条件表达式的值为真就执行循环体语句，先判断后执行。
```

### do ... while 语句

```c
do
{
    循环体语句;
}while(循环条件表达式);
// 执行过程:先执行循环体语句，再检查循环条件表达式的值是否为真，如果为真则继续执行循环体语句，否则结束循环。,
```

### for 语句

```c
for(表达式1;表达式2;表达式3)
{
    循环体语句;
}
/*
说明:
表达式1:设置初始条件，只执行一次，为0个或多个变量设置初值。
表达式2:是循环条件表达式，用来判定是否继续循环。在每次执行循环体之前要先执行表达式2，然后再决定是否继续执行循环。
表达式3:作为循环的调整，比如是循环体变量增值，它是执行循环体语句之后再执行。
*/
```

### 中断语句

break语句: 跳出本层循环，执行循环后的语句。

continue语句: 跳出本次循环，执行下一次循环。

goto语句: 跳出到指定的标号位。

# 五、函数

## 函数概念

函数是完成==一个个特定任务的语句==集合，它能完成你所想要的某种特定任务，当你要用时，只需要调用它即可，在后续的修改或是维护过程中，只需要针对这—个进行修改即可。

使用函数的好处:

1. 减少程序的代码量
2. 代码复用
3. 使程序具有良好的结构

## 函数的定义

函数的定义∶包括两个部分，分别是“函数头”和“函数体”。

```c
返回值数据 类型函数名(数据类型 形参1,数据类型 形参2...)//函数头(
	//函数体（可以有多条语句)
}
```

函数定义需要注意的问题：

1. 函数的返回值和参数可以是任意类型，包括空类型!!!当函数无返回值或无形参列表时，建议用void指明
2. 函数名是一种标识符，必须符合标识符的命名规则!建议单词首字母不大写。
3. 函数的参数可以没有，也可以有多个参数
4. ==一个项目中必须要有一个主函数且只有一个==，没有主函数，程序找不到执行的入口，代码无法运行。
5. 函数头要有头注释(功能，参数，返回值)，函数定义不可以嵌套。

6. 函数如果不被调用，就不会执行

函数的声明函数声明的好处∶

1. 函数列表更加一目了然，方便使用。
2. 便于多人合作共同开发。
3. 可以用于商业，保护知识产权(只将调用方式告诉他人，但是函数中具体的实现算法隐藏）。

声明的方式∶只保留函数头且后面加分号,通常放在包含头文件的下面。

例子：

```c
#include <stdio.h>

void fun1(); // 声明函数
int fun2(int a,int b,char c);

// 主函数
int main(void)
{
	int res;
	fun1(); // 调用函数
	fun1();
	res = fun2(3,2,'+');
	printf("res = %d\n",res);
	return 0;
}

// 定义函数
void fun1(void)
{
	printf("**********\n");
	printf("==========\n");
}

int fun2(int a,int b,char c)
{
	int res;
	switch(c)
	{
		case '+': res = a + b;break;
		case '-': res = a - b;break;
		case '*': res = a * b;break;
		case '/': res = a / b;break;
		default: printf("请从+、-、*、/中选择运算符\n");
	}
	return res;
}
```

# 六、数组

数组概念:

- 类型相同的数据元素的集合。
- 这个类型可以是基本的数据类型，也可以是"构造类型"
- 这些数据元素只能顺序的存放在内存的某段区域。

## 一维数组

### 数组定义：

数据类型  数组名1[EXP],数组名2[EXP],...;

注意：

1. 数组的长度: EXP表示数组元素的个数即"数组的长度"
2. EXP为整型常量表达式，不能包含变量。整型常量表达式: 只能是字面值组合而成的表达式
3. 数组的长度和类型最好用宏定义的形式

```c
#include <stdio.h>

//宏定义，在编译之前会将所有SIZE替换为5
#define SIZE 5

int main()
{
	int a = 5;
	int arr1[5];
	int arr2[a + 1]; // 错误，不能包含变量 
	// 使用宏定义方便对代码的修改
	int arr3[SIZE];
	int arr4[SIZE];
	return 0;
}
```

数据类型  数组名[数组元素个数] = {第一个元素值，第二个元素的值.…};

### 数组的初始化：

```c
int main()
{
	int arr1[5] = {1,2,3,4,5}; //全部赋初值
	int arr2[5] = {1,2}; // 部分赋初值，未赋值的元素默认为0
	int arr3[] = {1,2,3,4,5}; // 省略长度赋初值,数组的长度根据初值个数决定
    int arr4[5]; // 未赋初值，arr4z中的元素不是零，而是未知数
	return 0;
}
```

注意：

初值的个数不能大于数组的长度，否则会产生运行时错误。

每个初值的类型最好与数组的类型—致。

### 数组的存储

数组可以用来存储多个数据，数组占用多个连续的内存空间，这些内存空间用来存储数据。

注意：

1. 保存数组所需字节单元的个数与数组的类型和长度有关，总字节数= sizeof(数组类型)*数组长度
2. 数组长度的计算可以采用sizeof的方式完成

```c
int main()
{
	int arr[5] = {1,2,3,4,5}; 

	printf("arr占的总字节数 = %d\n",sizeof(int)*5); // 输出数组arr占据的字节数
	printf("arr占的总字节数 = %d\n",sizeof(arr));
	printf("arr的长度 = %d\n",sizeof(arr) / sizeof(int)); // 输出arr的长度
    printf("arr占的总字节数 = %d\n",sizeof(arr[0])); // 输出数组中元素所占的字节
	return 0;
}
```

### 数组的引用

引用普通变量时，直接使用变量名；而引用数组元素时，使用数组名[下标]。

注意:  如有数组int arr[N];测数组的下标为:0~N-1，即最大的下标为数组长度-1。

```c

#define SIZE 5

int main()
{
	int i;
	int arr[SIZE]; 
	for(i = 0;i < SIZE;i++)
	{
		printf("请输入数组的第%d个元素:",i+1);
		scanf("%d",&arr[i]);
	}

	for(i = 0;i < SIZE;i++)
	{
		printf("数组的第%d个元素为%d:\n",i+1,arr[i]);
	}
}
```

### 数组作为函数参数

数组作为函数参数可以理解为形参数组和实参数组共享—块内存空间，形参数组的长度可以省略,编译器不会检查。

```c
void fun(int arr[])
{
	arr[2] = 11111;
}
int main()
{
	int a[] = {1,2,3,4,5};
	printf("第三个元素: %d\n",a[2]); // 3
	fun(a);
	printf("第三个元素: %d\n",a[2]); // 11111
}
```

