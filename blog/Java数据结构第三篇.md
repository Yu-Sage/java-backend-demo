# Java数据结构学习笔记（第三篇）：包装类和泛型

> 前两篇铺完了复杂度这把尺子和集合框架的地图，这篇回头补两块地基：一块是"集合为什么装不进 `int`"，一块是"`ArrayList<Integer>` 后面那对尖括号到底在干什么"。这两块不弄明白，后面手写 `ArrayList` 和 `HashMap` 会一直卡壳。

---

## 一、集合装不进 int

上一篇写通讯录时，用的是 `ArrayList<String>`。如果换成存分数，第一反应可能是这样：

```java
ArrayList<int> scores = new ArrayList<>();   // 编译报错
```

编译器直接拒绝。想存整数，只能写成：

```java
ArrayList<Integer> scores = new ArrayList<>();
```

为什么不能直接放 `int`？

因为集合框架里的很多操作，是在 `Object` 这一层做的：

- `contains` 要调用元素的 `equals` 方法比较
- `remove` 也要靠 `equals` 找到目标
- 打印时调用 `toString`
- 底层数组声明成 `Object[]`，什么都能往里放

而 `int`、`double`、`char` 这些**基本类型不是对象，不继承 `Object`**，自然调不了这些方法。

> 💡 打个比方：快递柜只收打包好的包裹，不接收散装的一把米。基本类型就是那把米，包装类就是装好的袋子。

所以 Java 给每个基本类型配了一个"袋子"——包装类。

---

## 二、八个基本类型，八个包装类

| 基本类型 | 包装类 | 占用 |
|---|---|---|
| `byte` | `Byte` | 1 字节 |
| `short` | `Short` | 2 字节 |
| `int` | `Integer` | 4 字节 |
| `long` | `Long` | 8 字节 |
| `float` | `Float` | 4 字节 |
| `double` | `Double` | 8 字节 |
| `char` | `Character` | 2 字节 |
| `boolean` | `Boolean` | 1 位（逻辑上） |

规律很好记：**首字母大写**。只有两个例外——`int` 对应 `Integer`，`char` 对应 `Character`，不是 `Int` 和 `Char`。

> 💡 包装类都是 `final` 的，不能被继承；里面存值的字段也是 `final` 的。换句话说，一个 `Integer` 对象一旦创建，里面的值就改不了。

---

## 三、装箱和拆箱

### 3.1 把基本类型装进袋子，再取出来

```java
public class Wx1 {
    public static void main(String[] args) {
        Integer a = Integer.valueOf(10);   // 手动装箱
        int b = a.intValue();              // 手动拆箱
        System.out.println("a = " + a + "，b = " + b);

        Integer c = 20;                    // 自动装箱
        int d = c;                         // 自动拆箱
        System.out.println("c = " + c + "，d = " + d);

        System.out.println("c + 1 = " + (c + 1));   // 先拆箱，再加
        System.out.println("a 和 c 的值一样吗：" + a.equals(c));
    }
}
```

运行结果：

```
a = 10，b = 10
c = 20，d = 20
c + 1 = 21
a 和 c 的值一样吗：false
```

两个动作有了正式名字：

- **装箱**：基本类型 → 包装类对象，`Integer.valueOf(10)`
- **拆箱**：包装类对象 → 基本类型，`a.intValue()`

`Integer.valueOf(10)` 和 `new Integer(10)` 效果上都是造一个值为 10 的对象，但 `new Integer(...)` 从 Java 9 开始已经标记为过时，平时一律用 `valueOf`。

### 3.2 自动装箱拆箱是编译器帮忙

`Integer c = 20;` 这行看着像直接赋值，其实编译器悄悄做了转换。反编译一下就能看到：

```
2: invokestatic  #2    // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
```

拆箱时对应的是：

```
7: invokevirtual #3    // Method java/lang/Integer.intValue:()I
```

也就是说，**自动装箱拆箱不是运行时魔法，是编译期自动补上的方法调用**。所以它并不是"免费"的——循环里频繁装箱拆箱，会实实在在多创建对象。

> ⚠️ 在循环里写 `sum += list.get(i)`，如果 `list` 是 `ArrayList<Integer>`，每次都会拆箱；反过来往 `List<Integer>` 里 `add(i)`，每次都会装箱。数据量大时这部分开销不能忽略。

