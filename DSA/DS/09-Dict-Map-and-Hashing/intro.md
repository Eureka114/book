
# 映射(Map)、词典(Dictionary)和散列(Hash)

## 键值对

1. 键值对是计算系统和应用程序中的一种基本数据表示方式。设计人员通常希望采用一种开放式数据结构，以便在不修改现有代码或数据的情况下进行未来扩展。在这种情况下，数据模型的全部或部分内容可以用 `<属性名、值>`形式的 2 元组集合来表示，每个元素都是一个属性值对。根据特定应用和程序员选择的实现方式，属性名可能是唯一的，也可能不是唯一的。

   > A name–value pair, also called an attribute–value pair, key–value pair, or field–value pair, is a fundamental data representation in computing systems and applications. Designers often desire an open-ended data structure that allows for future extension without modifying existing code or data. In such situations, all or part of the data model may be expressed as a collection of 2-tuples in the form <attribute name, value> with each element being an attribute–value pair. Depending on the particular application and the implementation chosen by programmers, attribute names may or may not be unique.
   >
   > ——From [Name–value pair - Wikipedia](https://en.wikipedia.org/wiki/Name–value_pair)

## 映射和词典的定义

1. **词条(Entry)**：一种组合关系，由**关键码(Key)**和**数据项(Value)**合成。
2. **映射(Map)**的定义：
   1. 基本定义：假设两个非空集合$X, Y$，存在一个法则$f$，使得对$X$中的每个元素$x$，按法则$f$，在$Y$中有唯一确定的元素$y$与之对应，那么称$f$为从$X$到$Y$的映射。
   2. 在计算机科学中，映射是一种存放一组条目的容器，每个条目(Entry)都包含一个关键码(key)和一个值(value)。每个键都是唯一的，用于标识与之相关联的值。映射提供了一种快速查找值的方法，只需知道其关联的键。需要特别指出，在映射中，各条目的关键码**不允许重复冗余**。
   3. 映射中的元素由其关键码唯一标识，而且映射的作用就是通过关键码直接找到对应的元素，故不妨就把关键码理解为指向对应元素的”地址引用“。

3. **词典(Dictionary)**：词典是一种数据结构，它也存储键值对。在词典中，键也是唯一的，每个键都对应一个值。词典提供了一种方法，可以通过键快速访问、插入或删除对应的值。在词典中，各条目的关键码**可以重复冗余**。
4. 对于**映射和词典**这类结构，在作为基本数据单位的词条内部，关键码与数值的地位等同，二者不必加以区分。此类结构所支持的这种新的数据访问方式，即所谓的循数值访问。
5. 特别地，在许多编程语言中，映射和词典实际上是同一种数据结构。例如，在Python中，被称为字典（Dictionary），而在Java中，则被称为映射（Map）。

## Map的ADT及具体实现

1. Map类的接口：	

   ```java
   public interface Map { 
       //查询映射结构当前的规模 
       public int getSize(); 
       //判断映射结构是否为空 
       public boolean isEmpty(); 
       //若映射中存在以key为关键码的条目，则返回该条目的数据对象；否则，返回null 
       public Object get(Object key); 
       //若映射中不存在以key为关键码的条目，则插入条目(key, value)并返回null 
       //否则，将已有条目的数据对象替换为value，并返回原先的数据对象 
       public Object put(Object key, Object value); 
       //若映射中存在以key为关键码的条目，则删除之并返回其数据对象；否则，返回null 
       public Object remove(Object key); 
       //返回映射中所有条目的一个迭代器 
       public Iterator entries(); 
   } 
   ```

   > 这里，没有采取意外错的形式来处理退化情况。原因是在实际中，出现退化情况的概率很高，若采用报意外错的形式，效率十分低下。不过，直接返回null的做法也存在不足，其中最大的问题在于歧义性。比如，按照这一约定，我们将无法以key = null为条件进行查找。

2. 判等器(Equality Tester)：

   1. 映射结构必须能够比较任意一对关键码是否相等，每个映射结构在被创建的时候，都需要指定某一具体标准，以便进行关键码的比较。因此，为了实现映射结构，首先必须实现这样的一个判等器。

      ```java
      public interface EqualityTester { 
      	public boolean isEqualTo(Object a, Object b);//若a与b相等，则返回true；否则，返回false 
      }
      public class EqualityTesterDefault implements EqualityTester { 
          public EqualityTesterDefault() {} 
          public boolean isEqualTo(Object a, Object b) 
          { return (a.equals(b)); }//使用Java提供的判等器 
      }
      ```

   2. Java提供的`equals()`是一个较为通用的判等器，我们也可以新建一个类，利用Java提供的`euqals()`方法和自己实现的某些额外方法，实现一个更加通用的判等器。利用这种模式，程序员完全可以编写出独立的通用判等器，而无需触及对象内部的结构。


## java.util包中的映射类

1. Java在`java.util`包中已定义了一个名为`java.util.Map`的映射接口，而且也约定禁止关键码的重复。与第6.1.1节定义的映射接口相比，`java.util.Map`接口并未直接提供迭代器方法，而是通过两个名为`keys()`和`values()`的方法，间接地提供关于关键码或数据对象的迭代器。
2. [Map (Java Platform SE 8 ) (oracle.com)](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html)

## C++ STL Unordered-map

1. std::unordered_map 是一种关联容器，含有带唯一键的键-值对。搜索、插入和元素移除拥有平均常数时间复杂度。

   元素在内部不以任何特定顺序排序，而是组织进桶中。元素放进哪个桶完全依赖于对应键的散列。具有相同散列码的键出现于同一个桶。这允许对单独元素的快速访问，因为一旦计算其散列，它即代表元素所放进的确切的桶。

   如果映射的键相等性谓词在传递两个键时返回 true，则它们被认为 等价。如果两个键等价，则散列函数必须对它们返回相同的值。

   std::unordered_map 满足容器 (Container) 、知分配器容器 (AllocatorAwareContainer) 和无序关联容器 (UnorderedAssociativeContainer) 的要求。

2. [std::unordered_map - cppreference.com](https://zh.cppreference.com/w/cpp/container/unordered_map)

## 哈希表

如果将条目的关键码视作其在映射结构中的存放位置，则可以散列表（Hash table）的形式来实现映射结构。哈希表又称散列表，一种以「key-value」形式存储数据的数据结构。所谓以「key-value」形式存储数据，是指任意的键值 key 都唯一对应到内存中的某个位置。只需要输入查找的键值，就可以快速地找到其对应的 value。可以把哈希表理解为一种高级的数组，这种数组的下标可以是很大的整数，浮点数，字符串甚至结构体。

## 从IBM公司电话说起

1. 在[联系 IBM 支持 - IBM 文档](https://www.ibm.com/docs/zh/i/7.3?topic=overview-contacting-support)中，IBM公司的电话号码是`1-800-IBM-CALL`和`1-800-IBM-4YOU`。为什么电话号码中可以出现字母？观察9键键盘，在某些数字键下面，会有英文字母出现。所以，IBM公司的电话号码可以由`1-800-426-2255`改写成`1-800-IBM-CALL`。IBM公司的这种方式很是巧妙，借助个性化的电话号码，让用户能够加深对其的印象。这中间蕴含的思维方式，即是**散列**。
2. IBM的巧妙体现在两个方面：
   1. 让你能够记住这一家公司的电话号码；
   2. 拨号时，仍然使用的是数字系统，只需要对键盘进行字母+数字改造即可实现上述内容。

## 各种访问方式的比较

1. 循秩访问(Call by Rank)：Vector；
2. 循位置访问(Call by Position)：List；
3. 循关键码访问(Call by Key)：BST（二叉搜索树）；
4. 循数值访问(Call by Value)：Hashing（哈希、杂凑、**散列**）。

## 联合数组

联合数组(Associative Array)：可以根据数据元素的取值，直接访问。其下标不再是整数，甚至没有了大小次序，这样，更为直观、便捷。支持的语言有：`Java, Python, Perl, Ruby, PHP`等。

部分语言的例子：

```java
//1.java: HashMap+HashTable
import java.util.*;
public class Hash {
    public static void main(String[] args) {
        HashMap HM = new HashMap(); //Map
        HM.put("东岳", "泰山"); HM.put("西岳", "华山"); HM.put("南岳", "衡山");
        HM.put("北岳", "恒山"); HM.put("中岳", "嵩山"); System.out.println(HM);
        Hashtable HT = new Hashtable(); //Dictionary
        HT.put("东岳", "泰山"); HT.put("西岳", "华山"); HT.put("南岳", "衡山");
        HT.put("北岳", "恒山"); HT.put("中岳", "嵩山"); System.out.println(HT);
	}
}
```

```python
# 2.python: Dictionary Class
beauty = dict( { "沉鱼":"西施", "落雁":"昭君", "闭月":"貂蝉", "羞花":"玉环" } )
print( beauty )
beauty["红颜"] = "圆圆"
print( beauty )
for (alias, name) in beauty.items() :
	print( alias, ":", name )
for alias, name in sorted( beauty.items() ) :
	print( alias, ":", name )
for alias in sorted( beauty.keys(), reverse = True ) :
	print( alias, ":", beauty[alias] )
```

## 实例：电话簿

需求： 为一所学校制作电话簿。号码$\leftrightarrows$教员、学生、员工、办公室

1. 使用蛮力法（使用数组实现，按电话号码索引）（R=Real，实际拥有的个数；P=Possible，所有可能的个数。）
   1. 时间复杂度：$O(1)$。
   2. 空间复杂度：$O(P+R)$。
   3. 效率：$\eta=\dfrac{R}{P}$，过低（一般地，不到千分之一）。

2. 使用散列（简单地改造数组为桶数组(Bucket Array)，即散列表/哈希表(Hash Table)）
   1. 桶(Bucket)：直接或间接地指向一个词条。
   2. 哈希表：容量为$M$，满足$N<M<<R$，空间复杂度$O(N+M)=O(N)$。（压缩数组的长度，尽量保证$M=\Theta(N)$）
   3. 定址（散列(Hashing)）：根据词条的key（未必可比较）而直接确定散列表的入口。（无论表有多长）
   4. 确定散列函数`hash()`：`hash(key) -> &entry`，将词条的关键码转化为某一个桶单元，并根据这一个桶单元而找到目标词条。
   5. 时间复杂度：若合理地进行设计，则为$O(1)$。
3. 散列的具体实现：
   1. 确定哈希表：这里哈希表长为$M=\Theta(N)$。（C为常数）
   2. 确定散列函数：可以有很多种构造方法，这里使用取余法`hash(key) = key % M`。
   3. 空间效率：一般可以使用装填因子$\lambda=\dfrac{N}{M}$来衡量。

## 散列表的冲突

1. 散列冲突(Hash Collision)：关键码不同的词条被映射到同一散列地址的情况，即`hash(key1)=hash(key2)`，而`key1!=key2`。原因是，某些散列函数只是将一个相对较大的集合映射到一个远远相对较小的集合，而按照抽屉原理/鸽巢原理，冲突无法彻底避免。

2. 总而言之，随机越强、规律性越弱的散列函数越好。当然，完全符合上述条件的散列函数并不存在，我们只能通过先验地消除可能导致关键码分布不均匀的因素，最大限度地模拟理想的随机函数，尽最大可能降低发生冲突的概率。

3. 举例：将人所对应的词条按照生日（月/日）做散列存储。散列表长$M = 365$，装填因子$\lambda=\dfrac{N}{M}$（N为在场人数） 。

   > 至少有两位同学生日相同的可能性$P_{365}(n) = ?$
   >
   > 
   > $$
   > P_{365}(1) = 0, P_{365}(2) = 1/365,\cdots, P_{365}(22) = 47.6\%, \\P_{365}(23) = 50.7\%,\cdots P_{365}(100)=99.999969\%
   > $$

   

4. 在装填因子确定之后，散列策略的选取将至关重要，散列函数的设计也很有讲究。

5. 两项基本任务：

   1. 精心设计散列表及散列函数，尽可能降低冲突的概率。
   2. 制定可行的预案，以便在发生冲突时，能够尽快予以排解。
