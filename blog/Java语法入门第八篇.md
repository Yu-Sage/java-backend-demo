# Java语法入门学习笔记（第八篇）：认识String类

> 继续Java入门学习，这篇整理String类：怎么创建、怎么比较、怎么查找和修改，还有它"不可变"这个重要特性，最后认识一下StringBuilder。

---

## 一、String是什么？

平时写代码，用得最多的类型可能就是字符串了：

- 注册账号时填的昵称
- 聊天软件里发出去的一句话
- 搜索框里输入的关键词

这些在Java里都是字符串，统一用 `String` 类来表示。

`String` 是**引用类型**，不是基本类型。而且用双引号 `""` 括起来的内容，本身就已经是一个 `String` 对象，可以直接调用方法：

```java
System.out.println("hello".length());  // 5
```

> 💡 刚开始容易把 `String` 当成基本类型，其实它是类。创建出来的变量存的是对象的地址，不是字符串本身。

---

## 二、创建字符串

常用的创建方式有三种：

```java
public class TestString {
    public static void main(String[] args) {
        // 1. 直接用双引号，最常见
        String name1 = "小鲤鱼";

        // 2. new 一个 String 对象
        String name2 = new String("小鲤鱼");

        // 3. 用字符数组拼出来
        char[] chars = {'小', '鲤', '鱼'};
        String name3 = new String(chars);

        System.out.println(name1);
        System.out.println(name2);
        System.out.println(name3);

        System.out.println(name1.length());   // 3
        System.out.println(name1.isEmpty());  // false
    }
}
```

输出：

```
小鲤鱼
小鲤鱼
小鲤鱼
3
false
```

这里用到两个很常用的方法：

- `length()`：返回字符串的长度，也就是字符个数
- `isEmpty()`：长度为 0 时返回 true，否则返回 false

> **⚠️ 注意：**
> 数组的长度是 `length`（没有括号），字符串的长度是 `length()`（有括号），这两个别写混了。

---

## 三、字符串的比较

比较两个字符串是不是一样，是特别常见的操作，比如登录时判断用户名对不对。

### 3.1 用 == 比较（比的是地址）

```java
public class TestEquals {
    public static void main(String[] args) {
        String a = new String("奶茶");
        String b = new String("奶茶");
        String c = a;

        System.out.println(a == b);  // false
        System.out.println(a == c);  // true
    }
}
```

`a` 和 `b` 的内容虽然都是"奶茶"，但是 `new` 了两次，是两个不同的对象，地址不一样，所以 `==` 的结果是 false。

`c = a` 只是把 `a` 的地址复制给了 `c`，两个变量指向同一个对象，所以是 true。

> **⚠️ 结论：** 对于引用类型，`==` 比较的是**地址**，不是内容。想知道内容是否相同，要用 `equals`。

### 3.2 用 equals 比较（比的是内容）

```java
String a = new String("奶茶");
String b = new String("奶茶");
String c = new String("奶茶");

System.out.println(a.equals(b));  // true
System.out.println(a.equals(c));  // true
```

`equals` 是 String 重写过的方法，会一个字符一个字符地比内容，内容一样就返回 true。

> 💡 `equals` 区分大小写，`"Java".equals("java")` 返回 false。如果不想区分大小写，用 `equalsIgnoreCase`。

```java
System.out.println("Java".equals("java"));            // false
System.out.println("Java".equalsIgnoreCase("java"));  // true
```

### 3.3 用 compareTo 比较大小（字典序）

有时候不光要判断相等，还要比较"谁排在前面"，比如通讯录按名字排序。

`compareTo` 返回一个 int，规则是：

- 返回负数：当前字符串排在参数字符串前面
- 返回 0：两个字符串一样
- 返回正数：当前字符串排在后面

```java
String s1 = "apple";
String s2 = "apricot";

System.out.println(s1.compareTo(s2));  // -2
```

它是从第一个字符开始，一个接一个往下比的：

```
a p p l e
a p r i c o t
    ↑
第 3 个字符不同：'p'(112) - 'r'(114) = -2
```

