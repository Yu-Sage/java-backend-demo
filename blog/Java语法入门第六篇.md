# Java语法入门学习笔记（第六篇）：继承和多态

> 继续Java入门学习，这篇整理继承和多态，这是面向对象的精髓，用生活化的例子帮你理解。

---

## 一、继承

### 1.1 为什么需要继承？

假设我们要写狗类和猫类：

```java
// 狗类
class Dog {
    String name;
    int age;

    void eat() {
        System.out.println(name + "正在吃饭");
    }

    void sleep() {
        System.out.println(name + "正在睡觉");
    }

    void bark() {
        System.out.println(name + "汪汪汪");
    }
}

// 猫类
class Cat {
    String name;
    int age;

    void eat() {
        System.out.println(name + "正在吃饭");
    }

    void sleep() {
        System.out.println(name + "正在睡觉");
    }

    void mew() {
        System.out.println(name + "喵喵喵");
    }
}
```

发现问题了吗？`name`、`age`、`eat()`、`sleep()` 这些代码重复写了两遍！

狗和猫都是动物，有很多共同特征。能不能把共同的部分抽出来，复用一下？这就是**继承**。

### 1.2 什么是继承？

继承就是：**子类把父类的属性和方法拿过来用，自己只需要写特有的部分。**

就像儿子继承父亲的财产，父亲有的儿子都有，儿子还能有自己的东西。

- 被继承的类叫：**父类 / 基类 / 超类**
- 继承的类叫：**子类 / 派生类**

### 1.3 继承的语法

用 `extends` 关键字：

```java
class 子类名 extends 父类名 {
    // 子类特有的内容
}
```

用继承重新写狗和猫：

```java
// 父类：动物
class Animal {
    String name;
    int age;

    void eat() {
        System.out.println(name + "正在吃饭");
    }

    void sleep() {
        System.out.println(name + "正在睡觉");
    }
}

// 子类：狗，继承动物
class Dog extends Animal {
    // 只需要写狗特有的
    void bark() {
        System.out.println(name + "汪汪汪");
    }
}

// 子类：猫，继承动物
class Cat extends Animal {
    // 只需要写猫特有的
    void mew() {
        System.out.println(name + "喵喵喵");
    }
}

public class Test {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.name = "旺财";
        dog.eat();      // 从父类继承来的
        dog.sleep();    // 从父类继承来的
        dog.bark();     // 子类自己的

        Cat cat = new Cat();
        cat.name = "咪咪";
        cat.eat();
        cat.sleep();
        cat.mew();
    }
}
```

输出：
```
旺财正在吃饭
旺财正在睡觉
旺财汪汪汪
咪咪正在吃饭
咪咪正在睡觉
咪咪喵喵喵
```

> 💡 继承的好处：代码复用，不用重复写相同的代码。

---

## 二、super关键字

### 2.1 为什么需要super？

如果子类和父类有同名的成员，直接访问会优先用子类自己的。想用父类的怎么办？用 `super`。

```java
class Father {
    int money = 100;
}

class Son extends Father {
    int money = 50;

    void showMoney() {
        System.out.println("儿子的钱：" + money);       // 子类自己的
        System.out.println("父亲的钱：" + super.money); // 父类的
    }
}

public class Test {
    public static void main(String[] args) {
        Son son = new Son();
        son.showMoney();
    }
}
```

输出：
```
儿子的钱：50
父亲的钱：100
```

### 2.2 super的用法

> **super的三个作用：**
> 1. `super.成员变量`：访问父类的成员变量
> 2. `super.方法名()`：调用父类的成员方法
> 3. `super(参数)`：调用父类的构造方法

```java
class Father {
    String name = "父亲";

    void sayHello() {
        System.out.println("我是父亲");
    }
}

class Son extends Father {
    String name = "儿子";

    void test() {
        System.out.println(super.name);  // 访问父类变量
        super.sayHello();                // 调用父类方法
    }
}
```

---

## 三、子类构造方法

### 3.1 先有父，再有子

