
Unicode 返回为 0 至 0x10FFFF,收录了当今几乎所有文字，每个文字映射为Unicode的一个码点。

注意U+FFFF也只能表示`16*16*16*16=65536`个字，所以不能容纳所有文字（2016年收录共约128237个），
所以需要扩展：

```
同学可能会发现，4 位的 16 进制数字只能表示 0 至 0xFFFF，但之前我们说 UCS 的码点是从 0 至 0x10FFFF，那怎么能表示多出来的码点？

其实，U+0000 至 U+FFFF 这组 Unicode 字符称为基本多文种平面（basic multilingual plane, BMP），还有另外 16 个平面。那么 BMP 以外的字符，JSON 会使用代理对（surrogate pair）表示 \uXXXX\uYYYY。在 BMP 中，保留了 2048 个代理码点。
如果第一个码点是 U+D800 至 U+DBFF，我们便知道它的代码对的高代理项（high surrogate），之后应该伴随一个 U+DC00 至 U+DFFF 的低代理项（low surrogate）。然后，我们用下列公式把代理对 (H, L) 变换成真实的码点
```


然而Unicode使用不太方便，为了将Unicode转为比较方便的字节，于是采用(Uniform Transformation Format)UTF，
来存储各个码点，比方说UTF-8，UTF-16和UTF-32，其中UTF-8最流行，为可变长编码。

对于 JSON字符串中的 \uXXXX 是以 16 进制表示码点 U+0000 至 U+FFFF，我们需要：

1.    解析 4 位十六进制整数为码点；
2.    由于字符串是以 UTF-8 存储，我们要把这个码点编码成 UTF-8。

也就是说U+XXXX是unicode格式，也是我们可见格式，转化为UTF-8的字节序列(char*)便于程序处理的格式，

UTF-8 成为现时互联网上最流行的格式，有几个原因：

1.    它采用字节为编码单元，不会有字节序（endianness）的问题。
2.    每个 ASCII 字符只需一个字节去储存。
3.    如果程序原来是以字节方式储存字符，理论上不需要特别改动就能处理 UTF-8 的数据。
