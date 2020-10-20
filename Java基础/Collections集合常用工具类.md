# Collections集合常用工具类

Collections工具类常用方法：

* 排序
* 查找、替换

## 排序

~~~java
void reverse(List list);	//	反转
void shuffle(List list);	//	随机排序
void sort(List list);	//	按自然排序的升序排序
void sort(List list, Comparator c);	//	自定义排序规则
void swap(List list, int a, int b);	//	交换两个索引位置的元素
void rotate(List list, int distance);	//	当distance为正数时，将list后distance个元素整体移到前面；反之同理
~~~

## 查找&替换

~~~java
int binarySearch(List list, Object key);	//	对list集合进行二分查找，返回查询元素所在位置的索引；注：list集合必须是有序的
int max(Collection coll);	//	按照自然排序，返回最大的元素
int min(Collection coll);	//	同上
void fill(List list, Object o);	//	用指定元素o来替换list集合中所有元素
int frequency(Collection c, Object o);	//	统计并返回元素o出现的次数
int indexOfSublist(List list, List traget);	//	统计target在list中第一次出现时的索引
boolean replaceAll(List list, Object oldVal, Object newVal);	//	用新元素替换旧元素
~~~

## 同步控制

由于`HashSet`, `TreeSet`, `ArrayList`, `LinkedList`, `HashMap`,`TreeMap`都是线程不安全的，因此`Collections`提供了多个静态方法`synchronizedXxx()`来将它们包装成为线程同步的集合

~~~java
synchronizedCollection(Collection<T>  c) //返回指定 collection 支持的同步（线程安全的）collection。
synchronizedList(List<T> list)//返回指定列表支持的同步（线程安全的）List。
synchronizedMap(Map<K,V> m) //返回由指定映射支持的同步（线程安全的）Map。
synchronizedSet(Set<T> s) //返回指定 set 支持的同步（线程安全的）set。
~~~

**这些方法效率很低，一般不建议使用，如若需要线程安全的集合，则考虑使用JUC包下的并发集合**

## 其他

### 快速失败(fail-fast)

**快速失败(fail-fast)**是Java集合中的一种错误检测机制。**在使用迭代器进行集合遍历时，在多线程下操作非安全失败的集合时旧可能处罚快速失败机制，会抛出`ConcurenctModificationException`异常。在单线程下，若在集合遍历的过程对对象进行了修改，也会触发快速失败机制**

> 注：增强for循环也是使用迭代器进行遍历

*若线程A正在对集合进行遍历，此时线程B对集合进行修改操作，或者线程A在集合遍历的过程中对集合进行修改操作，都会导致线程A抛出`ConcurrentModificationException`异常*

### 原因

每当迭代器使用`hashNext()/next()`遍历下一个元素之前，都会检查`modCount`变量是否为`exceptionModCount`值，如果为`true`，则返回遍历；若不是则抛出异常，遍历中止。如果在集合遍历的过程中对集合中元素进行修改，会改变`modCount`值，因此会抛出异常，故而在遍历过程中不可进行修改操作。

因此在集合遍历的过程中，最好使用迭代器的方法，原因是`Iterator`的方法修改集合时，会修改到`exceptionModCount`的值，所以不会抛出异常。

~~~java
final void checkForComodification() {
    if (modCount != exceptedModCount) {
        throw new ConcurrentModificationException();
    }
}
~~~

### 阿里巴巴开发手册中的相关规定

- 不要再`foreach`循环中进行元素的`remove/add`操作，删除元素应使用`Iterator`的方式，如果并发操作，需要对`Iterator`对象加锁

### 安全失败(fail-safe)

采用了安全失败机制的集合，再遍历时，不会直接在集合原来的内容上访问，而是先复制原有集合的内容，在复制的内容上进行操作，所以在修改操作的过程中，不会被迭代器检查到，因此不会抛出`ConcurrentModificationException`异常。

### Java Array to List

#### 简介

`Arrays.asList()`在平时很常用，可以使用它将数组转换为List集合

~~~java
List<String> myList = Arrays.asList("A", "B", "C");
~~~

> 注：`Arrays.asList()`将数组转换为集合之后，底层其实还是数组

**`Arrays.asList()`是泛型方法，传入的对象必须是对象数组，而不能是基本类型**

~~~java
int[] myArray = { 1, 2, 3 };
List myList = Arrays.asList(myArray);
System.out.println(myList.size());//1
System.out.println(myList.get(0));//数组地址值
System.out.println(myList.get(1));//报错：ArrayIndexOutOfBoundsException
int [] array=(int[]) myList.get(0);
System.out.println(array[0]);//1
~~~

*当传入一个原生数据类型数组时，`Arrays.asList()` 的真正得到的参数就不是数组中的元素，而是数组对象本身！*

**使用包装类型数组，可以解决这个问题**

~~~java
Integer[] myArray = {1, 2, 3};
~~~

