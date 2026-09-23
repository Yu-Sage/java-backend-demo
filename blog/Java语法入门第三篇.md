# Java语法入门学习笔记（第三篇）：输入输出与方法

> 继续Java入门学习，这篇整理输入输出和方法的核心知识点，适合初学者参考。

---

## 一、输出

Java中常用的输出方式有三种：`println`、`print`、`printf`

### 1.1 println：输出并换行

```java
System.out.println("hello");  // 输出hello，然后换行
System.out.println("world");  // 输出world，然后换行
```

输出结果：
```
hello
world
```

### 1.2 print：输出不换行

```java
System.out.print("hello");  // 输出hello，不换行
System.out.print("world");  // 输出world，不换行
```

输出结果：
```
helloworld
```

### 1.3 printf：格式化输出

和C语言的printf基本一致，可以格式化输出：

```java
int x = 10;
double y = 3.14;

System.out.printf("x = %d\n", x);      // %d表示整数
System.out.printf("y = %.2f\n", y);    // %.2f表示保留2位小数
System.out.printf("x = %d, y = %.2f\n", x, y);
```

输出结果：
```
x = 10
y = 3.14
x = 10, y = 3.14
```

**常用格式化符号：**

| 符号 | 含义 | 示例 |
|------|------|------|
| `%d` | 整数 | `printf("%d", 10)` |
| `%f` | 浮点数 | `printf("%.2f", 3.14)` |
| `%s` | 字符串 | `printf("%s", "hello")` |
| `%c` | 字符 | `printf("%c", 'A')` |
| `%b` | 布尔值 | `printf("%b", true)` |

> 💡 这个表格不用死记，用到的时候查一下就行。

---

## 二、输入

### 2.1 使用Scanner读取输入

Java中使用`Scanner`类来读取键盘输入：

```java
import java.util.Scanner;  // 必须导入这个包

public class Test {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);  // 创建Scanner对象

        // 读取字符串
        System.out.print("请输入你的名字：");
        String name = sc.nextLine();
        System.out.println("你好，" + name);

        // 读取整数
        System.out.print("请输入你的年龄：");
        int age = sc.nextInt();
        System.out.println("你的年龄是：" + age);

        // 读取浮点数
        System.out.print("请输入你的身高：");
        double height = sc.nextDouble();
        System.out.println("你的身高是：" + height);
    }
}
```

### 2.2 循环读取多个数字

```java
import java.util.Scanner;

public class Test {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int sum = 0;
        int count = 0;

        System.out.println("请输入数字，输入Ctrl+Z结束：");
        while (sc.hasNextInt()) {
            int num = sc.nextInt();
            sum += num;
            count++;
        }

        System.out.println("平均值：" + (sum * 1.0 / count));
    }
}
```

> ⚠️ 注意：循环输入多个数据时，Windows用 **Ctrl+Z** 结束输入，Linux/Mac用 **Ctrl+D**。

---

## 三、方法的概念

### 3.1 什么是方法？

方法就是一个代码片段，类似于C语言中的"函数"。

**为什么要用方法？**
1. 模块化组织代码，代码更清晰
2. 代码可以重复使用，不用重复写
3. 让代码更好理解
4. 直接调用现有方法，不用重复造轮子

比如：判断闰年的代码，在很多地方都要用，就可以封装成一个方法，需要的时候直接调用。

---

### 3.2 方法的定义

**语法格式：**

```java
修饰符 返回值类型 方法名(参数类型 参数名...) {
    方法体;
    return 返回值;
}
```

**示例1：判断闰年**

```java
public static boolean isLeapYear(int year) {
    if ((year % 4 == 0 && year % 100 != 0) || year % 400 == 0) {
        return true;
    }
    return false;
}
```

**示例2：两个整数相加**

```java
public static int add(int a, int b) {
    return a + b;
}
```

### 重点注意事项

> **⚠️ 必须记住：**
> 1. 修饰符：现阶段直接用 `public static` 固定搭配
> 2. 返回值类型：有返回值要写类型，没有返回值写 `void`
> 3. 方法名：小驼峰命名，比如 `add`、`isLeapYear`
> 4. 参数列表：没有参数就空着，有参数要写类型，多个参数用逗号隔开
> 5. 方法必须写在类里面
> 6. 方法不能嵌套定义（方法里面不能再定义方法）
> 7. Java中没有方法声明，直接定义就行

---

### 3.3 方法的调用执行过程

**调用过程：**
调用方法 → 传递参数 → 执行方法体 → 遇到return返回 → 回到调用处继续执行

```java
public class Method {
    public static void main(String[] args) {
        int a = 10;
        int b = 20;

        System.out.println("调用方法之前");
        int ret = add(a, b);  // 调用add方法
        System.out.println("调用方法之后");
        System.out.println("ret = " + ret);
    }

    public static int add(int x, int y) {
        System.out.println("方法中 x = " + x + " y = " + y);
        return x + y;  // 返回结果
    }
}
```

输出结果：
```
调用方法之前
方法中 x = 10 y = 20
调用方法之后
ret = 30
```

> 💡 注意：定义方法的时候不会执行，只有调用的时候才执行！一个方法可以被多次调用。

---

### 3.4 实参和形参（重点）

