# Java数据结构学习笔记（第五篇）：ArrayList 与顺序表

> 上一篇把 `List` 接口的规则讲完了，最后留了个问题：`ArrayList` 和 `LinkedList` 方法一模一样，凭什么要分成两个？这篇先拆 `ArrayList`——它底下就是本篇要讲的顺序表。看完你会发现，所谓 `ArrayList`，本质就是"一个会自动变长的数组"。

---

## 一、线性表：逻辑上是一条线

课件第一页给的定义很短：

> 线性表是 n 个具有相同特性的数据元素的有限序列。

拆开看是三个限定词：

- **相同特性**：一列数、一串名字、一副牌，类型得一致。混着放就不算线性表。
- **有限**：个数是确定的，n 可以是 0。
- **序列**：元素之间有先后。除了第一个和最后一个，每个元素都有唯一的前驱和后继。

生活里的例子到处都是：食堂窗口前排队的人、电影院的一排座位、公交线路上的站点。它们共同的特点是——**你能说出"第几个"**。

这里有个特别容易混的点：**逻辑上线性，不等于物理上连续**。

| 层面 | 说的是什么 | 例子 |
|---|---|---|
| 逻辑结构 | 元素排成一条线，有前有后 | 排队的人 |
| 物理结构 | 内存里到底怎么摆 | 数组（连续）、链表（分散） |

排队的人逻辑上是一条线，但他们站的位置不一定挨着；反过来，内存里连续摆放的数组，逻辑上也可以被解读成别的东西。课件那句"逻辑上是线性结构，物理上不一定是连续的"，讲的就是这件事。

常见的线性表有四种：**顺序表、链表、栈、队列**。后三种后面单独讲，这篇只碰顺序表。

---

## 二、顺序表：用数组实现的线性表

顺序表的定义更具体：**用一段物理地址连续的存储单元，依次存放数据元素**。翻译成人话——找一块连续的内存，一个挨一个地放。

数组本身就是"连续的一段"，所以顺序表一般就用数组实现，增、删、查、改都在这一个数组上做。

动手之前，必须先把两个数分清：

```
下标：   0      1      2      3      4   ...    9
      ┌──────┬──────┬──────┬──────┬──────┬─────┬──────┐
      │ 小明 │ 小红 │ 小刚 │      │      │ ... │      │
      └──────┴──────┴──────┴──────┴──────┴─────┴──────┘
        └──── size = 3 ────┘    └── 还空着 7 个位置 ──┘
                              capacity（数组长度）= 10
```

- **size**：真正存了几个元素，是逻辑长度
- **capacity**：数组本身有多长，是物理容量

`size` 永远小于等于 `capacity`。这两个数一旦用混，程序要么打印出一串 `null`，要么往数组外面写。后面手写代码时，这是第一个要盯住的地方。

课件列出了一个顺序表该有的接口：

| 方法 | 作用 |
|---|---|
| `SeqList()` / `SeqList(int initcapacity)` | 构造，可指定初始容量 |
| `add(int data)` | 尾插 |
| `add(int pos, int data)` | 在 pos 位置插入 |
| `contains(int toFind)` | 是否包含某个元素 |
| `indexOf(int toFind)` | 找位置，找不到返回 -1 |
| `get(int pos)` | 取 pos 位置的元素 |
| `set(int pos, int value)` | 改 pos 位置的元素 |
| `remove(int toRemove)` | 删除第一次出现的元素 |
| `size()` | 有效元素个数 |
| `clear()` | 清空 |
| `display()` | 打印（辅助方法，不属于顺序表本身） |

这张表和上一篇 `List` 的几乎一一对应——`ArrayList` 就是把这张表用数组实现了一遍。所以手写一遍，等于把 `ArrayList` 拆开看了一遍。

---

## 三、手写一个顺序表

课件给的是空壳方法，直接照着填空容易变成"抄了一遍但没懂"。这里换个场景：把顺序表当成**教室里的座位**，`String[]` 存名字，`size` 记坐了几个人。

