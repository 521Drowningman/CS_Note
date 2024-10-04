# 基础语法

## 输入

```rust
 // 这里是创建了一个空字符串
let mut input = String::new();  // rust 中的标准输入读取的是字符串;使用和String类型关联的new()函数,由此创建字符串实例
// io::stdin()创建了句柄(指向),返回一个 Stdin 类型的实例，这个实例就是一个句柄。这和文件标识符很像
io::stdin().read_line(&mut input).expect("读取输入失败");
```

