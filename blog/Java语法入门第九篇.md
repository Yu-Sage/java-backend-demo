# Java语法入门学习笔记（第九篇）：认识异常

> 前几篇的代码基本都是一路顺利跑到底，这一篇换个角度：看看程序出问题的时候会发生什么。异常怎么分类、怎么主动抛出、怎么接住，finally 又为什么必须存在，最后自己定义一个异常类。

---

## 一、异常是什么？

生活里总有意外：点外卖时地址填错、小区门禁坏了、手机刚好没电。程序也一样，代码写得再仔细，运行过程中还是可能遇到各种"不正常"。

Java 把**程序执行过程中发生的不正常行为**称为异常。

先看三个最常见的。

### 1.1 除数为 0

```java
public class Ex1 {
    public static void main(String[] args) {
        System.out.println(10 / 0);
    }
}
```

运行结果：

```
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at Ex1.main(Ex1.java:3)
```

`ArithmeticException` 是算术异常，冒号后面的 `/ by zero` 是原因：被 0 除了。

### 1.2 数组下标越界

```java
public class Ex2 {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3};
        System.out.println(arr[100]);
    }
}
```

运行结果：

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 100
	at Ex2.main(Ex2.java:4)
```

数组只有 3 个元素，下标最大是 2，访问 `arr[100]` 就越界了，异常信息里会带上那个越界的下标。

### 1.3 空指针

```java
public class Ex3 {
    public static void main(String[] args) {
        int[] arr = null;
        System.out.println(arr.length);
    }
}
```

运行结果：

```
Exception in thread "main" java.lang.NullPointerException
	at Ex3.main(Ex3.java:4)
```

`arr` 是 `null`，也就是没有指向任何数组，这时候去取 `length`，相当于对着空气问"你有多长"，只能得到 `NullPointerException`。

> 💡 这三种异常有个共同点：**编译的时候都能过，是程序真正跑起来才炸的**。这类统一叫运行时异常。

> **⚠️ 注意：** 语法写错不算异常。比如把 `System.out.println` 写成 `system.out.println`，这是编译不通过，属于"编译期错误"，不在异常的讨论范围里。

---

## 二、异常的体系结构

Java 里的异常不是零零散散的，它们都挂在同一棵继承树上：

```
Throwable
├── Error                        JVM 层面的严重问题，程序救不回来
│   ├── StackOverflowError       栈溢出
│   └── OutOfMemoryError         内存耗尽
└── Exception                    程序可以处理的异常
    ├── RuntimeException         运行时异常（非受查）
    │   ├── ArithmeticException
    │   ├── ArrayIndexOutOfBoundsException
    │   └── NullPointerException
    └── 其他                     编译时异常（受查）
        ├── FileNotFoundException
        └── CloneNotSupportedException
```

三个层次各自的职责：

| 类型 | 含义 | 程序能不能处理 | 生活比喻 |
|------|------|---------------|---------|
| `Throwable` | 异常体系的顶层 | —— | 所有"出问题"的总称 |
| `Error` | JVM 自己都解决不了的严重问题 | 不能 | 楼体塌了，换门锁没用 |
| `Exception` | 程序可以通过代码处理的异常 | 能 | 感冒发烧，吃药休息能好 |

- `Throwable` 是顶层类，往下分出 `Error` 和 `Exception` 两支
- `Error` 典型的代表是 `StackOverflowError`（递归太深）和 `OutOfMemoryError`（内存耗尽），这类问题一旦出现，程序基本只能退出
- `Exception` 才是我们平时说的"异常"，也是这一篇重点要处理的

> 💡 判断一个问题是 `Error` 还是 `Exception`，可以想一句：**"这段代码能不能补救？"** 能补救就是 Exception，补不了就是 Error。

---

## 三、异常的分类

按发生的时机，异常分成两类。

### 3.1 编译时异常（受检查异常）

编译阶段就报错，编译器强制要求你处理，不处理连 `.class` 文件都生成不了。

```java
import java.io.FileNotFoundException;

public class CompileErr1 {
    public static void main(String[] args) {
        throw new FileNotFoundException("菜单找不到");
    }
}
```

编译结果：

```
CompileErr1.java:5: 错误: 未报告的异常错误FileNotFoundException; 必须对其进行捕获或声明以便抛出
        throw new FileNotFoundException("菜单找不到");
        ^
