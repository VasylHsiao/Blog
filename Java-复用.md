# 面向对象-复用

# 复用基本概念

可以直接使用别人构建或调试过的代码，而无需从零开始编写新代码。

几种方式来达到该目的：

1. 组合（Composition）：在新类中创建并使用现有类的对象，不局限于现有类的形式。
2. **继承（Inheritance）**：创建现有类的子类，采用现有类的形式。
3. **委托（Delegation**）：介于组合与继承之间（个人理解，应该属于组合），在新类中创建并使用现有类的对象，并且在新类中公开现有类对象的方法，允许选择部分方法，而不一定是全部。

## 组合（Composition）

在新类中，创建现有类的对象，从而执行相关功能，非常常见。

## 继承（Inheritance）

```java
package org.vasyl.day1;

public class SpaceShipControls {
    void up(int velocity) {
    }

    void down(int velocity) {
    }

    void left(int velocity) {
    }

    void right(int velocity) {
    }

    void forward(int velocity) {
    }

    void back(int velocity) {
    }

    void turboBoost() {
    }
}

// 继承
class DerivedSpaceShip extends SpaceShipControls {
    private String name;

    public DerivedSpaceShip(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return name;
    }

    public static void main(String[] args) {
        DerivedSpaceShip protector = new DerivedSpaceShip("NSEA Protector");
        protector.forward(100);
    }
}

// 委托
class SpaceShipDelegation {
    private String name;
    private SpaceShipControls controls = new SpaceShipControls();

    public SpaceShipDelegation(String name) {
        this.name = name;
    }

    // Delegated methods:
    public void back(int velocity) {
        controls.back(velocity);
    }

    public void down(int velocity) {
        controls.down(velocity);
    }

    public void forward(int velocity) {
        controls.forward(velocity);
    }

    public void left(int velocity) {
        controls.left(velocity);
    }

//    public void right(int velocity) {
//        controls.right(velocity);
//    }
//
//    public void turboBoost() {
//        controls.turboBoost();
//    }
//
//    public void up(int velocity) {
//        controls.up(velocity);
//    }

    public static void main(String[] args) {
        SpaceShipDelegation protector =
                new SpaceShipDelegation("NSEA Protector");
        protector.forward(100);
    }
}
```

注意点：

1. 当你创建派生类的对象时，它包含基类的子对象，该自对象与基类的对象是一样的。
2. Java自动在派生类构造函数中插入对基类构造函数的调用，对基类构造函数的调用必须是派生类构造函数中的第一个操作。

## 委托（**Delegation**）

代码见上。

总而言之，都是为了代码复用，只是方式不同：

- 委托可以被看作是对象级别的重用机制，而继承是类级别的重用机制。
- 委托允许子类只复用父类中的部分方法，而不是全部，安全性以及灵活性更高。
- “有一个”的关系复用往往使用组合/委托（比如，轮子、窗户是现有类，车是新类）；
    
    “是一个”的关系复用往往使用继承（比如，车是现有类，跑车是新类）。
    

# 继承详细知识

继承：派生类→基类（父类、超类）。基类包含派生自它的子类之间共享的所有特征和行为。基类表示核心思想，派生类则表示该核心的不同实现方式以及其他附加信息。

<aside>
💡 单继承结构：Java中所有的类都直接或间接继承自java.lang.Object；当一个类没有指定extends时，就是默认为extends Object。

</aside>

## 里氏替换原则（Liskov substitution principle）

面向对象的基本设计原则之一，描述了有关继承的原则，指导我们在继承关系中进行父子类的设计，减少继承带来的风险与缺点（带来侵入性，降低程序的可移植性，增加了对象间的耦合性，等），增强程序的健壮性，比如在版本升级时也可以保持良好的兼容性、可维护性和可扩展性。

实现开闭原则的重要方式之一。

1. 定义
    
    里式替换原则有两层定义:
     定义1：
    
    > If S is a subtype of T, then objects of type T may be replaced with objects of type S, without breaking the program。
     如果S是T的子类，则T的对象可以替换为S的对象，而不会破坏程序。
    > 
    
    定义2：
    
    > Functions that use pointers of references to base classes must be able to use objects of derived classes without knowing it。
     所有引用其父类对象方法的地方，都可以透明的替换为其子类对象
    > 
    
    这两种定义方式其实都是一个意思，即：
    
    应用程序中任何父类对象出现的地方，我们都可以用其子类的对象来替换，并且可以保证原有程序的逻辑行为和正确性。
    