创建子类对象时，会**先调用父类的构造方法，再调用子类的构造方法**。

就像先有父亲，才有儿子。

```java
class Father {
    Father() {
        System.out.println("父亲的构造方法");
    }
}

class Son extends Father {
    Son() {
        // 这里默认有一行 super(); 调用父类无参构造
        System.out.println("儿子的构造方法");
    }
}

public class Test {
    public static void main(String[] args) {
        Son son = new Son();
    }
}
```

输出：
```
父亲的构造方法
儿子的构造方法
```

### 3.2 父类有参构造怎么办？

如果父类只有有参构造，子类必须显式调用：

```java
class Animal {
    String name;

    Animal(String name) {
        this.name = name;
        System.out.println("动物构造方法");
    }
}

class Dog extends Animal {
    Dog(String name) {
        super(name);  // 必须写，调用父类有参构造
        System.out.println("狗构造方法");
    }
}

public class Test {
    public static void main(String[] args) {
        Dog dog = new Dog("旺财");
        System.out.println(dog.name);
    }
}
```

输出：
```
动物构造方法
狗构造方法
旺财
```

> **⚠️ 注意：**
> 1. `super()` 必须是构造方法的第一条语句
> 2. `super()` 和 `this()` 不能同时出现
> 3. 父类如果只有有参构造，子类必须显式调用 `super(参数)`

---

## 四、super和this的区别

| 区别 | this | super |
|------|------|-------|
| 含义 | 当前对象的引用 | 父类对象的引用 |
| 访问变量 | 访问本类的变量 | 访问父类的变量 |
| 调用方法 | 调用本类的方法 | 调用父类的方法 |
| 构造方法 | `this()` 调用本类构造 | `super()` 调用父类构造 |

> 💡 简单记：this找自己，super找父亲。

---

## 五、继承中的初始化顺序

有继承关系时，执行顺序是：

```
父类静态代码块 → 子类静态代码块 → 父类实例代码块 → 父类构造方法 → 子类实例代码块 → 子类构造方法
```

```java
class Father {
    static {
        System.out.println("父类静态代码块");
    }
    {
        System.out.println("父类实例代码块");
    }
    Father() {
        System.out.println("父类构造方法");
    }
}

class Son extends Father {
    static {
        System.out.println("子类静态代码块");
    }
    {
        System.out.println("子类实例代码块");
    }
    Son() {
        System.out.println("子类构造方法");
    }
}

public class Test {
    public static void main(String[] args) {
        new Son();
        System.out.println("---第二次---");
        new Son();
    }
}
```

输出：
```
父类静态代码块
子类静态代码块
父类实例代码块
父类构造方法
子类实例代码块
子类构造方法
---第二次---
父类实例代码块
父类构造方法
子类实例代码块
子类构造方法
```

> 💡 静态代码块只执行一次（类加载时），实例代码块和构造方法每次创建对象都执行。

---

## 六、protected关键字

四种访问权限在继承中的表现：

| 权限 | 同包同类 | 同包不同类 | 不同包子类 | 不同包其他类 |
|------|---------|-----------|-----------|-------------|
| private | ✅ | ❌ | ❌ | ❌ |
| default | ✅ | ✅ | ❌ | ❌ |
| protected | ✅ | ✅ | ✅ | ❌ |
| public | ✅ | ✅ | ✅ | ✅ |

> 💡 protected就是专门给子类用的，不同包的子类也能访问。

---

## 七、final关键字

final可以修饰变量、方法、类：

### 7.1 修饰变量：常量，不能修改

```java
final int NUM = 10;
// NUM = 20;  // 编译报错，不能修改
```

### 7.2 修饰方法：不能被重写

```java
class Father {
    final void test() {
        System.out.println("父类方法");
    }
}

class Son extends Father {
    // 不能重写test方法，编译报错
    // void test() {}
}
```

### 7.3 修饰类：不能被继承

```java
final class Animal {
}

// 编译报错，不能继承final类
// class Dog extends Animal {}
```

