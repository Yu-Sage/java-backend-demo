# Java数据结构学习笔记（第四篇）：List接口

> 上一篇把泛型这块地基补完了，从这篇开始正式进入具体的容器。第一个登场的是 `List`——集合框架里最常用的一支。不过它本身是个接口，不能 `new`，得先搞清楚它规定了什么，再去找实现类。

---

## 一、List 站在哪一层

打开课件第一页，是一张很简单的继承图：

```
Iterable（能逐个遍历）
   └─ Collection（容器的通用操作）
        └─ List（有序、可重复、有下标）
             ├─ ArrayList
             └─ LinkedList
```

箭头方向要读对：`List` 继承 `Collection`，`Collection` 继承 `Iterable`。也就是说，越往下功能越多。

这三层不是随便分的，每一层都在解决一类问题：

- `Iterable` 只保证一件事：**这个容器能被一个一个地遍历**
- `Collection` 在遍历之上，加了**增、删、查、清空**这些所有容器都需要的动作
- `List` 再加一层**下标**，于是可以精确到"第几个"

下一篇要讲的 `ArrayList` 和 `LinkedList`，都是 `List` 的实现类——接口是同一套，底下的实现完全是两条路。

---

## 二、三个接口各管什么

### 2.1 Iterable：只负责"能遍历"

| 方法 | 返回 | 作用 |
|---|---|---|
| `iterator()` | `Iterator<T>` | 拿到迭代器，逐个访问 |
| `forEach(Consumer<? super T>)` | `void` | 对每个元素执行一段动作 |
| `spliterator()` | `Spliterator<T>` | 并行遍历用，了解即可 |

`forEach` 是 Java 8 加的默认方法，可以配合 lambda 写：

```java
list.forEach(s -> System.out.println(s));
```

它本质上还是拿迭代器挨个跑一遍，只是写法短了。

### 2.2 Collection：所有容器的公共动作

| 方法 | 返回 | 作用 |
|---|---|---|
| `size()` | `int` | 元素个数 |
| `isEmpty()` | `boolean` | 是不是空的 |
| `contains(Object o)` | `boolean` | 有没有 o |
| `add(E e)` | `boolean` | 加一个元素 |
| `remove(Object o)` | `boolean` | 删掉一个 o |
| `clear()` | `void` | 全部清空 |
| `containsAll(Collection<?> c)` | `boolean` | 是不是包含 c 里的所有元素 |
| `addAll(Collection<? extends E> c)` | `boolean` | 把 c 里的元素全加进来 |
| `removeAll(Collection<?> c)` | `boolean` | 删掉所有出现在 c 里的元素 |
| `retainAll(Collection<?> c)` | `boolean` | 只保留 c 里也有的 |
| `toArray()` | `Object[]` | 转成数组 |
| `iterator()` | `Iterator<E>` | 拿迭代器 |

> 💡 注意这张表里**没有 `get`**。因为 `Collection` 管的是 `List`、`Set`、`Queue` 这些所有容器，而 `Set` 不按位置存放、`Queue` 只让你从队头取，它们都没有"第几个"这个概念。所以按下标取元素这件事，只能由 `List` 自己加。

### 2.3 List：多了"下标"这一层

| 方法 | 返回 | 作用 |
|---|---|---|
| `get(int index)` | `E` | 取下标 index 的元素 |
| `set(int index, E e)` | `E` | 改下标 index 的元素，返回被换掉的旧值 |
| `add(int index, E e)` | `void` | 插到 index 这个位置 |
| `remove(int index)` | `E` | 删下标 index 的元素，返回被删掉的 |
| `indexOf(Object o)` | `int` | o 第一次出现的下标 |
| `lastIndexOf(Object o)` | `int` | o 最后一次出现的下标 |
| `subList(int from, int to)` | `List<E>` | 截取 `[from, to)` 这一段 |
| `listIterator()` | `ListIterator<E>` | 双向迭代器，能往前也能往后 |
| `sort(Comparator<? super E> c)` | `void` | 排序 |
| `replaceAll(UnaryOperator<E>)` | `void` | 每个元素统一做一次替换 |

到这里就能看出分层的意义了：`Collection` 的 `add` 只管"加进去"，`List` 的 `add(int, E)` 还能管"加到第几个"；`Collection` 的 `remove` 只认对象，`List` 还能按下标删。

---

## 三、List 就是线性表