```java
class SeatList {
    private String[] seats;
    private int size;

    SeatList() {
        seats = new String[10];
        size = 0;
    }

    void add(String name) {
        if (size == seats.length) {
            grow();
        }
        seats[size] = name;
        size++;
    }

    void add(int pos, String name) {
        if (pos < 0 || pos > size) {
            throw new IndexOutOfBoundsException("位置不合法：" + pos);
        }
        if (size == seats.length) {
            grow();
        }
        for (int i = size; i > pos; i--) {
            seats[i] = seats[i - 1];
        }
        seats[pos] = name;
        size++;
    }

    String get(int pos) {
        if (pos < 0 || pos >= size) {
            throw new IndexOutOfBoundsException("位置不合法：" + pos);
        }
        return seats[pos];
    }

    void set(int pos, String name) {
        if (pos < 0 || pos >= size) {
            throw new IndexOutOfBoundsException("位置不合法：" + pos);
        }
        seats[pos] = name;
    }

    int indexOf(String name) {
        for (int i = 0; i < size; i++) {
            if (seats[i].equals(name)) {
                return i;
            }
        }
        return -1;
    }

    boolean contains(String name) {
        return indexOf(name) != -1;
    }

    String remove(int pos) {
        if (pos < 0 || pos >= size) {
            throw new IndexOutOfBoundsException("位置不合法：" + pos);
        }
        String old = seats[pos];
        for (int i = pos; i < size - 1; i++) {
            seats[i] = seats[i + 1];
        }
        size--;
        seats[size] = null;
        return old;
    }

    int size() {
        return size;
    }

    void clear() {
        for (int i = 0; i < size; i++) {
            seats[i] = null;
        }
        size = 0;
    }

    private void grow() {
        String[] bigger = new String[seats.length + (seats.length >> 1)];
        for (int i = 0; i < size; i++) {
            bigger[i] = seats[i];
        }
        seats = bigger;
    }

    public String toString() {
        StringBuilder sb = new StringBuilder("[");
        for (int i = 0; i < size; i++) {
            if (i > 0) {
                sb.append(", ");
            }
            sb.append(seats[i]);
        }
        return sb.append("]").toString();
    }
}

public class Al1 {
    public static void main(String[] args) {
        SeatList class3 = new SeatList();
        class3.add("小明");
        class3.add("小红");
        class3.add("小刚");
        System.out.println("初始：" + class3 + "，共 " + class3.size() + " 人");

        class3.add(1, "小美");
        System.out.println("插队后：" + class3);

        class3.set(2, "小红红");
        System.out.println("改名后：" + class3 + "，下标 2 是 " + class3.get(2));

        System.out.println("小刚的下标是：" + class3.indexOf("小刚"));
        System.out.println("有没有小美：" + class3.contains("小美"));

        String left = class3.remove(1);
        System.out.println("离开的是：" + left + "，剩下：" + class3);

        class3.clear();
        System.out.println("清空后：" + class3 + "，大小 " + class3.size());
    }
}
```

跑出来是这样：

```
初始：[小明, 小红, 小刚]，共 3 人
插队后：[小明, 小美, 小红, 小刚]
改名后：[小明, 小美, 小红红, 小刚]，下标 2 是 小红红
小刚的下标是：3
有没有小美：true
离开的是：小美，剩下：[小明, 小红红, 小刚]
清空后：[]，大小 0
```

代码不算长，但有几处值得停下来看。

### 3.1 尾插：先看还有没有空位

```java
void add(String name) {
    if (size == seats.length) {
        grow();
    }
    seats[size] = name;
    size++;
}
```

`size == seats.length` 就是"坐满了"。没满就写进 `seats[size]`，然后 `size++`；满了先扩容。

注意写的是 `seats[size]`，不是 `seats[size + 1]`。`size` 表示"已经坐了几个人"，而下标从 0 开始，所以下一个空位恰好是 `seats[size]`。

### 3.2 指定位置插入：从后往前挪

现在坐了三个人，要在 1 号位插一个"小美"：

```
原始：  [小明, 小红, 小刚]          size = 3

第 1 步：seats[3] = seats[2]
        [小明, 小红, 小刚, 小刚]

第 2 步：seats[2] = seats[1]
        [小明, 小红, 小红, 小刚]

第 3 步：seats[1] = 小美
        [小明, 小美, 小红, 小刚]    完成
```

代码就是那个 `for`：

```java
for (int i = size; i > pos; i--) {
    seats[i] = seats[i - 1];
}
```

> ⚠️ 这个循环**必须从后往前**。如果写成 `for (int i = pos; i < size; i++) seats[i + 1] = seats[i];`，第一次就把 `seats[2]` 覆盖成 `seats[1]` 的值，第二次又把 `seats[3]` 覆盖成已经被改过的 `seats[2]`——最后一个"小刚"直接消失，中间的人重复出现，等于数据被自己踩掉了。

挪完之后 `size++`，因为元素多了一个。

### 3.3 删除：从前往后挪

删除的方向正好相反。删掉 1 号位的"小美"：

```
原始：  [小明, 小美, 小红, 小刚]    size = 4

第 1 步：seats[1] = seats[2]   → [小明, 小红, 小红, 小刚]
第 2 步：seats[2] = seats[3]   → [小明, 小红, 小刚, 小刚]
最后：  size--，seats[size] = null
        → [小明, 小红, 小刚]      size = 3
```

```java
for (int i = pos; i < size - 1; i++) {
    seats[i] = seats[i + 1];
}
size--;
seats[size] = null;
```

