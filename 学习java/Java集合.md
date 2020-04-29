## Java集合

集合类存放于Java.util包中，主要有三种set,list,map

1. Collection：Collection是集合List，Set，Queue的最基本的接口
2. Iterator：迭代器，可以通过迭代器遍历集合中的数据
3. Map：是映射表的基础接口



### List

List是有序的Collection，有三个实现类：ArrayList，Vector，LinkList

#### ArrayList（数组）

适合查找遍历，不适合插入删除

#### Vector（数组实现，线程同步）

某一时刻只有一个线程能够写Vector

#### LinkList（链表）

链式结构，适合数据的动态插入和删除



### Set

注重独一无二的性质，值不能重复

对象的相等性本质是对象hashCode值判断，<u>如果想让两个不同对象视为相等，就必须覆盖Object的hashCode方法和equals方法</u>

#### hashSet（Hash表）

按照哈希值来存的所以取数据也是按照哈希值取得。元素的哈希值通过元素的hashcode方法来获取

<u>HashSet首先判断两个元素的哈希值，如果一样，接着回比较equals方法，结构为true则视为同一元素，否则不是</u>

#### TreeSet（二叉树）

Integer和String对象都可以进行默认的TreeSet排序，而定义类的对象是不可以的。自定义类必须实现Comparable接口，并覆写相应CompareTo()函数

#### LinkHashSet（HashSet+LinkedHashMap）

继承于HashSet，基于LinkedHashMap来实现



### Map

#### HashMap（数组+链表+红黑树）

非线程安全，即同一时刻可以有多个线程同时写HashMap，导致数据的不一致性

如果要满足线程安全，可以用Collections的synchronizedMap方法使HashMap具有线程安全的能力，或者用ConcurrentHashMap

大方向上，HashMap是一个数组，数组的每个元素是一个单向链表

在Java8中，链表元素超过8个以后，会将链表转换为红黑树，降低查找的时间复杂度O(logN)







#### CurrentHashMap

线程安全

#### HashTale（线程安全）



#### TreeMap（可排序）



#### LinkHashMap（记录插入顺序）

是HashMap的一个子类



