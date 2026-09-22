# Java语法入门学习笔记（第二篇）：运算符与流程控制

> 继续Java入门学习，这篇整理运算符和流程控制的核心知识点，适合初学者参考。

---

## 一、什么是运算符？

计算机最基本的用途之一就是执行数学运算，比如 `10 + 20`、`a < b` 这些。

其中的 `+`、`<` 就是**运算符**——对操作数进行操作的符号，不同运算符的含义不同。

Java提供了丰富的运算符，主要分为：
- 算术运算符（+ - * / %）
- 关系运算符（< > == !=）
- 逻辑运算符（&& || !）
- 位运算符（& | ~ ^）
- 移位运算符（<< >> >>>）
- 条件运算符（? :）

---

## 二、算术运算符

### 2.1 基本四则运算符：+ - * / %

```java
int a = 20;
int b = 10;

System.out.println(a + b);   // 30  加
System.out.println(a - b);   // 10  减
System.out.println(a * b);   // 200 乘
System.out.println(a / b);   // 2   除
System.out.println(a % b);   // 0   取模（余数）
```

### 重点注意事项

> **⚠️ 必须记住：**
> 1. 都是二元运算符，使用时必须有左右两个操作数
> 2. **int / int 结果还是int，会向下取整**（小数部分直接舍弃）
> 3. 做除法和取模时，右操作数不能为0
> 4. 两侧操作数类型不一致时，会向类型大的提升

**int除法向下取整的例子：**

```java
int a = 3;
int b = 2;
System.out.println(a / b);  // 输出1，不是1.5！

// 如果想要数学中的结果，需要转成double
double d = a * 1.0 / b;
System.out.println(d);  // 输出1.5
```

**除以0会报错：**

```java
int a = 1;
int b = 0;
System.out.println(a / b);
// 报错：ArithmeticException: / by zero
```

**类型提升的例子：**

```java
System.out.println(1 + 0.2);  // 输出1.2，int被提升为double
```

---

### 2.2 增量运算符：+= -= *= /= %=

这种运算符操作完成后，会把结果赋值给左操作数：

```java
int a = 1;

a += 2;   // 相当于 a = a + 2，输出3
a -= 1;   // 相当于 a = a - 1，输出2
a *= 3;   // 相当于 a = a * 3，输出6
a /= 3;   // 相当于 a = a / 3，输出2
a %= 3;   // 相当于 a = a % 3，输出2
```

> ⚠️ 注意：只有变量才能使用增量运算符，常量不能用！

---

### 2.3 自增/自减运算符：++ --

`++` 是给变量的值+1，`--` 是给变量的值-1。

**单独使用时，前置和后置没有区别：**

```java
int a = 1;
a++;    // 后置++，a变成2
++a;    // 前置++，a变成3
```

**混合使用时，区别就来了：**

```java
int a = 1;

System.out.println(a++);  // 后置++：先用原来的值，再+1 → 输出1
System.out.println(a);    // 输出2

System.out.println(++a);  // 前置++：先+1，再用新值 → 输出3
System.out.println(a);    // 输出3
```

> 💡 记忆口诀：
> - 后置++：先使用，后自增
> - 前置++：先自增，后使用

> ⚠️ 注意：只有变量才能用自增/自减，常量不能用！

---

## 三、关系运算符

关系运算符有六个：`== != < > <= >=`，计算结果是 `true` 或 `false`。

```java
int a = 10;
int b = 20;

System.out.println(a == b);   // false  相等
System.out.println(a != b);   // true   不相等
System.out.println(a < b);    // true   小于
System.out.println(a > b);    // false  大于
System.out.println(a <= b);   // true   小于等于
System.out.println(a >= b);   // false  大于等于
```

> ⚠️ 注意：
> - `=` 是赋值，`==` 是判断相等，别搞混了！
> - 不能连着写，比如 `3 < a < 5` 是错的，Java和数学不一样

---

## 四、逻辑运算符（重点）

逻辑运算符有三个：`&&`（与）、`||`（或）、`!`（非），结果都是boolean类型。

### 4.1 逻辑与 &&

**两个都为真，结果才为真；只要有一个假，结果就是假。**