- 循环终点是 `size - 1`，因为循环体里访问的是 `i + 1`，取到 `size - 1` 正好能把最后一个元素搬过来。
- 最后那行 `seats[size] = null` 不是多余的。数组槽位里还留着对"小刚"的引用，即使 `size` 已经减了，这个对象也不会被回收。列表活得越久，这种残留越像内存泄漏。

### 3.4 get / set：判断要用 size

```java
String get(int pos) {
    if (pos < 0 || pos >= size) {
        throw new IndexOutOfBoundsException("位置不合法：" + pos);
    }
    return seats[pos];
}
```

判断用的是 `size`，不是 `seats.length`。数组后面那几个空位物理上确实存在，但里面是 `null`，不是有效元素，取到了也不该算数。

> 💡 `ArrayList` 的 `get` 也是这么查的。JDK 8 里是 `rangeCheck(index)`，判断 `index >= size` 就抛 `IndexOutOfBoundsException`。上一篇 `nums.remove(30)` 报的 `Index: 30, Size: 3`，就是这个方法拼出来的。

### 3.5 扩容：1.5 倍

```java
private void grow() {
    String[] bigger = new String[seats.length + (seats.length >> 1)];
    for (int i = 0; i < size; i++) {
        bigger[i] = seats[i];
    }
    seats = bigger;
}
```

`seats.length >> 1` 就是长度除以 2。10 个座位满了换 15 个，15 满了换 22 个。新数组申请好，把旧数据一个个搬过去，再让 `seats` 指向新数组——旧数组没人引用，等着被回收。

这段就是 `ArrayList` 扩容的雏形。第八节会看到，JDK 里的写法和它几乎一样。

---

## 四、ArrayList 到底是什么

课件介绍 `ArrayList` 时列了六条，逐条拆一下：

| 说明 | 含义 |
|---|---|
| 以泛型方式实现，必须先实例化 | `ArrayList<String>` 里的 `String` 就是元素类型，`new` 时补上 |
| 实现了 `RandomAccess` | 支持随机访问，按下标取是 O(1) |
| 实现了 `Cloneable` | 可以 `clone()` |
| 实现了 `Serializable` | 可以序列化，能写进文件或走网络 |
| 不是线程安全的 | 单线程放心用，多线程要另想办法 |
| 底层连续空间 + 动态扩容 | 本质是"会自动变长的顺序表" |

后面三条都是**标记接口**——接口里没有方法，只是个"标签"，让别的代码 `instanceof` 一下就知道该怎么对待你。

### 4.1 RandomAccess：告诉别人"我按下标取很快"

```java
public interface RandomAccess {
}
```

里面是空的。但 JDK 里有些算法会先判断：

```java
if (list instanceof RandomAccess) {
    // 用下标 for 遍历
} else {
    // 用迭代器遍历
}
```

原因很简单：`ArrayList` 的 `get(i)` 是直接算地址，O(1)；`LinkedList` 的 `get(i)` 得从头一个个数过去，O(N)。同样是遍历一遍，前者用下标 for 是 O(N)，后者用下标 for 会退化成 O(N²)。

所以 `ArrayList` 实现了这个接口，`LinkedList` 没有。这是一个很诚实的"速度声明"。

### 4.2 Cloneable：能克隆，但只复制一层

`ArrayList` 的 `clone()` 大概是这样的：

```java
ArrayList<?> v = (ArrayList<?>) super.clone();
v.elementData = Arrays.copyOf(elementData, size);
```

它复制了一个新数组，但**数组里的元素对象还是同一批引用**。所以字符串这种不可变对象看不出问题：

```java
ArrayList<String> a = new ArrayList<>();
a.add("小明");
ArrayList<String> b = (ArrayList<String>) a.clone();
b.set(0, "小红");
System.out.println(a.get(0));   // 还是 小明
```

`set` 换的是引用，`a` 的数组没被动过。但如果元素是可变对象（比如一个 `Student`），改 `b.get(0).name` 会连 `a` 里那个一起改——它们指向同一个对象。

> 💡 顺便一个细节：`clone()` 里写的是 `Arrays.copyOf(elementData, size)`，只复制有效元素。所以克隆出来的列表容量也被裁到 `size`，不会把后面那一堆空位带过去。

### 4.3 Serializable：能存能传

`elementData` 被标记成了 `transient`：

```java
transient Object[] elementData;
```

`transient` 的意思是"默认序列化时跳过这个字段"。`ArrayList` 自己写了 `writeObject`，只写 `size` 和 `size` 个有效元素，不把后面那半截空数组写进文件。文件更小，也不会把 `null` 带进去。

### 4.4 非线程安全

多个线程同时对同一个 `ArrayList` 做 `add`，可能出两种问题：

- 两个线程同时判断"还有空位"，都往同一个下标写，后写的覆盖先写的，元素凭空少一个
- 扩容时一个线程刚申请完新数组，另一个线程还在往旧数组里写

常见的三个替代方案：

