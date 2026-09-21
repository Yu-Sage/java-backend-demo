# Java语法入门学习笔记（第一篇）

> 从零开始学Java，整理了入门阶段的核心知识点和注意事项，适合初学者参考。

---

## 一、Java语言简介

Java是一门面向对象的编程语言，可用于后端开发、Android应用、大数据等领域。

Java最大的特点是**跨平台**——写一次代码，Windows、Mac、Linux都能运行，因为有JVM（Java虚拟机）帮你做翻译。

---

## 二、第一个Java程序

### Hello World

学编程都是从Hello World开始的，Java也不例外：

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

### 重点注意事项

> **⚠️ 必须记住：**
> 1. 一个源文件里只能有一个 `public` 修饰的类
> 2. 源文件名必须和 `public` 类名完全一样（大小写都不能差）
>    - 类名：`HelloWorld`
>    - 文件名：`HelloWorld.java`

---

## 三、怎么运行Java程序？

### 运行步骤

```
1. 写代码 → 存成 .java 文件
2. 编译 → 用 javac 生成 .class 文件
3. 运行 → 用 java 命令跑起来
```

```bash
javac HelloWorld.java   # 编译
java HelloWorld          # 运行
```

### JDK、JRE、JVM的关系

这三个一开始很容易搞混，用一张图看懂三者的嵌套关系：

```
JDK（开发工具包）
├── JRE（运行环境）
│   ├── JVM（虚拟机）—— 真正跑代码的地方
│   └── 核心类库 —— Java自带的工具包
└── 开发工具 —— 编译器javac、调试工具jconsole等
```

| 名词 | 作用 |
|------|------|
| **JDK** | 写Java程序必须装，里面啥都有，包含编译器和调试工具 |
| **JRE** | 只运行Java程序的话，装这个就行，包含JVM和核心类库 |
| **JVM** | 虚拟机，跨平台的关键，真正执行字节码的地方 |

> 💡 简单记：JDK ⊃ JRE ⊃ JVM
> 开发Java程序 → 装JDK
> 只运行Java程序 → 装JRE
> 跨平台的核心 → JVM

---

## 四、新手易错点汇总

初学Java时这些错误很常见，提前注意：

| 序号 | 错误类型 | 表现 |
|------|---------|------|
| 1 | 文件名后缀不对 | 存成了 .txt，不是 .java |
| 2 | 类名和文件名不一致 | 类名Hello，文件名World.java |
| 3 | main方法拼错 | 写成了mian |
| 4 | 类没加public | 程序找不到入口 |
| 5 | 语句没分号 | 每句代码后面要加 `;` |
| 6 | 用了中文分号 | 必须是英文 `;`，不是中文 `；` |
| 7 | 没配环境变量 | 系统提示javac不是内部命令 |

---

## 五、Java注释

注释是给人看的说明，编译器会忽略不执行。

### 三种注释方式

**1. 单行注释**（最常用）

```java
// 这是单行注释，解释当前这行代码
int a = 10;  // 定义整型变量a
```

**2. 多行注释**

```java
/*
 * 这是多行注释
 * 可以写好几行
 * 一般用来注释掉暂时不用的代码
 */
int b = 20;
```

**3. 文档注释**

