# Java语法入门学习笔记（第七篇）：抽象类和接口

> 继续Java入门学习，这篇整理抽象类、接口和Object类，这些是面向对象更高级的用法。

---

## 一、抽象类

### 1.1 什么是抽象类？

先看一个例子：我们有一个图形类，里面有个画图的方法：

```java
class Shape {
    void draw() {
        System.out.println("画一个图形");
    }
}
```

但是问题来了："图形"到底长什么样？圆形？矩形？三角形？

`Shape` 这个类太抽象了，它的 `draw` 方法根本不知道该画什么，实际画图都是子类来做的。

像这种**没有足够信息来描述一个具体对象的类**，就叫**抽象类**。

### 1.2 抽象类的语法

用 `abstract` 修饰类，抽象类里可以有抽象方法（没有方法体的方法）：

```java
// 抽象类
abstract class Shape {
    // 抽象方法：没有方法体，子类必须重写
    abstract void draw();

    // 抽象类里也可以有普通方法和属性
    double area;

    double getArea() {
        return area;
    }
}
```

### 1.3 抽象类的使用

抽象类不能直接创建对象，必须被继承，子类重写抽象方法：

```java
// 矩形类，继承抽象类
class Rect extends Shape {
    double length;
    double width;

    Rect(double length, double width) {
        this.length = length;
        this.width = width;
    }

    // 必须重写抽象方法
    @Override
    void draw() {
        System.out.println("画矩形：长=" + length + " 宽=" + width);
    }
}

// 圆形类，继承抽象类
class Circle extends Shape {
    double r;
    static final double PI = 3.14;

    Circle(double r) {
        this.r = r;
    }

    @Override
    void draw() {
        System.out.println("画圆形：半径=" + r);
    }
}

public class Test {
    public static void main(String[] args) {
        // Shape shape = new Shape();  // 报错！抽象类不能实例化

        Shape rect = new Rect(10, 5);
        rect.draw();

        Shape circle = new Circle(3);
        circle.draw();
    }
}
```

输出：
```
画矩形：长=10.0 宽=5.0
画圆形：半径=3.0
```

### 1.4 抽象类的特性

> **⚠️ 记住这几点：**
> 1. 抽象类不能直接创建对象
> 2. 抽象方法不能是private、final、static的（因为要被子类重写）
> 3. 子类继承抽象类，必须重写所有抽象方法，否则子类也是抽象类
> 4. 抽象类里可以没有抽象方法，但有抽象方法的类一定是抽象类
> 5. 抽象类里可以有构造方法，供子类调用

### 1.5 为什么要用抽象类？

普通类也能被继承，方法也能被重写，为啥非要用抽象类？

因为**抽象类多了一层编译器校验**：
- 如果你不小心创建了父类对象，抽象类会直接报错提醒你
- 如果你忘了重写抽象方法，编译器也会报错

就像final修饰变量一样，都是为了预防出错，让问题早点被发现。

---

## 二、接口

### 2.1 什么是接口？

生活中的接口：
- 电脑的USB口，可以插U盘、鼠标、键盘（只要符合USB规范）
- 电源插座，可以插电脑、电视、电饭煲（只要符合插座规范）

**接口就是公共的行为规范标准，符合规范就能通用。**

在Java中，接口是多个类的公共规范，是一种引用数据类型。

### 2.2 接口的定义

用 `interface` 关键字：

```java
// 定义USB接口
interface USB {
    // 接口中的方法默认是 public abstract 的
    void openDevice();   // 打开设备
    void closeDevice();  // 关闭设备
}
```

> 💡 接口里的方法默认就是 `public abstract`，可以不写，写了也没错。

### 2.3 接口的使用

接口不能直接用，必须有类来**实现**接口，用 `implements` 关键字：

```java
// 鼠标类，实现USB接口
class Mouse implements USB {
    @Override
    public void openDevice() {
        System.out.println("打开鼠标");
    }

    @Override
    public void closeDevice() {
        System.out.println("关闭鼠标");
    }

    // 鼠标特有的方法
    void click() {
        System.out.println("鼠标点击");
    }
}

// 键盘类，实现USB接口
class KeyBoard implements USB {
    @Override
    public void openDevice() {
        System.out.println("打开键盘");
    }

    @Override
    public void closeDevice() {
        System.out.println("关闭键盘");
    }

    // 键盘特有的方法
    void input() {
        System.out.println("键盘输入");
    }
}
```

### 2.4 用电脑使用USB设备