| 方案 | 特点 |
|---|---|
| `Vector` | 方法上加 `synchronized`，老类，性能一般 |
| `CopyOnWriteArrayList` | 写的时候复制整份数组，读不加锁，适合读多写少 |
| 自己加锁 / `Collections.synchronizedList` | 灵活，但复合操作仍要自己保证原子性 |

> 💡 `Collections.synchronizedList` 只保证**单个方法**线程安全，`if (!list.contains(x)) list.add(x)` 这种"先查后加"依然要自己同步。这类组合操作是并发里最常见的坑。

---

## 五、构造方法：三种写法

```java
import java.util.ArrayList;
import java.util.List;

public class Al2 {
    public static void main(String[] args) {
        List<String> empty = new ArrayList<>();
        System.out.println("空列表：" + empty + "，大小 " + empty.size());

        List<Integer> withCapacity = new ArrayList<>(10);
        System.out.println("指定容量后，大小：" + withCapacity.size());

        withCapacity.add(1);
        withCapacity.add(2);
        System.out.println("加了两个：" + withCapacity + "，大小 " + withCapacity.size());

        ArrayList<Integer> copy = new ArrayList<>(withCapacity);
        copy.add(3);
        System.out.println("复制出来的：" + copy);
        System.out.println("原来的：" + withCapacity);

        List raw = new ArrayList();
        raw.add("111");
        raw.add(100);
        System.out.println("裸类型塞进去：" + raw);
        try {
            String s = (String) raw.get(1);
            System.out.println(s);
        } catch (ClassCastException e) {
            System.out.println("取出来强转就炸：" + e);
        }
    }
}
```

输出：

```
空列表：[]，大小 0
指定容量后，大小：0
加了两个：[1, 2]，大小 2
复制出来的：[1, 2, 3]
原来的：[1, 2]
裸类型塞进去：[111, 100]
取出来强转就炸：java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
```

三处细节。

### 5.1 指定容量，不等于已经有元素

`new ArrayList<>(10)` 只是把底层数组先开成 10 个格子，`size` 仍然是 0，打印出来是 `[]`。

这个构造的价值在第八节：如果你确定要装 100 个元素，直接 `new ArrayList<>(100)`，能省掉 7 次扩容。

### 5.2 拷贝构造：复制一份，互不影响

`new ArrayList<>(withCapacity)` 会把另一个集合的元素**复制**进新列表。之后给 `copy` 加元素，原列表不变：

```
复制出来的：[1, 2, 3]
原来的：[1, 2]
```

这和上一节 `clone()` 是同一个道理：复制的是"有哪些元素"，不是元素对象本身。

### 5.3 裸类型：能编译，但两处都在提醒你

```java
List raw = new ArrayList();
raw.add("111");
raw.add(100);
```

左边没写尖括号，编译器就不检查类型，字符串和整数都能塞进去。`javac` 编译这段时会冒一句：

```
注: Al2.java使用了未经检查或不安全的操作。
```

这是 unchecked 警告，意思是"我知道你在干什么，但我不保证安全"。真正出事是在取的时候：

```
取出来强转就炸：java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
```

> **⚠️ 泛型是编译期的检查，运行期会被擦除（第三篇专门验证过）。声明时不写类型，编译器就没法替你挡；等强转失败，只能在运行期炸。**
>
> 正确写法永远是 `List<String> list = new ArrayList<>();`——左边写接口、右边写实现，中间别省尖括号。

---

## 六、常用操作跑一遍

```java
import java.util.ArrayList;
import java.util.List;

public class Al3 {
    public static void main(String[] args) {
        List<String> courses = new ArrayList<>();
        courses.add("JavaSE");
        courses.add("JavaWeb");
        courses.add("JavaEE");
        courses.add("JVM");
        courses.add("测试课程");
        System.out.println("初始：" + courses + "，共 " + courses.size());

        System.out.println("第 2 门（下标 1）：" + courses.get(1));
        courses.set(1, "JavaWEB");
        System.out.println("改名后：" + courses);

        courses.add(1, "数据结构");
        System.out.println("插入后：" + courses);

        courses.remove("JVM");
        System.out.println("删掉 JVM：" + courses);

        courses.remove(courses.size() - 1);
        System.out.println("删掉最后一门：" + courses);

        if (courses.contains("测试课程")) {
            System.out.println("测试课程还在");
        }

        courses.add("JavaSE");
        System.out.println("JavaSE 第一次下标：" + courses.indexOf("JavaSE"));
        System.out.println("JavaSE 最后一次下标：" + courses.lastIndexOf("JavaSE"));

        List<String> part = courses.subList(0, 3);
        System.out.println("前三门：" + part);

        courses.clear();
        System.out.println("清空后大小：" + courses.size());
    }
}
```

输出：

