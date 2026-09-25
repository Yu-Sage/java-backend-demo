# Java语法入门学习笔记（第五篇）：类和对象

> 继续Java入门学习，这篇整理类和对象的核心知识点。面向对象是Java的灵魂，用生活化的例子帮你理解。

---

## 一、面向对象初步认知

### 1.1 什么是面向对象？

Java是一门纯面向对象的语言，简单说就是：**一切皆为对象**。

比如你身边的手机、电脑、水杯、甚至你自己，都可以看成一个对象。每个对象都有自己的特征和能做的事情。

### 1.2 面向对象 vs 面向过程

用**做蛋炒饭**来举例：

**面向过程（一步一步来）：**
1. 打鸡蛋
2. 搅拌鸡蛋
3. 热锅倒油
4. 倒鸡蛋
5. 倒米饭
6. 翻炒
7. 加盐
8. 出锅

注重的是**过程**，每一步都要自己来，少一步都不行。

**面向对象（找对象帮忙）：**
1. 叫厨师（对象）
2. 说"来份蛋炒饭"
3. 厨师做好了

不用管厨师具体怎么做的，只需要和**对象**（厨师）交互就行。

> 💡 两种方式没有好坏之分，小任务用面向过程简单，大项目用面向对象更好维护。

---

## 二、类的定义和使用

### 2.1 什么是类？

**类就是对象的设计图。**

比如要造汽车，先要有设计图，设计图上写清楚：
- 汽车有什么：颜色、品牌、价格（属性）
- 汽车能做什么：跑、刹车、鸣笛（功能）

有了设计图，就能造出很多辆汽车。

### 2.2 定义一个类

```java
class Car {
    // 属性（成员变量）：汽车有什么
    String color;    // 颜色
    String brand;    // 品牌
    int price;       // 价格

    // 功能（成员方法）：汽车能做什么
    void run() {
        System.out.println(brand + "汽车正在跑");
    }

    void stop() {
        System.out.println(brand + "汽车刹车了");
    }
}
```

> 💡 类名用大驼峰，比如 `Car`、`Student`、`Person`

---

## 三、类的实例化（创建对象）

有了设计图（类），就能造出汽车（对象）了，用 `new` 关键字：

```java
public class Test {
    public static void main(String[] args) {
        // 造第一辆车
        Car car1 = new Car();
        car1.brand = "宝马";
        car1.color = "黑色";
        car1.price = 500000;
        car1.run();

        // 造第二辆车
        Car car2 = new Car();
        car2.brand = "奔驰";
        car2.color = "白色";
        car2.price = 600000;
        car2.run();
    }
}
```

输出：
```
宝马汽车正在跑
奔驰汽车正在跑
```

> 💡 一个类可以创建无数个对象，就像一张设计图可以造无数辆车。

---

## 四、this引用（重点）

### 4.1 为什么需要this？

看这个例子，形参名和成员变量名一样了：

```java
class Car {
    String brand;

    void setBrand(String brand) {
        brand = brand;  // 哪个是成员变量？哪个是参数？懵了！
    }
}
```

这时候就需要 `this` 来区分：

```java
class Car {
    String brand;

    void setBrand(String brand) {
        this.brand = brand;  // this.brand是成员变量，brand是参数
    }
}
```

### 4.2 this是什么？

**this就是"当前对象"的意思。**

哪个对象调用这个方法，this就代表哪个对象。

```java
public class Test {
    public static void main(String[] args) {
        Car car1 = new Car();
        car1.setBrand("宝马");  // 这里this就是car1

        Car car2 = new Car();
        car2.setBrand("奔驰");  // 这里this就是car2
    }
}
```

> 💡 简单记：this就像"我"，谁说话就代表谁。

---

## 五、构造方法

### 5.1 什么是构造方法？

构造方法就是**创建对象时自动调用的方法**，用来给对象初始化。

就像买车时，4S店直接给你把车配置好，不用你自己一个个装。

