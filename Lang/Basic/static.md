# Static关键字

## 在Java中

`static`关键字也有多种用途：

1. **在变量前**：`static`关键字可以使变量成为类的所有实例共享的变量。也就是说，无论创建多少个类的实例，都只有一个静态变量的副本。
```java
 public class MyClass {
     public static int myVar;  // 所有MyClass的实例共享这个变量
 }
```
 2. **在方法前**：`static`关键字可以使方法成为可以不通过类的实例就可以调用的方法。静态方法只能访问类的静态变量和静态方法。（在Java中，所有的方法都要定义在类中，没有所谓的“全局方法”）
```java
 public class MyClass {
     public static void myMethod() {}  // 可以通过MyClass.myMethod()直接调用，而不用实例化该类。
 }
```
 3. **在内部类前**：`static`关键字可以使内部类成为静态内部类。静态内部类与普通内部类的主要区别在于，静态内部类不持有外部类的引用，因此它不能访问外部类的非静态成员。
```java
 public class OuterClass {
     static class InnerClass {}  // 静态内部类
 }
```
 4. **在初始化块前**：`static`关键字可以用于定义静态初始化块，这是一种特殊的代码块，只在类加载时执行一次。
```java
 public class MyClass {
     static {  // 静态初始化块
         // 这里的代码只在类加载时执行一次
     }
 }
```

## 在C++中

1. `static`关键字有多种用途，具体取决于它的上下文：

   1. **在全局变量或函数前**：`static`关键字可以限制全局变量或函数的可见性，使其只在定义它的文件中可见。这可以防止其他文件通过外部链接访问这些变量或函数。
    ```cpp
    // file1.cpp
    static int myVar = 0;  // 只在file1.cpp中可见
    static void myFunc() {}  // 只在file1.cpp中可见
    ```
   2. **在类的成员变量前**：`static`关键字可以使类的成员变量成为类的所有实例共享的变量。也就是说，无论创建多少个类的实例，都只有一个静态成员变量的副本。
    ```cpp
    class MyClass {
    public:
        static int myVar;  // 所有MyClass的实例共享这个变量
    };
    ```
   3. **在类的成员函数前**：`static`关键字可以使类的成员函数成为可以不通过类的实例就可以调用的函数。静态成员函数只能访问类的静态成员变量。
   ```cpp
   class MyClass {
   public:
       static void myFunc() {}  // 可以通过MyClass::myFunc()直接调用
   };
   ```
   4. **在局部变量前**：`static`关键字可以使函数内的局部变量在函数调用结束后仍然保持其值。也就是说，静态局部变量的生命周期是整个程序的运行时间。（`static`为静态局部变量，当函数第一次调用完毕后，`static`类型的变量的值不会在第二次调用时会初始化掉。）
   ```cpp
   void myFunc() {
       static int myVar = 0;  // 在函数调用结束后仍然保持其值
       myVar++;
   }
   //另一个例子：
   static int a=1;
   a++;
   return a;
   //第一次调用返回2，第二次返回3......
   ```