2. 原则解读
    1. 子类必须完全实现父类的抽象方法，但不能重写父类的非抽象方法。
    2. 子类可以增加自己特有的方法。
    3. 子类重载父类方法时，前置条件（即方法的形参/输入参数）要更为宽松/相同。
    4. 子类实现父类方法（即重写/重载方法、实现抽象方法）时，后置条件（即方法返回值/输出参数）要更为严格/相同。
    
    所以，在需要进行继承时，尽量不要从可实例化的父类中继承，而是要使用基于抽象类和接口的继承。
    

## 多态

定义时，对象引用为基类类型，而不清楚具体子类类型；具体运行时，会根据对象实例的具体子类类型，执行相应的方法而不出错，这就是对象的多态性（Polymorphism）；

简单来说就是，对于同一个接口/行为，使用不同类型实例会执行不同操作。

面向对象的程序设计是通过“动态绑定”的方式来实现对象多态性的。

三个必要条件：

1. 继承
2. 重写
3. 父类引用指向子类对象：Parent p = new Child()；

```java
// 基类Parent，派生里Child
Parent p = new Child()；
p.move();
```

注意点：

在进行编译时，只检查对象的引用类型`Parent`，所以如果`Parent`类型中没有`move()`方法的话会报错；而在执行阶段，运行的是对象实例的方法，即`Child`的方法`move()`。

## 重写（Override）

派生类对基类中允许其访问的方法的实现过程的重新编写。

注意一下几点：

1. 重写仅存在于继承关系中，子类仅能重写它有访问权限的父类方法；
2. 方法名称、参数列表完全相同；
    
    
3. 访问权限要比被重写方法更为宽松/相同；（与里氏替换原则中的类似，前置条件）
4. 返回值要要比重写方法更为严格/相同【java5中要求必须一致，java7、8则允许为原返回值类型的子类型】；（与里氏替换原则中的相同，后置条件）
5. 重写的方法能够抛出任何非强制异常，无论被重写的方法是否抛出异常。但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常

<aside>
📒 对于类中`private`的方法，已经被隐式地指定为了`fianl`。

其实不是因为指定为了`final`，才无法被重写，更为准确的理解是：
重写仅发生在该方法是基类接口的情况下，若该方法是private的那它就不属于基类接口，它仅是隐藏在类中的代码而已。如果你在派生类中以相同的名称、参数创建了public/protected/包访问权限的方法，也不会报错，这些方法被视为新的方法，与基类中的该方法没有任何联系。

当然，仅指定为`final`，也无法被重写。

</aside>

重载（Overload）

1. 重载不局限于继承关系，一般在同一个类中出现。
2. 方法名称相同，但是参数列表一定不同；
    
    
3. 访问权限、返回值、异常均可以作修改，无要求。但如果是继承关系中的重载，建议遵循里氏替换原则中对重载的相关要求（前置条件、后置条件）。

最常见的就是构造函数的重载。

重写与重载的区别：个人理解，重载后的方法与原方法在新类中是共存的；但重写后新类中仅有重写后的方法。

# final关键字介绍

初始化后不能再被改变。

## final修饰数据

1. final修饰的基本类型数值，为编译时常量：在编译阶段就可以确定其数值，并带入相关计算，减少了运行时的负担。
2. final修饰的对象引用类型，不是编译时常量：引用本身值不变，其始终指向一个对象，但是该对象实例本身是可以改变的，在编译阶段并不确定，在运行阶段会被赋值、改变。
3. 必须保证final修饰的数据在使用之前完成初始化：
    1. 在声明定义式就完成初始化`private final int i = 1;`
    2. 对于空白`final private final int i;`必须在构造函数中进行初始化。

<aside>
📒 `static`强调的是仅一个，其修饰的数据在类加载时已被初始化，并不会因为创新的对象而改变。

</aside>

## final修饰参数

1. 所修饰的参数类型是基本类型：在方法中，不能改变其数值；
2. 所修饰的参数类型是引用类型：在方法中，不能改变其指向的对象。