```java
class Computer {
    void powerOn() {
        System.out.println("打开电脑");
    }

    void powerOff() {
        System.out.println("关闭电脑");
    }

    // 使用USB设备（参数是接口类型，接收所有实现了USB的类）
    void useDevice(USB usb) {
        usb.openDevice();

        // 判断具体是哪种设备
        if (usb instanceof Mouse) {
            Mouse mouse = (Mouse) usb;
            mouse.click();
        } else if (usb instanceof KeyBoard) {
            KeyBoard keyBoard = (KeyBoard) usb;
            keyBoard.input();
        }

        usb.closeDevice();
    }
}

public class Test {
    public static void main(String[] args) {
        Computer computer = new Computer();
        computer.powerOn();

        computer.useDevice(new Mouse());     // 用鼠标
        computer.useDevice(new KeyBoard());  // 用键盘

        computer.powerOff();
    }
}
```

输出：
```
打开电脑
打开鼠标
鼠标点击
关闭鼠标
打开键盘
键盘输入
关闭键盘
关闭电脑
```

### 2.5 接口的特性

> **⚠️ 记住这几点：**
> 1. 接口不能直接new对象
> 2. 接口中的方法默认是 `public abstract`，只能是public
> 3. 接口中的方法不能有方法体（JDK8以后可以有default方法）
> 4. 接口中的变量默认是 `public static final`，必须初始化
> 5. 接口中没有构造方法和静态代码块
> 6. 重写接口方法时，必须用public修饰

---

## 三、实现多个接口

Java中类只能单继承（一个类只能有一个父类），但**一个类可以实现多个接口**。

用动物来举例：

```java
// 动物类
class Animal {
    String name;

    Animal(String name) {
        this.name = name;
    }
}

// 会飞的接口
interface IFlying {
    void fly();
}

// 会跑的接口
interface IRunning {
    void run();
}

// 会游泳的接口
interface ISwimming {
    void swim();
}
```

**猫：会跑**

```java
class Cat extends Animal implements IRunning {
    Cat(String name) {
        super(name);
    }

    @Override
    public void run() {
        System.out.println(name + "用四条腿跑");
    }
}
```

**青蛙：会跑又会游泳**

```java
class Frog extends Animal implements IRunning, ISwimming {
    Frog(String name) {
        super(name);
    }

    @Override
    public void run() {
        System.out.println(name + "往前跳");
    }

    @Override
    public void swim() {
        System.out.println(name + "蹬腿游泳");
    }
}
```

**鸭子：会跑、会游泳、还会飞**

```java
class Duck extends Animal implements IRunning, ISwimming, IFlying {
    Duck(String name) {
        super(name);
    }

    @Override
    public void fly() {
        System.out.println(name + "用翅膀飞");
    }

    @Override
    public void run() {
        System.out.println(name + "用两条腿跑");
    }

    @Override
    public void swim() {
        System.out.println(name + "漂在水上");
    }
}
```

> 💡 继承是 is-a（是一个），接口是 has-a（具有某种能力）。
> 猫是动物，具有会跑的能力；鸭子是动物，具有会跑、会游泳、会飞的能力。

### 接口的好处：不关注类型，只关注能力

```java
// 散步方法：只要会跑的都行，不管是什么动物
public static void walk(IRunning running) {
    System.out.println("去散步啦");
    running.run();
}

public static void main(String[] args) {
    walk(new Cat("小猫"));
    walk(new Frog("小青蛙"));
    walk(new Duck("小鸭子"));
}
```

输出：
```
去散步啦
小猫用四条腿跑
去散步啦
小青蛙往前跳
去散步啦
小鸭子用两条腿跑
```

---

## 四、接口间的继承

接口和接口之间可以用 `extends` 继承，而且可以多继承：

```java
interface IRunning {
    void run();
}

interface ISwimming {
    void swim();
}

// 两栖接口：继承了跑和游泳两个接口
interface IAmphibious extends IRunning, ISwimming {
}

// 实现两栖接口，就要同时实现run和swim
class Frog implements IAmphibious {
    @Override
    public void run() {
        System.out.println("青蛙跳");
    }

    @Override
    public void swim() {
        System.out.println("青蛙游");
    }
}
```

> 💡 接口间的继承相当于把多个接口合并在一起。

---

## 五、接口实例：给对象数组排序

普通的整数可以直接比较大小，但对象怎么比较？

比如有一组学生，想按分数排序：

```java
class Student {
    String name;
    int score;

    Student(String name, int score) {
        this.name = name;
        this.score = score;
    }

    @Override
    public String toString() {
        return name + ":" + score;
    }
}
```

直接用 `Arrays.sort()` 会报错，因为系统不知道学生怎么比较大小。

### 实现Comparable接口

让Student实现 `Comparable` 接口，重写 `compareTo` 方法：