```java
int a = 1;
int b = 2;

System.out.println(a == 1 && b == 2);   // true  左真且右真
System.out.println(a == 1 && b > 100);  // false 左真但右假
System.out.println(a > 100 && b == 2);  // false 左假但右真
System.out.println(a > 100 && b > 100); // false 左假且右假
```

### 4.2 逻辑或 ||

**只要有一个为真，结果就为真；两个都假才是假。**

```java
int a = 1;
int b = 2;

System.out.println(a == 1 || b == 2);   // true  左真且右真
System.out.println(a == 1 || b > 100);  // true  左真但右假
System.out.println(a > 100 || b == 2);  // true  左假但右真
System.out.println(a > 100 || b > 100); // false 左假且右假
```

### 4.3 逻辑非 !

**真变假，假变真。**

```java
int a = 1;
System.out.println(!(a == 1));   // false，a==1是true，取非变false
System.out.println(!(a != 1));   // true，a!=1是false，取非变true
```

### 4.4 短路求值（重点）

`&&` 和 `||` 遵守短路求值规则：

```java
System.out.println(10 > 20 && 10 / 0 == 0);  // 输出false，没有报错！
System.out.println(10 < 20 || 10 / 0 == 0);  // 输出true，没有报错！
```

为什么 `10 / 0` 没有报错？因为短路了！

> **⚠️ 短路规则：**
> - 对于 `&&`：左侧为false，结果一定是false，**右侧不执行**
> - 对于 `||`：左侧为true，结果一定是true，**右侧不执行**

而 `&` 和 `|` 不支持短路，会执行两侧：

```java
System.out.println(10 > 20 & 10 / 0 == 0);  // 报错！右侧执行了
```

---

## 五、位运算符

位运算是按二进制位进行计算的，计算机内部都是用二进制存储数据的。

位运算符有四个：`&`（与）、`|`（或）、`~`（取反）、`^`（异或）

### 5.1 按位与 &

**两个二进制位都是1，结果才是1，否则是0。**

```java
int a = 10;  // 二进制：01010
int b = 20;  // 二进制：10100
System.out.println(a & b);  // 输出0，按位与后没有同时为1的位
```

### 5.2 按位或 |

**两个二进制位都是0，结果才是0，否则是1。**

```java
int a = 10;  // 二进制：01010
int b = 20;  // 二进制：10100
System.out.println(a | b);  // 输出30，二进制11110
```

### 5.3 按位取反 ~

**0变1，1变0。**

```java
int a = 0xf;  // 十六进制，二进制0000...1111
System.out.printf("%x\n", ~a);  // 输出fffffff0
```

### 5.4 按位异或 ^

**相同为0，不同为1。**

```java
int a = 0x1;  // 二进制01
int b = 0x2;  // 二进制10
System.out.printf("%x\n", a ^ b);  // 输出3，二进制11
```

> 💡 小技巧：两个相同的数异或结果为0，可以用来交换两个变量的值。

---

## 六、移位运算符（了解）

移位就是把二进制数整体往左或往右移动，简单理解就是"搬家"。

移位运算符有三个：`<<`（左移）、`>>`（右移）、`>>>`（无符号右移）

### 6.1 左移 <<

**怎么移？左边的位丢掉，右边补0。**

举个简单的例子：

```
原数：5，二进制是 0101
左移1位：1010，变成10
左移2位：010100，变成20
```

```java
int a = 5;      // 二进制：0101
System.out.println(a << 1);  // 输出10，左移1位
System.out.println(a << 2);  // 输出20，左移2位
```

> 💡 **记忆方法：**
> - 左移1位 = 原数 × 2
> - 左移2位 = 原数 × 2 × 2 = 原数 × 4
> - 左移N位 = 原数 × 2的N次方

### 6.2 右移 >>

**怎么移？右边的位丢掉，左边补符号位（正数补0，负数补1）。**

举个简单的例子：

```
原数：20，二进制是 10100
右移1位：01010，变成10
右移2位：00101，变成5
```

```java
int a = 20;     // 二进制：10100
System.out.println(a >> 1);  // 输出10，右移1位
System.out.println(a >> 2);  // 输出5，右移2位
```