```
初始：[JavaSE, JavaWeb, JavaEE, JVM, 测试课程]，共 5
第 2 门（下标 1）：JavaWeb
改名后：[JavaSE, JavaWEB, JavaEE, JVM, 测试课程]
插入后：[JavaSE, 数据结构, JavaWEB, JavaEE, JVM, 测试课程]
删掉 JVM：[JavaSE, 数据结构, JavaWEB, JavaEE, 测试课程]
删掉最后一门：[JavaSE, 数据结构, JavaWEB, JavaEE]
JavaSE 第一次下标：0
JavaSE 最后一次下标：4
前三门：[JavaSE, 数据结构, JavaWEB]
清空后大小：0
```

对着输出看几个点。

**`get` 和 `set` 用的是下标，不是"第几个"。** `get(1)` 拿到的是第 2 门，因为下标从 0 开始。课件注释写"index 必须介于 [0, size) 之间"，取到 `size` 就过界了。

**`add(1, ...)` 会把后面的人整体右移。** 插入"数据结构"之后，原来的 `JavaWEB`、`JavaEE`、`JVM`、`测试课程` 下标全部加一。

**`remove` 有两个版本。** `remove("JVM")` 传的是对象，删掉第一个相等的；`remove(courses.size() - 1)` 传的是 `int`，删的是下标。上一篇专门讲过这个重载陷阱，在 `List<Integer>` 上尤其危险。

**`indexOf` / `lastIndexOf`。** 加了一个重复的 `JavaSE` 之后，第一次出现在 0，最后一次在 4。两个方法找不到时都返回 `-1`。

**`subList(0, 3)` 左闭右开。** 取下标 0、1、2，也就是"前三门"。它返回的是视图不是副本——上一篇验证过，改视图会改原表。

**`clear()` 只清元素，不缩容。** `size` 归 0，但底层数组还是原来那么大。想真正把空间还回去，得再调 `trimToSize()`。

---

## 七、三种遍历

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class Al4 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        list.add("D");

        System.out.println("下标 for：");
        for (int i = 0; i < list.size(); i++) {
            System.out.print(list.get(i) + " ");
        }
        System.out.println();

        System.out.println("增强 for：");
        for (String s : list) {
            System.out.print(s + " ");
        }
        System.out.println();

        System.out.println("迭代器：");
        Iterator<String> it = list.iterator();
        while (it.hasNext()) {
            System.out.print(it.next() + " ");
        }
        System.out.println();
    }
}
```

```
下标 for：
A B C D 
增强 for：
A B C D 
迭代器：
A B C D
```

三种都能走完，差别在"怎么拿到下一个元素"：

| 方式 | 拿到元素的方式 | 适合 |
|---|---|---|
| 下标 for | `list.get(i)` | `ArrayList`（O(1)），需要下标时 |
| 增强 for | 编译器转成迭代器 | 只读遍历，写法最短 |
| 迭代器 | `it.next()` | 遍历中要删除元素时 |

课件说最常用的是前两种，这话没错。但得知道代价：

- 在 `ArrayList` 上，下标 for 和迭代器都是 O(N)，差别只是常数
- 在 `LinkedList` 上，下标 for 会变成 O(N²)，这时候必须用迭代器

> 💡 增强 for 编译后就是 `Iterator` 的 `hasNext()` / `next()`。所以上一篇那个 `ConcurrentModificationException`，在增强 for 里同样会抛——它们本来就是同一套机制。

---

## 八、扩容机制

课件在这一节先抛了个问题：

```java
List<Integer> list = new ArrayList<>();
for (int i = 0; i < 100; i++) {
    list.add(i);
}
```

问"有缺陷吗？为什么？"

功能上没缺陷，能正常跑完。但它背后偷偷扩容了 7 次，每次都申请新数组、拷旧数据。这个代价可以用反射看到——`ArrayList` 的容量就是 `elementData` 数组的长度：

```java
import java.lang.reflect.Field;
import java.util.ArrayList;

public class Al5 {
    static int capacityOf(ArrayList<?> list) throws Exception {
        Field field = ArrayList.class.getDeclaredField("elementData");
        field.setAccessible(true);
        return ((Object[]) field.get(list)).length;
    }

