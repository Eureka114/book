---
title: Java.util.ListIterator类
katex: true
categories: lang-features
---



# Java.util.ListIterator类

1. `java.util.ListIterator` 是 Java 集合框架中的一个接口，它是 `Iterator` 接口的一个扩展版本，专门为 List 集合设计。`ListIterator` 允许双向遍历（前向和后向），并且可以在遍历过程中修改列表元素。

   以下是 `ListIterator` 的一些主要方法：

   1. **hasNext()**：如果迭代器中还有更多元素，则返回 true。
   2. **next()**：返回迭代器中的下一个元素，并向前移动迭代器。
   3. **hasPrevious()**：如果以逆向遍历列表，列表迭代器有更多元素，则返回 true。
   4. **previous()**：返回列表中的上一个元素，并向后移动迭代器。
   5. **nextIndex()**：返回下一次调用 next() 方法时将返回的元素的索引。
   6. **previousIndex()**：返回下一次调用 previous() 方法时将返回的元素的索引。
   7. **remove()**：从列表中移除由 next() 或 previous() 方法返回的最后一个元素（可选操作）。
   8. **set(E e)**：用指定的元素替换 next() 或 previous() 返回的最后一个元素（可选操作）。
   9. **add(E e)**：将指定的元素插入列表（可选操作）。

   这些方法使得 `ListIterator` 在处理列表时比 `Iterator` 更加灵活和强大。

2. `Iterator`和`ListIterator`都是Java中的迭代器接口，用于遍历集合元素。但是，它们之间存在一些关键的区别：

   1. **方向**：`Iterator`只能向前移动，而`ListIterator`可以向前也可以向后移动。

   2. **添加元素**：`ListIterator`可以在遍历过程中添加元素，而`Iterator`不能。

   3. **修改元素**：`ListIterator`和`Iterator`都可以在遍历过程中使用`set()`方法修改元素，但是`ListIterator`提供了更多的灵活性。

   4. **获取索引**：`ListIterator`可以通过`nextIndex()`和`previousIndex()`获取当前元素的索引，而`Iterator`不能。

   5. **适用的集合**：`Iterator`可以应用于所有的Collection实现类，而`ListIterator`只能应用于实现了List接口的集合类。

   因此，如果你需要在遍历过程中进行更复杂的操作（如向前/向后遍历，添加元素，获取元素索引等），那么`ListIterator`可能是更好的选择。如果你只需要简单地遍历集合，那么`Iterator`就足够了。

3. 使用`ListIterator`的版本，可以在遍历过程中安全地删除元素：

   ```java
   import java.util.ArrayList;
   import java.util.List;
   import java.util.ListIterator;
   
   public class Failfast {
       public static void main(String[] args){
           List<String> list = new ArrayList<>();
           list.add("1");
           list.add("2");
           list.add("3");
           list.add("4");
           ListIterator<String> iter = list.listIterator();
           while (iter.hasNext()) {
               String tmp = iter.next();
               System.out.println(tmp);
               if (tmp.equals("2")) {
                   iter.remove();
               }
           }
       }
   }
   ```

   在这个版本中，我们使用了`ListIterator`的`remove()`方法来删除元素，这是在迭代过程中安全的。如果你试图直接通过`list.remove()`删除元素，将会抛出`ConcurrentModificationException`，因为这被认为是在迭代过程中不安全地修改了集合。