> 💡 **记忆方法：**
> - 右移1位 = 原数 ÷ 2
> - 右移2位 = 原数 ÷ 2 ÷ 2 = 原数 ÷ 4
> - 右移N位 = 原数 ÷ 2的N次方

### 6.3 无符号右移 >>>

**怎么移？右边的位丢掉，左边不管正负都补0。**

和右移的区别：右移负数时左边补1，无符号右移不管正负都补0。

```java
int a = -1;
System.out.println(a >>> 1);  // 无符号右移，左边补0
```

> ⚠️ 注意：
> 1. 移位效率比乘除高，乘除2的N次方时可以用移位代替
> 2. 初学者了解就行，实际开发用得不多

---

## 七、条件运算符

条件运算符是Java中唯一的三目运算符：

```
表达式1 ? 表达式2 : 表达式3
```

- 表达式1为true → 整个表达式的值是表达式2
- 表达式1为false → 整个表达式的值是表达式3

```java
// 求两个数的最大值
int a = 10;
int b = 20;
int max = a > b ? a : b;
System.out.println(max);  // 输出20
```

> ⚠️ 注意：
> 1. 表达式2和表达式3的结果要同类型（除非能隐式转换）
> 2. 表达式不能单独存在，结果必须被使用

---

## 八、运算符优先级

运算符是有优先级的，比如 `*` 和 `/` 比 `+` 和 `-` 优先级高。

**不用刻意记优先级，有歧义的地方加括号就行！**

```java
// 求a和b的平均值
int a = 10;
int b = 20;

// 错误写法：+优先级高于>>，先算a+(b-a)再右移
int c = a + (b - a) >> 1;

// 正确写法：加括号明确优先级
int c = a + ((b - a) >> 1);
```

---

## 九、流程控制

程序的执行流程主要有三种：
1. **顺序结构**：从上到下依次执行
2. **分支结构**：根据条件选择执行
3. **循环结构**：重复执行某段代码

---

## 十、分支结构

### 10.1 if语句

**语法格式1：单分支**

```java
if (布尔表达式) {
    // 条件为true时执行
}
```

**语法格式2：双分支**

```java
if (布尔表达式) {
    // 条件为true时执行
} else {
    // 条件为false时执行
}
```

**例子：判断成绩**

```java
int score = 92;
if (score >= 90) {
    System.out.println("优秀");
} else {
    System.out.println("继续加油");
}
```

**语法格式3：多分支**

```java
if (布尔表达式1) {
    // 语句1
} else if (布尔表达式2) {
    // 语句2
} else {
    // 语句3
}
```

**例子：成绩分级**

```java
int score = 85;
if (score >= 90) {
    System.out.println("优秀");
} else if (score >= 80) {
    System.out.println("良好");
} else if (score >= 70) {
    System.out.println("中等");
} else if (score >= 60) {
    System.out.println("及格");
} else {
    System.out.println("不及格");
}
```

### 10.2 if语句练习

**练习1：判断奇数偶数**

```java
int num = 10;
if (num % 2 == 0) {
    System.out.println("num是偶数");
} else {
    System.out.println("num是奇数");
}
```

**练习2：判断正数负数零**

```java
int num = 10;
if (num > 0) {
    System.out.println("正数");
} else if (num < 0) {
    System.out.println("负数");
} else {
    System.out.println("0");
}
```

**练习3：判断闰年**

```java
int year = 2000;
if (year % 100 == 0) {
    // 世纪闰年：能被400整除
    if (year % 400 == 0) {
        System.out.println("是闰年");
    } else {
        System.out.println("不是闰年");
    }
} else {
    // 普通闰年：能被4整除
    if (year % 4 == 0) {
        System.out.println("是闰年");
    } else {
        System.out.println("不是闰年");
    }
}
```

### 10.3 if语句注意事项

> **⚠️ 代码风格：**
> 推荐 `{` 放在 if/else 同一行，代码更紧凑

```java
// 推荐风格
if (x == 10) {
    // 语句1
} else {
    // 语句2
}
```

> **⚠️ 分号问题：**
> 多写分号会导致逻辑错误

```java
int x = 20;
if (x == 10); {  // 这里多了分号！
    System.out.println("hehe");
}
// 结果输出hehe，因为分号结束了if语句
```