```java
/**
 * 这是文档注释
 * 写在类或方法上面，描述功能
 * 可以用javadoc工具生成API文档
 */
public class Demo {
    /**
     * main方法，程序入口
     * @param args 命令行参数
     */
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

> ⚠️ 注意：多行注释不能嵌套使用。

---

## 六、标识符和关键字

### 标识符命名规则

标识符就是给类、方法、变量起的名字：

✅ **可以用：** 字母、数字、下划线 `_`、美元符号 `$`
❌ **不能用：** 数字开头、Java关键字
⚠️ **注意：** 严格区分大小写，`name` 和 `Name` 是两个不同的名字

> 💡 命名建议：类名用大驼峰 `HelloWorld`，变量名用小驼峰 `userName`

### 关键字

关键字是Java预留的特殊单词，比如 `public`、`class`、`static`、`int`、`if` 等，初学者先混个眼熟就行。

---

## 七、常量和变量

### 常量

常量就是不会变的值：

```java
public class Demo {
    public static void main(String[] args) {
        System.out.println("hello world!");  // 字符串常量
        System.out.println(100);              // 整数常量
        System.out.println(3.14);             // 小数常量
        System.out.println('A');              // 字符常量
        System.out.println(true);             // 布尔常量
    }
}
```

### 变量

变量就是会变的值，相当于一个盒子，里面装的东西可以换：

```java
public class Demo {
    public static void main(String[] args) {
        // 定义变量并赋值
        int a = 10;       // 定义变量a，装着10
        System.out.println(a);  // 输出10

        // 修改变量的值
        a = 100;          // 把a里面的值换成100
        System.out.println(a);  // 输出100

        // 其他类型变量
        double d = 3.14;
        char c = 'A';
        boolean b = true;

        System.out.println(d);
        System.out.println(c);
        System.out.println(b);

        // 一行定义多个同类型变量
        int a1 = 10, a2 = 20, a3 = 30;
        System.out.println(a1 + a2 + a3);  // 输出60

        // 先定义后赋值
        int x;
        x = 50;
        System.out.println(x);
    }
}
```

> ⚠️ 注意：
> - 变量使用前必须赋值，否则编译报错
> - 变量名一旦确定，类型就不能变了（Java是静态类型语言）
> - 同一个作用域内不能定义同名变量

---

## 八、8种基本数据类型

Java有8种基本数据类型，整理成表格方便记忆。

**记忆技巧：** 整型的范围可以用2的n次方来记，n是总位数减去1（因为最高位是符号位）：

**怎么算？**
- 占n字节 = 8n位
- 有符号整数：范围是 -2^(8n-1) ~ 2^(8n-1)-1
- 比如int占4字节 = 32位，就是 -2³¹ ~ 2³¹-1

**举个例子：**
- byte：1字节 = 8位 → -2⁷ ~ 2⁷-1 → -128 ~ 127
- short：2字节 = 16位 → -2¹⁵ ~ 2¹⁵-1 → -32768 ~ 32767
- int：4字节 = 32位 → -2³¹ ~ 2³¹-1 → 约±21亿
- long：8字节 = 64位 → -2⁶³ ~ 2⁶³-1 → 非常大

| 类型 | 关键字 | 占用字节 | 占多少位 | 范围（2的次方形式） | 具体数值 |
|------|--------|---------|---------|-------------------|---------|
| 字节型 | byte | 1字节 | 8位 | -2⁷ ~ 2⁷-1 | -128 ~ 127 |
| 短整型 | short | 2字节 | 16位 | -2¹⁵ ~ 2¹⁵-1 | -32768 ~ 32767 |
| 整型 | int | 4字节 | 32位 | -2³¹ ~ 2³¹-1 | 约±21亿 |
| 长整型 | long | 8字节 | 64位 | -2⁶³ ~ 2⁶³-1 | 非常大 |
| 单精度 | float | 4字节 | 32位 | 浮点数（IEEE 754标准） | 小数（精度较低） |
| 双精度 | double | 8字节 | 64位 | 浮点数（IEEE 754标准） | 小数（常用，精度高） |
| 字符型 | char | 2字节 | 16位 | 0 ~ 2¹⁶-1 | 0 ~ 65535 |
| 布尔型 | boolean | 1字节 | 1位 | 只有两个值 | true / false |

> 💡 平时用的话，记住int约±21亿就够了，其他的用到再查。

### 几个容易忘的点

- 整数默认是 `int`，小数默认是 `double`
- 长整型后面要加 `L`，比如 `long l = 100L;`
- 字符串 `String` 不是基本类型，是引用类型
- char可以存中文，因为Java用Unicode编码，一个字符占2字节

### 什么是字节？

字节是计算机存储容量的基本单位：

```
1字节(Byte) = 8位(bit)
1KB = 1024字节
1MB = 1024KB
1GB = 1024MB
```

> 💡 1GB内存大概能存多少？按纯文本算，大约能存5亿个汉字。

---

## 九、类型转换

### 自动转换（小的→大的）

小类型可以自动转成大类型，不会丢数据：

```
byte → short → int → long → float → double
```

```java
int a = 10;
long b = a;     // int自动转long，没问题
```

### 强制转换（大的→小的）

大类型转小类型要手动转，可能会丢精度：

```java
double d = 3.14;
int a = (int) d;  // 强制转，小数没了
System.out.println(a);  // 输出3
```

### 类型提升

```java
byte x = 10;
byte y = 20;
// byte z = x + y;  // 报错！byte相加会自动提升成int
int z = x + y;      // 这样才行
```

为什么？因为byte只有1字节，相加可能会超过范围，所以Java自动把它们提升成int再计算。

**再举几个例子：**

```java
byte a = 10;
byte b = 20;
int c = a + b;        // 正确，结果是int
byte d = (byte)(a + b);  // 强制转回来

short s = 100;
int i = s;            // 正确，short自动转int
```

---

## 十、字符串

字符串就是一串文字，用双引号括起来：

```java
public class Demo {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = " world";
        System.out.println(s1 + s2);  // 拼接，输出hello world
    }
}
```

### 字符串和数字互转

```java
// 数字转字符串
int num = 10;
String str1 = num + "";             // 方法一：拼接空字符串（最简单）
String str2 = String.valueOf(num);   // 方法二：调用valueOf方法（更规范）

// 字符串转数字
String s = "100";
int n = Integer.parseInt(s);
System.out.println(n + 1);  // 输出101
```

> ⚠️ 注意：字符串转数字的时候，字符串内容必须是合法的数字，否则会报错！比如 `"100a"` 转int就会出错。

**再举几个例子：**

```java
// 字符串拼接
String name = "张三";
int age = 20;
System.out.println("我叫" + name + "，今年" + age + "岁");
// 输出：我叫张三，今年20岁

// 其他类型转字符串
double d = 3.14;
String s1 = d + "";
System.out.println(s1);  // 输出3.14
```

---

## 总结

本文整理了Java入门阶段的核心知识点，主要包括：

- Java语言基础与跨平台原理
- 第一个Hello World程序及运行方式
- JDK、JRE、JVM三者的关系
- 常见编译错误与注意事项
- 三种注释方式与使用场景
- 标识符命名规则与关键字
- 常量与变量的定义及使用
- 8种基本数据类型及取值范围
- 类型转换规则与类型提升
- 字符串基本使用与互转

这些内容是Java编程的基础，掌握后才能继续学习流程控制、数组、面向对象等进阶内容。下一篇将继续学习运算符。

---

**Java入门学习笔记系列，持续更新中。**
