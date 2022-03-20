# Java基础
##### 1.  Java 中的几种基本数据类型是什么？对应的包装类型是什么？各自占用多少字节呢？

Java 中有 8 种基本数据类型，分别为：
1.  6 种数字类型：
    -   4 种整数型：`byte`(1字节)、`short`(2字节)、`int`(4字节)、`long`(8字节)
    -   2 种浮点型：`float`(4字节)、`double`(8字节)
2.  1 种字符类型：`char`(2字节)
3.  1 种布尔型：`boolean`。
这八种基本类型都有对应的包装类分别为：`Byte`、`Short`、`Integer`、`Long`、`Float`、`Double`、`Character`、`Boolean` 。

**什么是自动拆装箱？**

-   **装箱**：将基本类型用它们对应的引用类型包装起来；
-   **拆箱**：将包装类型转换为基本数据类型；

##### 2.  `String`、 `StringBuffer` 和 `StringBuilder` 的区别是什么? `String` 为什么是不可变的?
**可变性**
`String` 类中使用 `final` 关键字修饰字符数组来保存字符串，我们知道被 `final` 关键字修饰的类不能被继承，修饰的方法不能被重写，修饰的变量是基本数据类型则值不能改变，修饰的变量是引用类型则不能再指向其他对象。因此，`final` 关键字修饰的数组保存字符串并不是 `String` 不可变的根本原因，因为这个数组保存的字符串是可变的（`final` 修饰引用类型变量的情况）。
`String` 真正不可变有下面几点原因：
1.  保存字符串的数组被 `final` 修饰且为私有的，并且`String` 类没有提供/暴露修改这个字符串的方法。
2.  `String` 类被 `final` 修饰导致其不能被继承，进而避免了子类破坏 `String` 不可变。

`StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串，不过没有使用 `final` 和 `private` 关键字修饰，最关键的是这个 `AbstractStringBuilder` 类还提供了很多修改字符串的方法比如 `append` 方法。

**线程安全性**
`String` 中的对象是不可变的，也就可以理解为常量，线程安全。`AbstractStringBuilder` 是 `StringBuilder` 与 `StringBuffer` 的公共父类，定义了一些字符串的基本操作，如 `expandCapacity`、`append`、`insert`、`indexOf` 等公共方法。`StringBuffer` 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。`StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的。
**性能**
每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象。`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

**对于三者使用的总结：**
1.  操作少量的数据: 适用 `String`
2.  单线程操作字符串缓冲区下操作大量数据: 适用 `StringBuilder`
3.  多线程操作字符串缓冲区下操作大量数据: 适用 `StringBuffer`

```md
String是可变的，StringBuffer和StringBuilder是不可变的：因为String类的底层是使用一个final关键字修饰的，并且是私有的字符串(char)数组来保存的字符串，所以是不可变的，我们平常修改String的值，其实修改的是他指向的内存地址，StringBuffer和StringBuilder继承的都是AbstractStringBuilder类，它没有被private和final修饰，而且提供了很多修改的方法，比如append；
String和StringBuffer是线程安全的：因为String是不可变的，可以理解为常量，所以是线程安全的，StringBuffer对那些方法加了同步锁，也是线程安全的；StringBuilder并没有加锁，所以是非线程安全的；
String在数据量较大的情况下性能是不如StringBuffer和StringBuilder的，因为在对String改变的时候，每次都会生成一个新的字符串，是会多占用一块内存的，而StringBuffer和StringBuilder是直接操作的原有的对象不会多占用内存；
所以在数据量少的情况下用String就可以了，在数据量较大的情况下，如果不要求线程安全就用StringBuilder，需要线程安全就用StringBuffer。
```

##### 3.  == 与 equals?hashCode 与 equals ?
**`==`** 对于基本类型和引用类型的作用效果是不同的：
-   对于基本数据类型来说，`==` 比较的是值。
-   对于引用数据类型来说，`==` 比较的是对象的内存地址。

> 因为 Java 只有值传递，所以，对于 == 来说，不管是比较基本数据类型，还是引用数据类型的变量，其**本质比较的都是值**，只是**引用类型变量存的值是对象的地址**。

**`equals()`** 不能用于判断基本数据类型的变量，只能用来判断两个对象是否相等。`equals()`方法存在于`Object`类中，而`Object`类是所有类的直接或间接父类，因此所有的类都有`equals()`方法。
`equals()` 方法存在两种使用情况：

-   **类没有重写 `equals()`方法** ：通过`equals()`比较该类的两个对象时，等价于通过`==` 比较这两个对象，使用的默认是 `Object`类`equals()`方法。
-   **类重写了 `equals()`方法** ：一般我们都重写 `equals()`方法来比较两个对象中的属性是否相等；若它们的属性相等，则返回 true(即，认为这两个对象相等)。

`String` 中的 `equals` 方法是被重写过的，因为 `Object` 的 `equals` 方法是比较的对象的内存地址，而 `String` 的 `equals` 方法比较的是对象的值。

当创建 `String` 类型的对象时，虚拟机会在常量池中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个 `String` 对象。

``````java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}

``````

**hashCode() 与 equals()**
`hashCode()` 的作用是获取哈希码（`int` 整数），也称为散列码。这个哈希码的作用是确定该对象在哈希表中的索引位置。

`hashCode()`定义在 JDK 的 `Object` 类中，这就意味着 Java 中的任何类都包含有 `hashCode()` 函数。另外需要注意的是： `Object` 的 `hashCode()` 方法是本地方法，也就是用 C 语言或 C++ 实现的，该方法通常用来将对象的内存地址转换为整数之后返回。

**每个对象都有hashcode，对象的hashcode怎么得来的呢？**
首先一个对象肯定有物理地址，在别的博文中会hashcode说成是代表对象的地址，这里肯定会让读者形成误区，对象的物理地址跟这个hashcode地址不一样，**hashcode代表对象的地址说的是对象在hash表中的位置，物理地址说的对象存放在内存中的地址**，那么对象如何得到hashcode呢？

通过对象的内部地址(也就是物理地址)转换成一个整数，然后该整数通过hash函数的算法就得到了hashcode。**所以，hashcode是什么呢？就是在hash表中对应的位置。**

这里如果还不是很清楚的话，举个例子，hash表中有 hashcode为1、hashcode为2、(…)3、4、5、6、7、8这样八个位置，有一个对象A，A的物理地址转换为一个整数17(这是假如)，就通过直接取余算法，17%8=1，那么A的hashcode就为1，且A就在hash表中1的位置。

添加元素进`HastSet`的过程
>当你把对象加入 `HashSet` 时，`HashSet` 会先计算对象的 `hashCode` 值来判断对象加入的位置，同时也会与其他已经加入的对象的 `hashCode` 值作比较，如果没有相符的 `hashCode`，`HashSet` 会假设对象没有重复出现。但是如果发现有相同 `hashCode` 值的对象，这时会调用 `equals()` 方法来检查 `hashCode` 相等的对象是否真的相同。如果两者相同，`HashSet` 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就大大减少了 `equals` 的次数，相应就大大提高了执行速度。

`hashCode()` 和 `equals()`都是用于比较两个对象是否相等。

**那为什么 JDK 还要同时提供这两个方法呢？**

这是因为在一些容器（比如 `HashMap`、`HashSet`）中，有了 `hashCode()` 之后，判断元素是否在对应容器中的效率会更高（参考添加元素进`HastSet`的过程）！

我们在前面也提到了添加元素进`HastSet`的过程，如果 `HashSet` 在对比的时候，同样的 `hashCode` 有多个对象，它会继续使用 `equals()` 来判断是否真的相同。也就是说 `hashCode` 帮助我们大大缩小了查找成本。

**那为什么不只提供 `hashCode()` 方法呢？**

这是因为两个对象的`hashCode` 值相等并不代表两个对象就相等。

**那为什么两个对象有相同的 `hashCode` 值，它们也不一定是相等的？**

因为 `hashCode()` 所使用的哈希算法也许刚好会让多个对象传回相同的哈希值。越糟糕的哈希算法越容易碰撞，但这也与数据值域分布的特性有关（所谓哈希碰撞也就是指的是不同的对象得到相同的 `hashCode` )。

总结下来就是 ：

-   如果两个对象的`hashCode` 值相等，那这两个对象不一定相等（哈希碰撞）。
-   如果两个对象的`hashCode` 值相等并且`equals()`方法也返回 `true`，我们才认为这两个对象相等。
-   如果两个对象的`hashCode` 值不相等，我们就可以直接认为这两个对象不相等。

****为什么重写 equals() 时必须重写 hashCode() 方法？

因为两个相等的对象的 `hashCode` 值必须是相等。也就是说如果 `equals` 方法判断两个对象是相等的，那这两个对象的 `hashCode` 值也要相等。

如果重写 `equals()` 时没有重写 `hashCode()` 方法的话就可能会导致 `equals` 方法判断是相等的两个对象，`hashCode` 值却不相等。

**思考** ：重写 `equals()` 时没有重写 `hashCode()` 方法的话，使用 `HashMap` 可能会出现什么问题。

**总结** ：

-   `equals` 方法判断两个对象是相等的，那这两个对象的 `hashCode` 值也要相等。
-   两个对象有相同的 `hashCode` 值，他们也不一定是相等的（哈希碰撞）。


```md
==判断的是两个对象的内存地址是否相等，也就是判断这两个对象是不是同一个对象 equals也是判断两个对象是否相等，但是一般有两种情况，类没有重写equals方法，那他跟==的作用是一样的，还有一种就是重写了equals方法，像String类，重写之后就是判断这两个字符串的内容是否相等，一般都会重写equals方法

hashcode()获取哈希码，哈希码的作用就是用来确定对象在hash表中的位置。这个逻辑大概就是，对象的物理地址经过一系列转换和hash算法后，得到一个hashcode。
但是这个算法算出来两个对象的hashcode有可能相等，这就是hash碰撞，这个时候就需要equals方法来判断这两个对象到底是否相等。所以，两个对象的hashcode相等时，这两个对象不一定相等，只有equals为true时，才相等；如果两个对象的hashcode都不相等，那么他们两个就一定不相等。

因此重写equals方法后必须重写hashcode方法，因为两个对象相等的话，他们的hashcode也一定要相等，但如果重写了equals方法没重写hashcode方法的话，可能会导致，其实两个对象是相等的，但hashcode不相等的情况
```


##### 4.  Java 反射？反射有什么缺点？你是怎么理解反射的（为什么框架需要反射）
##### 5.  谈谈对 Java 注解的理解，解决了什么问题
##### 6.  Java 泛型了解么？什么是类型擦除？介绍一下常用的通配符
##### 7.  内部类了解吗？匿名内部类了解吗
##### 8.  BIO,NIO,AIO 有什么区别?******