1 个错误
```

`FileNotFoundException` 就是典型的编译时异常。编译器在提醒：这个异常你必须交代清楚——要么当场 `try-catch` 处理，要么用 `throws` 声明交给上层。

> 💡 编译时异常像出门前家人提醒你带伞：不带上，门都出不去（编译不过）。

### 3.2 运行时异常

编译能通过，运行的时候才出问题。`RuntimeException` 及其所有子类都属于这一类，前面见到的 `NullPointerException`、`ArrayIndexOutOfBoundsException`、`ArithmeticException` 都是。

编译器不会强制你处理它们，因为这类问题往往来自代码逻辑本身，应该在写代码时避免，而不是靠 `try-catch` 兜住。

两类异常的对比：

| | 编译时异常 | 运行时异常 |
|---|---|---|
| 发生时机 | 编译阶段 | 运行阶段 |
| 编译器是否强制处理 | 是 | 否 |
| 典型代表 | `FileNotFoundException`、`CloneNotSupportedException` | `NullPointerException`、`ArrayIndexOutOfBoundsException`、`ArithmeticException` |
| 生活比喻 | 出门前必须带伞 | 走到半路下雨，淋了再说 |

---

## 四、防御式编程：先检查，还是先做再说

错误在代码里是客观存在的，关键是怎么应对。常见两种思路：

- **LBYL**（Look Before You Leap）：操作之前先检查，事前防御型
- **EAFP**（It's Easier to Ask Forgiveness than Permission）：先做，出问题再处理，事后认错型

还是用取餐柜举例。LBYL 的写法：

```java
public static void takeFoodLbyl(String[] boxes, int code) {
    if (code < 0 || code >= boxes.length) {
        System.out.println("取餐码不对");
        return;
    }
    System.out.println("取到：" + boxes[code]);
}
```

EAFP 的写法：

```java
public static void takeFoodEafp(String[] boxes, int code) {
    try {
        System.out.println("取到：" + boxes[code]);
    } catch (ArrayIndexOutOfBoundsException e) {
        System.out.println("取餐码不对");
    }
}
```

两种都能防住问题，区别在代码的组织方式。

如果流程有五步——打开 App、选店、下单、支付、等餐，LBYL 每一步后面都要跟一个 `if` 判断加 `return`，正常流程被切成一节一节，读起来很碎；EAFP 则是把五步连续写进 `try`，出错统一在 `catch` 里处理。

> 💡 Java 的异常处理走的就是 EAFP：**正常流程和错误流程分开写**，读代码时先看清主线，出错的部分集中收在下面。

> **⚠️ 注意：** LBYL 并不是错的。参数校验（下一节的 `throw`）依然是"先检查"的思路。两种方式在实际开发里是配合使用的，不是二选一。

---

## 五、throw：主动抛出异常

有些错误不是"碰巧发生"，而是我们**检查出来**的。这时候应该主动告诉调用者，而不是让程序莫名其妙地崩掉。

语法很简单：

```java
throw new XXXException("异常产生的原因");
```

还是取餐柜的例子：取餐码根本不存在，就明确抛一个异常出去。

```java
public class Ex10 {
    public static void main(String[] args) {
        String[] boxes = {"奶茶", "炸鸡", "汉堡"};

        System.out.println(takeFood(boxes, 1));

        takeFood(boxes, 5);
        System.out.println("这一句不会执行");
    }

    public static String takeFood(String[] boxes, int code) {
        if (boxes == null) {
            throw new NullPointerException("取餐柜还没通电");
        }
        if (code < 0 || code >= boxes.length) {
            throw new ArrayIndexOutOfBoundsException("取餐码 " + code + " 不存在");
        }
        return boxes[code];
    }
}
```

运行结果：

```
炸鸡
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 取餐码 5 不存在
	at Ex10.takeFood(Ex10.java:16)
	at Ex10.main(Ex10.java:7)
```

`takeFood(boxes, 1)` 正常拿到"炸鸡"；`takeFood(boxes, 5)` 抛出了异常，后面的 `System.out.println("这一句不会执行")` 就真的没有执行。

关于 `throw`，有几条规则要记住：

1. `throw` 必须写在**方法体内部**
2. 抛出的对象必须是 `Exception` 或者它的子类
3. 抛的是 `RuntimeException` 及其子类，可以不用处理，直接交给 JVM
4. 抛的是编译时异常，必须处理，否则编译不通过
5. 异常一旦抛出，它后面的代码就不会执行了

> 💡 `throw` 是"制造"一个异常对象并丢出去。注意它和后面要讲的 `throws` 只差一个字母，作用完全不同。

---

## 六、throws：把异常交给调用者

有些方法自己没法处理问题，只能告诉上层："我这里可能会出错，你看着办。" 这时用 `throws` 写在方法签名上。

场景：服务员去加载菜单文件，文件不存在，他没办法凭空变出一个文件来，只能把问题报给店长。

```java
import java.io.FileNotFoundException;