如果前面都一样，那就比长度：

```java
System.out.println("abc".compareTo("abcdef"));  // -3
```

`abc` 是 `abcdef` 的前缀，短的排前面，返回长度差 -3。

还有一个忽略大小写的版本：

```java
System.out.println("ABC".compareTo("abc"));            // -32
System.out.println("ABC".compareToIgnoreCase("abc"));  // 0
```

---

## 四、字符串查找

想在一段文字里找某个字或者某个词在哪里，可以用下面这几个方法：

```java
public class TestSearch {
    public static void main(String[] args) {
        String msg = "今天买了苹果，苹果很甜";

        System.out.println(msg.charAt(0));           // 今
        System.out.println(msg.indexOf("苹果"));      // 4
        System.out.println(msg.indexOf("苹果", 5));   // 7
        System.out.println(msg.lastIndexOf("苹果"));  // 7
        System.out.println(msg.indexOf("香蕉"));      // -1
    }
}
```

它们的作用分别是：

| 方法 | 作用 |
|------|------|
| `charAt(int index)` | 取指定位置的字符，下标从 0 开始 |
| `indexOf(String str)` | 从前往后找，返回第一次出现的位置 |
| `indexOf(String str, int from)` | 从 from 位置开始往后找第一次出现的位置 |
| `lastIndexOf(String str)` | 从后往前找，返回最后一次出现的位置 |

> **⚠️ 注意：** 找不到时返回的是 `-1`，不是 0，也不会抛异常。所以判断"有没有"的时候，要和 `-1` 比较。

> 💡 `charAt` 的下标如果为负数或者越界，会抛 `IndexOutOfBoundsException`，用之前最好先确认长度。

---

## 五、字符串和其他类型的转换

### 5.1 数字和字符串互转

```java
public class TestConvert {
    public static void main(String[] args) {
        // 数字 → 字符串
        int age = 18;
        String ageText = String.valueOf(age);
        System.out.println(ageText);  // 18

        double price = 9.9;
        String priceText = String.valueOf(price);
        System.out.println(priceText);  // 9.9

        // 字符串 → 数字
        int year = Integer.parseInt("2024");
        double weight = Double.parseDouble("52.5");
        System.out.println(year);    // 2024
        System.out.println(weight);  // 52.5
    }
}
```

> **⚠️ 注意：** `parseInt` 只能转"纯数字"的字符串。如果传进去 `"12abc"`、`"abc"` 或者空字符串，会抛 `NumberFormatException`。所以转换之前最好先确认内容。

### 5.2 大小写转换

```java
String code = "Java2024";
System.out.println(code.toUpperCase());  // JAVA2024
System.out.println(code.toLowerCase());  // java2024
```

> 💡 这两个方法只转换字母，数字和符号不变，而且返回的是新字符串，原字符串不受影响。

### 5.3 字符串和字符数组互转

```java
String word = "java";

// 字符串 → 字符数组
char[] chars = word.toCharArray();
for (char c : chars) {
    System.out.print(c + " ");   // j a v a
}
System.out.println();

// 字符数组 → 字符串
char[] letters = {'h', 'e', 'l', 'l', 'o'};
String hello = new String(letters);
System.out.println(hello);   // hello
```

### 5.4 格式化

`String.format` 可以按模板拼字符串，`%d` 是整数，`%s` 是字符串，`%02d` 表示不足两位时前面补 0：

```java
String date = String.format("%d年%02d月%02d日", 2024, 9, 5);
System.out.println(date);   // 2024年09月05日
```

---

## 六、字符串替换

替换就是把字符串里的某部分内容换成别的：

```java
public class TestReplace {
    public static void main(String[] args) {
        String fruits = "苹果,香蕉,橘子";

        // 全部替换
        System.out.println(fruits.replace(",", "、"));      // 苹果、香蕉、橘子

        // 只替换第一个
        System.out.println("aaa".replaceFirst("a", "b"));   // baa

        // 替换所有
        System.out.println("aaa".replaceAll("a", "b"));     // bbb
    }
}
```

