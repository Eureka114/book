# 重载与重写



|      | 重写（子类覆盖父类） | 重载（同类同名方法） |         重载运算符          |
| :--: | :------------------: | :------------------: | :-------------------------: |
| Java |      @override       |   参数列表必须不同   |             无              |
| C++  | virtual ... override |   参数列表必须不同   | class operator+ (...){...;} |

## 重写(Overriding)

在Java和C++中，重写是指在子类中定义一个与父类中同名、同参数列表的方法。子类的方法会覆盖父类的方法。

```Java
public class Parent { public void display() { /*...*/ } }
public class Child extends Parent {
    @Override
    public void display() { /*...*/ }
}
```

```c++
class Parent {
public:
    virtual void display() { /*...*/ }//在C++中，重写方法通常会被声明为virtual，一般在父类声明。
}

class Child : public Parent {
public:
    void display() override { /*...*/ }//子类使用Override关键字
};
```

## 重载(Overloading)

指的是在同一个类中可以有多个同名方法。但这些方法的参数列表必须不同（即参数的数量或类型不同）。返回类型不能用来区分重载的方法。

```Java
public class OverloadExample {
    public void display(int num) { /*...*/ }
    public void display(String str) { /*...*/ }
}//Java
```

```c++
class OverloadExample {
public:
    void display(int num) { /*...*/ }
    void display(std::string str) { /*...*/ }
};//C++
```

> 注意：在C++中，类的定义实际上是一个声明语句。在C++中，所有的声明语句都需要以分号结束。在Java中，类的定义被视为一个完整的语句，不需要额外的分号来结束。
