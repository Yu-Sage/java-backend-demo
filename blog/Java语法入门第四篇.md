# Java语法入门学习笔记（第四篇）：数组的定义与使用

> 继续Java入门学习，这篇整理数组的核心知识点，适合初学者参考。

---

## 一、数组的基本概念

### 1.1 为什么要用数组？

假设要存5个学生的成绩，按照之前的写法：

```java
int score1 = 70;
int score2 = 80;
int score3 = 85;
int score4 = 60;
int score5 = 90;
```

这样写没问题，但如果有100个学生呢？要创建100个变量吗？

观察发现：这些成绩的类型都是相同的，那有没有一种类型可以存储相同类型的多个数据？这就是**数组**。

### 1.2 什么是数组？

**数组：相同类型元素的集合，在内存中是一段连续的空间。**

就像现实中连在一起的车库：
- 每个车位都一样大（类型相同）
- 车位是连在一起的（空间连续）
- 每个车位有编号（下标，从0开始）

---

## 二、数组的创建与初始化

### 2.1 数组的创建

```java
T[] 数组名 = new T[N];
```

- `T`：数组中元素的类型
- `T[]`：数组的类型
- `N`：数组的长度

```java
int[] array1 = new int[10];      // 可以容纳10个int的数组
double[] array2 = new double[5]; // 可以容纳5个double的数组
```

### 2.2 数组的初始化

**动态初始化：创建时指定长度**

```java
int[] array = new int[10];  // 长度为10，元素默认都是0
```

**静态初始化：创建时直接指定内容**

```java
int[] array1 = new int[]{1, 2, 3, 4, 5};
double[] array2 = new double[]{1.0, 2.0, 3.0};

// 简写方式（推荐）
int[] array3 = {1, 2, 3, 4, 5};
```

### 重点注意事项

> **⚠️ 必须记住：**
> 1. 静态初始化时，`{}`中数据类型必须和`[]`前的类型一致
> 2. 静态初始化简写`int[] arr = {1,2,3}`不能拆成两步写
> 3. 数组没有初始化时，元素有默认值

**基本类型的默认值：**

| 类型 | 默认值 |
|------|--------|
| byte/short/int/long | 0 |
| float/double | 0.0 |
| char | \u0000 |
| boolean | false |
| 引用类型 | null |

---

## 三、数组的使用

### 3.1 访问数组元素

数组通过**下标**访问元素，下标从0开始：

```java
int[] array = {10, 20, 30, 40, 50};

System.out.println(array[0]);  // 10
System.out.println(array[1]);  // 20
System.out.println(array[2]);  // 30

// 也可以修改元素
array[0] = 100;
System.out.println(array[0]);  // 100
```

> ⚠️ 注意：下标范围是 `[0, 数组长度)`，不能越界！

```java
int[] array = {1, 2, 3};
System.out.println(array[3]);  // 报错！下标越界
// ArrayIndexOutOfBoundsException
```

### 3.2 遍历数组

**方式一：普通for循环**

```java
int[] array = {10, 20, 30, 40, 50};
for (int i = 0; i < array.length; i++) {
    System.out.println(array[i]);
}
```

> 💡 `数组名.length` 可以获取数组的长度

**方式二：for-each循环（推荐，更简单）**

```java
int[] array = {10, 20, 30, 40, 50};
for (int x : array) {
    System.out.println(x);
}
```

for-each不需要写下标，不容易写错，遍历数组时推荐用这个。

---

## 四、数组是引用类型（重点）

### 4.1 JVM内存分布（简单了解）

JVM把内存分成了几个区域，我们只关心两个：

| 区域 | 存什么 | 特点 |
|------|--------|------|
| **虚拟机栈** | 局部变量、方法调用信息 | 方法结束就销毁 |
| **堆** | new出来的对象 | 只要还在使用就不会销毁 |

### 4.2 基本类型 vs 引用类型

```java
int a = 10;           // 基本类型，变量里直接存值
int[] arr = {1,2,3};  // 引用类型，变量里存的是地址
```

**区别：**
- 基本类型变量：空间里直接存值
- 引用类型变量：空间里存的是对象在堆中的地址

画个图理解：

```
栈（main方法）          堆
┌──────────┐        ┌──────────┐
│ a = 10   │        │  arr[0]=1│
│ arr = 地址 ──────→│  arr[1]=2│
└──────────┘        │  arr[2]=3│
                    └──────────┘
```

arr变量里存的是数组在堆中的首地址，通过这个地址就能找到数组。

### 4.3 引用变量的赋值

```java
int[] array1 = {1, 2, 3};
int[] array2 = array1;  // array2和array1指向同一个数组

array2[0] = 100;
System.out.println(array1[0]);  // 输出100！
```

因为array1和array2指向同一块内存，修改其中一个，另一个也会变。

### 4.4 认识null

`null`表示空引用，不指向任何对象：

```java
int[] arr = null;
System.out.println(arr[0]);  // 报错！空指针异常
// NullPointerException
```

> ⚠️ 注意：null表示无效的内存位置，不能对它进行任何读写操作。

---

## 五、数组与方法

### 5.1 数组作为方法参数

**传基本类型：形参改变不影响实参**

```java
public static void func(int x) {
    x = 10;
}

public static void main(String[] args) {
    int num = 0;
    func(num);
    System.out.println(num);  // 输出0，没变
}
```