    public static void main(String[] args) throws Exception {
        ArrayList<Integer> noPlan = new ArrayList<>();
        int last = 0;
        int grows = 0;
        for (int i = 1; i <= 100; i++) {
            noPlan.add(i);
            int capacity = capacityOf(noPlan);
            if (capacity != last) {
                System.out.println("加到第 " + i + " 个元素，容量变成 " + capacity);
                last = capacity;
                grows++;
            }
        }
        System.out.println("没预估容量，一共扩容 " + grows + " 次");

        ArrayList<Integer> planned = new ArrayList<>(100);
        last = capacityOf(planned);
        int plannedGrows = 0;
        for (int i = 1; i <= 100; i++) {
            planned.add(i);
            int capacity = capacityOf(planned);
            if (capacity != last) {
                plannedGrows++;
                last = capacity;
            }
        }
        System.out.println("一开始就给了 100 的容量，扩容 " + plannedGrows + " 次");
    }
}
```

```
加到第 1 个元素，容量变成 10
加到第 11 个元素，容量变成 15
加到第 16 个元素，容量变成 22
加到第 23 个元素，容量变成 33
加到第 34 个元素，容量变成 49
加到第 50 个元素，容量变成 73
加到第 74 个元素，容量变成 109
没预估容量，一共扩容 7 次
一开始就给了 100 的容量，扩容 0 次
```

容量序列是 `10 → 15 → 22 → 33 → 49 → 73 → 109`，每次大约是上一次的 1.5 倍：

```
10  →  10 + 10 >> 1  =  10 +  5  =  15
15  →  15 + 15 >> 1  =  15 +  7  =  22
22  →  22 + 22 >> 1  =  22 + 11  =  33
33  →  33 + 33 >> 1  =  33 + 16  =  49
49  →  49 + 49 >> 1  =  49 + 24  =  73
73  →  73 + 73 >> 1  =  73 + 36  =  109
```

`>> 1` 是右移一位，等于除以 2 再向下取整：15 的一半是 7，不是 7.5。

### 8.1 源码怎么写的

本机 JDK 8 的 `ArrayList` 里，尾插是这么三步：

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```

`ensureCapacityInternal` 负责算"至少要多大"，如果发现是默认的空数组，就把需求提到 10；然后交给 `ensureExplicitCapacity`：

```java
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```

真正搬家的在 `grow`：

```java
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

对应到自己的手写版：

| ArrayList | 手写版 | 干的事 |
|---|---|---|
| `size` | `size` | 有效元素个数 |
| `elementData` | `seats` | 底层数组 |
| `grow()` | `grow()` | 算新容量 + 拷贝 |
| `Arrays.copyOf` | 循环搬 | 把旧数据搬到新数组 |

几个细节：

- `add` 里的 `size + 1` 是"加完之后至少需要多大"
- `ensureExplicitCapacity` 里的 `modCount++` 每次 `add` 都执行，不管有没有扩容。这就是上一篇 `ConcurrentModificationException` 的计数器来源
- `grow` 先按 1.5 倍算；如果还不够（比如 `addAll` 一次加很多），就直接用 `minCapacity`

### 8.2 第一次为什么是 10

无参构造时，`elementData` 指向的不是长度 10 的数组，而是一个**共享的空数组**：

```java
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
```

第一次 `add` 时，`calculateCapacity` 发现是这个空数组，就把需求提到 `DEFAULT_CAPACITY`：

```java
private static int calculateCapacity(Object[] elementData, int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}
```

`DEFAULT_CAPACITY = 10`。所以"第一次分配 10"准确点说，是**第一次 add 时分配 10**，构造的时候还是空的。这也解释了 `Al5` 第一行——加到第 1 个元素，容量才变成 10。

### 8.3 JDK 17 有什么不同

课件引用的是 JDK 17 的源码，扩容入口从 `grow` 里的两行计算换成了 `ArraysSupport.newLength`：

```java
int newCapacity = ArraysSupport.newLength(oldCapacity,
        minCapacity - oldCapacity, /* minimum growth */
        oldCapacity >> 1        /* preferred growth */);
```

课件对这个方法的注释是"当前长度加上 minGrowth 和 prefGrowth 中的较大值"，也就是 `oldLength + max(minGrowth, prefGrowth)`。首选增量仍然是 `oldCapacity >> 1`（一半），所以**算出来的序列还是 10、15、22……，1.5 倍这件事没变**，只是代码搬进了工具类，顺便处理了溢出。

> ⚠️ 课件最后一节"ArrayList 的问题"里写的是"增容一般是呈 2 倍的增长"。这句话和前面的源码对不上——**`ArrayList` 用的是 1.5 倍**，`oldCapacity >> 1` 就是一半。2 倍是不少动态数组（以及部分语言实现）的选择，但不是 JDK 这个类的做法。本篇特意跑 `Al5`，就是为了让这个数字有出处。

---

## 九、扑克牌：洗牌与发牌

顺序表最适合拿来练手的就是"一副牌"。52 张牌天然是一个线性表：有顺序、能按下标取、能删。

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Random;

class Card {
    String suit;
    int rank;

    Card(String suit, int rank) {
        this.suit = suit;
        this.rank = rank;
    }

    public String toString() {
        return suit + rank;
    }
}

public class Al6 {
    static List<Card> newDeck() {
        String[] suits = {"♠", "♥", "♣", "♦"};
        List<Card> deck = new ArrayList<>(52);
        for (String suit : suits) {
            for (int rank = 1; rank <= 13; rank++) {
                deck.add(new Card(suit, rank));
            }
        }
        return deck;
    }

    static void shuffle(List<Card> deck) {
        Random random = new Random(20261116L);
        for (int i = deck.size() - 1; i > 0; i--) {
            int j = random.nextInt(i + 1);
            Card temp = deck.get(i);
            deck.set(i, deck.get(j));
            deck.set(j, temp);
        }
    }

    public static void main(String[] args) {
        List<Card> deck = newDeck();
        System.out.println("刚拆封：" + deck.size() + " 张，第一张是 " + deck.get(0));

        shuffle(deck);
        System.out.println("洗完后前五张：" + deck.subList(0, 5));

        List<List<Card>> hands = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            hands.add(new ArrayList<>());
        }
        for (int round = 0; round < 5; round++) {
            for (int player = 0; player < 3; player++) {
                hands.get(player).add(deck.remove(0));
            }
        }

        System.out.println("A：" + hands.get(0));
        System.out.println("B：" + hands.get(1));
        System.out.println("C：" + hands.get(2));
        System.out.println("牌堆还剩：" + deck.size() + " 张");
    }
}
```

