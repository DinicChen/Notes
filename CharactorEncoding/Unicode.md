# Unicode的历史
一切得从ASCII码讲起。1960年，美国人依照自己的语言发明了 **ASCII** ，全称American Standard Code for Information Interchange。这套只支持英语系字符的编码规范，使用1个字节的前7位映射128个字符，其中33个控制字符和95个可显示字符（大小写字母各26个，10个阿拉伯数字，以及其他标点符号）。

![ASCII][ASCII]

ASCII码无法在信息的存储和传输中表示英语系以外的字符，所以各个国家地区陆续推出了自己的编码规范。既然世界上存在着多种编码方式，那么同一个二进制数字就可以被解释成不同的符号。因此，要想打开一个文本文件，就必须知道它的编码方式，否则用错误的编码方式解读，就会出现乱码。按照这个形势发展下去，势必严重影响计算机科学的长远发展，每推出一个字符集都会增加一个历史包袱。为了解决这个问题，人们尝试寻找一套统一的字符集标准，统一这个混乱的局面。可以想象，如果有一种编码将世界上所有的字符都纳入其中，每一个字符都给予一个独一无二的编码，那么乱码问题就会消失。

于是同时有两个组织开始研究这件事情：

1. 国际标准化组织ISO推出了ISO/IEC 10646标准，该标准定义了一个统一的字符集：Universal Multiple-Octet Coded Character Set，简称UCS。它涵盖两种编码方式：UCS-2和UCS-4，分别使用16bit和32bit存储一个字符。UCS-4等价于UTF-32，但是UCS-2和UTF-16存在一定差别，UCS-2只是UTF-16的子集。

2. 美国一个软件制造商协会推出一套统一字符集：Unicode。最开始它是设计为一个字符存储于2个字节的编码方式，也就是UTF-16（现在的UTF-16不再仅仅只支持2个字节了）。

这两个协会后来发现了对方都在做同样的事，而人类无需两套字符集，于是这两家机构合并了彼此的研究成果，诞生了现在的 **Unicode**。
Unicode有一句口号:

> enabling people around the world to use computers in any language。

Unicode的规范规定，使用U+前缀加上一个十六进制整数表示一个字符，比如U+0041表示大写拉丁字母A。整个Unicode的字符集，需要U+000000到U+10FFFF的存储空间，一共使用了21个bit（1使用1个bit，0、F都要4个bit），共有1114112个位置（000000-0FFFFF共2^20 = 1,048,576，100000-10FFFF共2^16 = 65,536）。前面提到BMP平面内U+D800到U+DFFF是保留区域，所以Unicode可用位置为1114112 - 2048 = 1112064个。

# Unicode平面映射
Unicode的编码空间可以划分为17个 ***平面*** （plane），每个平面包含2^16（65,536）个码位。平面的码位可表示为从U+xx0000到U+xxFFFF，其中xx表示十六进制值从0x00到0x10，共计17个。第一个平面称为 **基本多语言平面** （Basic Multilingual Plane, BMP），或称第零平面（Plane 0）。其他平面称为 **辅助平面** （Supplementary Planes）。其中，BMP平面内从U+D800到U+DFFF之间的码位区块是永久保留不映射到Unicode字符。UTF-16就利用保留下来的0xD800到0xDFFF区段的码位来对辅助平面的字符的码位进行编码。

![Unicode planes][Unicode planes]

第0平面：

![plane 0][plane 0]

第1平面：

![plane 1][plane 1]

第2平面：

![plane 2][plane 2]

第14平面：

![plane 14][plane 14]

目前大部分Unicode处于前三个平面内，空白方格表示未使用的码位。第0平面包含了绝大部分我们日常能见到的字符，包括各种常见语言；第0平面还有个Surrogate区域，主要用于扩展UTF-16，显示在16bit编码空间之外的字符。第1平面包含很多历史字符，以及emoji字符。第2平面包含了很多汉字。第14平面使用了很少一部分。第15、16平面用于私有空间。

# UTF、UCS的关系
这里需要先分清两个概念： **字符集** 以及 **编码方式** 。

> 字符集：“收集一系列字符，然后规定每个字符映射到某个整数上”。<br>
编码方式：“就规定这些映射字符的整数，应该如何在计算机中以二进制的形式存储或传输”。

Unicode是一个字符集，它只规定了符号的二进制代码，却没有规定这个二进制代码应该如何存储。这里就有两个问题：

1. 如何才能区别Unicode和ASCII？计算机怎么知道三个字节表示一个符号，而不是分别表示三个符号呢？
2. 英文字母只用一个字节表示就够了，如果Unicode统一规定，每个符号用三个或四个字节表示，那么每个英文字母前都必然有二到三个字节是0，这对于存储来说是极大的浪费。

这些问题就要编码方式来解决。Unicode的官方规范定义了三种编码方式：**UTF-8**、**UTF-16**、**UTF-32**。很多地方默认Unicode编码对应的就是UTF-16编码。

