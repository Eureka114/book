# Operator - 双冒号

## 范围解析运算符`::`

1. **访问类的静态成员**：如果你有一个类的静态成员（无论是变量还是函数），你可以使用范围解析运算符来访问它。

```cpp
class MyClass {
public:
    static void myMethod() {
        std::cout << "This is a static method." << std::endl;
    }
};
// 调用静态方法
MyClass::myMethod();
```

> 在Java中，访问类的静态成员，使用`.`操作符。

```Java
public class C1{
	static int var=107;
	static void staticMethod(){
		//...;
	}
}
public class C{
	public static void main(String[] args){
		int var1=C1.var;
		C1.staticMethod();
	}
}
```



2. **在类的外部定义成员函数**：如果你在类的声明中只提供了成员函数的原型，而没有提供实现，那么你可以在类的外部使用范围解析运算符来定义这个函数。（不建议，Java不支持）

```cpp
class MyClass {
public:
    void myMethod();
};
// 在类的外部定义成员函数
void MyClass::myMethod() {
    std::cout << "This is a method." << std::endl;
}
```

3. **指定命名空间**：如果你在一个命名空间中定义了一个函数或变量，你可以使用范围解析运算符来指定这个命名空间。

```cpp
namespace MyNamespace {
    void myFunction() {
        std::cout << "This is a function in MyNamespace." << std::endl;
    }
}
// 调用命名空间中的函数
MyNamespace::myFunction();
```

## Java 8开始的方法引用(Method References)

1. 方法引用可以用来引用类的方法或构造函数，通常用在函数式编程中，如流（Stream）操作或者Lambda表达式。

   ```java
   List<String> list = Arrays.asList("Apple", "Banana", "Cherry");
   
   // 引用String类的toUpperCase方法
   list.stream().map(String::toUpperCase).forEach(System.out::println);
   
   // 引用System.out类的println方法
   list.forEach(System.out::println);
   //p.s. 和.运算符一个样
   ```

   