```
刚拆封：52 张，第一张是 ♠1
洗完后前五张：[♣1, ♦1, ♣13, ♠13, ♦11]
A：[♣1, ♠13, ♠11, ♦2, ♣3]
B：[♦1, ♦11, ♦9, ♠1, ♣10]
C：[♣13, ♥11, ♠12, ♣2, ♥5]
牌堆还剩：37 张
```

### 9.1 造一副牌

```java
for (String suit : suits) {
    for (int rank = 1; rank <= 13; rank++) {
        deck.add(new Card(suit, rank));
    }
}
```

四种花色 × 十三张点数 = 52。`new ArrayList<>(52)` 直接给了 52 的容量，所以这 52 次 `add` 一次都不会扩容——这就是"预估容量"的实际用处。

### 9.2 洗牌：从后往前换

```java
for (int i = deck.size() - 1; i > 0; i--) {
    int j = random.nextInt(i + 1);
    Card temp = deck.get(i);
    deck.set(i, deck.get(j));
    deck.set(j, temp);
}
```

这个算法叫 Fisher–Yates 洗牌，两个细节：

- `random.nextInt(i + 1)` 取的是 `[0, i]`，**包含 i 自己**。如果写成 `nextInt(i)`，取到的是 `[0, i-1]`，第 i 张在这一轮必然被换走、不可能留在原位，而之后循环只处理前面的位置，它再也回不到第 i 位。结果就是"本来在第 i 位的牌永远不可能停在第 i 位"，分布是有偏的。
- 从后往前扫。轮到第 i 张时，它后面的位置都已经定好了，只和前面（含自己）交换，不会破坏已定的部分。

> 💡 课件里写的是 `random.nextInt(i)`，从"洗得均匀"的角度看是个小瑕疵。这里改成了 `i + 1`，其余思路一致。

### 9.3 发牌：`remove(0)` 的代价

```java
hands.get(player).add(deck.remove(0));
```

三个人轮流，每人 5 张，一共从牌堆头部拿走 15 张。

`remove(0)` 删的是第一个元素，后面那 51 张要整体前移一位。发 15 张牌，累计搬动的元素是 51 + 50 + … + 37，大约 660 次。52 张牌感觉不到，但如果是十万条数据的队列，每次 `remove(0)` 都搬十万个元素，就会明显卡。

这个代价正好接上下一节。

---

## 十、杨辉三角

力扣 118 题，是 `List<List<Integer>>` 的典型用法——每一行本身就是一个 `ArrayList`。

```java
import java.util.ArrayList;
import java.util.List;

public class Al7 {
    static List<List<Integer>> yanghui(int rows) {
        List<List<Integer>> triangle = new ArrayList<>();
        for (int i = 0; i < rows; i++) {
            List<Integer> row = new ArrayList<>();
            for (int j = 0; j <= i; j++) {
                if (j == 0 || j == i) {
                    row.add(1);
                } else {
                    row.add(triangle.get(i - 1).get(j - 1) + triangle.get(i - 1).get(j));
                }
            }
            triangle.add(row);
        }
        return triangle;
    }

    public static void main(String[] args) {
        List<List<Integer>> triangle = yanghui(7);
        for (int i = 0; i < triangle.size(); i++) {
            for (int k = 0; k < triangle.size() - i - 1; k++) {
                System.out.print("  ");
            }
            System.out.println(triangle.get(i));
        }
    }
}
```

```
            [1]
          [1, 1]
        [1, 2, 1]
      [1, 3, 3, 1]
    [1, 4, 6, 4, 1]
  [1, 5, 10, 10, 5, 1]
[1, 6, 15, 20, 15, 6, 1]
```

规律只有两条：

1. 每行第一个和最后一个都是 1
2. 中间的元素 = 上一行同列 + 上一行前一列

```java
if (j == 0 || j == i) {
    row.add(1);
} else {
    row.add(triangle.get(i - 1).get(j - 1) + triangle.get(i - 1).get(j));
}
```