**形参**：方法定义时的参数，比如 `add(int x, int y)` 中的 x 和 y
**实参**：调用方法时传的值，比如 `add(10, 20)` 中的 10 和 20

```java
public static int add(int x, int y) {  // x, y是形参
    return x + y;
}

add(2, 3);  // 2, 3是实参，调用时传给形参x和y
```

**重点：Java中是值传递，实参的值会拷贝一份给形参，形参和实参是两个独立的变量！**

**经典例子：交换两个变量**

```java
public class Test {
    public static void main(String[] args) {
        int a = 10;
        int b = 20;
        swap(a, b);
        System.out.println("main: a = " + a + " b = " + b);
    }

    public static void swap(int x, int y) {
        int tmp = x;
        x = y;
        y = tmp;
        System.out.println("swap: x = " + x + " y = " + y);
    }
}
```

输出结果：
```
swap: x = 20 y = 10
main: a = 10 b = 20
```

**为什么交换失败了？**

> ⚠️ 原因分析：
> - 实参a和b在main方法的内存空间中
> - 形参x和y在swap方法的内存空间中
> - 调用时只是把a和b的值拷贝了一份给x和y
> - 所以修改x和y不会影响a和b

> 💡 解决办法：传引用类型参数（比如数组），这个等学数组的时候再详细讲。

---

### 3.5 没有返回值的方法

如果方法不需要返回值，返回值类型写 `void`：

```java
public static void printHello() {
    System.out.println("Hello");
    // 不需要return，或者写return;
}
```

---

## 四、方法重载

### 4.1 为什么需要重载？

如果我们想写一个加法方法，既能加int，又能加double，怎么办？

**笨办法：写不同名字的方法**

```java
public static int addInt(int x, int y) {
    return x + y;
}

public static double addDouble(double x, double y) {
    return x + y;
}
```

这样太麻烦了，取名字都头疼。能不能都叫 `add` 呢？可以！这就是方法重载。

### 4.2 什么是方法重载？

**多个方法名字相同，但参数列表不同，就叫方法重载。**

```java
public class Test {
    public static void main(String[] args) {
        add(1, 2);              // 调用add(int, int)
        add(1.5, 2.5);          // 调用add(double, double)
        add(1.5, 2.5, 3.5);     // 调用add(double, double, double)
    }

    public static int add(int x, int y) {
        return x + y;
    }

    public static double add(double x, double y) {
        return x + y;
    }

    public static double add(double x, double y, double z) {
        return x + y + z;
    }
}
```

### 重点注意事项

> **⚠️ 重载的规则：**
> 1. 方法名必须相同
> 2. 参数列表必须不同（个数不同、类型不同、类型顺序不同）
> 3. 和返回值类型无关！只改返回值不算重载

**错误示例：只改返回值不算重载**

```java
public static int add(int x, int y) {
    return x + y;
}

// 错误！只有返回值不同，不算重载，编译报错
public static double add(int x, int y) {
    return x + y;
}
```

---

## 五、递归

### 5.1 什么是递归？

**方法在执行过程中调用自身，就叫递归。**

递归相当于数学上的"数学归纳法"，有一个起始条件，然后有一个递推公式。

**递归的两个必要条件：**
1. 把原问题拆分成子问题，子问题和原问题解法相同
2. 必须有递归出口（结束条件）

### 5.2 递归示例：求N的阶乘

**分析：**
- 起始条件：1! = 1（递归出口）
- 递推公式：n! = n × (n-1)!

```java
public class Test {
    public static void main(String[] args) {
        int n = 5;
        int ret = factor(n);
        System.out.println("ret = " + ret);
    }

    public static int factor(int n) {
        if (n == 1) {
            return 1;  // 递归出口
        }
        return n * factor(n - 1);  // 调用自身
    }
}
```

输出结果：
```
ret = 120
```

### 5.3 递归执行过程详解

以 `factor(5)` 为例，执行过程：

```
factor(5)
  ↓ 5 * factor(4)
factor(4)
  ↓ 4 * factor(3)
factor(3)
  ↓ 3 * factor(2)
factor(2)
  ↓ 2 * factor(1)
factor(1)
  ↓ 返回1
factor(2) = 2 * 1 = 2
  ↓ 返回2
factor(3) = 3 * 2 = 6
  ↓ 返回6
factor(4) = 4 * 6 = 24
  ↓ 返回24
factor(5) = 5 * 24 = 120
  ↓ 返回120
```

> 💡 理解递归的关键：方法调用时会有一个"调用栈"，每次调用都会压入栈中，遇到return就弹出栈，回到上一层继续执行。

---

## 总结

本文整理了Java输入输出和方法的核心知识点：

**输入输出部分：**
- 三种输出方式：println（换行）、print（不换行）、printf（格式化）
- 使用Scanner读取输入：nextLine、nextInt、nextDouble
- 循环输入用Ctrl+Z结束

**方法部分：**
- 方法的定义和调用
- 实参和形参的关系：Java是值传递，形参是实参的拷贝
- 没有返回值的方法用void
- 方法重载：方法名相同，参数列表不同
- 递归：方法调用自身，必须有出口

这些是Java编程的重要基础，掌握后才能继续学习数组、面向对象等内容。下一篇将继续学习数组。

---

**Java入门学习笔记系列，持续更新中。**
