---
layout: post
title:  "PHP Traits"
date:   2017-01-20 10:17:06 +0800
categories: php
---

# 写在前面

本文是2017的第一篇文章，希望写博客的好习惯能够真正养成。

# What is Traits

Traits是单继承语言的一种重用机制，这种特性怎么能在PHP(**世界最好的语言**)缺席呢?Traits在Ruby中叫**Mixins**（crispm提醒）,感觉这个名字更容易理解。Traits能够使用户更方便的使用不在一个类层级的独立类，同时避免多继承和Mixins的诡异问题（函数重名）。

# PHP Traits

## Eg1
```
<?php

trait helloTraitA {
    function sayHelloA() {
    }
}

trait helloTraitB {
    function sayHelloB() {
    }
}

class World {
    use helloTraitA, helloTraitB; // 引用Trait
}

$o = new World();
$o->sayHelloA();
$o->sayHelloB();
```

可能你还觉得上面的例子没有说明什么，那么你看看传统方法的实现:

```
class HelloA {
    public static function sayHelloA() {
    }
}

class HelloB {
    public static function sayHelloB() {
    }
}

class World {
    public function sayHelloA() {
        HelloA::sayHelloA();
    }
    public function sayHelloB() {
        HelloA::sayHelloB();
    }
}

$o = new World();
$o -> sayHelloA();
$o -> sayHelloB();
```

> 如果不用Trait,面向对象的代码可能会很丑陋

## 使用场景

究竟哪些场景适合做成Trait呢？

- 外部工具