### 3.3 那道经典面试题：127 和 128

```java
public class Wx2 {
    public static void main(String[] args) {
        Integer a = 127;
        Integer b = 127;
        System.out.println("127 == 127：" + (a == b));

        Integer c = 128;
        Integer d = 128;
        System.out.println("128 == 128：" + (c == d));

        Integer e = -128;
        Integer f = -128;
        System.out.println("-128 == -128：" + (e == f));

        Integer g = -129;
        Integer h = -129;
        System.out.println("-129 == -129：" + (g == h));

        System.out.println("128 用 equals 比：" + c.equals(d));
    }
}
```

运行结果：

```
127 == 127：true
128 == 128：false
-128 == -128：true
-129 == -129：false
128 用 equals 比：true
```

同样的写法，127 是 `true`，128 却是 `false`。原因在 `Integer.valueOf` 的内部：

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

`Integer` 内部维护了一个缓存数组，默认范围是 **-128 到 127**：

| 值 | 走哪条路 | 结果 |
|---|---|---|
| -128 ～ 127 | 直接返回缓存里现成的对象 | 两个变量指向同一个对象，`==` 为 `true` |
| 小于 -128 或大于 127 | `new` 一个新的对象 | 两个变量指向不同对象，`==` 为 `false` |

`==` 比较的是**引用**（是不是同一个对象），`equals` 比较的才是**值**。

> **⚠️ 结论：比较两个 `Integer` 的值，永远用 `equals`，或者先转成 `int` 再比。** 用 `==` 在 127 以内碰巧是对的，一旦超过就翻车，而且这种 bug特别难查——测试数据小的时候一切正常，上线数据一大就出问题。

> 💡 补充一句：如果一边是 `int`、一边是 `Integer`，比如 `int x = 128; Integer y = 128; x == y`，这时 `y` 会自动拆箱，比较的是值，结果是 `true`。规则只对"两个都是包装类"的情况生效。

---

## 四、泛型：让容器只装一种类型

### 4.1 先用 Object 凑合一下

既然 `Object` 是所有类的父类，那能不能用一个 `Object[]` 装下所有东西？

```java
class ObjectBox {
    private Object[] data = new Object[5];
    private int size = 0;

    void add(Object value) {
        data[size++] = value;
    }

    Object get(int index) {
        return data[index];
    }
}

public class Wx3 {
    public static void main(String[] args) {
        ObjectBox box = new ObjectBox();
        box.add("小明");
        box.add(18);

        String name = (String) box.get(0);   // 取出来必须强转
        System.out.println("名字：" + name);

        String wrong = (String) box.get(1);  // 存进去的是 Integer，运行时才炸
        System.out.println("第二个：" + wrong);
    }
}
```

运行结果：

```
名字：小明
Exception in thread "main" java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
	at Wx3.main(Wx3.java:23)
```

这个"万能盒子"有两个毛病：

1. **取出来必须强转**，因为 `get` 的返回类型是 `Object`
2. **存错类型编译器不管**，只有运行时强转才会暴露，直接抛 `ClassCastException`

第二个问题最要命：错误拖到运行时才出现，可能是在某个深夜的生产环境。

### 4.2 换成泛型盒子

```java
class Box<T> {
    private Object[] data = new Object[5];
    private int size = 0;

    void add(T value) {
        data[size++] = value;
    }

    @SuppressWarnings("unchecked")
    T get(int index) {
        return (T) data[index];
    }

    int size() {
        return size;
    }
}

public class Wx4 {
    public static void main(String[] args) {
        Box<String> names = new Box<>();
        names.add("小明");
        names.add("小红");

        String first = names.get(0);        // 不用强转
        System.out.println("第一个：" + first);
        System.out.println("一共：" + names.size());

        Box<Integer> scores = new Box<>();
        scores.add(95);
        int top = scores.get(0);            // 取出时自动拆箱
        System.out.println("最高分：" + top);
    }
}
```

运行结果：

```
第一个：小明
一共：2
最高分：95
```

差别很明显：

- 声明 `Box<String>` 之后，`add` 只收 `String`，`get` 直接返回 `String`
- 不需要强转，取出 `Box<Integer>` 的元素还能自动拆箱

