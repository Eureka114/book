# 结构体指针

## 结构体指针的定义与使用

1. 当一个指针变量用来指向一个结构体变量时，称之为结构体指针变量。结构体指针变量的值所指向的**结构体变量的起始地址**。通过结构体指针即可访问该结构体变量。

2. 定义：结构体名.指针变量名

3. 实例：

   ```c++
   struct student
   {
   	char name[20];//姓名 
   	char sex;//性别
   	float score;//成绩 
   };
   student *p;
   ```

4. 结构体指针变量也必须要赋值后才能使用。赋值是把**结构体变量的首地址赋予该指针变量**，不能把结构名赋予该指针变量。

   ```C++
   student *point;
   student variable;
   point=variable;//[错误] cannot convert 'student' to 'student*' in assignment
   point=&variale;//正确
   ```

5. 定义结构体**变量**时，系统才会分配内存。

6. 特点：

   - 结构体变量可以整体操作，例如：swap(a[j],a[j+1]);

   - 结构体变量的成员访问也很方便、清晰，例如：cin>>a[i].name;

   - 结构体变量的初始化和数组的初始化类似，例如：student op={“gaoxiang”,89,90,179};

7. 成员运算符“.”在存取成员数值时使用，其优先级最高，并具有左结合型。

8. 结构体成员函数调用的一般形式为：结构体变量名.成员函数。结构体成员函数默认将结构体变量作为引用参数。（此外，还有构造函数，真与Java的class类似了属于是）

   ```C++
    struct Student {  
       std::string name;  
       int age;  
       Student(const std::string& name, int age):name(name), age(age) {}    // 构造函数  
       void displayInfo() const {    // 成员函数：显示学生信息  
           std::cout << "Name: " << name << ", Age: " << age << std::endl;  
       }
   };  
   int main() {  
       // 创建Student对象  
       Student student1("Alice", 20);  
       // 调用成员函数  
       student1.displayInfo();  // 输出：Name: Alice, Age: 20  
       return 0;  
   }
   ```

   ## 自引用结构

1. 在一个结构体内部包含一个类型为该结构体本身的成员非法，因为可以出现无限套娃。但是，包含一个指针而不是结构体是合法的，因为编译器在结构体的长度确定之前就已经知道指针的长度。

   ```C++
   struct stu
   {
   	char name[20];//姓名
   	int age;//年龄
   	int score;//成绩
   	stu *p; 
   }
   ```

   

2. 定义：当一个结构体中有一个或多个成员是**指针**，它们所指向的类型就是**本结构体类型**时，通常这种结构体称为“引用自身的结构体”，即“**自引用结构**”。

3. 作用：自引用结构是C/C++语言实现动态数据结构的**基石**。包括动态的**链表**、**堆**、**栈**、**树**，无不是自引用结构的具体实现。下面的可以实现链表：

   ```C++
   struct node
   {
   	int x,y;
   	node *next;
   } point;
   ```