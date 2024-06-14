# 树迭代器

1. 树的遍历是许多复杂操作的基础，也构成了许多高级算法的基本框架，通常，这些算法都是通过在遍历的过程中对访问到的对象实施某种操作，以实现特定的功能。

2. 在 C 或 C++等程序语言中，可以借助函数指针将具体的操作蕴含于遍历算法之内，这样就可以编写一个统一的遍历算法，而在解决不同的应用问题时，我们只需集中精力有针对性地编写具体的操作。

3. 然而遗憾的是，出于对系统安全性的考虑，Java 并不支持函数指针这一机制。故此，我们只能另辟蹊径。Java中解决这类问题的一种可行办法，就是采用迭代器模式。

4. 具体地，我们可以基于`Iterator`接口，利用列表实现一个`IteratorTree`类，其中的`preorderIteratorTree()`、`inorderIteratorTree()`、`postorderIteratorTree()`和`levelTraversalIterator()`方法，分别按照前序遍历、后序遍历和层次遍历的次序，创建一个由树中所有节点组成的迭代器，以供后续操作使用。

5. `Java`代码实现：

   ```java
   public class IteratorTree implements Iterator { 
       private List list;//列表 
       private Position nextPosition;//当前（下一个）元素的位置 
   
       //默认构造方法 
       public IteratorTree() { list = null; }
   
       //前序遍历 
       public void elementsPreorderIterator(TreeLinkedList T) { 
           if (null == T) return;//递归基 
           list.insertLast(T);//首先输出当前节点 
           TreeLinkedList subtree = T.getFirstChild();//从当前节点的长子开始 
           while (null != subtree) {//依次对当前节点的各个孩子 
               this.elementsPreorderIterator(subtree);//做前序遍历 
               subtree = subtree.getNextSibling(); 
           }
       }
   
       //中序遍历
       public void elementsInorderIterator(TreeLinkedList T) {
           if (null == T) return;//递归基
           TreeLinkedList subtree = T.getFirstChild();//从当前节点的长子开始
           if (null != subtree) {//若当前节点有孩子
               this.elementsInorderIterator(subtree);//则对其长子做中序遍历
               list.insertLast(T);//然后才输出当前节点
               subtree = subtree.getNextSibling();//从当前节点的下一个兄弟开始
               while (null != subtree) {//依次对当前节点的各个孩子
                   this.elementsInorderIterator(subtree);//做中序遍历
                   subtree = subtree.getNextSibling();
               }
           } else list.insertLast(T);//若当前节点无孩子，则直接输出当前节点
       }
   
       //后序遍历 
       public void elementsPostorderIterator(TreeLinkedList T) { 
           if (null == T) return;//递归基 
           TreeLinkedList subtree = T.getFirstChild();//从当前节点的长子开始 
           while (null != subtree) {//依次对当前节点的各个孩子 
               this.elementsPostorderIterator(subtree);//做后序遍历 
               subtree = subtree.getNextSibling(); 
           }
           list.insertLast(T);//当所有后代都访问过后，最后才访问当前节点 
       }
   
       //层次遍历 
       public void levelTraversalIterator(TreeLinkedList T) { 
           if (null == T) return; 
           Queue_List Q = new Queue_List();//空队 
           Q.enqueue(T);//根节点入队 
           while (!Q.isEmpty()) {//在队列重新变空之前 
               TreeLinkedList tree = (TreeLinkedList) (Q.dequeue());//取出队列首节点 
               list.insertLast(tree);//将新出队的节点接入迭代器中 
               TreeLinkedList subtree = tree.getFirstChild();//从tree的第一个孩子起 
               while (null != subtree) {//依次找出所有孩子，并 
               Q.enqueue(subtree);//将其加至队列中 
               subtree = subtree.getNextSibling(); 
               }
           }
       }
       //检查迭代器中是否还有剩余的元素 
       public boolean hasNext() { return (null != nextPosition); }
       //返回迭代器中的下一元素 
       public Object getNext() throws ExceptionNoSuchElement { 
           if (!hasNext()) throw new ExceptionNoSuchElement("No next position"); 
           Position currentPosition = nextPosition; 
           if (currentPosition == list.last())//若已到达尾元素，则 
           	nextPosition = null;//不再有下一元素 
           else nextPosition = list.getNext(currentPosition);//转向下一元素 
           return currentPosition.getElem(); 
       }
   }
   ```