那如果硬要塞错类型呢？

```java
Box<String> names = new Box<>();
names.add("小明");
names.add(123);
```

编译就过不去：

```
WxErr1.java:5: 错误: 不兼容的类型: int无法转换为String
        names.add(123);
                  ^
1 个错误
```

**错误从运行时提前到了编译期**，这正是泛型最大的价值。

> 💡 注意上面 `Box` 内部还是用了 `Object[]`。因为 `new T[10]` 在 Java 里是不合法的——泛型类型在运行时被擦掉了，编译器不知道 `T` 到底是什么，没法创建数组。所以标准做法就是内部存 `Object[]`，取的时候强转，JDK 的 `ArrayList` 也是这么干的。

### 4.3 泛型到底解决了什么

三句话：

1. **类型参数化**：`Box<T>` 里的 `T` 是个占位符，用的时候再填具体类型
2. **编译期检查**：填了 `String`，就只能放 `String`，放别的当场报错
3. **免强转**：取出来就是填进去的类型，不用写 `(String)`

---

## 五、泛型类的写法和用法

### 5.1 定义

```java
class Box<T> {
    // 这个类里可以正常使用 T
}
```

`<T>` 就是类型形参列表，可以有多个：

```java
class Pair<K, V> {
    K key;
    V value;
}
```

### 5.2 使用

```java
Box<String> names = new Box<String>();   // 完整写法
Box<String> names2 = new Box<>();        // 类型推导，右边不用重复写
```

右边那对空尖括号叫**类型推导**——编译器从左边的 `Box<String>` 就能推出 `T` 是 `String`。JDK 7 之后都这么写，简洁。

### 5.3 命名约定

类型参数一般用单个大写字母，约定俗成有这么几个：

| 字母 | 含义 | 常见场景 |
|---|---|---|
| `E` | Element | 集合里的元素，`ArrayList<E>` |
| `K` | Key | 键，`Map<K,V>` |
| `V` | Value | 值，`Map<K,V>` |
| `T` | Type | 一般类型 |
| `N` | Number | 数字类型 |
| `S`、`U` | 第二、第三个类型 | 多个类型参数时 |

叫什么其实不影响编译，但按约定写，别人一眼就能看懂这个参数是干什么的。

### 5.4 裸类型不要碰

`Box box = new Box();` 这种不带尖括号的写法叫**裸类型**（raw type）。它是为了兼容 JDK 1.5 之前的老代码才保留的，用了等于放弃泛型的全部好处，还会产生编译警告。

> **⚠️ 新代码里永远不要出现裸类型。** 看到 `List list = new ArrayList();` 这种写法，基本可以判断是十几年前的代码。

---

## 六、类型擦除：尖括号在运行时其实不存在

### 6.1 先看一个反直觉的结果

```java
import java.util.ArrayList;

public class Wx5 {
    public static void main(String[] args) {
        ArrayList<String> names = new ArrayList<>();
        ArrayList<Integer> scores = new ArrayList<>();

        System.out.println("名字列表的类型：" + names.getClass().getSimpleName());
        System.out.println("分数列表的类型：" + scores.getClass().getSimpleName());
        System.out.println("两个列表是同一个类吗：" + (names.getClass() == scores.getClass()));
    }
}
```

运行结果：

```
名字列表的类型：ArrayList
分数列表的类型：ArrayList
两个列表是同一个类吗：true
```

`ArrayList<String>` 和 `ArrayList<Integer>` 在运行时是**同一个类**。尖括号里的东西，运行的时候根本没有。

### 6.2 擦除是怎么发生的

Java 的泛型是**编译期**的机制，编译成字节码时，类型参数会被擦掉，替换成它的边界（没写边界就替换成 `Object`），需要的地方再自动补上强转。

反编译一下刚才的 `Box`：

```
void add(T);
  ...
  16: aastore                              // 按 Object 存进数组

T get(int);
  ...
  6: areturn                              // 直接返回 Object
```

看起来签名还是 `T`，但字节码里的实际动作是 `aastore`（存对象引用）、`areturn`（返回对象引用），操作的都是 `Object`。

再看调用方 `Wx4` 的字节码：

```
22: invokevirtual #7    // Method Box.get:(I)Ljava/lang/Object;
25: checkcast     #8    // class java/lang/String
```

