---
layout: post
title:  "Return value of a function in Shell"
date:   2016-07-27 00:33:06 +0800
categories: stuff
---

## Shell获取返回值的几种方法

### 1. 全局变量

```
fooFunc() {
    RESULT="hello"
}

fooFunc
echo $RESULT
```

### 2. 子程序调用，把结果写到标准输出

```
fooFunc() {
    echo "value"
}

GREAT=`fooFunc`
echo $GREAT
```

> 这种有个问题，假设在子函数中多次echo，读到的值不是预期的

### 3. 使用函数返回值返回

```
fooFunc() {
    return 2;
}

echo $? # 输出2
```

> PS:这种方式的输出值只能是**[0-255]**

## 总结

个人认为还是第二种比较合理，只要合理的echo，其实打印信息本身也应该收敛，而不是在函数内部输出