- `replaceAll`：替换所有匹配的内容
- `replaceFirst`：只替换第一个匹配的内容
- `replace`：普通字符串替换，不涉及正则，日常用得更多

> **⚠️ 注意：** 字符串是不可变的，替换**不会修改原字符串**，而是返回一个新的字符串。想要结果就必须重新赋值：
> ```java
> String s = "a-b-c";
> s.replace("-", "+");        // 这样写没用，结果直接丢掉了
> s = s.replace("-", "+");    // 要这样赋值
> ```

---

## 七、字符串拆分

拆分就是把一个完整的字符串按分隔符切成几段，返回一个字符串数组。

### 7.1 基本拆分

```java
String address = "福建省-泉州市-丰泽区";
String[] parts = address.split("-");

for (String p : parts) {
    System.out.println(p);
}
```

输出：

```
福建省
泉州市
丰泽区
```

### 7.2 拆分键值对

这种写法在开发里很常见，比如解析 `name=小明&age=18` 这样的参数串：

```java
String params = "name=小明&age=18";
String[] pairs = params.split("&");

for (String pair : pairs) {
    String[] kv = pair.split("=");
    System.out.println(kv[0] + "：" + kv[1]);
}
```

输出：

```
name：小明
age：18
```

### 7.3 特殊字符要转义

如果分隔符是 `.`、`|`、`*`、`+` 这些特殊字符，直接写是拆不出来的，必须加转义：

```java
String ip = "192.168.1.1";
String[] nums = ip.split("\\.");   // 注意这里是两个反斜杠

for (String num : nums) {
    System.out.println(num);
}
```

输出：

```
192
168
1
1
```

> **⚠️ 为什么是两个反斜杠？**
> `split` 的参数是**正则表达式**，而 `.` 在正则里表示"任意字符"，所以要写成 `\.` 才是真正的点号。但 Java 字符串里反斜杠本身又需要再转义一次，写成 `\\`，合起来就是 `"\\."`。
>
> 直接写 `split(".")` 会切出一个空数组，这是新手常踩的坑。
>
> 常见的特殊分隔符还有 `|`、`*`、`+`，在 Java 代码里都要写成 `"\\|"`、`"\\*"`、`"\\+"`；如果分隔符是反斜杠本身，要写 `"\\\\"`。

### 7.4 限制拆分成几段

`split` 的第二个参数可以限制最多拆成几段：

```java
String line = "2024-09-05-星期一";
String[] result = line.split("-", 2);

System.out.println(result[0]);  // 2024
System.out.println(result[1]);  // 09-05-星期一
```

---

## 八、字符串截取

截取就是从一段完整的字符串里取出一部分：

```java
public class TestSubstring {
    public static void main(String[] args) {
        String id = "350500200501011234";

        String birthday = id.substring(6, 14);   // 20050101
        System.out.println(birthday);

        String year = id.substring(6, 10);       // 2005
        System.out.println(year);

        String orderNo = "ORD20240905001";
        String tail = orderNo.substring(orderNo.length() - 4);  // 5001
        System.out.println(tail);
    }
}
```

- `substring(int beginIndex)`：从 beginIndex 一直截到末尾
- `substring(int beginIndex, int endIndex)`：截取 `[beginIndex, endIndex)` 这一段

> **⚠️ 注意：** 区间是**前闭后开**的，包含 beginIndex，不包含 endIndex。比如 `substring(0, 5)` 取的是下标 0 到 4，一共 5 个字符，不是 6 个。

看个示意图会更清楚：

```
下标:  0  1  2  3  4  5  6  7  8  9
字符:  h  e  l  l  o  w  o  r  l  d
                     ↑        ↑
                  begin=5   end=8

substring(5, 8) → "wor"
```

---

## 九、其他常用方法

### 9.1 去掉首尾空格

用户输入的时候很容易多打几个空格，可以用 `trim` 处理：

