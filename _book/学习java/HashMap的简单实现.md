### HashMap的简单使用

```Java
public class TestHashMap {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("a","aaa");
        map.put("b","bbb");
        map.put("c","ccc");
        map.put("d","ddd");
        map.put("e","eee");
        System.out.println(map.get("a"));
    }
}
//运行结果：aaa
```

HashMap存储Key-Value（键值对）数据

### HashMap实现原理

在Java8之前，通过数组+链表实现

Java8中，改为数组+链表+红黑树。当链表长度超过8时，将链表改为红黑树，降低数据查找的时间复杂度

HashMap成员

一个数组，数组里面每个元素都是链表

链表存储对象(Entry)：包括Key，Value，next，hash

### HashMap简单实现

定义Map接口

```Java
public interface MyMap<K,V> {

    public V put(K k,V v);
    public V get(K k);
    public int size();
    interface Entry<K,V>{
        public K getKey();
        public V getValue();
    }
    
}
```

HashMap简单实现

```Java
public class MyHashMap<K, V> implements MyMap<K, V> {

    //数组加链表
    private Entry<K, V>[] table;
    private int size = 0;
    private static int defaultLength = 16;

    public MyHashMap() {
        table = new Entry[defaultLength];
    }

    @Override
    public V put(K k, V v) {
        //1.key进行hash  2.对应数组下标  3.如果是null  4.是否可以存储
        int index = hash(k);
        Entry<K, V> entry = table[index];
        if (null == entry) {
            table[index] = new Entry<>(k, v, null, index);
        } else {
            table[index] = new Entry<>(k, v, entry, index);
        }
        size++;

        return table[index].getValue();
    }

    private int hash(K k) {
        int index = k.hashCode() % (defaultLength - 1);
        return Math.abs(index);
    }

    @Override
    public V get(K k) {

        if (size == 0) {
            return null;
        }
        //hash出来的index  获取到当前Entry对象  判断对象有没有值  比较 在继续查找直到  key  hashcode
        int index = hash(k);

        Entry<K, V> entry = getEntry(k, index);

        return entry == null ? null : entry.getValue();
    }

    private Entry<K, V> getEntry(K k, int index) {
        for (Entry<K, V> entry = table[index]; entry != null; entry = entry.next) {
            if (entry.hash == index && (k == entry.getKey() || k.equals(entry.getKey()))) {
                return entry;
            }
        }
        return null;
    }

    @Override
    public int size() {
        return size;
    }

    static class Entry<K, V> implements  MyMap.Entry<K, V> {

        K k;
        V v;
        MyHashMap.Entry<K, V> next;
        int hash;

        public Entry(K k, V v, MyHashMap.Entry<K, V> next, int hash) {
            this.k = k;
            this.v = v;
            this.next = next;
            this.hash = hash;
        }


        @Override
        public K getKey() {
            return k;
        }

        public V getValue() {
            return v;
        }
    }
}
```

测试

```Java
public class TestMyHashMap {
    public static void main(String[] args) {
        MyMap<String, String> map = new MyHashMap();
        map.put("yll", "yll");
        System.out.println(map.get("yll"));
        System.out.println(map.size());
    }
}
/*运行结果：
yll
1
*/
```

这里只实现了Java8之前的数组加链表形式

### HashMap注意事项

HashMap是线程不安全的，put方法和扩容时都是不安全的

但是get方法是线程安全的





### hash的作用

hashcode通过hash函数计算得到，hashcode就是在hash表中对应的位置

每个对象都有hashcode，将对象的物理地址转换为一个整数，将整数通过hash计算得到hashcode

hashCode方法的存在是为了减少equals方法的调用次数，从而提高程序效率

#### hashCode()与equals()

Java的基类Object中的 equals()方法用于判断两个对象是否相等，hashCode()方法用于计算对象的哈希码。equals()和hashCode()都不是final方法，都可以被重写(overwrite)

#### hashCode方法

hashCode()是一个native方法（一个Native Method就是一个java调用非java代码的接口），而且返回值类型是整形

### HashMap中的hash函数

```Java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

这段代码类似作用是为了增加hashcode的随机性



