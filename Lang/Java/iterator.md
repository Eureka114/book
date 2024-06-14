# Java 迭代器

1. 定义：迭代器是属于**设计模式**之一，迭代器模式提供了一种方法来顺序访问一个聚合对象中各个元素，而不保留该对象的内部表示。

   迭代器本身也是一个序列`S`，在任何时候，迭代器中都有**唯一的当前元素**。迭代器还必须提供某种机制，使得我们可以不断转向`S`中的下一元素，并将其置为新的当前元素。实际上，一个位置本身已经可以被看作是一个迭代器了，只不过它还不能不断更新。总之，所谓迭代器，就是对一组对象之间的位置、直接后继等 关系的集成。

   > 1. `Iterator对象`称为**迭代器**，主要用于遍历`Collection集合`中的元素。
   > 2. 所有实现了`Collection接口`的集合类都有一个`iterator()`方法，用以返回一个实现了`Iterator接口的对象`，即可以返回一个`迭代器`。
   > 3. `Iterator`仅用于遍历集合，`Iterator`本身并不存放对象。

   > 迭代器提供了一种统一的机制，无论对象之间的具体组织形式如何，它都可以从一组对象中为我们逐一列举出每个元素。

2. 迭代器的ADT：其ADT必须支持以下两个方法：`bool hasNext()`和`E getNext()`。除此之外，为了支持上述对数据结构遍历的统一机制，对象集合的ADT必须提供`Iterator elements()`的方法，而支持位置概念的ADT，还要提供`Iterator position()`方法。

3. Java中的简单迭代器：Java通过`java.util.iterator`提供了一个迭代器。其简单实现：

   ```java
   public static void printVector(java.util.Vector v) {
       java.util.Iterator it = v.iterator();
       while (it.hasNext()) {
           System.out.println(it.next());
       }
   }
   ```

4. Java迭代器接口：

   ```java
   public interface Iterator<E>{
   	boolean hasNext();//检查迭代器中是否还有剩余的元素
   	E getNext();//返回迭代器中的下一元素 
   }
   ```

5. 迭代器的实现：
   1. 使用**快照**的方法实现：所谓“快照”，指我们为容器创建迭代器的时候，相当于给容器拍了一张快照（Snapshot）。之后即便我们增删容器中的元素，快照中的元素并不会做相应的改动。而迭代器遍历的对象是快照而非容器，这样就避免了在使用迭代器遍历的过程中，增删容器中的元素，导致的不可预期的结果或者报错。这里，我们可以使用支持对后续对这些元素的遍历操作的数据结构，如**栈和列表**。时间复杂度：$O(n)$。

      > `!isEmpty()==hasNext()`, `pop()==getNext()`

   2. 借助如**列表**等的数据结构来实现迭代器：可以实现位置迭代器（如下），也可以实现元素迭代器（从实现位置迭代器派生出来，最后返回其位置内的元素）。时间复杂度：$O(1)$（基于列表）。

      ```java
      public class IteratorPosition implements Iterator {
      	private List list;//列表
      	private Position nextPosition;//当前（下一个）位置
      
      //默认构造方法
      	public IteratorPosition() { list = null; }
      
      //构造方法
      	public IteratorPosition(List L) {
      		list = L;
      		if (list.isEmpty())//若列表为空，则
      			nextPosition = null;//当前位置置空
      		else//否则
      			nextPosition = list.first();//从第一个位置开始
      	}
      
      //检查迭代器中是否还有剩余的位置
      	public boolean hasNext() { return (nextPosition != null); }
      
      //返回迭代器中的下一位置
      	public Object getNext() throws ExceptionNoSuchElement {
      		if (!hasNext()) throw new ExceptionNoSuchElement("意外：没有下一位置");
      		Position currentPosition = nextPosition;
      		if (currentPosition == list.last())//若已到达尾位置，则
      			nextPosition = null;//不再有下一个位置
      		else//否则
      			nextPosition = list.getNext(currentPosition);//转向下一位置
      		return currentPosition;
      	}
      }
      ```

6. Java中的列表和迭代器：

   1. 在使用迭代器的过程中，如果原容器中的内容正在被（比如另一个线程）修改，就很可能会造成危险的后果。若需在容器中的某一“位置”进行插入、删除或替换之类的操作，最好是通过一个位置对象来指明。
   2. `Fail-fast`机制：`java.util.iterator`的大多数实现都提供了Fail-fast机制，在利用迭代器遍历某一容器的过程中，一旦发现该容器的内容有所改变，迭代器就会抛出`ConcurrentModificationException`意外错并立刻退出（并发修改异常）。
   3. 在Java中，还可以在由`List`类实现迭代器的情况下，使用`ListIterator`迭代器，它可以在迭代过程中做添加和删除操作，还可以向前/向后遍历元素。

