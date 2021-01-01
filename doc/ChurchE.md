# 十分钟魔法练习：丘奇编码

### By 「玩火」

> 前置技能：λ演算

## Intro

众所周知， λ 演算是一个图灵完备的计算模型，它能计算任何图灵机能算的东西。那么很显然它也能表示任何我们平时所用的 C 、 Java 能表示的数据结构。虽然这听起来挺不可思议的，毕竟 λ 演算中本身只有变量、函数定义、函数应用三种结构。

信息的编码大概是计算机科学中最为接近魔法的内容，凝结了最强的人类的智慧结晶。同一个量的不同表现形式，同构、抽象与组合都让人感到惊叹不已。

为了方便起见，这里引入一个语法糖 let 绑定（let-binding）来**命名**表达式：

```
x = E
...后续代码
```

它解糖（Desugar）后等价于：

```
(λ x. ...后续代码) E
```

## 布尔

通常来说丘奇编码（Church Encoding）的布尔表达为：

```
true  = λ x. λ y. x
false = λ x. λ y. y
```

理论上这两个量的定义互相替换后和这种表达也是同构的，不过通常来说大家约定这种表示因为它更符合直觉。

实际上定义了布尔以后并不需要定义 if ，布尔量本身就可以接替 if 的作用，只需要将 if 的两个分支应用上去：

```
(boolValue thenTodo elseTodo)
```

如果`boolValue`是`true`那么求值就会得到`thenTodo`否则会得到`elseTodo`。

我们不需要 if ，这很神奇。不过为了语义考虑也可以定义一个没有实际意义的 if ：

```
if = λ x. λ a. λ b. (x a b)
```

这样 `if true a b` 就可以得到 `a` ， `if false a b` 就可以得到 `b` 。

## 自然数

皮亚诺构造（Peano Construct）是目前普遍使用的自然数定义。简单来说， 0 用 Z 表示， n 用 n 个 S 和一个 Z 表示。比如 3 就是 SSSZ 。而皮亚诺构造的加法就相当于把一个数的 Z 换成另一个数，就比如 3+3 就是 SSS(SSSZ) 。乘法就相当于把一个数的每个 S 换成另一个数的 S 部分，比如 3*3 就是 (SSS)(SSS)(SSS)Z 。

而这在 λ 演算中可以表示为：

```
0 = λ f. λ x. x
3 = λ f. λ x. f (f (f x))
```

这样的表示方法叫丘奇数（Church number），非常类似于皮亚诺构造。实际上，它是和皮亚诺构造同构的。

丘奇数的加法和乘法很简单，加法只需要把 x 替换成另一个数就好了，乘法只需要把f替换成另一个数就好了：

```
+ = λ m. λ n. (λ f. λ x. m f (n f x))
* = λ m. λ n. (λ f. λ x. m (n f) x)
```

而某种程度上来说，一个自然数就是固定次数的循环，以 x 为初始值，把 f 循环执行 n 遍。比如 m*n 就相当于把 m 循环累加加 n 次。

我们不需要 for ，这很神奇。

## 元组

终于到了数据结构部分， λ 表达式保存数据的原理是把参数全部放在一个接受一个提取器的函数里面：

```
pair   = λ a. λ b. λ f. f a b
first  = λ p. p (λ x. λ y. x)
second = λ p. p (λ x. λ y. y)
```

这样就可以保证 `first (pair x y)` 始终等于 `x` 而 `second (pair x y)` 始终等于 `y` 。其中 `λ x. λ y. x` 和 `λ x. λ y. y` 就是提取器函数。

进一步讲，把元组串起来就可以变成列表，比如：

```
list' = pair a1 (pair a2 (pair a3 ...))
```

而如果列表分叉就成了树：

```
tree' = pair (pair a1 a2) (pair a3 a4)
```

我们用函数构造出了数据结构，这很神奇。