在介绍三种UTF编码方式之前，我们先讲讲另一个相关的概念，也是一个历史问题，就是UCS。Unicode最开始是合并了ISO创建的UCS字符集，而这个UCS字符集存在两种编码方式，分别是UCS-2和UCS-4，它们分别占用2个字节和4个字节。从Unicode 1.1开始，已经废弃了UCS-2编码方式，而被UTF-16代替，UCS-2只能描述2个字节内的字节，超过两个字节则不能描述，而作为UCS-2超集的UTF-16则可以。

UTF-8、UTF-16、UTF-32这三种编码方式，其中UTF-32是定长（4个字节）的编码方式，而UTF-8和UTF-16是变长的编码方式。UTF-8最开始的定义是可以使用1至6个字节代表一个字符的，后来改为1至4个字节，而UTF-16则可以使用2个或者4个字节。

|Encoding|Word size(bits)|Unicode support|
|:-|:-|:-|
|UCS-2|16|BMP only|
|UCS-4|32|full|
|UTF-8|8|full|
|UTF-16|16|full|
|UTF-32|32|full|

# UTF-8实现原理
UTF-8的编码规则可以概括为二条：

1. 单字节的字符，字节的第一位设为0，后面7位为这个符号的Unicode码。因此对于英语字母，UTF-8编码和ASCII码是相同的。
2. 对于n字节(n = 2,3,4)的字符，第一个字节的前n位都设为1，第n+1位设为0，后面字节的前两位一律设为10。剩下的没有提及的二进制位，全部为这个符号的unicode码。

所以，UTF-8的字符编码区间如下表格，x位是有效的二进制位，它们拼接起来就是对应的Unicode，表中的1和0，只是用于描述当前字符占用几个字节。

|字节数|最小值|最大值|Byte 1|Byte 2|Byte 3|Byte 4|
|:-|:-|:-|:-|:-|:-|:-|
|1|U+0000|U+007F|0xxxxxxx||||			
|2|U+0080|U+07FF|110xxxxx|10xxxxxx|||		
|3|U+0800|U+FFFF|1110xxxx|10xxxxxx|10xxxxxx||
|4|U+10000|U+1FFFFF|11110xxx|10xxxxxx|10xxxxxx|10xxxxxx|

举两个例子：

希伯来语字母aleph（א）的Unicode代码是U+05D0（二进制0000 0101 1101 0000），属于U+0080到U+07FF区域，使用双字节的格式：110xxxxx 10xxxxxx。从最后一个二进制位开始，依次从后向前填入格式中的x，多出的位在高位补0。：11010111 10010000。最后结果用十六进制写起来就是0xD7 0x90，这就是aleph（א）的UTF-8编码。

汉字“啊”的Unicode是U+554A（二进制0101 0101 0100 1010），属于U+0800到U+FFFF区域，使用3字节格式：1110xxxx 10xxxxxx 10xxxxxx。从最后一个二进制位开始，依次从后向前填入格式中的x，多出的位在高位补0：11100101 10010101 10001010，转换成十六进制就是0xE5958A。

# UTF-16的实现原理
Unicode的编码空间从U+000000到U+10FFFF，可以将其分为U+0000到U+FFFF和U+10000到U+10FFFF两个区间。而U+0000到U+FFFF区间内有一个部分U+D800到U+DFFF是用于代理区域的（Surrogate area），这个区域的码位是永久保留，不映射到Unicode字符。UTF-16就利用保留下来的0xD800-0xDFFF区段的码位来对第1平面的字符（U+10000到U+10FFFF）的码位进行编码。

## 从U+0000至U+D7FF以及从U+E000至U+FFFF的码位
这个是BMP平面去掉代理区域U+D800到U+DFFF剩下的区域。UTF-16使用两个字节编码这个范围内的码位，UCS-2也可以编码这个区域的Unicode字符，这个区域是UTF-16和UCS-2唯一的交集。

## 从U+10000到U+10FFFF的码位
辅助平面中的码位，在UTF-16中被编码为32bit，4个字节。

编码算法：
辅助平面的码位（U+10000到U+10FFFF）减去0x10000，得到的值的范围U+0到U+FFFFF，最多占20bit长，我们将20bit长分为两部分：

1. 高位的10bit的值（值的范围为0到0x3FF）被加上0xD800得到第一个码元（值的范围是0xD800到0xDBFF），称作 **高位代理** （high surrogate）。
2. 低位的10bit的值（值的范围是0到0x3FF）被加上0xDC00得到第二个码元（值的范围是0xDC00到0xDFFF），称作 **低位代理** （low surrogate）。
3.将高位代理和低位代理结合起来得到的四个字节，就是最终的Unicode编码。

例如U+10440编码（������）:

0x10440减去0x10000，结果为0x00440，二进制为0000 0000 0100 0100 0000。
分区它的上10位值和下10位值（使用二进制）:0000000001 and 000100000。
添加0xD800到上值，以形成高位：0xD800 + 0x0001 = 0xD801。
添加0xDC00到下值，以形成低位：0xDC00 + 0x0040 = 0xDC40。