```java
class Student implements Comparable {
    String name;
    int score;

    Student(String name, int score) {
        this.name = name;
        this.score = score;
    }

    // 定义比较规则：按分数排序
    @Override
    public int compareTo(Object o) {
        Student s = (Student) o;
        if (this.score > s.score) {
            return -1;  // 当前对象排在前面（降序）
        } else if (this.score < s.score) {
            return 1;
        } else {
            return 0;
        }
    }

    @Override
    public String toString() {
        return name + ":" + score;
    }
}

public class Test {
    public static void main(String[] args) {
        Student[] students = {
            new Student("张三", 95),
            new Student("李四", 96),
            new Student("王五", 97),
            new Student("赵六", 92)
        };

        Arrays.sort(students);
        System.out.println(Arrays.toString(students));
    }
}
```

输出：
```
[王五:97, 李四:96, 张三:95, 赵六:92]
```

> 💡 `compareTo` 返回值：
> - 负数：当前对象排在前面
> - 正数：当前对象排在后面
> - 0：一样大

---

## 六、抽象类和接口的区别

这是面试常考题，用表格对比：

| 区别 | 抽象类 | 接口 |
|------|--------|------|
| 关键字 | `abstract class` | `interface` |
| 成员变量 | 可以有各种变量 | 只能是 `public static final` |
| 成员方法 | 可以有普通方法和抽象方法 | 只能有抽象方法（JDK8后可有default方法） |
| 构造方法 | 有 | 没有 |
| 继承/实现 | 类只能单继承 | 类可以实现多个接口 |
| 关系 | 子类用 `extends` 继承 | 类用 `implements` 实现 |

**怎么选择？**
- 如果几个类有共同的属性和方法，用**抽象类**（比如动物都有名字、年龄）
- 如果只是定义一种能力/规范，用**接口**（比如会飞、会跑）

---

## 七、Object类

### 7.1 什么是Object类？

Object是Java中所有类的父类，所有类都默认继承Object。

也就是说，Object的引用可以接收任何对象：

```java
class Person {}
class Student {}

public class Test {
    public static void main(String[] args) {
        print(new Person());
        print(new Student());
    }

    // Object可以接收任何对象
    public static void print(Object obj) {
        System.out.println(obj);
    }
}
```

### 7.2 toString方法

Object的toString方法默认打印的是地址，看不懂：

```java
Person p = new Person();
System.out.println(p);  // 输出 Person@1b6d3586
```

重写toString，打印想看的内容：

```java
class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "姓名：" + name + "，年龄：" + age;
    }
}
```

### 7.3 equals方法

`==` 和 `equals` 的区别：

- `==` 比较基本类型：比较值
- `==` 比较引用类型：比较地址
- `equals` 默认也是比较地址，需要重写才能比较内容

```java
class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 重写equals：比较内容
    @Override
    public boolean equals(Object obj) {
        if (obj == null) {
            return false;
        }
        if (this == obj) {
            return true;
        }
        if (!(obj instanceof Person)) {
            return false;
        }
        Person p = (Person) obj;
        return this.name.equals(p.name) && this.age == p.age;
    }
}

public class Test {
    public static void main(String[] args) {
        Person p1 = new Person("小明", 18);
        Person p2 = new Person("小明", 18);

        System.out.println(p1 == p2);       // false，地址不同
        System.out.println(p1.equals(p2));  // true，内容相同
    }
}
```

### 7.4 hashCode方法

hashCode用来计算对象的哈希值（可以理解为对象在内存中的位置编号）。

默认情况下，两个内容相同的对象hashCode不同：

```java
Person p1 = new Person("小明", 18);
Person p2 = new Person("小明", 18);
System.out.println(p1.hashCode());  // 460141958
System.out.println(p2.hashCode());  // 1163157884（不一样）
```

重写hashCode，让内容相同的对象hashCode也相同：

```java
@Override
public int hashCode() {
    return Objects.hash(name, age);
}
```

> 💡 一般重写equals时，也要重写hashCode，保证两个对象equals为true时，hashCode也相同。

---

## 总结

本文整理了Java抽象类、接口和Object类的核心知识点：

**抽象类：**
- 用abstract修饰，不能实例化
- 可以有抽象方法和普通方法
- 子类必须重写抽象方法
- 作用：编译器校验，预防出错

**接口：**
- 用interface定义，用implements实现
- 方法默认public abstract，变量默认public static final
- 一个类可以实现多个接口
- 接口间可以多继承
- 常用接口：Comparable（比较）、Cloneable（克隆）

**抽象类vs接口：**
- 抽象类：有共同属性和方法，单继承
- 接口：定义能力/规范，多实现

**Object类：**
- 所有类的父类
- toString：打印对象信息
- equals：比较对象内容
- hashCode：计算哈希值

抽象类和接口是面向对象的重要内容，理解了这些，面向对象就更扎实了。下一篇继续学习异常和集合。

---

**Java入门学习笔记系列，持续更新中。**
