# 接口

接口和抽象类，提供了一种更加结构化地将接口（对外界暴露，以供外界访问调用的入口）与实现（具体的方法体实现细节）分离的方法。

1. 抽象类（的继承）、接口（的实现）都可以体现多态性，接口和抽象类的重要作用也依赖于多态性。
2. 个人理解，实现（implements），基本等同于类的继承（extends）

# 抽象类

介于普通类和接口之间，抽象类的目的是为其派生类提供一个通用的接口（对外界暴露，以供外界访问调用的入口），并通过通用接口来操作一系列派生类。

1. 创建抽象类的对象是没有意义的，即使有构造器，编译器也会阻止这样做
2. 创建抽象类是为了通过通用接口操作一系列的类，本身无需具体实现
3. 可以将不含任何抽象方法的类指明为`abstract`，这样可以阻止该类创建对象
4. 抽象类中，也可以包含属性，无限制
5. 抽象类中，也允许包含普通方法，无限制
6. 抽象类中，抽象方法的访问权限除了禁用`private abstract`以外，可以是任何级别。
7. 派生类需要重写实现抽象类的所有抽象方法，否则派生类也会成为抽象类，强制加上`abstract`
    
    <aside>
    📒 抽象类与普通类的区别，基本在于 含有抽象方法 这一点上
    
    </aside>
    

## 抽象方法

仅有声明，没有方法体：

```java
abstract class Basic{
	// 抽象方法
	public abstract void unimplemented(Object obj);

	// 普通方法
	public void play(){……}
}
```

# 接口

interface

1. 接口中，没有构造器
2. 接口中，属性只能是静态的，均被隐式的指定为`public static final`的静态属性
3. 接口中，Java8前仅允许存在抽象方法，均为隐式的`abstract`；但是Java8新增允许使用`default`来创建存在方法体的方法；Java8允许添加`static`静态方法。
4. 接口中，方法的访问权限仅允许为public（缺省也默认为public）
5. 接口与抽象类最明显的区别就是使用上的惯用方式：
    1. 接口的典型使用是一个类的类型或一个形容词，如Runnable或Serializable
    2. 而抽象类通常是类层级接口的一部分或一件事物的类型，如ActionHero
    3. 抽象类可以单继承，但是接口仅能多实现

```java
// interfaces/AnInterface.java
interface AnInterface {
    void firstMethod();

    void secondMethod();

    default void newMethod() {
        System.out.println("newMethod");
    }
}

// interfaces/AnImplementation.java
class AnImplementation implements AnInterface {
    public void firstMethod() {
        System.out.println("firstMethod");
    }

    public void secondMethod() {
        System.out.println("secondMethod");
    }

    public static void main(String[] args) {
        AnInterface i = new AnImplementation();
        i.firstMethod();
        i.secondMethod();
        i.newMethod();
    }
}
```

## 接口中的default方法

1. Java8，`default`用于在接口中创建有方法体的方法；
2. 一般来说，该方法是，各个实现类中共同需要的方法(实现细节完全相同)，这样做的话则无需再在各个实现类中重复编写
3. 该方法可以直接调用

## 接口中的static方法

1. Java8，允许接口中添加`static`静态方法；
2. 无需实现就可以直接调用；
3. 这是模版方法设计模式的一个版本(在 “设计模式” 一章中详细描述)，静态方法runOps() 是一个模版方法

```java
package org.vasyl.day6;

interface Operations {
    void execute();

		// 静态方法，作为的是一个模版方法
    static void runOps(Operations... ops) {
        for (Operations op : ops) {
            op.execute();
        }
    }

    static void show(String msg) {
        System.out.println(msg);
    }
}

class Bing implements Operations {
    @Override
    public void execute() {
        Operations.show("Bing");
    }
}

class Crack implements Operations {
    @Override
    public void execute() {
        Operations.show("Crack");
    }
}

class Twist implements Operations {
    @Override
    public void execute() {
        Operations.show("Twist");
    }
}

public class InterfaceDemo {
    public static void main(String[] args) {
        Operations.runOps(new Bing(), new Crack(), new Twist());
    }
}
```

# 接口与抽象类的区别

| ///// | 抽象类 | 接口 |
| --- | --- | --- |
| 组合方式 | 单继承，本身也可以继承其他类 | 多实现，本身也可以继承其他接口 |
| 构造器 | 有，但是自身不能构建对象 | 没有 |
| 属性 | 可以包含属性，无限制 | 不支持对象状态，仅允许含有静态属性，隐式地指定为public static final |
| 方法 | 可以包含普通方法，无限制 | 仅允许包含抽象方法（隐式的abstract）；但在Java8中，允许default创建含有方法体的方法，允许static方法 |
| 方法访问权限 | 抽象方法中不允许存在private abstract的，其他无限制 | 方法均为隐式地指定为public |
| 作用原理 | 多态 | 多态 |

使用原则：

1. 大多数时候，普通类已经做得很好，如果不行的话，再使用接口或抽象类；
2. 使用接口和抽象类时，“尽可能抽象”，即尽量使用接口

# 完全解耦

## 示例一：策略设计模式

