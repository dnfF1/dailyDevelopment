# 集合的定义
- 存放对象引用的容器，长度可变
# 集合的类型
- list
- set
- map
# 常用集合类
## Collection接口
### List接口
- ArrayList：数组结构，扩容增加0.5倍
- LinkedList：双向链表结构
- Vector：数组结构【线程安全】，扩容增加1倍
### Set接口
- HashSet：哈希表结构，基于HashMap，值存储在HashMap的key，HashMap的value都是static final Object PRESENT = new Object()
- LinkedHashSet：哈希表+链表结构
- TreeSet：红黑树
- 只支持iterator迭代
### 如何使集合不被修改
- Collections.unmodifiableCollection(Collection c)创建一个只读集合
```java
List<Integer> list = new ArrayList<>();
Collection<Integer> clist = Collections.unmodifiableCollection(list);
```
### Iterator
- iter.hasNext()
- iter.next()，会调用checkForComodification校验modCount是否被修改，被修改则会抛出ConcurrentModificationException
### 边遍历边移除元素
- 唯一正确方式：Iterator.remove()
### 遍历集合的方式
- for
- foreach
- iterator
- ArrayList实现了RandomAccess接口，for遍历走的是索引
### 数组和List
- 数组->List：Arrays.asList(arr)
- List->数组：list.toArray()
### 如何使集合线程安全
- Collections.synchronizedList(Collection c)转换成线程安全的容器
```java
List<Integer> list = new ArrayList<>();
Collection c = Collections.synchronizedList(list);
```
### transient
- 不允许序列化
- 减小序列化之后的文件
## Map接口
- HashMap：1.8前，数组+链表结构；1.8，数组+链表+红黑树结构，默认大小16，扩容*2
- HashTable：数组+链表结构【线程安全】不允许有null值，默认大小11，扩容到2*n+1
- TreeMap：红黑树
- ConcurrentHashMap：数组+链表【线程安全】
## HashMap
- 允许key和value为null
- 1.8前，数组+链表
- 1.8，数组+链表+红黑树，链表节点数据超过8，转化成红黑树，查询复杂度O(n)->O(logn)
### jdk1.7，多线程下，扩容会导致什么问题？为什么？
- 扩容，数组长度*2
- 链表采用头插法进行转移
- 扩容后，重新计算每个元素的hash值
- resize扩容完，transfer原数据迁移到新的Table后，才更新阈值threshold
- 多个线程同时进行扩容，thread1在执行Entry<> next = e->next()后挂起，thraed2执行完毕，thread1再执行，导致最后一个节点多了一次指向，从而导致最后两个节点形成环
### jdk1.8，多线程下，会导致什么问题？
- 扩容，数组长度*2
- 链表采用尾插法
- 扩容后，数据要么在原位置，要么在原位置+增加的数组大小的位置
- resize扩容及迁移
- 多个线程同时插入，hash值一样会导致数据覆盖
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/1717355218a84ee7~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)
### 红黑树
- 有颜色的二叉平衡查找树
- 节点要么黑色，要么红色
- 根节点是黑色
- 节点是红色，子节点必须是黑色
- 每个节点到叶子节点【空节点】所经过的黑色节点数是一样的
### HashMap扩容，为什么都是2的N次幂
- hash让hashCode的高16位和低16位异或^
- 只有数组长度是2的幂方时，hash&(length-1) 和 hash%length 等价，执行效率高
## ConcurrentHashMap
### jdk1.7
- Segment + HashEntry
- 默认16个Segment
- 每个Segment是可重入锁ReentrantLock
- 对Segment数组的数据进行修改时，必须先获得对应的Segment锁
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735521de4886d~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)
### jdk1.8
- Node + synchronized + CAS
- synchronized只锁当前链表或红黑树的首节点
- hash不冲突就没有并发
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735522b19186a~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.png)