```java
class Car {
    String brand;
    String color;

    // 构造方法：名字和类名一样，没有返回值
    Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
        System.out.println("汽车造好了：" + brand + " " + color);
    }

    void run() {
        System.out.println(brand + "正在跑");
    }
}

public class Test {
    public static void main(String[] args) {
        // 创建对象时直接传参数，自动调用构造方法
        Car car = new Car("宝马", "黑色");
        car.run();
    }
}
```

输出：
```
汽车造好了：宝马 黑色
宝马正在跑
```

### 5.2 构造方法的特点

> **⚠️ 记住这几点：**
> 1. 名字必须和类名一模一样
> 2. 没有返回值，连void都不能写
> 3. 创建对象时自动调用，只调用一次
> 4. 可以有多个构造方法（重载）

### 5.3 构造方法重载

可以提供多种创建对象的方式：

```java
class Car {
    String brand;
    String color;

    // 无参构造：创建默认汽车
    Car() {
        this.brand = "未知品牌";
        this.color = "未知颜色";
    }

    // 有参构造：创建指定汽车
    Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
    }
}

public class Test {
    public static void main(String[] args) {
        Car car1 = new Car();              // 用无参构造
        Car car2 = new Car("宝马", "黑色"); // 用有参构造
    }
}
```

> ⚠️ 注意：如果你写了构造方法，编译器就不会自动生成无参构造了。

---

## 六、封装（重点）

### 6.1 什么是封装？

封装就是：**把细节藏起来，只对外提供简单的使用方式。**

比如电视机：
- 内部有复杂的电路板、芯片（藏起来）
- 对外只提供遥控器、电源键（简单接口）
- 你不需要知道电视内部怎么工作，按遥控器就行

### 6.2 访问权限

Java有四种访问权限，从开放到封闭：

| 权限 | 谁能访问 | 比喻 |
|------|---------|------|
| `public` | 所有人 | 你的外貌，谁都能看见 |
| `protected` | 自己和子类 | 家里的东西，自家人能用 |
| `default`（不写） | 同一个包 | 小区里的设施，邻居能用 |
| `private` | 只有自己 | 你的钱包，只有你能碰 |

### 6.3 封装的实际用法

一般成员变量设为private，提供public的get/set方法：

```java
class Person {
    // 私有变量，外面不能直接访问
    private String name;
    private int age;

    // 提供公开的方法来访问
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        // 可以在这里加判断，防止乱赋值
        if (age > 0 && age < 150) {
            this.age = age;
        } else {
            System.out.println("年龄不合理");
        }
    }
}

public class Test {
    public static void main(String[] args) {
        Person p = new Person();
        p.setName("小明");
        p.setAge(18);
        System.out.println(p.getName() + " " + p.getAge());
    }
}
```

> 💡 封装的好处：可以控制数据的合法性，别人不能随便乱改。

---

## 七、static静态成员

### 7.1 什么是static？

static修饰的成员叫**静态成员**，是**所有对象共享的**。

比如一个班级里：
- 每个学生有自己的名字、学号（每个对象一份，叫实例变量）
- 但教室是同一个，所有学生共享（用static修饰，叫静态变量）

### 7.2 静态变量

```java
class Student {
    String name;        // 实例变量：每个学生一份
    int age;

    static String classroom = "306教室";  // 静态变量：所有学生共享

    void introduce() {
        System.out.println(name + "在" + classroom);
    }
}

public class Test {
    public static void main(String[] args) {
        // 静态变量直接用类名访问
        System.out.println(Student.classroom);

        Student s1 = new Student();
        s1.name = "小明";

        Student s2 = new Student();
        s2.name = "小红";

        s1.introduce();
        s2.introduce();

        // 改了静态变量，所有对象都变了
        Student.classroom = "308教室";
        s1.introduce();
        s2.introduce();
    }
}
```

输出：
```
306教室
小明在306教室
小红在306教室
小明在308教室
小红在308教室
```

### 7.3 静态方法

