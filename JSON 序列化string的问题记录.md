# 问题
在go中使用JSON序列化string时,出现了`239 191 189`这个奇怪的东西.

# 追踪
1. go 里面的string其实就是[]byte，里面维护着一堆byte.
2. go 里面JSON序列化string时，会按照utf-8解码，如果发现非法表示的，则会用`REPLACEMENT CHARACTER`(239 192 189)这个大小替换．

## 示例
```go
b = []byte{8,192,149}
s = string(b)
// JSON Marshal
// Got {8 239 191 189 239 191 189}
```

# 结论
如果你选择用json来序列化string时，注意你的string是可utf-8正常解释的，如果是string([]byte{...})这样子，很容易会出错．

# 延伸

## UTF-8 编码
### 结构
UTF-8使用一至六个字节为每个字符编码（2003年11月UTF-8被RFC 3629重新规范，只能使用原来Unicode定义的区域，U+0000到U+10FFFF，也就是说最多四个字节）
1. 128个US-ASCII字符只需一个字节编码（Unicode范围由U+0000至U+007F）。
2. 带有附加符号的拉丁文、希腊文、西里尔字母、亚美尼亚语、希伯来文、阿拉伯文、叙利亚文及它拿字母则需要两个字节编码（Unicode范围由U+0080至U+07FF）。
3. 其他基本多文种平面（BMP）中的字符（这包含了大部分常用字，如大部分的汉字）使用三个字节编码（Unicode范围由U+0800至U+FFFF）。

在U+0080的以下字符都使用内含其字符的单字节编码。这些编码正好对应7比特的ASCII字符。在其他情况，有可能需要多达4个字符组来表示一个字符。这些多字节的最高有效比特会设置成1，以防止与7比特的ASCII字符混淆，并保持标准的字节主导字符串运作顺利。也就是说：
```
在 0-127范围: utf-8 表示为　0zzzzzzz(z可以是０或者是１)
在 128-范围: utf-8 表示肯定是 1xxx 1xxx(开头，最高有效位都是１)
```

## Base64
如果需要对binary的数据进行传输，可以用base64或者16进制进行string,然后传输．
