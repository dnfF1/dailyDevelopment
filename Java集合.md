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
- ArrayList实现了RandomAccess接口，for遍历走的时索引
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
- HashMap：1.8前，数组+链表结构；1.8，数组+链表+红黑树结构
- HashTable：数组+链表结构【线程安全】
- TreeMap：红黑树
- ConcurrentHashMap：数组+链表【线程安全】
## ConcurrentHashMap