```java
package org.vasyl.day6;

import java.util.Arrays;

public class ApplicatorDemo {
    // 运行测试
    public static void main(String[] args) {
        String str = "My Name Is Vasyl Hsiao";
        Applicator.apply(new Upcase(), str);
        Applicator.apply(new Downcase(), str);
        Applicator.apply(new Splitter(), str);
    }
}

class Applicator {
    // 接口调用代码，体现多态性，可复用
    public static void apply(Processor p, Object o) {
        System.out.println(p.name());
        System.out.println(p.process(o));
    }
}

// 基类，或者说是，接口
class Processor {
    public Object name() {
        return this.getClass().getName();
    }

    public Object process(Object o){
        return o;
    };
}

// 各个派生类
class Upcase extends Processor {
    @Override
    public Object process(Object o) {
        return o.toString().toUpperCase();
    }
}

class Downcase extends Processor {
    @Override
    public Object process(Object o) {
        return o.toString().toLowerCase();
    }
}

class Splitter extends Processor {
    @Override
    public Object process(Object o) {
        return Arrays.toString(o.toString().split(" "));
    }
}
```

1. `Applicator.apply()`方法可以根据传入的参数类型的不同，从而具备不同行为，称为策略设计模式，实际上就是多态
2. 这里的`Processor`不只可以为普通基类，也可以是抽象类或者接口，”继承“或”实现“并不影响多态的体现，”实现“也可以看作是”继承“

下面还有一个接口`Filter`，以及其派生类。可以看到`Filter`接口与`Processor`接口非常类似（`Filter`类中`process()`方法的输入输出都是`Waveform`类型，其并不能直接继承`Processor`），但是`Filter`接口并不能直接使用`Applicator.apply()`方法及相关代码。

主要是因为`Applicator.apply()`方法和`Processor`过于耦合，这阻止了`Applicator.apply()`方法被复用。那使用接口`Filter`时，怎样复用`Applicator.apply()`方法呢？

```java
package org.vasyl.day6;

// 基类，或者说是，接口
class Filter {
    public String name() {
        return getClass().getSimpleName();
    }

    public Waveform process(Waveform input) {
        return input;
    }
}

// 各个派生类
class LowPass extends Filter {
    double cutoff;

    public LowPass(double cutoff) {
        this.cutoff = cutoff;
    }

    @Override
    public Waveform process(Waveform input) {
        return input; // Dummy processing 哑处理
    }
}

class HighPass extends Filter {
    double cutoff;

    public HighPass(double cutoff) {
        this.cutoff = cutoff;
    }

    @Override
    public Waveform process(Waveform input) {
        return input;
    }
}

class BandPass extends Filter {
    double lowCutoff, highCutoff;

    public BandPass(double lowCut, double highCut) {
        lowCutoff = lowCut;
        highCutoff = highCut;
    }

    @Override
    public Waveform process(Waveform input) {
        return input;
    }
}

class Waveform {
    private static long count;
    private final long id = count++;

    @Override
    public String toString() {
        return "Waveform:" + id;
    }
}
```

## 示例二：适配器设计模式

如果`Processor`是一个接口，那么限制就会变得松动到足以复用`Applicator.apply()`方法。

1. 将`Processor`改为接口类型：

```java
package org.vasyl.day6;

import java.util.Arrays;

// 基类，或者说是，接口
interface Processor {
    default Object name() {
        return this.getClass().getName();
    }

     Object process(Object o);
}

// 各个派生类
class Upcase implements Processor {
    @Override
    public Object process(Object o) {
        return o.toString().toUpperCase();
    }
}

class Downcase implements Processor {
    @Override
    public Object process(Object o) {
        return o.toString().toLowerCase();
    }
}

class Splitter implements Processor {
    @Override
    public Object process(Object o) {
        return Arrays.toString(o.toString().split(" "));
    }
}
```

1. 使用适配器设计模式，允许代码接受已有接口(接口Filter)并产生需要的接口(接口Processor)：其他接口Filter -> 需要的接口Processor；
    
    对接适配类似但不同的两个接口，编写的适配器可使得原来调用接口(接口Processor)的代码无需变动，就可以适配其他接口(接口Filter)，以提高接口调用的代码部分的复用性：
    

```java
package org.vasyl.day6;

class FilterAdapter implements Processor { // 继承实现需要的接口，下面进行接口转换时使用了委托
    // 待适配的接口类型
    Filter filter;

    FilterAdapter(Filter filter) {
        this.filter = filter;
    }

    @Override
    public Object name() {
        return filter.name();
    }

    @Override
    public Object process(Object o) {
        return filter.process((Waveform) o);
    }
}

// 运行测试
public class FilterDemo {
    public static void main(String[] args) {
        // Applicator.apply()的方法体无需进行修改，即可复用，调用新的接口Filter

        Waveform w = new Waveform();
        Applicator.apply(new FilterAdapter(new LowPass(1.0)), w);
        Applicator.apply(new FilterAdapter(new HighPass(2.0)), w);
        Applicator.apply(new FilterAdapter(new BandPass(3.0, 4.0)), w);
    }
}
```

1. 适配器代码中，使用了委托

一句话概括，使用适配器设计模式，编写一个适配器作接口转换，可使得在调用其他类似接口（Filter接口）时能够复用策略设计模式的接口调用代码（调用`Processor`接口的代码`Applicator.apply()`）；

相当于将`Applicator.apply()`方法与`Processor接口`进行了解耦，使其可应用于其他类似接口。

<aside>
❓ ?问题：在上面2个实例中，即使`Processor`不是接口，而是类，也可以通过适配器成功进行接口调用代码的复用啊，那把Processor变为接口的意义是什么呢，究“解耦”在哪里了？

</aside>