课件里有一句话很关键：

> 站在数据结构的角度来看，List 就是一个线性表，即 n 个具有相同类型元素的有限序列。

拆开看是三个特征：

**1. 有序**

放进去的顺序，就是取出来的顺序。先加"买菜"再加"取快递"，遍历时第一个一定是"买菜"。

**2. 可重复**

同一个值可以出现很多次。`["小明", "小红", "小明"]` 是完全合法的 `List`。

**3. 有下标**

每个元素都有一个位置编号，从 `0` 开始，到 `size() - 1` 结束，中间不跳号。

> 💡 对最常用的 `HashSet` 来说，这三点正好是它的反面：不按插入顺序保存，也不允许重复。所以"要不要按位置取"这一条，基本就决定了该选 `List` 还是 `Set`。

"有限序列"里的"序列"，强调的是**位置有意义**：`[A, B]` 和 `[B, A]` 是两个不同的线性表。

---

## 四、接口不能直接 new

这一点课件特意用黑体标了：**List 是个接口，并不能直接用来实例化。**

不信可以试试：

```java
import java.util.List;

public class LxErr1 {
    public static void main(String[] args) {
        List<String> list = new List<>();
    }
}
```

编译直接拒绝：

```
LxErr1.java:5: 错误: List是抽象的; 无法实例化
        List<String> list = new List<>();
                            ^
1 个错误
```

正确的写法是先 `import` 一个实现类，再 `new` 它：

```java
import java.util.ArrayList;
import java.util.List;

List<String> list = new ArrayList<>();
```

左边声明成 `List`，右边 `new` 的是 `ArrayList`。

> 💡 为什么左边不直接写 `ArrayList<String>`？因为后面万一想换成 `LinkedList`，只改右边这一处就够了，用到 `list` 的地方一行都不用动。这就是"面向接口编程"最朴素的好处。

---

## 五、增、查、改跑一遍

先拿一个周末待办清单试试手：

```java
import java.util.ArrayList;
import java.util.List;

public class Lx1 {
    public static void main(String[] args) {
        List<String> todo = new ArrayList<>();
        todo.add("买菜");
        todo.add("取快递");
        todo.add("写作业");

        System.out.println("一共 " + todo.size() + " 件事");
        System.out.println("第一件：" + todo.get(0));
        System.out.println("清单：" + todo);

        todo.add(1, "交电费");
        System.out.println("插队后：" + todo);

        String old = todo.set(0, "买水果");
        System.out.println("被换掉的是：" + old);
        System.out.println("现在：" + todo);
    }
}
```

运行结果：

```
一共 3 件事
第一件：买菜
清单：[买菜, 取快递, 写作业]
插队后：[买菜, 交电费, 取快递, 写作业]
被换掉的是：买菜
现在：[买水果, 交电费, 取快递, 写作业]
```

几个观察：

- `add(E)` 是**尾插**，直接加到最后
- `add(int, E)` 是**插入**，原来下标 1 及之后的元素统统往后挪一位
- `get(0)` 取第一个，下标从 0 开始
- `set(0, "买水果")` 会把旧值**返回**出来，所以能拿到"被换掉的是：买菜"
- 直接 `println` 一个 `List`，会打印成 `[元素1, 元素2, ...]` 的样子，这是 `AbstractCollection` 重写 `toString` 的功劳

> ⚠️ 下标范围是 `0` 到 `size() - 1`。`get(size())` 会抛 `IndexOutOfBoundsException`，不是返回 `null`。

---

## 六、删除的两个版本：最容易踩的坑

`List` 有两个 `remove`，签名不一样：

```
public abstract boolean remove(java.lang.Object);
public abstract E remove(int);
```

这是 `javap` 反编译 `java.util.List` 看到的原文。一个是删对象、返回 `boolean`；一个是删下标、返回被删掉的元素。

平时用 `List<String>` 不会混，因为传字符串肯定走 `Object` 那版。但换成 `List<Integer>` 就麻烦了：

```java
import java.util.ArrayList;
import java.util.List;

public class Lx2 {
    public static void main(String[] args) {
        List<String> fruits = new ArrayList<>();
        fruits.add("苹果");
        fruits.add("香蕉");
        fruits.add("橙子");

        fruits.remove(1);         // 按下标删，删掉"香蕉"
        fruits.remove("橙子");     // 按对象删，删掉"橙子"
        System.out.println("还剩：" + fruits);

        List<Integer> nums = new ArrayList<>();
        nums.add(10);
        nums.add(20);
        nums.add(30);

        nums.remove(1);                    // 删的是下标 1，不是值 1
        nums.remove(Integer.valueOf(30));  // 想删值 30，得先装箱
        System.out.println("数字还剩：" + nums);
    }
}
```