> 💡 我们常用的String类就是final修饰的，不能被继承。

---

## 八、继承 vs 组合

### 8.1 继承：is-a（是一个）

狗是动物，猫是动物 → 用继承

```java
class Animal {}
class Dog extends Animal {}  // 狗是一个动物
```

### 8.2 组合：has-a（有一个）

汽车有轮胎、有发动机 → 用组合

```java
class Tire {}    // 轮胎
class Engine {}  // 发动机

class Car {
    Tire tire;      // 汽车有轮胎
    Engine engine;  // 汽车有发动机
}
```

> 💡 实际开发中，能用组合就尽量用组合，继承层次不要超过3层。

---

## 九、多态

### 9.1 什么是多态？

**多态：同一件事，不同对象做，产生不同的结果。**

比如"吃饭"这件事：
- 猫吃饭 → 吃鱼
- 狗吃饭 → 吃骨头
- 人吃饭 → 吃米饭

都是吃饭，但不同的对象结果不一样。

### 9.2 多态的实现条件

> **多态必须同时满足三个条件：**
> 1. 有继承关系
> 2. 子类重写父类的方法
> 3. 父类引用指向子类对象（向上转型）

```java
// 1. 父类
class Animal {
    String name;

    Animal(String name) {
        this.name = name;
    }

    // 2. 父类方法
    void eat() {
        System.out.println(name + "在吃饭");
    }
}

// 1. 子类继承
class Cat extends Animal {
    Cat(String name) {
        super(name);
    }

    // 2. 子类重写方法
    @Override
    void eat() {
        System.out.println(name + "吃鱼~~~");
    }
}

// 1. 子类继承
class Dog extends Animal {
    Dog(String name) {
        super(name);
    }

    // 2. 子类重写方法
    @Override
    void eat() {
        System.out.println(name + "吃骨头~~~");
    }
}

public class Test {
    // 3. 父类引用作为参数
    public static void feed(Animal animal) {
        animal.eat();  // 多态：不同对象调用不同方法
    }

    public static void main(String[] args) {
        Cat cat = new Cat("咪咪");
        Dog dog = new Dog("旺财");

        feed(cat);  // 咪咪吃鱼~~~
        feed(dog);  // 旺财吃骨头~~~
    }
}
```

输出：
```
咪咪吃鱼~~~
旺财吃骨头~~~
```

---

## 十、方法重写（Override）

### 10.1 什么是重写？

子类对父类的方法重新实现，**方法名、参数列表、返回值都一样**，只是方法体不同。

就像儿子继承了父亲的手艺，但自己改进了做法。

```java
class Father {
    void cook() {
        System.out.println("父亲做的菜");
    }
}

class Son extends Father {
    @Override  // 重写的注解，帮我们检查是否正确
    void cook() {
        System.out.println("儿子做的菜，更好吃");
    }
}
```

### 10.2 重写的规则

> **⚠️ 重写的规则：**
> 1. 方法名、参数列表必须和父类一样
> 2. 返回值类型可以一样，或者是父子关系
> 3. 访问权限不能比父类更严格（父类public，子类不能是private）
> 4. static、private、final修饰的方法不能被重写
> 5. 构造方法不能被重写

### 10.3 重写 vs 重载

| 区别 | 重写（Override） | 重载（Overload） |
|------|-----------------|-----------------|
| 位置 | 子类和父类之间 | 同一个类中 |
| 方法名 | 必须相同 | 必须相同 |
| 参数列表 | 必须相同 | 必须不同 |
| 返回值 | 相同或父子关系 | 可以不同 |
| 作用 | 子类重新实现父类方法 | 同一个方法名处理不同参数 |

---

## 十一、向上转型和向下转型

### 11.1 向上转型

**父类引用指向子类对象**，自动转换，安全。

```java
Animal animal = new Cat("咪咪");  // 向上转型，自动的
animal.eat();  // 调用的是Cat的eat方法（多态）
```

**三种使用场景：**