`get` 的签名明明是返回 `Object`，紧接着一条 `checkcast` 把它转成 `String`——**这个强转是编译器偷偷加的**。另一处取 `Integer` 的地方同样：

```
101: invokevirtual #7   // Method Box.get:(I)Ljava/lang/Object;
104: checkcast     #20  // class java/lang/Integer
107: invokevirtual #21  // Method java/lang/Integer.intValue:()I
```

先转成 `Integer`，再调 `intValue` 拆箱。

> 💡 一句话总结：**泛型的类型检查发生在编译期，运行期一律按 `Object` 处理，取的时候补强转。** 所以泛型不会带来运行时的额外开销，代价是运行时拿不到真实类型——`new T()`、`T.class`、`instanceof T` 这些都做不到。

### 6.3 桥接方法

擦除还会带来一个副作用。看这个例子：

```java
class Node<T> {
    T data;

    void setData(T data) {
        this.data = data;
    }
}

class StringNode extends Node<String> {
    @Override
    void setData(String data) {
        super.setData(data);
    }
}

public class Wx9 {
    public static void main(String[] args) {
        StringNode node = new StringNode();
        node.setData("小明");

        Node<String> asNode = node;
        asNode.setData("小红");

        System.out.println(node.data);
    }
}
```

运行结果：

```
小红
```

擦除之后，父类的 `setData` 变成了 `setData(Object)`，子类的是 `setData(String)`。参数类型不同，按 Java 的规则这**不算重写**，多态就失效了。

编译器怎么补救？它会在子类里自动生成一个"桥接方法"：

```
class StringNode extends Node<java.lang.String> {
  void setData(java.lang.String);
    Code:
       2: invokespecial #2   // Method Node.setData:(Ljava/lang/Object;)V

  void setData(java.lang.Object);
    Code:
       2: checkcast     #3   // class java/lang/String
       5: invokevirtual #4   // Method setData:(Ljava/lang/String;)V
}
```

第二个 `setData(Object)` 就是编译器生成的桥接方法：先强转成 `String`，再调用真正的 `setData(String)`。这样通过父类引用调用时，多态依然成立。

> 💡 这部分属于"了解即可"。日常写代码不会碰到桥接方法，但面试问"泛型擦除会带来什么问题"时，能说出"重写失效、需要桥接方法"就很加分。

---

## 七、泛型上界：限制能填什么类型

### 7.1 只允许数字类型

如果盒子里要做求和，就得保证元素能当数字用。这时给 `T` 加个上界：

```java
class NumberBox<T extends Number> {
    private Object[] data = new Object[5];
    private int size = 0;

    void add(T value) {
        data[size++] = value;
    }

    @SuppressWarnings("unchecked")
    T get(int index) {
        return (T) data[index];
    }

    double sum() {
        double total = 0;
        for (int i = 0; i < size; i++) {
            total += ((Number) data[i]).doubleValue();
        }
        return total;
    }
}

public class Wx6 {
    public static void main(String[] args) {
        NumberBox<Integer> ints = new NumberBox<>();
        ints.add(10);
        ints.add(20);
        System.out.println("整数和：" + ints.sum());

        NumberBox<Double> doubles = new NumberBox<>();
        doubles.add(1.5);
        doubles.add(2.5);
        System.out.println("小数和：" + doubles.sum());
    }
}
```

运行结果：

```
整数和：30.0
小数和：4.0
```

`<T extends Number>` 的意思是：`T` 必须是 `Number` 或者 `Number` 的子类。`Integer`、`Double`、`Long` 都行，`String` 不行。

硬要用 `String` 会怎样？

```java
NumberBox<String> box = new NumberBox<>();
box.add("小明");
```

编译时直接报错：

```
WxErr2.java:3: 错误: 类型参数String不在类型变量T的范围内
        NumberBox<String> box = new NumberBox<>();
                  ^
  其中, T是类型变量:
    T扩展已在类 NumberBox中声明的Number
2 个错误
```

### 7.2 约束"能比较"

还有一种常见写法：

```java
class SortedBox<E extends Comparable<E>> {
    // E 必须实现 Comparable 接口，也就是能和自己比较大小
}
```