运行结果：

```
还剩：[苹果]
数字还剩：[10]
```

`List<String>` 那部分很好理解。关键是 `List<Integer>`：

- `nums.remove(1)` 里的 `1` 是 `int`，编译器优先匹配 `remove(int index)`，所以删的是**下标 1**（也就是 20），根本没打算删值 1
- `nums.remove(Integer.valueOf(30))` 传的是 `Integer` 对象，这才走 `remove(Object)`，删掉值 30

那如果写成 `nums.remove(30)` 呢？编译器还是选 `remove(int)`，把 30 当下标用。实测结果：

```
IndexOutOfBoundsException: Index: 30, Size: 3
```

> **⚠️ 这是 `List<Integer>` 最容易翻车的地方：想删值，一定要 `remove(Integer.valueOf(x))`，或者先 `int idx = list.indexOf(x)` 再按下标删。** 后者更直观，也不容易看错。

---

## 七、查找：找不到返回 -1

```java
import java.util.ArrayList;
import java.util.List;

public class Lx3 {
    public static void main(String[] args) {
        List<String> names = new ArrayList<>();
        names.add("小明");
        names.add("小红");
        names.add("小明");
        names.add("小刚");

        System.out.println("有没有小红：" + names.contains("小红"));
        System.out.println("小明第一次出现在：" + names.indexOf("小明"));
        System.out.println("小明最后一次出现在：" + names.lastIndexOf("小明"));
        System.out.println("小美在哪儿：" + names.indexOf("小美"));
    }
}
```

运行结果：

```
有没有小红：true
小明第一次出现在：0
小明最后一次出现在：2
小美在哪儿：-1
```

三个要点：

- `indexOf` 从头往后找，返回**第一次**出现的下标
- `lastIndexOf` 从后往前找，返回**最后一次**出现的下标
- 找不到返回 **`-1`**，不抛异常。所以判断"存不存在"最稳的写法是 `indexOf(x) != -1`；`contains` 内部也是这么干的

> 💡 `indexOf` 判断相等用的是 `equals`，不是 `==`。对 `String` 来说这意味着比的是内容；如果放的是自定义对象，记得把 `equals` 重写好，否则找到的永远是自己那个引用。

---

## 八、截取：subList 是视图，不是复制

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Lx4 {
    public static void main(String[] args) {
        List<Integer> scores = new ArrayList<>(Arrays.asList(90, 85, 78, 92, 88));

        List<Integer> part = scores.subList(1, 4);   // 左闭右开，取下标 1、2、3
        System.out.println("截出来的：" + part);

        part.set(0, 100);
        System.out.println("改完截取部分：" + part);
        System.out.println("原来的列表：" + scores);
    }
}
```

运行结果：

```
截出来的：[85, 78, 92]
改完截取部分：[100, 78, 92]
原来的列表：[90, 100, 78, 92, 88]
```

两个重点：

**1. 左闭右开**

`subList(1, 4)` 取的是下标 1、2、3，**不包括 4**。这个区间写法跟 `String.substring` 是一致的，记住"含头不含尾"就行。

**2. 返回的是视图**

我改的是 `part`，结果 `scores` 里的 85 也变成了 100。因为 `subList` 并没有复制出一份新列表，它返回的是一个"贴着原列表看"的视图，改视图就是改原表。

> **⚠️ 想要一份独立的副本，得自己复制：`List<Integer> copy = new ArrayList<>(part);`。**
>
> 另外，截取之后如果又往原列表增删元素，再操作这个视图会抛 `ConcurrentModificationException`。视图只适合"临时看一眼"或"临时改一段"，不适合长期持有。

---

## 九、排序

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Lx5 {
    public static void main(String[] args) {
        List<Integer> scores = new ArrayList<>(Arrays.asList(90, 85, 78, 92, 88));
        scores.sort(null);
        System.out.println("从小到大：" + scores);

        scores.sort((a, b) -> b - a);
        System.out.println("从大到小：" + scores);

        List<String> names = new ArrayList<>(Arrays.asList("小明", "欧阳娜娜", "李雷"));
        names.sort((a, b) -> a.length() - b.length());
        System.out.println("按名字长短：" + names);
    }
}
```