```java
String input = "  小明  ";
System.out.println("[" + input + "]");          // [  小明  ]
System.out.println("[" + input.trim() + "]");   // [小明]
```

> 💡 `trim` 只去掉开头和结尾的空白字符（空格、换行、制表符等），中间的空格会保留。

### 9.2 几个高频方法汇总

| 方法 | 作用 |
|------|------|
| `length()` | 获取字符串长度 |
| `isEmpty()` | 判断是否为空字符串 |
| `charAt(int index)` | 获取指定位置的字符 |
| `trim()` | 去掉首尾空白 |
| `contains(String str)` | 判断是否包含某个子串 |

---

## 十、字符串的不可变性

这是 String 最重要的一个特性：**String 对象一旦创建，内容就固定了，改不了。**

```java
String s = "hello";
String s2 = s.replace('l', 'L');

System.out.println(s);    // hello
System.out.println(s2);   // heLLo
```

`s` 还是 `hello`，因为 `replace` 并没有修改原来的对象，而是造了一个新字符串返回。

再看 `+=`：

```java
String s = "hello";
s += " world";
System.out.println(s);   // hello world
```

看起来像是 `s` 变了，其实是 `s` 指向了一个新对象 `"hello world"`，原来那个 `"hello"` 对象还在，只是没人用它了。

> 💡 可以把 String 想成一张打印好的纸质文件：想改内容只能重新打印一份，原来那张纸上的字不会变。所以每次"修改"，其实都在造新对象。

### 10.1 一个常见的错误说法

网上有种说法："String 不可变，是因为内部存字符的数组被 `final` 修饰了。"

这个说法不准确。`final` 修饰引用，只表示"这个引用不能指向别的数组"，数组里面的内容照样能改：

```java
final int[] arr = {1, 2, 3};

arr[0] = 100;                  // 可以改，编译通过
// arr = new int[]{4, 5, 6};   // 这行才会编译报错
System.out.println(arr[0]);   // 100
```

String 真正不可变的原因是：

1. 内部存字符的数组是 `private` 的，外面拿不到
2. String 没有提供任何修改内容的方法
3. 所有"修改"类的方法，都是返回一个新对象

---

## 十一、字符串常量池

先看一段代码，猜猜结果：

```java
String a = "hello";
String b = "hello";
String c = new String("hello");

System.out.println(a == b);        // true
System.out.println(a == c);        // false
System.out.println(a.equals(c));   // true
```

为什么 `a == b` 是 true？

因为用双引号直接写的字符串，会被放进**字符串常量池**。内容相同的字符串，池子里只存一份，`a` 和 `b` 都指向这同一个对象。

而 `new String("hello")` 每次都会在堆里新建一个对象，所以 `a == c` 是 false，但内容一样，`equals` 是 true。

> 💡 可以这样理解：常量池像一个"公共字库"，同样的内容只做一块模板，大家共用；`new` 相当于每次都重新刻一块。

> **⚠️ 所以结论很明确：** 比较字符串内容永远用 `equals`，不要用 `==`。

---

## 十二、StringBuilder

### 12.1 为什么需要 StringBuilder

先看这段代码：

```java
String s = "";
for (int i = 0; i < 10000; i++) {
    s += i;
}
System.out.println(s);
```

逻辑没问题，但效率很低。因为 String 不可变，每次 `+=` 都要创建一个新的 String 对象，循环一万次就产生一大堆临时对象。