class MenuLoader {
    public void loadMenu(String fileName) throws FileNotFoundException {
        if (!fileName.equals("menu.txt")) {
            throw new FileNotFoundException("菜单文件名不对：" + fileName);
        }
        System.out.println("菜单加载成功：" + fileName);
    }
}

public class Ex11 {
    public static void main(String[] args) {
        MenuLoader loader = new MenuLoader();
        try {
            loader.loadMenu("menu.txt");
            loader.loadMenu("menu2.txt");
        } catch (FileNotFoundException e) {
            System.out.println("菜单加载失败：" + e.getMessage());
        }
        System.out.println("服务员继续招呼客人");
    }
}
```

运行结果：

```
菜单加载成功：menu.txt
菜单加载失败：菜单文件名不对：menu2.txt
服务员继续招呼客人
```

`loadMenu` 自己不处理，用 `throws` 声明"我可能抛 `FileNotFoundException`"；`main` 里用 `try-catch` 接住，接住之后程序照常往下走。

几条规则：

1. `throws` 必须跟在方法的**参数列表之后**
2. 声明的异常必须是 `Exception` 或者它的子类
3. 方法内部可能抛出多个异常时，`throws` 后面用逗号隔开；如果有父子关系，直接写父类就行
4. 对于编译时异常，调用者必须处理——要么 `try-catch`，要么继续用 `throws` 往上抛

比如第 3 条，`FileNotFoundException` 继承自 `IOException`，那下面两种写法是等价的：

```java
public void loadMenu(String fileName) throws IOException, FileNotFoundException { }
public void loadMenu(String fileName) throws IOException { }
```

> **⚠️ 注意：** `throws` 只是"声明"，**并没有真正处理异常**，它只是把责任推给了调用者。真正要处理，还得靠 `try-catch`。

---

## 七、try-catch：真正接住异常

场景：奶茶店按人数分杯子，结果来的人数是 0。店员不能直接崩溃，而应该提示"人数不能为 0"，然后继续营业。

```java
public class Ex4 {
    public static void main(String[] args) {
        try {
            int cups = 10;
            int people = 0;
            System.out.println(cups / people);
            System.out.println("这一句不会执行");
        } catch (ArithmeticException e) {
            System.out.println("人数不能为 0：" + e.getMessage());
        }
        System.out.println("后面的代码继续执行");
    }
}
```

运行结果：

```
人数不能为 0：/ by zero
后面的代码继续执行
```

`try` 里第一行除法就抛了异常，所以第二行的输出被跳过；`catch` 接住并处理完，程序从 `try-catch` 后面继续执行。

语法格式：

```java
try {
    // 可能出现异常的代码
} catch (异常类型 e) {
    // 接住之后怎么处理
} finally {
    // 可选，收尾工作
}
```

异常对象 `e` 常用的三种打印方式：

| 写法 | 输出内容 | 适合场景 |
|------|---------|---------|
| `e.getMessage()` | 只有原因，比如 `/ by zero` | 给用户看的提示 |
| `System.out.println(e)` | 异常类型 + 原因 | 简单排查 |
| `e.printStackTrace()` | 完整调用栈 | 调试，定位是哪一行出的问题 |

> 💡 平时调试用 `printStackTrace()` 最多，因为它会告诉你异常从哪一行开始、经过了哪些方法。

### 7.1 catch 的类型必须对得上

异常是**按类型捕获**的。类型对不上，就等于没接住。

```java
public class Ex5 {
    public static void main(String[] args) {
        try {
            int[] seats = {1, 2, 3};
            System.out.println(seats[3]);
        } catch (NullPointerException e) {
            System.out.println("这里抓的是空指针，抓不到越界");
        }
        System.out.println("后面的代码");
    }
}
```

运行结果：

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 3
	at Ex5.main(Ex5.java:5)
```

抛出的是 `ArrayIndexOutOfBoundsException`，`catch` 等的却是 `NullPointerException`，两者没有继承关系，接不住。异常继续往外抛，最后交给 JVM，程序终止，`"后面的代码"` 也没打印出来。

