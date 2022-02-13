# 面向对象-多态

# 相关概念

面向对象的三个基本特征：封装、继承、多态。

封装，通过合并特征和行为来创建数据类型，将实现细节与可供外部访问调用的接口相分离；

继承，采用现有类的形式，继承现有类的特性和行为，以提高代码复用性。

多态，基于继承，通过基类调用时同一方法表现出不同的行为，消除类型之间的耦合；

把对象引用作为基类引用，称为向上转型：

```java
public tune(Parent i){
	i.play();
	……
}

tune(new Child());
```

可以在不修改`tune()`方法代码的情况下，根据传入的派生类Child的不同具体类型，执行不同的`play()`方法（该方法在派生类中均有被重写）。

简单来说，我们只要编写与基类接口打交道的代码，那么其对于派生类来说也可以正常工作。

# 绑定

在上述例子中，编译器是如何得知基类引用i究竟指向哪个具体的子类类型呢？这与绑定有关。

## 绑定相关概念

绑定：

将一个方法调用与一个方法主体（具体类型中的方法体）关联起来。

前期绑定：

在运行前（一般由编译器和链接器实现）完成绑定，这是面向过程语言默认选择的方式。

后期绑定：

在编译阶段不清楚，但是在运行阶段根据对象的具体类型进行绑定，又称为“动态绑定”或“运行时绑定”。

1. Java是通过后期绑定来实现多态的。
2. Java中除了static和final方法（private方法也是隐式的final）外，其他所有方法都是后期绑定的。
3. 对于属性，无论是否静态，任何属性的访问都会直接在编译时解析并绑定（不是指确定数值），都不是多态的。

# 总结

多态，是方法的多态，是Java通过后期绑定来实现的

四个必要条件：

1. 继承
2. 重写
3. 动态绑定
4. 基类引用指向派生类对象

```java
// 基类Parent，派生类Child
Parent p = new Child()；
p.move();

// 或者，更常见的是，在方法声明、调用中
// 方法声明：
public tune(Parent i){
	i.play();
	……
}
// 方法调用：
tune(new Child());
```

注意点：

1. 在进行编译时，只检查对象的引用类型`Parent`，所以如果`Parent`基类类型中没有`move()`方法的话会报错；而在执行阶段，通过动态绑定，运行的是派生类对象实例的方法，即`Child`的方法`move()`。
2. 对于“重写”的private方法，无法实现多态：
    
    ```java
    public class PrivateOverride {
        private void f() {
            System.out.println("private f()");
        }
    
        public static void main(String[] args) {
            PrivateOverride po = new Derived();
            po.f();
        }
    }
    
    class Derived extends PrivateOverride {
        public void f() {
            System.out.println("public f()");
        }
    }
    
    输出:
    private f()
    ```
    
    分析：
    
    1. 基类中的方法f()与派生类中的方法f()，并不是重写与被重写的关系，仅仅方法名称和参数列表相同而已，是不同类中的不同方法；
    2. 类加载过程中，无论有没有创建基类对象，基类都会被加载进来，所以在执行时，可以执行到基类的方法f()。【个人理解】
3. 对于静态方法，无法体现多态，其为前期绑定。
4. 对于属性，无论是否静态，任何属性的访问都会直接在编译时解析并绑定，都不是多态的。
    
    ```java
    class Super {
        public int field = 0;
    
        public int getField() {
            return field;
        }
    }
    
    class Sub extends Super {
        public int field = 1;
    
        @Override
        public int getField() {
            return field;
        }
    
        public int getSuperField() {
            return super.field;
        }
    }
    
    public class FieldAccess {
        public static void main(String[] args) {
            Super sup = new Sub(); // Upcast System.out.println("sup.field = " + sup.field +
            ", sup.getField() = " + sup.getField());
            Sub sub = new Sub();
            System.out.println("sub.field = " + sub.field +
                    ", sub.getField() = " + sub.getField()
        }
    }
    ```
    
    分析：Super.field 和 Sub.field 被分配了不同的存储空间，因此， Sub类实际上包含了两个称为 field 的属性:它自己的和来自Super的。在引用Sub类的field 时，默认的field属性是属于他自己的那个field属性。
    

# 构造器与多态

构造器是隐式static的，所以本身不具有多态性。

下面展示一个构造器调用的方法具有多态性的例子。
在基类构造器中，调用派生类重写的方法，那么在初始化过程中该方法会表现出多态性：

```java
class Super {
    public Super() {
        System.out.println("Super() start!");
        getField();
        System.out.println("Super() end!");
    }

    public void getField() {
        System.out.println("super getfield!");
    }
}

class Sub extends Super {
    public int field = 1;

    public Sub(){
        System.out.println("Sub(),getfield: " + field);
    }

    @Override
    public void getField() {
        System.out.println("sub getfield: " + field);
    }
}

// 执行
public class mainTest extends Object {
    public static void main(String[] args) {
        new Sub();
    }
}

// 输出：
Super() start!
sub getfield: 0
Super() end!
Sub(),getfield: 1
```

1. `getField()`方法表现出了多态性，执行的内容为派生类重写的方法体；
2. 更意外的是，在派生类的构造器执行之前，派生类的方法竟然被调用了，并且派生类中的成员变量`field`值为0，而不是1。
3. 这说明在初始化的所有事发生前，分配给对象成员的存储空间已经被初始化为二进制0/null/false；在后续执行构造函数时，才按照声明顺序进行了赋值。
