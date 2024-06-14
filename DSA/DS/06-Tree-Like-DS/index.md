# 07-BST Applications

这四种树分别是Segment Tree（线段树）、Interval Tree（间隔树）、Range Tree（范围树）、Binary Indexed Tree（二叉索引树）。
>严格上来说，BIT不算树，事实上是将根据数字的二进制表示来对数组中的元素进行逻辑上的分层存储。

## 使用场景

- **Segment tree** stores intervals, and optimized for "**which of these intervals contains a given point**" queries.

- **Interval tree** stores intervals as well, but optimized for "**which of these intervals overlap with a given interval**" queries. It can also be used for point queries - **similar to segment tree**.

- **Range tree** stores points, and optimized for "**which points fall within a given interval**" queries.

- **Binary indexed tree** stores items-count per index, and optimized for "**how many items are there between index m and n**" queries.

  > k is the number of reported results.
## 时间复杂度
One Dimension

|   Operation   |  Segment   |  Interval  |   Range    |  Indexed   |
| :-----------: | :--------: | :--------: | :--------: | :--------: |
| Preprocessing | $n \log n$ | $n \log n$ | $n \log n$ | $n \log n$ |
|     Query     | $k+\log n$ | $k+\log n$ | $k+\log n$ |  $\log n$  |
|     Space     | $n \log n$ |    $n$     |    $n$     |    $n$     |
| Insert/Delete |  $\log n$  |  $\log n$  |  $\log n$  |  $\log n$  |

Higher Dimensions ($d>1$)

|   Operation   |      Segment      |    Interval    |       Range       |    Indexed    |
| :-----------: | :---------------: | :------------: | :---------------: | :-----------: |
| Preprocessing |   $n(\log n)^d$   |   $n \log n$   |   $n(\log n)^d$   | $n(\log n)^d$ |
|     Query     |  $k+(\log n)^d$   | $k+(\log n)^d$ |  $k+(\log n)^d$   | $(\log n)^d$  |
|     Space     | $n(\log n)^{d-1}$ |   $n \log n$   | $n(\log n)^{d-1}$ | $n(\log n)^d$ |

注：以上内容来自[Stack Overflow](Stackoverflow.com)并经过了本人的认真阅读。在对比了国内外的大部分参考资料后， 决定以国外的英文讲解为主，中文的内容由于翻译等的错误和不妥，将不会排至首位使用，除非在经过仔细核对后。

## 参考资料

1. [algorithm - Are interval, segment, fenwick trees the same? - Stack Overflow](https://stackoverflow.com/questions/2795989/are-interval-segment-fenwick-trees-the-same)
2. [algorithm - What are the differences between segment trees, interval trees, binary indexed trees and range trees? - Stack Overflow](https://stackoverflow.com/questions/17466218/what-are-the-differences-between-segment-trees-interval-trees-binary-indexed-t)
3. [Interval tree - Wikipedia](https://en.wikipedia.org/wiki/Interval_tree)
4. [Segment tree - Wikipedia](https://en.wikipedia.org/wiki/Segment_tree)
5. [Range tree - Wikipedia](https://en.wikipedia.org/wiki/Range_tree)
6. [Fenwick tree - Wikipedia](https://en.wikipedia.org/wiki/Fenwick_tree)
7. [Multi-dimensional Dynamic Ranged Tree Based On An AVL Tree](https://www.cs.unb.ca/tech-reports/documents/TR95_100.pdf)
8. [树状数组 - OI Wiki (oi-wiki.org)](https://oi-wiki.org/ds/fenwick/)
9. [线段树 - OI Wiki (oi-wiki.org)](https://oi-wiki.org/ds/seg/)
