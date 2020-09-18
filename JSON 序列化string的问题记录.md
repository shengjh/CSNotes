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