`Comparable<E>` 是"可比较"的接口，`String`、`Integer` 都实现了它。加了这层约束，类内部就可以放心调用 `compareTo` 排序。

> 💡 这个写法后面会反复出现：`TreeSet`、`TreeMap` 要求元素能排序，`Collections.sort` 要求元素能比较，底层都是这个约束。

> **⚠️** 上界只能有一个类，但接口可以多个，写成 `<T extends A & B & C>`。类必须放在第一个。

---

## 八、泛型方法

泛型不只能加在类上，也能加在方法上。

```java
import java.util.Arrays;

public class Wx7 {
    static <E> void swap(E[] arr, int i, int j) {
        E temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    public static void main(String[] args) {
        Integer[] scores = {90, 85, 78};
        swap(scores, 0, 2);
        System.out.println("交换后：" + Arrays.toString(scores));

        String[] names = {"小明", "小红", "小刚"};
        swap(names, 0, 1);
        System.out.println("交换后：" + Arrays.toString(names));

        Wx7.<String>swap(names, 1, 2);
        System.out.println("再交换：" + Arrays.toString(names));
    }
}
```

运行结果：

```
交换后：[78, 85, 90]
交换后：[小红, 小明, 小刚]
再交换：[小红, 小刚, 小明]
```

三个细节：

1. **`<E>` 写在返回值类型前面**，位置是 `static <E> void swap(...)`
2. **静态方法必须自己声明 `<E>`**，因为静态方法不属于任何对象，拿不到类上的 `T`
3. **调用时可以省略类型**，编译器从参数 `Integer[]`、`String[]` 就能推出来；想写明就写 `Wx7.<String>swap(...)`

> 💡 泛型方法和泛型类上的 `T` 是两套东西。类上的 `T` 在实例方法里能用，静态方法里不能用，得自己重新声明一个。

---

## 九、通配符：`?` 是干什么的

### 9.1 一个方法只能收一种类型

假设有个打印方法：

```java
static void printAll(List<String> list) {
    for (String s : list) {
        System.out.println(s);
    }
}
```

它只能收 `List<String>`。换成 `List<Integer>` 就传不进去——**`List<String>` 和 `List<Integer>` 没有继承关系**，虽然 `String` 和 `Integer` 都继承自 `Object`。

这跟数组不一样：`Object[] arr = new String[10]` 是合法的，但 `List<Object> list = new ArrayList<String>()` 不行。泛型为了类型安全，放弃了这个"协变"。

想让方法接收各种类型的 List，用通配符 `?`：

```java
static void printAll(List<?> list) {
    for (Object o : list) {
        System.out.println(o);
    }
}
```

`List<?>` 表示"元素类型未知的 List"，什么 `List` 都能传进来。

### 9.2 上界通配符：只能读，不能写

```java
import java.util.ArrayList;
import java.util.List;

class Fruit {
    public String toString() {
        return "水果";
    }
}

class Apple extends Fruit {
    public String toString() {
        return "苹果";
    }
}

class Banana extends Fruit {
    public String toString() {
        return "香蕉";
    }
}

public class Wx8 {
    static void printAll(List<? extends Fruit> list) {
        for (Fruit fruit : list) {
            System.out.println("拿到：" + fruit);
        }
        // list.add(new Apple());   // 这行编译不过
    }

    static void addFruits(List<? super Fruit> list) {
        list.add(new Apple());
        list.add(new Banana());
        System.out.println("现在这筐里有：" + list);
    }

    public static void main(String[] args) {
        List<Apple> apples = new ArrayList<>();
        apples.add(new Apple());
        printAll(apples);

        List<Fruit> fruits = new ArrayList<>();
        addFruits(fruits);

        List<Object> objects = new ArrayList<>();
        addFruits(objects);
    }
}
```

运行结果：

```
拿到：苹果
现在这筐里有：[苹果, 香蕉]
现在这筐里有：[苹果, 香蕉]
```

`List<? extends Fruit>` 能接 `List<Apple>`、`List<Banana>`、`List<Fruit>`，但**不能往里 `add`**。

为什么？因为编译器只知道"这筐里装的是 Fruit 或它的子类"，但不知道具体是哪一种。万一这筐是 `List<Banana>`，你塞一个 `Apple` 进去就破坏了类型安全。所以干脆禁止写入。