```java
// 1. 直接赋值
Animal a1 = new Cat("咪咪");

// 2. 方法传参
public static void feed(Animal a) {
    a.eat();
}
feed(new Dog("旺财"));

// 3. 方法返回
public static Animal getAnimal() {
    return new Cat("咪咪");
}
```

> ⚠️ 向上转型后，只能调用父类有的方法，不能调用子类特有的方法。

### 11.2 向下转型

**父类引用转回子类引用**，需要强制转换，有风险。

```java
Animal animal = new Cat("咪咪");

// 向下转型，需要强转
Cat cat = (Cat)animal;
cat.mew();  // 可以调用子类特有方法了
```

**风险：如果实际不是这个类型，会报错！**

```java
Animal animal = new Dog("旺财");
// Cat cat = (Cat)animal;  // 运行报错！ClassCastException
```

### 11.3 instanceof关键字

向下转型前，先用 `instanceof` 判断一下，更安全：

```java
Animal animal = new Dog("旺财");

if (animal instanceof Cat) {
    Cat cat = (Cat)animal;
    cat.mew();
} else if (animal instanceof Dog) {
    Dog dog = (Dog)animal;
    dog.bark();
}
```

> 💡 `instanceof` 判断左边的对象是不是右边的类型，是就返回true。

---

## 十二、多态的优缺点

### 12.1 优点

**1. 避免大量if-else，代码更简洁**

不用多态：
```java
void drawShape(String type) {
    if (type.equals("圆形")) {
        System.out.println("●");
    } else if (type.equals("方形")) {
        System.out.println("■");
    } else if (type.equals("三角形")) {
        System.out.println("▲");
    }
}
```

用多态：
```java
class Shape {
    void draw() {}
}
class Circle extends Shape {
    @Override
    void draw() { System.out.println("●"); }
}
class Square extends Shape {
    @Override
    void draw() { System.out.println("■"); }
}

void drawShape(Shape shape) {
    shape.draw();  // 一行搞定，不用if-else
}
```

**2. 扩展能力强**

新增一个三角形，只需要写一个新类继承Shape，不用改原来的代码。

### 12.2 缺点

- 代码运行效率稍微降低（需要动态判断调用哪个方法）
- 不能直接调用子类特有的方法

---

## 十三、注意：不要在构造方法中调用重写方法

这是一个坑！看代码：

```java
class Father {
    Father() {
        func();  // 构造方法中调用了可重写的方法
    }

    void func() {
        System.out.println("Father.func()");
    }
}

class Son extends Father {
    int num = 1;

    @Override
    void func() {
        System.out.println("Son.func() num = " + num);
    }
}

public class Test {
    public static void main(String[] args) {
        Son son = new Son();
    }
}
```

输出：
```
Son.func() num = 0
```

为什么num是0而不是1？

因为创建Son对象时，先调用Father的构造方法，此时Son的num还没初始化（还是默认值0），但已经触发了多态，调用了Son的func方法。

> ⚠️ 结论：构造方法中尽量不要调用可重写的方法，容易出问题。

---

## 总结

本文整理了Java继承和多态的核心知识点：

**继承部分：**
- 继承：子类复用父类的代码，用extends
- super：访问父类的变量、方法、构造方法
- 子类构造：先父后子
- 初始化顺序：父静态 → 子静态 → 父实例 → 父构造 → 子实例 → 子构造
- final：修饰变量不能改，修饰方法不能重写，修饰类不能继承
- 组合优先于继承

**多态部分：**
- 多态三条件：继承、重写、父类引用指向子类对象
- 重写：子类重新实现父类方法
- 向上转型：自动，安全，但只能调用父类方法
- 向下转型：强转，有风险，用instanceof判断
- 多态好处：代码简洁，易扩展
- 坑：不要在构造方法中调用重写方法

继承和多态是面向对象的精髓，理解了这些，面向对象就算入门了。下一篇继续学习接口和抽象类。

---

**Java入门学习笔记系列，持续更新中。**
