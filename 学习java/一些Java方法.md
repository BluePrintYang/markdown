## 数组复制

```Java
int[] array = {1,5,7,4,8};
int[] b = Arrays.copyOfRange(array,1,9);
int[] cc = new int[array.length];
System.arraycopy(array,0,cc,2,3);
System.out.println(Arrays.toString(cc));
System.out.println(Arrays.toString(b));
//运行结果
//[0, 0, 1, 5, 7]
//[5, 7, 4, 8, 0, 0, 0, 0]
```

### arraycopy() 方法

> 5个参数，原数组，原数组起始索引，目标数组，目标数组起始索引，长度

位于 java.lang.System 类中，其语法形式如下：

```Java
System.arraycopy(dataType[] srcArray,int srcIndex,int destArray,int destIndex,int length)
```

其中，srcArray 表示原数组；srcIndex 表示原数组中的起始索引；destArray 表示目标数组；destIndex 表示目标数组中的起始索引；length 表示要复制的数组长度。

使用此方法复制数组时，length+srcIndex 必须小于等于 srcArray.length，同时 length+destIndex 必须小于等于 destArray.length。



###  CopyOfRange() 方法

> 根据起始索引和终止索引复制数组，传入3个参数

是另一种复制数组的方法，其语法形式如下：

```Java
Arrays.copyOfRange(dataType[] srcArray,int startIndex,int endIndex)
```

其中：

- srcArray 表示原数组。
- startIndex 表示开始复制的起始索引，目标数组中将包含起始索引对应的元素，另外，startIndex 必须在 0 到 srcArray.length 之间。
- endIndex 表示终止索引，目标数组中将不包含终止索引对应的元素，endIndex 必须大于等于 startIndex，可以大于 srcArray.length，如果大于 srcArray.length，则目标数组中使用默认值填充。



### Arrays.copyOf方法

> 传入2或3个参数，原数组，长度，数组类型

original - 要复制的数组 
newLength - 要返回的副本的长度 
newType - 要返回的副本的类型

其实Arrays.copyOf底层也是调用System.arraycopy实现的源码如下：

```Java
//基本数据类型（其他类似byte，short···）
public static int[] copyOf(int[] original, int newLength) {
        int[] copy = new int[newLength];
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
```