这种情况就该用 `StringBuilder`：

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append(i);
}
String result = sb.toString();
System.out.println(result);
```

StringBuilder 内部是一个可变的字符数组，`append` 直接往后追加，不会每次都创建新对象，效率高很多。

### 12.2 StringBuilder 的常用方法

```java
public class TestStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();

        sb.append("牛奶");
        sb.append("、面包");
        sb.append("、鸡蛋");
        System.out.println(sb);            // 牛奶、面包、鸡蛋
        System.out.println(sb.length());   // 8

        sb.insert(0, "购物清单：");
        System.out.println(sb);            // 购物清单：牛奶、面包、鸡蛋

        sb.delete(0, 5);
        System.out.println(sb);            // 牛奶、面包、鸡蛋

        sb.reverse();
        System.out.println(sb);            // 蛋鸡、包面、奶牛
    }
}
```

常用方法整理一下：

| 方法 | 作用 |
|------|------|
| `append(...)` | 在末尾追加内容，相当于 String 的 `+=` |
| `insert(int offset, String str)` | 在指定位置插入内容 |
| `delete(int start, int end)` | 删除 `[start, end)` 区间的内容 |
| `replace(int start, int end, String str)` | 把 `[start, end)` 区间替换成新内容 |
| `reverse()` | 反转字符串 |
| `setCharAt(int index, char ch)` | 修改指定位置的字符 |
| `charAt(int index)` | 获取指定位置的字符 |
| `length()` | 获取长度 |
| `toString()` | 转成 String |

### 12.3 String 和 StringBuilder 互转

这两个类不能直接赋值转换，要借助构造方法或者 `toString`：

```java
String s = "hello";

// String → StringBuilder
StringBuilder sb = new StringBuilder(s);

// StringBuilder → String
String back = sb.toString();
System.out.println(back);   // hello
```

---

## 十三、String、StringBuffer、StringBuilder 的区别

这三个经常被拿来比较，面试也爱问：

| 类型 | 内容能否修改 | 线程安全 | 适合的场景 |
|------|-------------|---------|-----------|
| String | 不能，每次修改都产生新对象 | 安全（因为不可变） | 少量拼接、作为常量使用 |
| StringBuffer | 能 | 安全（方法加了同步） | 多线程下频繁修改 |
| StringBuilder | 能 | 不安全 | 单线程下频繁修改，最常用 |

> 💡 日常写代码，绝大多数情况都是单线程，直接用 StringBuilder 就行。只有在多个线程同时修改同一个字符串缓冲区时，才需要考虑 StringBuffer。

---

## 十四、把知识串起来用一次

最后用一个小例子，把拆分、转换、格式化串起来：把 `2024-09-05` 变成 `2024年9月5日`。

```java
public class TestDemo {
    public static void main(String[] args) {
        String date = "2024-09-05";

        // 1. 按 - 拆开
        String[] parts = date.split("-");

        // 2. 把每个部分转成整数
        int year = Integer.parseInt(parts[0]);
        int month = Integer.parseInt(parts[1]);
        int day = Integer.parseInt(parts[2]);

        // 3. 按中文格式拼回去
        String result = String.format("%d年%d月%d日", year, month, day);
        System.out.println(result);   // 2024年9月5日
    }
}
```

---

## 总结

这篇把 String 类的主要内容过了一遍：

**创建：**
- 双引号、`new`、字符数组，三种常用方式

**比较：**
- `==` 比地址，`equals` 比内容
- `compareTo` 按字典序比大小，返回负数/0/正数
- 比较内容永远用 `equals`

**常用操作：**
- 查找：`charAt`、`indexOf`、`lastIndexOf`，找不到返回 -1
- 转换：`String.valueOf`、`parseInt`、`toUpperCase`、`toCharArray`、`format`
- 替换：`replace`、`replaceAll`、`replaceFirst`，都是返回新字符串
- 拆分：`split`，特殊字符要转义
- 截取：`substring`，区间前闭后开
- 去空格：`trim`

**不可变性：**
- String 一旦创建，内容不能改
- 所有"修改"操作都是创建新对象
- 双引号字符串放在常量池，`new` 出来的每次都是新对象

**StringBuilder：**
- 适合频繁修改字符串的场景
- 常用 `append`、`insert`、`delete`、`reverse`
- 通过 `toString` 转回 String

String 是平时用得最多的类之一，把这些方法练熟，后面写题目和做项目都会顺手很多。下一篇会整理异常处理：什么是异常、怎么用 try-catch 捕获、throws 又是什么意思。

---

**Java入门学习笔记系列，持续更新中。**
