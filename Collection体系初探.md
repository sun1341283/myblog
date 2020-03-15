# Collection 集合
  代表一组元素，不同的集合具有不同的性质，如是否重复、是否有序。它实现了Iterable接口，用于遍历。除了add(),remove(),contain(),toArray()等常规方法，
还有几个方法值得注意：
```
删除不存在于c中的元素，即取与c的交集。可能有坑的场景类似于HashMap和它的KeySet，它们的类似retainAll的操作会影响对方的值（原因没有搞清楚）
boolean retainAll(Collection<?> c);
```

```Java
自带了迭代器，传入一个函数式接口Predicate即可按条件删除
default boolean removeIf(Predicate<? super E> filter) {
        Objects.requireNonNull(filter);
        boolean removed = false;
        final Iterator<E> each = iterator();
        while (each.hasNext()) {
            if (filter.test(each.next())) {
                each.remove();
                removed = true;
            }
        }
        return removed;
    }
```    
```
没有搞太懂，先留着
default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }
default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }
default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
```
## 集合的几个典型实现类：
### AbstractList
  有序，可重复
#### ArrayList
底层是数组。

默认为空，添加一个元素时发生第一次扩容，容量为10；以后每次扩容增加一半。

删除时如果是删除最后一个，则直接将其置为null，
否则需要调用系统native方法复制部分数组。扩容时也需要复制数组，因此ArrayList增删比较慢。

查找和替换时直接对index处的元素进行操作，因此查改比较快。

#### LinkedList
底层是双向链表
```
单个节点保存前后两个节点，整个链表中保存首尾节点（首的prev为空，尾的next为空）
private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```
add(E)：将E构造为一个新节点，将原链表中的尾存入它的prev，将它本身存入尾的next，它变成尾。如果是插入index处，则需要先遍历找到节点，再更新prev、next

get(index): 遍历：虽然没有记住每一个节点的位置，但每个节点记住了前后的节点，只要从前往后数或者从后往前数一定的次数就能找到index对应的节点
（index不过半则从前往后数index次，否则从后往前数size-index次）

remove(index):先遍历找到该节点，再将其解列（更换前后节点的prev和next），相应的size也减一

set(index):先遍历找到该节点，再将item更换

##### 它的操作普遍需要遍历，对比ArrayList改查较慢，增删在大多数情况下都较前者快。

### AbstractSet
不可重复
#### HashSet
本质上是一个HashMap
add(o)： 向HashMap中存入key为o，value为一个全局变量new Object()，其他特性也跟HashMap一样
#### LinkedHashSet(LinkedHashMap)
继承自HashMap，增加了一个字段：accessOrder。当该集合创建时，accessOrder设为false，表示迭代时按照存入顺序；当该值设为true时，每次获取一个元素后
都将其放到双向链表的尾部（LinkedHashSet的节点保存了前后节点，即链表），遍历时则按照访问顺序。

### 还有很多等到看完后续博大的课再记录吧嘿嘿