```java
class Student {
    static String classroom = "306教室";

    // 静态方法
    public static String getClassroom() {
        return classroom;
    }
}

public class Test {
    public static void main(String[] args) {
        // 静态方法用类名调用
        System.out.println(Student.getClassroom());
    }
}
```

> **⚠️ 静态方法的限制：**
> - 静态方法里不能用this（因为没有对象）
> - 静态方法里不能直接访问非静态变量
> - 静态方法里不能直接调用非静态方法

---

## 八、代码块

用 `{}` 包起来的一段代码叫代码块。

### 8.1 构造代码块

写在类里的代码块，**每次创建对象时执行**，在构造方法之前：

```java
class Person {
    {
        System.out.println("构造代码块执行");
    }

    Person() {
        System.out.println("构造方法执行");
    }
}

public class Test {
    public static void main(String[] args) {
        Person p1 = new Person();
        Person p2 = new Person();
    }
}
```

输出：
```
构造代码块执行
构造方法执行
构造代码块执行
构造方法执行
```

### 8.2 静态代码块

用static修饰的代码块，**类加载时执行，只执行一次**：

```java
class Person {
    static {
        System.out.println("静态代码块执行");
    }

    {
        System.out.println("构造代码块执行");
    }

    Person() {
        System.out.println("构造方法执行");
    }
}

public class Test {
    public static void main(String[] args) {
        Person p1 = new Person();
        Person p2 = new Person();
    }
}
```

输出：
```
静态代码块执行    （只执行一次）
构造代码块执行
构造方法执行
构造代码块执行
构造方法执行
```

> 💡 执行顺序：静态代码块 → 构造代码块 → 构造方法

---

## 九、内部类

把一个类写在另一个类里面，叫内部类。了解就行，用得不多。

### 9.1 实例内部类

```java
class Outer {
    int num = 10;

    // 内部类
    class Inner {
        void show() {
            System.out.println("内部类访问外部类：" + num);
        }
    }
}

public class Test {
    public static void main(String[] args) {
        // 先创建外部类，再创建内部类
        Outer outer = new Outer();
        Outer.Inner inner = outer.new Inner();
        inner.show();
    }
}
```

### 9.2 静态内部类

```java
class Outer {
    static int num = 10;

    static class Inner {
        void show() {
            System.out.println("静态内部类：" + num);
        }
    }
}

public class Test {
    public static void main(String[] args) {
        // 不需要先创建外部类
        Outer.Inner inner = new Outer.Inner();
        inner.show();
    }
}
```

> 💡 内部类了解语法就行，实际开发用得最多的是匿名内部类，等学到接口再讲。

---

## 十、打印对象

直接打印对象，输出的是地址，看不懂：

```java
class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

public class Test {
    public static void main(String[] args) {
        Person p = new Person("小明", 18);
        System.out.println(p);  // 输出 Person@1b6d3586，看不懂
    }
}
```

重写 `toString` 方法，就能打印想看的内容：

```java
class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 重写toString方法
    @Override
    public String toString() {
        return "姓名：" + name + "，年龄：" + age;
    }
}

public class Test {
    public static void main(String[] args) {
        Person p = new Person("小明", 18);
        System.out.println(p);  // 输出 姓名：小明，年龄：18
    }
}
```

---

## 总结

本文整理了Java类和对象的核心知识点：

**基础概念：**
- 面向对象：一切皆为对象，靠对象交互完成任务
- 类是设计图，对象是实际造出来的东西
- 用new创建对象，用.访问属性和方法

**核心重点：**
- this：代表当前对象，区分成员变量和参数
- 构造方法：创建对象时自动调用，用于初始化
- 封装：藏起细节，对外提供接口，用private和public控制权限
- static：所有对象共享，用类名访问

**执行顺序：**
- 静态代码块 → 构造代码块 → 构造方法
- 静态代码块只执行一次

类和对象是面向对象的基础，下一篇继续学习继承和多态，那才是面向对象的精髓。

---

**Java入门学习笔记系列，持续更新中。**