> **⚠️ 注意：** `catch` 的类型要么和抛出的类型**完全一致**，要么是它的**父类**，否则捕获不到。

### 7.2 多个异常怎么接

一段代码里可能抛出好几种异常，这时可以写多个 `catch`：

```java
try {
    System.out.println(arr[100]);
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("下标越界");
} catch (NullPointerException e) {
    System.out.println("空指针");
}
```

如果几种异常的处理方式完全一样，可以用 `|` 合并成一个 `catch`：

```java
public class Ex6 {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3};
        try {
            System.out.println("开始点单");
            System.out.println(arr[100]);
            System.out.println("这一句不会执行");
        } catch (ArrayIndexOutOfBoundsException | NullPointerException e) {
            System.out.println("同一个 catch 接住：" + e.getClass().getSimpleName());
            System.out.println("原因：" + e.getMessage());
        }
        System.out.println("点单流程结束");
    }
}
```

运行结果：

```
开始点单
同一个 catch 接住：ArrayIndexOutOfBoundsException
原因：100
点单流程结束
```

还有一种情况要特别小心：**异常之间有父子关系时，子类必须写在前面，父类写在后面。**

```java
try {
    System.out.println(arr[100]);
} catch (Exception e) {              // Exception 是父类，先写它会先接住
    e.printStackTrace();
} catch (NullPointerException e) {   // 永远轮不到这一句
    e.printStackTrace();
}
```

编译结果：

```
CompileErr2.java:8: 错误: 已捕获到异常错误NullPointerException
        } catch (NullPointerException e) {
          ^
1 个错误
```

编译器直接拦住了：前面已经把 `Exception` 接走了，后面这个更具体的类型永远执行不到。

> **⚠️ 记住顺序：** 小的在前，大的在后。`NullPointerException` → `Exception`，而不是反过来。

### 7.3 一个 catch 兜底

因为 `Exception` 是所有异常的父类，所以下面这种写法能接住所有异常：

```java
try {
    // ...
} catch (Exception e) {
    e.printStackTrace();
}
```

能用，但不推荐。因为它把所有问题混成一锅，看不出到底是空指针还是越界，排查时反而更麻烦。除非确实只想"别让程序崩"，否则还是按类型分开写。

---

## 八、finally：不管成不成，都要收尾

场景很日常：做完饭，不管这道菜成功还是糊了，火要关、灶台要擦。`finally` 干的就是这件事。

```java
public class Ex7 {
    public static void main(String[] args) {
        try {
            int[] seats = {1, 2, 3};
            seats[5] = 10;
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("座位号不存在：" + e.getMessage());
        } finally {
            System.out.println("关火、收拾厨房");
        }
        System.out.println("餐厅打烊");

        System.out.println("func() 返回：" + func());
    }

    public static int func() {
        try {
            return 10;
        } finally {
            return 20;
        }
    }
}
```

运行结果：

```
座位号不存在：5
关火、收拾厨房
餐厅打烊
func() 返回：20
```

前四行的顺序很清楚：`catch` 处理完 → `finally` 执行 → 后面的代码执行。

最后一行值得单独说一下。`func()` 里 `try` 明明写的是 `return 10`，为什么结果是 20？

因为 **`finally` 在方法真正返回之前执行**。执行流程是：先算出 `try` 要返回 10，然后进入 `finally`，结果 `finally` 里也有一个 `return 20`，直接把返回值覆盖了，原来的 10 就回不去了。

> **⚠️ 注意：** 不要在 `finally` 里写 `return`。它会覆盖 `try` 里的返回值，编译器也会给出警告，属于典型的"看起来能跑、实际上埋雷"。

那既然 `try-catch` 后面的代码也会执行，为什么还要 `finally`？

因为**异常会打断执行顺序**。比如在 `try` 里打开了文件、连了数据库，然后中途 `return` 或者抛异常，后面那些"关闭资源"的代码根本走不到，资源就泄漏了。放进 `finally`，才能保证一定被执行。

```java
Scanner sc = null;
try {
    sc = new Scanner(System.in);
    return sc.nextInt();
} finally {
    if (sc != null) {
        sc.close();   // 不管上面怎么返回，这里都会关掉
    }
}
```

> 💡 `finally` 一般就干三件事：关流、释放连接、解锁。凡是"必须做、不做会出问题"的收尾动作，都放这里。

---

## 九、异常的处理流程

方法之间是会互相调用的，这种调用关系叫**调用栈**。异常如果在本方法里没被处理，就会沿着调用栈一层层往上找。

