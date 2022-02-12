# 面向对象-封装

# 概念

类或对象仅向外部公开必要的内容，隐藏内部实现细节。

# 包

包内包含一组类，被组织在一个单独的命名空间下（namesapce）下。

Internet域名→独一无二的package命名→编译后.class文件的存放目录结构（从CLASSPATH位置开始）

# 访问权限控制

通过权限访问修饰符，可以控制哪些代码可以访问类及其成员：

1. public：任何代码，都可以访问。
2. default：默认，包访问权限（package access），同一包下的所有类，都可以访问。
    
    有时也称为friendly。
    
    若一个类没有为自己设定明确的包名，那么Java会将这样的类默认隶属于其所在目录的默认包中，若该类是包访问权限，那么就代表着其为该目录下所有其他文件（仅是同一级目录下的，不包括下级目录）都提供了包访问权限。
    
3. protected：继承访问权限，类自身以及类内部方法，再加上子类，可以访问。
    
    对于private的、default的（与子类不在同一包下）父类成员，子类是会继承的，但是无权访问使用。
    
4. private：仅类自身和类内部的方法，可以访问。

# 类的访问权限

类的访问权限不可以声明为private、protected，因为这样的话，除了类自己或者其子类，其他任何类都不能访问这个类，没有意义。

但是，可以将类声明为包访问权限或public，然后将其构造器声明为private，这样只有自己能够创建自己的对象（在类的static成员中），其他类也可以访问该类：

```java
class Soup1{
	private Soup1(){}

	public static Soup1 makeSoup(){
		return new Soup1();
	}
}

或者

// 单例模式：只允许类闯将1个对象实例
class Soup2{
	private Soup2(){}

	private static Soup2 sp2 = new Soup2();
	
	public static Soup2 accessObj(){
		return sp2;
	}
}
```

这样其他类可以调用该类的static方法`makeSoup()`或者`accessObj()`让它自己来创建自己的对象，避免了其他类直接创建其对象。