> **⚠️ 悬垂else：**
> else会和最近的if匹配，建议都加大括号

---

### 10.4 switch语句

```java
switch (表达式) {
    case 常量值1:
        语句1;
        break;
    case 常量值2:
        语句2;
        break;
    default:
        语句3;
        break;
}
```

**执行流程：**
1. 先计算表达式的值
2. 和case依次比较，匹配就执行对应语句
3. 遇到break结束
4. 都不匹配执行default

**例子：**

```java
int day = 3;
switch (day) {
    case 1:
        System.out.println("星期一");
        break;
    case 2:
        System.out.println("星期二");
        break;
    case 3:
        System.out.println("星期三");
        break;
    default:
        System.out.println("其他");
        break;
}
```

> ⚠️ 注意：
> 1. 多个case后的常量值不能重复
> 2. switch括号内只能是：byte、char、short、int、String、枚举，**不能是long**
> 3. 不要忘记break，否则会穿透执行

---

## 十一、循环结构

### 11.1 while循环

```java
while (循环条件) {
    // 循环体
}
```

**例子：打印1到10**

```java
int i = 1;
while (i <= 10) {
    System.out.println(i);
    i++;
}
```

### 11.2 for循环

```java
for (表达式1; 表达式2; 表达式3) {
    // 循环体
}
```

- 表达式1：初始化循环变量，只执行一次
- 表达式2：循环条件
- 表达式3：循环变量更新

**例子：打印1到10**

```java
for (int i = 1; i <= 10; i++) {
    System.out.println(i);
}
```

### 11.3 do while循环

```java
do {
    // 循环体
} while (循环条件);
```

**特点：先执行一次循环体，再判断条件，至少执行一次。**

```java
int i = 1;
do {
    System.out.println(i);
    i++;
} while (i <= 10);
```

> ⚠️ 注意：do while最后的分号不要忘记！实际开发中用得少，推荐for和while。

---

### 11.4 break和continue的区别

这两个都是用来控制循环的，但作用完全不同：

| 关键字 | 作用 | 比喻 |
|--------|------|------|
| **break** | 结束整个循环，跳出循环体 | 看电影看到一半，直接走人，不看了 |
| **continue** | 跳过本次循环，进入下一次循环 | 看电影跳过这一段，继续看下一段 |

**break：结束整个循环**

```java
// 找到第一个偶数就停止，后面的都不看了
for (int i = 1; i <= 10; i++) {
    if (i % 2 == 0) {
        System.out.println("找到第一个偶数：" + i);
        break;  // 直接结束循环，i=2后面的都不执行了
    }
    System.out.println("当前数字：" + i);
}
// 输出：
// 当前数字：1
// 找到第一个偶数：2
```

**continue：跳过本次循环**

```java
// 只打印奇数，偶数跳过不打印
for (int i = 1; i <= 5; i++) {
    if (i % 2 == 0) {
        continue;  // 跳过本次，不执行下面的打印，直接进入下一次循环
    }
    System.out.println("奇数：" + i);
}
// 输出：
// 奇数：1
// 奇数：3
// 奇数：5
```

> 💡 **一句话总结：**
> - break：不干了，直接走人
> - continue：这次不干了，下次继续

---

## 总结

本文整理了Java运算符和流程控制的核心知识点：

**运算符部分：**
- 算术运算符：+ - * / %，注意int除法向下取整
- 增量运算符：+= -= *= /= %=
- 自增自减：++ --，注意前置和后置的区别
- 关系运算符：== != < > <= >=，结果是boolean
- 逻辑运算符：&& || !，重点掌握短路求值
- 位运算符：& | ~ ^，按二进制位计算
- 移位运算符：<< >> >>>，左移乘2右移除2
- 条件运算符：? :，唯一的三目运算符

**流程控制部分：**
- 顺序结构：从上到下执行
- 分支结构：if语句（单分支、双分支、多分支）、switch语句
- 循环结构：while、for、do while
- break结束循环，continue跳过本次循环

这些是Java编程的基础，掌握后才能继续学习数组、方法、面向对象等内容。下一篇将继续学习数组。

---

**Java入门学习笔记系列，持续更新中。**