所以U+10440的UTF-16编码是0xD801 0xDC40。

## 为什么4个字节的UTF-16编码需要去使用一个代理区域，不直接映射到4个字节上？
如果直接用普通的方式将辅助平面的码位（U+10000到U+10FFFF）映射到4个字节，而BMP平面（从U+0000至U+FFFF）继续使用2个字节，那么，最终在解析UTF-16的文件时，我们就无法知道字符之间的边界了，不知道哪里是使用2个字节表示一个字符，哪里是使用4个字节表示一个字符，但是，借助了代理区域之后，我们会发现，上面的低位代理和高位代理，这两个值的范围分别是0xD800到0xDBFF和0xDC00到0xDFFF，而它们总的范围就是0xD800到0xDFFF，刚好就是代理区域的区间，所以，4个字节的UTF-16，高位两个字节范围处于代理区域，而低位两个字节范围是不占用代理区域的。至此，我们就可以很容易识别出UTF-16的2个字节的码位和4个字节的码位。

这就是UTF-16中设计0xD800到0xDFFF这个代理区域的初衷。

# BOM
编码方式使用的最小字节组合称为 **码元**（Code Unit）。UTF-8的码元是1个字节，UTF-16的码元是2个字节，而UTF-32的码元是4个字节。码元大于一个字节则在存储和传输时就要考虑字节序的问题。字节序分两种，一种是 **大端** 方式（Big Endian），一种是 **小端** 方式（Little Endian）。这两个古怪的名称来自英国作家斯威夫特的《格列佛游记》。在该书中，小人国里爆发了内战，战争起因是人们争论吃鸡蛋时究竟是从大头（Big-endian）敲开还是从小头（Little-endian）敲开。为了这件事情，前后爆发了六次战争，一个皇帝送了命，另一个皇帝丢了王位。

字节序的大端方式规定，一个码元内的高位字节在前，低位字节在后；而小端方式规定，一个码元内的低位字节在前，高位字节在后。为了表示字节序，Unicode规范中规定UTF-16和UTF-32需要使用 **BOM** （byte order mark）描述字节序。BOM是一段添加在数据流开头的字符，这个字符的名字叫做 **零宽度非换行空格** （zero width no-break space），用`FEFF`表示。这正好是两个字节，而且FF比FE大1。如果一个文本文件的头两个字节是`FE FF`，就表示该文件采用大端方式；如果头两个字节是`FF FE`，就表示该文件采用小端方式。


|BOM|Encoding|Endian|
|:-|:-|:-|
|0xEF 0xBB 0xBF|UTF-8|endianless|
|0xFF 0xFE|UTF-16-LE|little endian|
|0xFE 0xFF|UTF-16-BE|big endian|
|0xFF 0xFE 0x00 0x00|UTF-32-LE|little endian|
|0x00 0x00 0xFE 0xFF|UTF-32-BE|big endian|

UTF-8的码元是以一个字节为单位，所以可以不BOM前缀，而且Unicode规范也推荐不要在UTF-8编码的内容添加BOM前缀，因为有些软件在解码时，会忘记处理UTF-8的BOM。

下表的例子说明UTF-16的大端方式和小端方式是如何存储数据的，其中一个例子是2字节的UTF-16字符，另一个是4个字节的UTF-16字符。需要注意一点，小端方式只是对同个码元内的字节倒序存储，而UTF-16的码元是2个字节，所以4个字节长的UTF-16是分为两半，每一半将内部的字节调到顺序。如果是UTF-32，由于它的码元是四个字符，则小端排序直接将4个字节颠倒顺序。

|字符|UTF-16|UTF-16小端|UTF-16大端|
|:-|:-|:-|:-|
|€:U+20AC|20AC|AC 20|20 AC|
|������:U+10437|D801 DC37|01 D8 37 DC|D8 01 DC 37|

另外，对于UTF-16和UTF-32，如果以BOM开头，则可以知道对应的字节序；但是如果没有BOM，则默认是big endian。

[ASCII]: https://upload.wikimedia.org/wikipedia/commons/thumb/4/4f/ASCII_Code_Chart.svg/1200px-ASCII_Code_Chart.svg.png
[Unicode planes]: https://www.w3.org/International/articles/definitions-characters/index-data/unicodeplanes.png
[plane 0]: https://upload.wikimedia.org/wikipedia/commons/8/8e/Roadmap_to_Unicode_BMP.svg
[plane 1]: https://upload.wikimedia.org/wikipedia/commons/0/00/Roadmap_to_the_Unicode_SMP.svg
[plane 2]: https://upload.wikimedia.org/wikipedia/commons/f/f9/Roadmap_to_Unicode_SIP.svg
[plane 14]: https://upload.wikimedia.org/wikipedia/commons/2/2c/Roadmap_to_Unicode_SSP.svg