### 9.3 下界通配符：能写，读出来只能当 Object

`List<? super Fruit>` 表示"元素类型是 Fruit 或它的父类"，能接 `List<Fruit>`、`List<Object>`。

这种写法**可以 `add`**——不管这筐是 `List<Fruit>` 还是 `List<Object>`，放一个 `Apple` 进去都是安全的，因为 `Apple` 一定是 `Fruit`，也一定是 `Object`。

但读出来只能当 `Object` 用，因为编译器不知道这筐到底是哪个父类。

三种写法对比：

| 写法 | 能接收 | 能读成什么 | 能写什么 | 角色 |
|---|---|---|---|---|
| `List<?>` | 任意 `List` | `Object` | 不能写（`null` 除外） | 只读遍历 |
| `List<? extends Fruit>` | `List<Fruit>` 及子类 | `Fruit` | 不能写 | 生产者（只往外拿） |
| `List<? super Fruit>` | `List<Fruit>` 及父类 | `Object` | `Fruit` 及其子类 | 消费者（只往里放） |

> 💡 这套规则有个好记的口诀：**PECS**——Producer Extends, Consumer Super。只往外取数据的用 `extends`，只往里存数据的用 `super`。

生活里也能对上：

- `? extends Fruit` 像一箱"至少是水果"的东西，你拿出来的肯定能吃，但不知道具体是苹果还是香蕉，所以不能往里乱塞
- `? super Fruit` 像一个"能装水果"的筐，苹果、香蕉、水果都能放进去，但掏出来只能当"东西"看

---

## 十、回到集合框架

这两块地基，在后面的容器里到处都是：

| 集合 | 泛型写法 | 含义 |
|---|---|---|
| `List` | `List<E>` | 装 E 类型的元素 |
| `Set` | `Set<E>` | 装不重复的 E |
| `Queue` | `Queue<E>` | 排队存放 E |
| `Map` | `Map<K, V>` | 键是 K，值是 V |
| `ArrayList` | `ArrayList<E>` | 底层是数组，元素类型 E |
| `HashMap` | `HashMap<K, V>` | 底层是哈希表，键值对 |

平时写的 `List<String> names = new ArrayList<>();`，就是把 `E` 填成了 `String`。

> 💡 下一篇开始讲 `List` 接口，再往后手写 `ArrayList` 时，类名会写成 `class MyArrayList<E>`，内部是 `Object[]`，取元素时强转——今天讲的这些会全部用上。到时候回头看这篇，会顺很多。

---

## 总结

这一篇的两个主角，其实都在解决同一个问题：**让类型在编译期就说清楚**。

**包装类：**
- 每个基本类型都有对应的包装类，`int` → `Integer`，`char` → `Character` 是仅有的两个不规则命名
- 装箱 `Integer.valueOf`，拆箱 `intValue`，自动装箱拆箱是编译器补的方法调用
- `Integer` 缓存 -128 ～ 127，这个范围内 `==` 为 `true`，超出就是 `false`
- 比较包装类的值，永远用 `equals`

**泛型：**
- 作用是把类型参数化，让容器只装一种类型，错误提前到编译期
- 写法 `class Box<T>`，使用 `Box<String> box = new Box<>();`
- 类型参数约定用 `E`、`K`、`V`、`T` 这些单字母
- 裸类型 `Box` 不要用

**类型擦除：**
- 泛型只在编译期存在，运行时被擦成 `Object`（或上界）
- 取元素时的强转是编译器插入的，所以没有运行时开销
- 代价是运行时拿不到真实类型，也导致重写失效、需要桥接方法

**上界与通配符：**
- `<T extends Number>` 限定 T 必须是 Number 的子类
- `<E extends Comparable<E>>` 限定元素能比较，后面排序会用到
- `? extends` 只能读，`? super` 能写，口诀 PECS
- `List<String>` 和 `List<Integer>` 没有继承关系，别指望互相赋值

下一篇开始进入具体的容器——`List` 接口。会讲清楚它规定了哪些方法、为什么要有接口、`ArrayList` 和 `LinkedList` 是怎么在这个接口下走出两条完全不同的路。复杂度那篇的 O(1)、O(N)，到时候会具体落到每个方法上。

---

**Java数据结构学习笔记系列，持续更新中。**