第 i 行有 i + 1 个元素，下标从 0 到 i。`j == 0` 是左边界，`j == i` 是右边界，都填 1；中间的 j 落在 1 到 i - 1，所以 `get(j - 1)` 最小取 0、`get(j)` 最大取 i - 1，正好都在上一行范围内，不会越界。

> 💡 这里的 `row` 每行都是新建的 `new ArrayList<>()`，所以改一行不影响别的行。如果图省事复用同一个 `row` 对象加进 `triangle`，最后七行会变成同一个引用，打印出来全是最后一行。

---

## 十一、ArrayList 的三个问题

课件最后一节列了三条，本质上都是"数组"这个底层结构带来的。

### 11.1 中间插入/删除要搬元素，O(N)

`Al1` 里那个 `for` 循环就是搬移。在第 pos 个位置插入，要搬 `size - pos` 个元素；删除同理。

平均来看，插入位置在中间，要搬一半的元素，所以时间复杂度是 **O(N)**。这也是 `ArrayList` 适合"尾部加、按下标读"，不适合"频繁在中间插删"的原因。

### 11.2 扩容要申请新空间、拷数据、释放旧空间

`Al5` 里 100 个元素扩了 7 次，最后一次从 73 扩到 109，要拷 73 个元素。数据量越大，单次扩容越贵。

不过这件事是**均摊**的：每次容量涨 1.5 倍，扩容次数是 O(log N)，总拷贝量也是 O(N)，平均到每次 `add` 仍是 O(1)。所以 `add` 的均摊复杂度是 O(1)，只是偶尔会卡一下。

### 11.3 扩容留空位，浪费空间

按 1.5 倍扩，容量 100 满了之后变 150：

```
容量 100，装满了  →  扩容到 150
再插 5 个        →  用了 105，空着 45 个位置
```

如果后面不再插入，这 45 个位置就一直空着。数组本身占着这块内存，槽位里是 `null` 引用，看着不起眼，数量大了也不划算。

课件这里写的是"2 倍增长，浪费 95 个"，按 1.5 倍算实际是浪费 45 个。结论是一样的：**扩容总会多要一些，用不满就浪费。**

### 11.4 怎么办：换链表

三个问题的根源是同一个——**顺序表要求元素连续存放**。连续才能 O(1) 按下标取，但也意味着插入要挪、扩容要整块拷贝。

如果不要"连续"，改成"每个元素记住下一个在哪"，插入就只需要改几个引用，也不用提前申请一大块连续空间。这就是**链表**。

代价是按下标取不再是 O(1)，得从头顺着走。所以选哪个，取决于你的操作里"按下标读"多，还是"中间插删"多。

下一篇就拆 `LinkedList` 和链表，看看它怎么用节点把这些事解决掉，又为此付出了什么代价。

---

## 总结

这篇从线性表一路走到 `ArrayList` 的源码，主线其实只有一句：**`ArrayList` 就是一个会自动扩容的数组**。

**概念：**
- 线性表：n 个相同类型的元素排成一条线，逻辑线性、物理不一定连续
- 顺序表：用连续空间（一般是数组）实现的线性表
- `size` 是有效元素个数，`capacity` 是数组长度，两者不能混

**手写顺序表的三个动作：**
- 尾插：先判满，再写 `seats[size]`，最后 `size++`
- 指定位置插入：**从后往前**搬，否则数据被自己覆盖
- 删除：**从前往后**搬，最后 `size--`，并把空出来的槽位置 `null`

**ArrayList 的六个标签：**
- 泛型实现、`RandomAccess`（按下标 O(1)）、`Cloneable`（浅拷贝）、`Serializable`
- 非线程安全，多线程考虑 `Vector` / `CopyOnWriteArrayList` / 外部加锁
- 底层连续空间 + 动态扩容，本质是动态顺序表

**扩容：**
- 无参构造时数组是空的，第一次 `add` 才分配 10
- 之后按 1.5 倍扩：10 → 15 → 22 → 33 → 49 → 73 → 109
- 每次扩容都要 `Arrays.copyOf`，但均摊到每次 `add` 仍是 O(1)
- 课件说的"2 倍"和源码不符，`ArrayList` 是 1.5 倍

**几个坑：**
- `new ArrayList<>(10)` 只给容量，`size` 还是 0
- 裸类型 `List list = new ArrayList()` 能过编译，取出来强转才炸
- `subList` 是视图，改它会改原表
- 遍历时删除要用 `iterator.remove()`
- `clear()` 不缩容，要缩用 `trimToSize()`

下一篇进 `LinkedList` 和链表：看看 `Node` 怎么把元素串起来、为什么 `get(i)` 变成了 O(N)，以及为什么用链表实现栈和队列那么自然。

---

**Java数据结构学习笔记系列，持续更新中。**