**传数组（引用类型）：形参修改会影响实参**

```java
public static void func(int[] a) {
    a[0] = 10;
}

public static void main(String[] args) {
    int[] arr = {1, 2, 3};
    func(arr);
    System.out.println(arr[0]);  // 输出10，变了！
}
```

> 💡 原因：传数组时，传的是地址，形参和实参指向同一块内存。

### 5.2 数组作为方法返回值

比如：获取斐波那契数列的前N项

```java
public static int[] fib(int n) {
    if (n <= 0) {
        return null;
    }
    int[] array = new int[n];
    array[0] = array[1] = 1;
    for (int i = 2; i < n; i++) {
        array[i] = array[i-1] + array[i-2];
    }
    return array;
}
```

---

## 六、数组常用操作

### 6.1 数组转字符串

使用`Arrays.toString()`方法：

```java
import java.util.Arrays;

int[] arr = {1, 2, 3, 4, 5, 6};
String str = Arrays.toString(arr);
System.out.println(str);  // 输出 [1, 2, 3, 4, 5, 6]
```

### 6.2 数组拷贝

**方式一：直接赋值（不是真拷贝，指向同一个数组）**

```java
int[] arr = {1, 2, 3};
int[] newArr = arr;  // 指向同一个数组
```

**方式二：Arrays.copyOf（真拷贝，创建新数组）**

```java
import java.util.Arrays;

int[] arr = {1, 2, 3, 4, 5};
int[] newArr = Arrays.copyOf(arr, arr.length);

newArr[0] = 100;
System.out.println(arr[0]);     // 输出1，没变
System.out.println(newArr[0]);  // 输出100，变了
```

**拷贝部分元素：**

```java
int[] newArr2 = Arrays.copyOfRange(arr, 2, 4);  // 拷贝下标2到3的元素
```

### 6.3 求数组平均值

```java
public static double avg(int[] arr) {
    int sum = 0;
    for (int x : arr) {
        sum += x;
    }
    return (double)sum / arr.length;
}
```

### 6.4 顺序查找

从头到尾一个个找：

```java
public static int find(int[] arr, int data) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == data) {
            return i;  // 找到了，返回下标
        }
    }
    return -1;  // 没找到，返回-1
}
```

### 6.5 二分查找（重点）

针对**有序数组**，二分查找效率更高。

**思路：**
1. 取中间位置的元素
2. 要找的数比中间小，去左边找
3. 要找的数比中间大，去右边找
4. 相等就找到了

```java
public static int binarySearch(int[] arr, int toFind) {
    int left = 0;
    int right = arr.length - 1;
    
    while (left <= right) {
        int mid = (left + right) / 2;
        if (toFind < arr[mid]) {
            right = mid - 1;   // 去左边找
        } else if (toFind > arr[mid]) {
            left = mid + 1;    // 去右边找
        } else {
            return mid;        // 找到了
        }
    }
    return -1;  // 没找到
}
```

> 💡 二分查找有多快？10000个元素最多只需要找14次！元素越多优势越大。

### 6.6 冒泡排序

**思路（升序）：**
1. 相邻元素两两比较，大的往后放
2. 一趟下来，最大的就到最后了
3. 重复这个过程，直到全部排好

```java
public static void bubbleSort(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        for (int j = 1; j < arr.length - i; j++) {
            if (arr[j-1] > arr[j]) {
                int tmp = arr[j-1];
                arr[j-1] = arr[j];
                arr[j] = tmp;
            }
        }
    }
}
```

> 💡 Java内置了更高效的排序：`Arrays.sort(arr)`

### 6.7 数组逆序

**思路：** 头尾交换，向中间靠拢

```java
public static void reverse(int[] arr) {
    int left = 0;
    int right = arr.length - 1;
    while (left < right) {
        int tmp = arr[left];
        arr[left] = arr[right];
        arr[right] = tmp;
        left++;
        right--;
    }
}
```

---

## 七、二维数组

二维数组本质上就是一维数组，每个元素又是一个一维数组。

```java
int[][] arr = {
    {1, 2, 3, 4},
    {5, 6, 7, 8},
    {9, 10, 11, 12}
};

// 遍历二维数组
for (int row = 0; row < arr.length; row++) {
    for (int col = 0; col < arr[row].length; col++) {
        System.out.printf("%d\t", arr[row][col]);
    }
    System.out.println();
}
```

输出：
```
1   2   3   4
5   6   7   8
9   10  11  12
```

> 💡 二维数组用得不多，了解就行，还有三维、四维数组，但基本用不到。

---

## 总结

本文整理了Java数组的核心知识点：

**数组基础：**
- 数组是相同类型元素的集合，内存连续
- 动态初始化和静态初始化
- 通过下标访问元素，下标从0开始
- 遍历数组用for循环或for-each

**引用类型：**
- 数组是引用类型，变量存的是地址
- 引用赋值后，两个变量指向同一个数组
- 传数组参数时，形参修改会影响实参

**常用操作：**
- Arrays.toString：数组转字符串
- Arrays.copyOf：数组拷贝
- 顺序查找、二分查找
- 冒泡排序、Arrays.sort
- 数组逆序

**二维数组：** 了解基本用法即可

数组是Java编程的重要基础，掌握后才能继续学习面向对象等内容。下一篇将继续学习面向对象。

---

**Java入门学习笔记系列，持续更新中。**
