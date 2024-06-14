
# 二叉堆、斐波那契堆

## 介绍

1. 二叉堆和斐波那契堆都是优先队列的实现方式，它们都可以用于实现诸如Dijkstra的最短路径算法等算法。以下是它们的定义和主要特性：
   - **二叉堆：**二叉堆是一种特殊的堆，它是完全二叉树或者是近似完全二叉树。二叉堆有两种：最大堆和最小堆。最大堆的性质是每个节点都大于或等于其子节点的值，最小堆的性质是每个节点都小于或等于其子节点的值。二叉堆的主要操作（插入、删除、获取最大/最小元素）的时间复杂度都是$O(log n)$。
   - **斐波那契堆**：斐波那契堆是一种优先队列数据结构，它比二叉堆具有**更好的平均情况时间复杂度**。斐波那契堆的主要优点是其删除和插入操作的平摊时间复杂度可以达到$O(1)$。但是，斐波那契堆的实现比二叉堆复杂得多。



## 二叉堆的Java实现（最小堆）

```java
import java.util.Arrays;

public class BinaryHeap {
    private static final int DEFAULT_CAPACITY = 10;
    private int size = 0;
    private int[] items = new int[DEFAULT_CAPACITY];

    private void ensureExtraCapacity() {
        if (size == items.length) {
            items = Arrays.copyOf(items, items.length * 2);
        }
    }

    public int peek() {
        if (size == 0) throw new IllegalStateException();
        return items[0];
    }

    public int poll() {
        if (size == 0) throw new IllegalStateException();
        int item = items[0];
        items[0] = items[size - 1];
        size--;
        heapifyDown();
        return item;
    }

    public void add(int item) {
        ensureExtraCapacity();
        items[size] = item;
        size++;
        heapifyUp();
    }

    public void heapifyDown() {
        int index = 0;
        while (hasLeftChild(index)) {
            int smallerChildIndex = getLeftChildIndex(index);
            if (hasRightChild(index) && rightChild(index) < leftChild(index)) {
                smallerChildIndex = getRightChildIndex(index);
            }

            if (items[index] < items[smallerChildIndex]) {
                break;
            } else {
                swap(index, smallerChildIndex);
            }
            index = smallerChildIndex;
        }
    }

    public void heapifyUp() {
        int index = size - 1;
        while (hasParent(index) && parent(index) > items[index]) {
            swap(getParentIndex(index), index);
            index = getParentIndex(index);
        }
    }

    private int getLeftChildIndex(int parentIndex) { return 2 * parentIndex + 1; }
    private int getRightChildIndex(int parentIndex) { return 2 * parentIndex + 2; }
    private int getParentIndex(int childIndex) { return (childIndex - 1) / 2; }

    private boolean hasLeftChild(int index) { return getLeftChildIndex(index) < size; }
    private boolean hasRightChild(int index) { return getRightChildIndex(index) < size; }
    private boolean hasParent(int index) { return getParentIndex(index) >= 0; }

    private int leftChild(int index) { return items[getLeftChildIndex(index)]; }
    private int rightChild(int index) { return items[getRightChildIndex(index)]; }
    private int parent(int index) { return items[getParentIndex(index)]; }

    private void swap(int indexOne, int indexTwo) {
        int temp = items[indexOne];
        items[indexOne] = items[indexTwo];
        items[indexTwo] = temp;
    }
}
```

## 二叉堆的C++实现（最小堆）

```cpp
#include <vector>
#include <stdexcept>

class BinaryHeap {
private:
    std::vector<int> heap;

    void heapify_up(int index) {
        while (index > 0 && heap[parent(index)] > heap[index]) {
            std::swap(heap[parent(index)], heap[index]);
            index = parent(index);
        }
    }

    void heapify_down(int index) {
        int child_index = left_child(index);
        if (child_index + 1 < heap.size() && heap[child_index] > heap[child_index + 1]) {
            child_index++;
        }

        if (child_index < heap.size() && heap[child_index] < heap[index]) {
            std::swap(heap[child_index], heap[index]);
            heapify_down(child_index);
        }
    }

    int parent(int index) { return (index - 1) / 2; }
    int left_child(int index) { return 2 * index + 1; }
    int right_child(int index) { return 2 * index + 2; }

public:
    void push(int key) {
        heap.push_back(key);
        heapify_up(heap.size() - 1);
    }

    void pop() {
        if (heap.size() == 0) {
            throw std::out_of_range("Heap is empty");
        }

        heap[0] = heap.back();
        heap.pop_back();

        heapify_down(0);
    }

    int top() {
        if (heap.size() == 0) {
            throw std::out_of_range("Heap is empty");
        }

        return heap[0];
    }
};
```

## 斐波那契堆的实现原理

1. 斐波那契堆是一种**非常高效**的优先队列数据结构，它的**主要优点**是**许多操作的平摊时间复杂度可以达到$O(1)$**。
2. 斐波那契堆的实现比二叉堆复杂得多，主要是因为它使用了一种称为"**级联剪切**"的技术来保持其高效性。斐波那契堆的基本结构是**一组**最小堆有序树的**集合**，这些树都满足斐波那契堆的性质。在斐波那契堆中，每个节点包含一个**关键字**（key）和**两个指针**，**一个指向它的子节点，一个指向它的兄弟节点**。每个节点还有一个布尔值"标记"（mark），用于在节点的子节点被删除时进行标记。
3. 斐波那契堆的主要操作包括插入、查找最小值、删除最小值和合并两个堆。以下是这些操作的基本原理：
   1. 插入：新节点被添加到根列表中，时间复杂度为$O(1)$。
   2. 查找最小值：由于所有的根节点都在根列表中，所以查找最小值只需要**遍历根列表**，时间复杂度为$O(1)$。
   3. 删除最小值：首先**找到最小值节点**，然后将其**所有子节点添加到根列表**中，然后删除最小值节点。最后，为了保持斐波那契堆的性质，需要进行一次"合并"操作，**将度数相同的树合并在一起**。这个操作的平摊时间复杂度为$O(\log n)$。
   4. 合并：将两个斐波那契堆的根列表合并在一起，然后进行一次"合并"操作，将度数相同的树合并在一起。这个操作的时间复杂度为$O(1)$。
4. 斐波那契堆的关键操作是**"级联剪切"**，当一个节点失去一个子节点时，如果该节点**未被标记**，则将其标记；如果已被**标记**，则将其从其父节点中剪切出来，添加到根列表中，并递归地对其父节点进行同样的操作。这个操作保证了斐波那契堆的高效性。