```java
public class Ex8 {
    public static void main(String[] args) {
        try {
            takeSeat();
        } catch (ArrayIndexOutOfBoundsException e) {
            e.printStackTrace();
        }
        System.out.println("after try catch");
    }

    public static void takeSeat() {
        int[] seats = {1, 2, 3};
        System.out.println(seats[100]);
    }
}
```

运行结果：

```
java.lang.ArrayIndexOutOfBoundsException: 100
	at Ex8.takeSeat(Ex8.java:13)
	at Ex8.main(Ex8.java:4)
after try catch
```

栈信息是**从下往上**读的：最下面 `main` 调用了 `takeSeat`，`takeSeat` 的第 13 行出了错。异常在 `takeSeat` 里没被处理，向上抛给 `main`，`main` 的 `catch` 接住了，所以 `after try catch` 还能打印。

如果 `main` 也不处理，那异常就一路抛到 JVM，程序直接终止——也就是最开始那几段代码的样子。

完整的处理流程可以拆成六步：

1. 先执行 `try` 里的代码
2. 一旦出错，`try` 里剩下的代码立刻停止
3. 拿抛出的异常类型去和各个 `catch` 比对
4. 匹配上就执行对应的 `catch`
5. 匹配不上就向上抛给调用者，继续找
6. 一路都没人处理，最后交给 JVM，程序终止

不管有没有匹配上，`finally` 都会在这个方法结束前执行。

> 💡 可以把调用栈想成外卖出问题时的上报链路：骑手找站长，站长找平台，平台找客服。谁都不处理，最后订单只能取消（程序终止）。

---

## 十、自定义异常类

Java 内置的异常虽然多，但不可能覆盖所有业务场景。比如"余额不足"这件事，Java 里就没有一个现成的异常类能准确表达。

这时候就自己定义一个。规矩只有两条：

1. 继承 `Exception` 或者 `RuntimeException`
2. 提供一个带 `String` 参数的构造方法，参数表示"异常产生的原因"

场景：ATM 取款，余额不够要明确告诉用户还差多少。

```java
class BalanceNotEnoughException extends RuntimeException {
    public BalanceNotEnoughException(String message) {
        super(message);
    }
}

class ATM {
    private int balance;

    public ATM(int balance) {
        this.balance = balance;
    }

    public void withdraw(int money) {
        if (money > balance) {
            throw new BalanceNotEnoughException("余额不足，当前余额 " + balance + " 元");
        }
        balance -= money;
        System.out.println("取款成功，取出 " + money + " 元，剩余 " + balance + " 元");
    }
}

public class Ex9 {
    public static void main(String[] args) {
        ATM atm = new ATM(500);

        atm.withdraw(300);

        try {
            atm.withdraw(500);
        } catch (BalanceNotEnoughException e) {
            System.out.println("取款失败：" + e.getMessage());
        }
    }
}
```

运行结果：

```
取款成功，取出 300 元，剩余 200 元
取款失败：余额不足，当前余额 200 元
```

这里继承的是 `RuntimeException`，因为"取钱时余额不够"属于业务上很常见的情况，不想让每个调用处都被迫写 `try-catch` 或者 `throws`。

两种父类的区别：

| 继承自 | 异常类型 | 编译器是否强制处理 | 适合什么场景 |
|--------|---------|-------------------|-------------|
| `Exception` | 受查异常 | 是，必须 try-catch 或 throws | 调用者必须知道、必须应对的问题，比如文件不存在 |
| `RuntimeException` | 非受查异常 | 否，可以交给 JVM | 业务上高频发生、调用方不一定想当场处理的问题，比如余额不足 |

如果换成继承 `Exception`，那么调用 `withdraw` 的地方就必须处理，否则编译不过：

```java
public void withdraw(int money) throws BalanceNotEnoughException { }
```

> 💡 自定义异常最大的意义是**让错误的名字自己会说话**。"余额不足"比"运行出错"清楚一百倍，日志里一眼就能看懂发生了什么。

---

## 十一、几个常被问到的点

### 11.1 throw 和 throws 的区别

这两个词长得像，考试和面试都爱考：

| | `throw` | `throws` |
|---|---|---|
| 位置 | 写在方法体内部 | 写在方法签名上，参数列表之后 |
| 后面跟什么 | 一个异常对象 | 一个或多个异常类型 |
| 作用 | 真正抛出一个异常 | 声明这个方法可能抛出异常 |
| 是否处理了异常 | 没有，只是抛出去 | 没有，只是把责任交给调用者 |
| 数量 | 一次只能抛一个 | 可以声明多个，用逗号隔开 |