## final修饰方法

给方法上锁，防止继承后子类覆写改变该方法。

类中所有private方法都隐式地指定为了final。

过去，final修饰方法的还有为了提升效率，将方法的调用转化为内嵌调用，但是如果方法体过于冗杂，内嵌调用带来的提升会被抵消，在Java新版本中禁止这样做了。

<aside>
📒 By the way，

</aside>

## final修饰类

给类上锁，无法被继承。

# 类的初始化

## 类的加载时机

一般来说，类的代码在首次使用时被加载。有以下几种情况：

1. 类的static成员/代码被访问。
    
    <aside>
    📒 static成员/代码在被加载时是按照在类中的定义顺序；
    static成员/代码仅被初始化一次。
    
    </aside>
    
2. 创建类的对象。其实，类的构造器也被隐式地指定为了`static`。

所以，准确的说，一个类当它任意一个static成员/代码被访问时，就会被加载。

## 初始化顺序

1. 静态初始化：
    1. 父类，static成员/代码块
    2. 子类，static成员/代码块
2. 父类初始化：
    1. 父类，普通成员和非static代码块
    2. 父类，构造函数
3. 子类初始化
    1. 子类，普通成员和非static代码块
    2. 子类，构造函数

```java
package org.vasyl.day4;

/**
 * 类的初始化顺序
 */
public class InitializationDemo {
    public static void main(String[] args) {
        new SubClass();
    }
}

class Parent {
    // 静态变量
    public static String p_StaticField = "父类--静态变量";
    protected int i = 1;
    protected int j = 8;

    // 变量
    public String p_Field = "父类--变量";

    // 静态初始化块
    static {
        System.out.println(p_StaticField);
        System.out.println("父类--静态初始化块");
    }

    // 初始化块
    {
        System.out.println(p_Field);
        System.out.println("父类--初始化块");
    }

    // 构造器
    public Parent() {
        System.out.println("父类--构造器");
        System.out.println("i=" + i + ", j=" + j);
        j = 9;
    }
}

class SubClass extends Parent {

    // 静态变量
    public static String s_StaticField = "子类--静态变量";

    // 变量
    public String s_Field = "子类--变量";

    // 静态初始化块
    static {
        System.out.println(s_StaticField);
        System.out.println("子类--静态初始化块");
    }

    // 初始化块
    {
        System.out.println(s_Field);
        System.out.println("子类--初始化块");
    }

    // 构造器
    public SubClass() {
        System.out.println("子类--构造器");
        System.out.println("i=" + i + ",j=" + j);
    }

    // 程序入口
    public static void main(String[] args) {
        new SubClass();
    }
}
```

输出：

![Untitled](%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1-%E5%A4%8D%E7%94%A8%200605ecd34dac4778971c652abe200a2d/Untitled.png)

分析：

1. 首先会试图SubClass.main()，这是Subclass类的静态方法，于是加载器寻找SubClass类的编译代码（在SubClass.class文件中），在加载过程中发现其有一个基类，于是继续加载基类Parent。（无论是否创建基类对象，基类都会被加载！）
    
    <aside>
    📒 static部分的加载顺序与类中的文本顺序一致。
    
    </aside>
    
2. 接下来，优先执行基类中的static部分的初始化，紧接着是派生类中的static部分的初始化。这点很重要，因为派生类中 static 的初始化可能依赖基类成员是否被正确地初始化。
3. 至此，必要的类加载完毕，开始创建对象，具体执行过程如下：
    1. 访问至派生类的构造器`public SubClass()`。派生类的构造器中在最开始会自动调用基类的构造器。（也可以使用super.手动添加调用）
    2. 访问至基类的构造器。执行构造器具体内容前，会先执行其变量、代码块的初始化
    3. 执行基类变量的初始化
    4. 执行基类代码块的初始化
    5. 执行基类构造器的具体内容。基类构造器执行完毕后，返回开始执行派生类的构造器。同样的，在执行派生类构造器具体内容前，会先执行其变量、代码块的初始化。
    6. 执行派生里变量的初始化
    7. 执行派生类代码块的初始化
    8. 执行派生类构造器的具体内容