运行结果：

```
从小到大：[78, 85, 88, 90, 92]
从大到小：[92, 90, 88, 85, 78]
按名字长短：[小明, 李雷, 欧阳娜娜]
```

- `sort(null)` 表示用**自然顺序**，前提是元素实现了 `Comparable`。`Integer`、`String` 都实现了，所以可以直接排
- 传一个 `Comparator` 就能自定义规则。`(a, b) -> b - a` 就是"后一个减前一个"，出来的效果是降序
- 按长度排的时候，"小明"和"李雷"都是两个字，排序后仍是"小明"在前——说明 `List.sort` 是**稳定排序**，相等元素保持原来的先后

> 💡 `(a, b) -> b - a` 只适合差值不会溢出的场景。如果数字可能是 `Integer.MIN_VALUE` 和 `Integer.MAX_VALUE`，相减就溢出了。规范写法是 `Integer.compare(b, a)`，`Double`、`Long` 同理。

---

## 十、批量操作：addAll、removeAll、retainAll

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Lx6 {
    public static void main(String[] args) {
        List<String> shelf = new ArrayList<>(Arrays.asList("Java编程思想", "算法导论", "人类简史"));
        List<String> borrow = Arrays.asList("算法导论", "明朝那些事儿");

        shelf.addAll(borrow);
        System.out.println("合并后：" + shelf + "，共 " + shelf.size());

        shelf.removeAll(borrow);
        System.out.println("去掉借来的：" + shelf);

        List<String> mine = new ArrayList<>(Arrays.asList("语文", "数学", "英语"));
        mine.retainAll(Arrays.asList("数学", "英语", "物理"));
        System.out.println("只留共有的：" + mine);

        System.out.println("空了吗：" + mine.isEmpty());
        mine.clear();
        System.out.println("清空后空了吗：" + mine.isEmpty() + "，大小 " + mine.size());
    }
}
```

运行结果：

```
合并后：[Java编程思想, 算法导论, 人类简史, 算法导论, 明朝那些事儿]，共 5
去掉借来的：[Java编程思想, 人类简史]
只留共有的：[数学, 英语]
空了吗：false
清空后空了吗：true，大小 0
```

这段最有意思的是第一个输出：合并后"算法导论"出现了**两次**（原本就有一本，借来的里面又有一本）。`List` 允许重复，`addAll` 也不会去重，就那么老实加进去。

然后 `removeAll(borrow)` 把**所有**出现在 `borrow` 里的元素都删掉，两本"算法导论"一起没了，只剩两本。

`retainAll` 正好相反，只保留"两边都有"的元素，等于求交集。

> 💡 这三个方法都接收 `Collection`，所以传 `List`、`Set` 都行；`Arrays.asList(...)` 也能直接传，因为它返回的本来就是 `List`。不过 `Arrays.asList` 返回的是**固定长度**的列表，往里 `add` 会抛 `UnsupportedOperationException`——这也是个常见坑，要改就套一层 `new ArrayList<>(Arrays.asList(...))`。

---

## 十一、三种遍历，以及遍历时删除

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Iterator;
import java.util.List;

public class Lx7 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C", "D"));

        System.out.println("下标遍历：");
        for (int i = 0; i < list.size(); i++) {
            System.out.print(list.get(i) + " ");
        }
        System.out.println();

        System.out.println("增强 for 遍历：");
        for (String s : list) {
            System.out.print(s + " ");
        }
        System.out.println();

        System.out.println("迭代器遍历并删除 B：");
        Iterator<String> it = list.iterator();
        while (it.hasNext()) {
            String s = it.next();
            if (s.equals("B")) {
                it.remove();
            } else {
                System.out.print(s + " ");
            }
        }
        System.out.println();
        System.out.println("删除后：" + list);
    }
}
```

运行结果：

```
下标遍历：
A B C D 
增强 for 遍历：
A B C D 
迭代器遍历并删除 B：
A C D 
删除后：[A, C, D]
```

三种方式各有适用场景：

| 方式 | 写法 | 适合 |
|---|---|---|
| 下标 for | `for (int i = 0; i < list.size(); i++) list.get(i)` | 需要下标，或要边遍历边按下标操作 |
| 增强 for | `for (String s : list)` | 只读遍历，代码最短 |
| 迭代器 | `while (it.hasNext()) it.next()` | 遍历过程中要删除元素 |