一句话记：**`throw` 是动作，`throws` 是声明。**

### 11.2 finally 一定执行吗

一般情况下一定执行，包括 `try` 里 `return`、抛异常、`catch` 里 `return` 这些情况。

但有一种例外：JVM 直接退出时，`finally` 来不及执行。

```java
public class Ex13 {
    public static void main(String[] args) {
        try {
            System.out.println("try 里的代码");
            System.exit(0);
        } finally {
            System.out.println("finally 里的代码");
        }
    }
}
```

运行结果：

```
try 里的代码
```

`System.exit(0)` 会直接结束整个 JVM，`finally` 没有机会运行。

> 💡 除了这种"整个进程被干掉"的极端情况，日常代码里的 `finally` 都可以认为是必执行的。

### 11.3 异常被捕获之后，程序还会继续吗

会。`catch` 处理完，程序从整个 `try-catch` 结构的后面继续执行，不会回到出错的那一行重来。

如果异常一直没被捕获，最终交给 JVM，程序就终止了，后面的代码都不会执行。

---

## 十二、把知识串起来用一次

最后用一个点外卖的小例子，把自定义异常、`throws`、`try-catch`、`finally` 串起来：

```java
class OrderSoldOutException extends Exception {
    public OrderSoldOutException(String message) {
        super(message);
    }
}

class Order {
    public void place(String name, int count) throws OrderSoldOutException {
        if (name.equals("杨枝甘露")) {
            throw new OrderSoldOutException("「" + name + "」卖完了");
        }
        System.out.println("下单成功：" + name + " × " + count);
    }
}

public class Ex12 {
    public static void main(String[] args) {
        Order order = new Order();
        try {
            order.place("杨枝甘露", 2);
            order.place("珍珠奶茶", 1);
        } catch (OrderSoldOutException e) {
            System.out.println("下单失败：" + e.getMessage());
        } finally {
            System.out.println("关闭下单窗口");
        }
        System.out.println("返回首页");
    }
}
```

运行结果：

```
下单失败：「杨枝甘露」卖完了
关闭下单窗口
返回首页
```

这个例子里：

- `OrderSoldOutException` 继承 `Exception`，是受查异常，所以 `place` 必须用 `throws` 声明
- 第一单"杨枝甘露"就售罄了，抛出异常，第二单"珍珠奶茶"没有执行
- `catch` 接住并给用户提示
- `finally` 无论如何都关闭下单窗口
- 最后回到首页，程序继续运行

---

## 总结

这篇把异常的主要内容过了一遍：

**异常是什么：**
- 程序执行过程中发生的不正常行为
- 常见的三类：算术异常、数组越界、空指针
- 编译期的语法错误不算异常

**体系结构：**
- `Throwable` 是顶层，分出 `Error` 和 `Exception`
- `Error` 是 JVM 都解决不了的严重问题，程序救不回来
- `Exception` 是代码可以处理的异常

**分类：**
- 编译时异常（受查）：编译器强制处理，不处理编译不过
- 运行时异常（非受查）：`RuntimeException` 及其子类，编译能过，运行才炸

**四种处理手段：**
- `throw`：在方法体里主动抛出一个异常对象
- `throws`：在方法签名上声明可能抛出的异常，交给调用者
- `try-catch`：真正接住并处理，按类型匹配
- `finally`：不管成功失败都要执行的收尾代码

**几个关键细节：**
- 异常一抛出，它后面的代码就不执行了
- `catch` 的类型必须一致或是父类，子类 catch 要写在父类前面
- `finally` 在 `return` 之前执行，不要在 `finally` 里写 `return`
- 异常会沿调用栈一层层往上抛，最后没人接就交给 JVM

**自定义异常：**
- 继承 `Exception` 或 `RuntimeException`
- 提供带 `String` 参数的构造方法
- 继承 `Exception` 是受查异常，继承 `RuntimeException` 是非受查异常

异常处理的意义不是"让程序永远不出错"，而是**出错的时候能说清楚出了什么、还能不能继续**。把这一篇的几种写法练熟，后面读源码、看日志会轻松很多。下一篇会接着整理 Java 集合框架里最常用的 ArrayList，看看它和数组到底差在哪。

---

**Java入门学习笔记系列，持续更新中。**