前两种都好说，重点在第三种。为什么遍历时删除必须用迭代器？试试直接用 `list.remove`：

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Lx8 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C", "D"));

        for (String s : list) {
            if (s.equals("B")) {
                list.remove(s);   // 遍历时直接改结构
            }
            System.out.println(s);
        }
    }
}
```

运行到第二个元素就炸了：

```
A
B
Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:909)
	at java.util.ArrayList$Itr.next(ArrayList.java:859)
	at Lx8.main(Lx8.java:9)
```

这个异常叫 **`ConcurrentModificationException`**，中文一般叫"并发修改异常"，但它跟多线程一点关系都没有——单线程也会抛。

原因在于 `ArrayList` 内部有个 `modCount` 计数器，每次增删都会 `+1`。迭代器创建时记下了当时的 `modCount`（叫 `expectedModCount`），之后每取一个元素都核对一次。你在循环里 `list.remove` 让 `modCount` 变了，下一次 `next()` 一比对，发现对不上，立刻抛异常。

这种"发现不对劲就赶紧报错"的设计叫 **fail-fast**。

> **⚠️ 增强 for 底层就是迭代器，所以在增强 for 里 `list.add`、`list.remove`、`list.clear` 都会抛这个异常。**
>
> 正确做法是用 `iterator.remove()`——它删完之后会顺手把 `expectedModCount` 同步成新的 `modCount`，所以不会报错。这也是 `Lx7` 里那段能正常跑完的原因。

---

## 十二、回到集合框架

到这里，`List` 这一层的规则就清楚了：

| 项目 | 内容 |
|---|---|
| 继承关系 | `Iterable` ← `Collection` ← `List` |
| 核心特征 | 有序、可重复、有下标 |
| 常用方法 | `add` / `get` / `set` / `remove` / `indexOf` / `subList` / `sort` |
| 能不能 new | 不能，必须用实现类 |
| 常见实现类 | `ArrayList`、`LinkedList` |

但还有个问题没解决：`ArrayList` 和 `LinkedList` 都实现了 `List`，方法签名一模一样，凭什么要有两个？

答案是**底层结构不同，快慢完全不同**。`ArrayList` 底下是数组，按下标取是 O(1)，但中间插入要挪动一片元素；`LinkedList` 底下是链表，插入删除只改几个指针，但想取第 100 个元素得从头一个个数过去。

这一篇讲的 `get`、`add(int, E)`、`remove(int)`，在两个实现类上跑出来的速度可能差几十倍。具体怎么差、差在哪里，就是接下来两篇的内容：先拆 `ArrayList` 和顺序表，再拆 `LinkedList` 和链表。

---

## 总结

`List` 这一篇信息量不大，但几个坑都挺真实：

**接口层次：**
- `Iterable` 管"能遍历"，`Collection` 管"通用增删查"，`List` 管"下标"
- `Collection` 里没有 `get`，因为 `Set`、`Queue` 没有位置概念

**List 的本质：**
- 线性表：有序、可重复、下标从 0 开始连续
- 是接口，不能 `new`，要用 `ArrayList` 或 `LinkedList` 实例化
- 声明时写 `List<E>`，实现类放右边，方便以后替换

**方法上的四个坑：**
- `remove(int)` 删下标、返回元素；`remove(Object)` 删对象、返回 `boolean`
- `List<Integer>` 里 `remove(1)` 删的是下标，想删值得 `remove(Integer.valueOf(1))`
- `subList` 返回视图不是副本，改它会改原表；要副本就 `new ArrayList<>(part)`
- 遍历时直接 `list.remove` 会抛 `ConcurrentModificationException`，得用 `iterator.remove()`

**几个返回值：**
- `indexOf` / `lastIndexOf` 找不到返回 `-1`，不抛异常
- `set` 返回被换掉的旧值
- `add(int, E)` 返回 `void`，别写成 `boolean b = list.add(1, x)`

下一篇进 `ArrayList` 和顺序表：会看看它内部那个数组是怎么扩容的、为什么说"中间插入慢"，以及怎么自己手写一个 `MyArrayList<E>`。今天这些方法，到时候会一个一个亲手实现一遍。

---

**Java数据结构学习笔记系列，持续更新中。**
