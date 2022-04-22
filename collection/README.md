## 概念

---

- **集合概念**

集合是对象的容器，实现了对对象常用的操作，类似数组的功能。

- **集合和数组的区别**

  1. 数组长度固定，集合长度不固定
  2. 数组可以存储基本类型和引用类型，集合只能存储引用类型

- **位置**

  java.util.*;

  <br/>

## Collection体系

---

![collection.png](https://s1.imagehub.cc/images/2022/03/03/collection.png)

<br/>

## Collection父接口

- **特点:**

  代表一组任意类型的对象，无序、无下标、不能重复。

- **方法:**

  `boolean add(Object obj) //添加一个对象。`

  `boolean addAll(Collection c) //讲一个集合中的所有对象添加到此集合中。`

  `void clear() //清空此集合中的所有对象。`

  `boolean contains(Object o) //检查此集合中是否包含o对象。`

  `boolean equals(Object o) //比较此集合是否与指定对象相等。`

  `boolean isEmpty() //判断此集合是否为空。`

  `boolean remove(Object o) //在此集合中移除o对象。`

  `int size() //返回此集合中的元素个数。`

  `Object[] toArray() //姜此集合转换成数组。`

  

```java
/**
 * Collection接口的使用 (保存字符串）
 * 1. 添加元素
 * 2. 删除元素
 * 3. 遍历元素
 * 4. 判断
 */
public class CollectionTest1 {
    public static void main(String[] args) {
        // 创建集合
        Collection collection = new ArrayList();

//      1. 添加元素
        collection.add("apple");
        collection.add("watermelon");
        collection.add("pineapple");
        System.out.println("ElementNum: " + collection.size());
        System.out.println(collection);

//      2. 删除元素
        collection.remove("pineapple");
        System.out.println("After delete: " + collection);

//      3. 遍历元素
        // for-each
        System.out.print("for-each edition: ");
         for(Object o : collection){
             System.out.println(o);
         }

         // iterator
        /*
        1. hasNext() 是否有下个元素
        2. next() 获取下个元素
        3. remove() 删除当前元素
         */
        System.out.print("iterator edition: ");
        Iterator it = collection.iterator();
        while (it.hasNext()) {
            String s = (String) it.next();
            System.out.println(s);
        }

//      4. 判断
        System.out.println(collection.contains("watermelon"));
        System.out.println(collection.isEmpty());
    }
}
```

```java
/**
 * Collection接口的使用 (保存对象信息）
 */
public class CollectionTest2 {
    public static void main(String[] args) {
        // 创建Collection对象
        Collection collection = new ArrayList();

        // 创建学生对象
        Student s1 = new Student("a", 20);
        Student s2 = new Student("b", 21);
        Student s3 = new Student("c", 22);

        // 添加数据
        collection.add(s1);
        collection.add(s2);
        collection.add(s3);
        System.out.println("ElementNum: " + collection.size());
        System.out.println(collection.toString());

        // 删除
        collection.remove(s1);
//      collection.clear();
        System.out.println("After remove" + collection.toString() + "\nElementNum: " + collection.size());

        // 遍历
        // for-each
        System.out.println("for-each edition: ");
        for (Object s : collection){
            System.out.println(s.toString());
        }

        // iterator
        System.out.println("iterator edition: ");
        Iterator it = collection.iterator();
        while (it.hasNext()){
            System.out.println(it.next().toString());
        }

        // 判断
        System.out.println(collection.contains(s2));
        System.out.println(collection.isEmpty());
    }
}
```

```java
/**
 * 学生类
 */
public class Student {
    private String name;
    private int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student [name = " + name + ", age = " + age + "]";
    }
}
```

<br/>

## Collection子接口

### **List集合**

- **特点:** 有序、有下标、元素可以重复。

- **方法:** 

  `void add(int index,Object o) //在index位置插入对象o。`

  `boolean addAll(index,Collection c) //将一个集合中的元素添加到此集合中的index位置。`

  `Object get(int index) //返回集合中指定位置的元素。`

  `List subList(int fromIndex,int toIndex) //返回fromIndex和toIndex之间的元素。`

```java
/*
  List子接口的使用（一）
  特点：1.有序有下标 2.可以重复
  操作:
  1.添加元素
  2.删除元素
  3.遍历元素
  4.判断
  5.获取位置
 */
public class ListTest1 {
    public static void main(String[] args) {
        // 创建集合
        List list = new ArrayList();

        //1. 添加元素
        list.add("Apple");
        list.add(0, "Huawei");
        list.add("Xiaomi");
        list.add("Samsung");
        System.out.println("ElementNum: " + list.size());
        System.out.println(list.toString());

        // 2. 删除元素
        list.remove("Samsung");
//      list.remove(0);
        System.out.println("After remove: " + list.toString());

        // 3. 遍历元素
        // for edition
        System.out.println("for edition: ");
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
        // for-each edition
        System.out.println("for-each edition: ");
        for (Object o : list) {
            System.out.println(o.toString());
        }
        // iterator edition
        System.out.println("iterator edition: ");
        Iterator iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next().toString());
        }
        // ListIterator edition
        System.out.println("ListIterator edition: ");
        ListIterator listIterator = list.listIterator();
        // order
        System.out.println("Order: ");
        while (listIterator.hasNext()) {
            System.out.println(listIterator.next());
        }
        // reverse order
        System.out.println("Reverse order:");
        while(listIterator.hasPrevious()) {
            System.out.println(listIterator.previous());
        }

        // 判断
        System.out.println("The list contains Apple is: " + list.contains("Apple"));
        System.out.println("The list is empty is: " + list.isEmpty());

        // 获取位置
        System.out.println("The position of Huawei is: " + list.indexOf("Huawei"));
    }
```

```java
/*
    List子接口的使用(二) 基本类型自动装箱
 */
public class ListTest2 {
    public static void main(String[] args) {
        // 创建集合
        List list = new ArrayList();

        // 添加基本类型数据
        // 自动装箱AutoBoxing
        list.add(10);
        list.add(20);
        list.add(30);
        list.add(40);
        list.add(50);

        System.out.println("ElementNum: " + list.size());
        System.out.println(list.toString());

        // 删除
//        list.remove(0);
//        list.remove((Object) 10);
        list.remove(new Integer(10));
        System.out.println("After remove: " + list.toString());

        // subList()方法: 返回从fromIndex到toIndex之间的元素,含头不含尾
        System.out.println("subList(1, 3): " + list.subList(1, 3));
    }
```

<br/>

### **List实现类**

#### **ArrayList [重点]**

- 数组结构实现，查询块、增删慢；
- JDK1.2版本，运行效率快、线程不安全。

```java
/*
    ArrayList的使用
    存储结构: 数组
    特点: 查找遍历快, 增删速度慢
 */
public class ArrayListTest1 {
    public static void main(String[] args) {
        // 创建集合
        ArrayList arrayList = new ArrayList();

        // 添加元素
        Student s1 = new Student("a", 20);
        Student s2 = new Student("b", 21);
        Student s3 = new Student("c", 22);
        Student s4 = new Student("d", 23);
        Student s5 = new Student("e", 24);
        arrayList.add(s1);
        arrayList.add(s2);
        arrayList.add(s3);
        arrayList.add(s4);
        arrayList.add(s5);
        System.out.println(arrayList.toString() + "\nElementNum: " + arrayList.size());

        // 删除
        arrayList.remove(s4);
        // 该语句本是比较是否否同一个对象
        // 要使该语句能删除ArrayList中的元素,就要重写Student类中的equals()方法,使其比较该对象的属性是否相同
        arrayList.remove(new Student("e", 24));
        System.out.println("After remove: " + arrayList.toString() + "\nElementNum: " + arrayList.size());

        // 遍历[重点]
        // iterator edition
        System.out.println("iterator edition: ");
        Iterator iterator = arrayList.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next().toString());
        }
        // ListIterator edition
        System.out.println("ListIterator order edition: ");
        ListIterator listiterator = arrayList.listIterator();
        while (listiterator.hasNext()) {
            System.out.println(listiterator.next().toString());
        }
        System.out.println("ListIterator Reverse order edition: ");
        while (listiterator.hasPrevious()) {
            System.out.println(listiterator.previous().toString());
        }

        // 判断
        System.out.println("The arrayList contains s1 is: " + arrayList.contains(s1));
        // 重写Student类中equals()方法
        System.out.println("The arrayList contains s2 is: " + arrayList.contains(new Student("b", 21)));
        System.out.println("The arrayList is empty is: " + arrayList.isEmpty());

        // 查找
        System.out.println("The index of s1 is: " + arrayList.indexOf(s1));
        System.out.println("The index of s2 is: " + arrayList.indexOf(new Student("b", 21)));
        
    }
```

```java
// 重写Student类equals()方法
@Override
public boolean equals(Object obj) {
    // 1. 是否为同一对象
    if(this == obj){
        return true;
    }
    // 2. 是否为空
    if(obj == null) {
        return false;
    }
    // 3. 是否是Student类型
    if(obj instanceof Student){
        Student s = (Student) obj;
        // 4. 属性是否相同
        if(this.name.equals(s.getName()) && this.age == s.getAge()){
            return true;
        }
    }
    // 5. 不满足返回false
    return false;
}
```

<br/>

#### **ArrayList源码分析**

- 默认容量大小：`private static final int DEFAULT_CAPACITY = 10;`

- 存放元素的数组：`transient Object[] elementData;`

- 实际元素个数：`private int size;`

- 创建对象时调用的无参构造函数：

  ```java
  //这是一个空的数组
  private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
  public ArrayList() {
      this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
  }
  ```

  当创建一个无参ArrayList时，数组容量为 0 ，size也为 0 .

  ```java
  public boolean add(E e) {
      ensureCapacityInternal(size + 1);  // Increments modCount!!
      elementData[size++] = e;
      return true;
  }
  ```

  当向ArrayList中使用`add(E e)`方法添加元素时，会调用

  `ensureCapacityInternal()`方法。

  ```java
  private void ensureCapacityInternal(int minCapacity) {
      ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
  }
  ```

  在`ensureCapacityInternal()`方法中，会将`calculateCapacity(elementData, minCapacity)`的值作为参数传入`ensureExplicitCapacity()`方法

  ```java
  private static int calculateCapacity(Object[] elementData, int minCapacity) {
      if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
          return Math.max(DEFAULT_CAPACITY, minCapacity);
      }
      return minCapacity;
  }
  ```

  在`calculateCapacity()`方法中，如果当前数组中元素个数为 0，则会将该数组的容量设值为`DEFAULT_CAPACITY` 和 `minCapacit`中更大的一个。即当添加第一个元素时，会将数组容量设置为默认容量大小10。否则数组容量大小就等于传入的`minCapatity`。再返回到上层方法`ensureExplicitCapacity()`中。

  ```java
  private void ensureExplicitCapacity(int minCapacity) {
      modCount++;
      // overflow-conscious code
      if (minCapacity - elementData.length > 0)
          grow(minCapacity);
  }
  ```

  在该方法中，当`minCapatity - elementData。length`的值 >0 时，调用`grow()`方法。

  ```java
  private void grow(int minCapacity) {
      // overflow-conscious code
      int oldCapacity = elementData.length;
      int newCapacity = oldCapacity + (oldCapacity >> 1);
      if (newCapacity - minCapacity < 0)
          newCapacity = minCapacity;
      if (newCapacity - MAX_ARRAY_SIZE > 0)
          newCapacity = hugeCapacity(minCapacity);
      // minCapacity is usually close to size, so this is a win:
      elementData = Arrays.copyOf(elementData, newCapacity);
  }
  ```

  该方法将当前数组长度`elementData.length`的值赋给变量`oldCapacity`，再声明一个`newCapcity`变量，其值为`oldCapacity + (oldCapacity >> 1)`(即`oldCapcity`的值的1/2)。

  当`newCapcity`的值小于`minCapcity`的值时，让`newCapacity = minCapacity`。

  然后为`elementData`数组赋予新的长度而不改变其中的元素。

  最后返回`add(E e)`方法中，将e存储在`elementData[size]`位置，再使`size++` 。

<br/>

#### **Vector**

- 数组结构实现，查询快、增删慢；
- JDK1.0版本，运行效率慢、线程安全。

```java
/*
    Vector的使用
    存储结构: 数组
    特点: 查找遍历快, 增删速度慢, 线程安全
 */
public class VectorTest1 {
    public static void main(String[] args) {
        // 创建集合
        Vector vector = new Vector();

        // 添加元素
        vector.add("apple");
        vector.add("banana");
        vector.add("pear");
        vector.add("peach");
        vector.add("watermelon");

        // 删除元素
        vector.remove("apple");
        vector.remove(3);

        // 遍历
        // Enumeration edition
        System.out.println("Enumeration edition: ");
        Enumeration enumeration = vector.elements();
        while (enumeration.hasMoreElements()) {
            System.out.println(enumeration.nextElement().toString());
        }

        // 判断
        System.out.println("The vector contains pear is: " + vector.contains("pear"));
        System.out.println("The vector is empty is: " + vector.isEmpty());
    }
```

<br/>

#### **LinkedList**

- 链表结构实现，增删快，查询慢。

```java
/*
    LinkedList的使用
    存储结构: 双向链表
    查找遍历慢, 增删速度快
 */
public class LinkedListTest1 {
    public static void main(String[] args) {
        // 创建集合
        LinkedList linkedList = new LinkedList();

        // 添加元素
        Student s1 = new Student("a", 20);
        Student s2 = new Student("b", 21);
        Student s3 = new Student("c", 22);
        Student s4 = new Student("d", 23);
        Student s5 = new Student("e", 24);
        linkedList.add(s1);
        linkedList.add(s2);
        linkedList.add(s3);
        linkedList.add(s4);
        linkedList.add(s5);
        System.out.println(linkedList + "\nElementNum: " + linkedList.size());

        // 删除
        linkedList.remove(s4);
        linkedList.remove(new Student("e", 24));
        System.out.println("After remove: " + linkedList + "\nElementNum: " + linkedList.size());

        // 遍历[重点]
        // for edition
        System.out.println("for edition: ");
        for (int i = 0; i < linkedList.size(); i++) {
            System.out.println(linkedList.get(i));
        }
        // for-each edition
        System.out.println("for-each edition: ");
        for (Object o : linkedList) {
            Student s = (Student) o;
            System.out.println(s.toString());
        }
        // iterator edition
        System.out.println("iterator edition: ");
        Iterator iterator = linkedList.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next().toString());
        }
        // ListIterator edition
        System.out.println("ListIterator order edition: ");
        ListIterator listiterator = linkedList.listIterator();
        while (listiterator.hasNext()) {
            System.out.println(listiterator.next().toString());
        }
        System.out.println("ListIterator Reverse order edition: ");
        while (listiterator.hasPrevious()) {
            System.out.println(listiterator.previous().toString());
        }

        // 判断
        System.out.println("The linkedList contains s1 is: " + linkedList.contains(s1));
        // 重写Student类中equals()方法
        System.out.println("The linkedList contains s2 is: " + linkedList.contains(new Student("b", 21)));
        System.out.println("The linkedList is empty is: " + linkedList.isEmpty());

        // 查找
        System.out.println("The index of s1 is: " + linkedList.indexOf(s1));
        System.out.println("The index of s2 is: " + linkedList.indexOf(new Student("b", 21)));


    }
```

<br/>

#### **LinkedList源码分析**

LinkedList首先有三个属性：

- 链表大小：`transient int size = 0;`
- （指向）第一个结点/头结点：` transient Node<E> first;`
- （指向）最后一个结点/尾结点：`transient Node<E> last;`

```java
// Node节点类
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

`item`中存储的是实际数据，`next`指向下一节点，`prev`指向上一节点。

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}

void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
```

当使用`add()`方法添加节点时，调用`linkLast()`方法。

首先创建一个`Node`节点` l` 将`last`尾节点（此时为空）指向它

然后创建一个`newNode`节点存储数据，并且它的前驱指向`l`，后继指向`null`，值为`e`。

再把`last`指向`newNode`。

当`l`是创建的第一个节点即`l == null`时，将`first`指向`newNode`;

否则将`l.next`即`l`的后继节点指向`newNode`。

<br/>

#### **ArrayList与LinkedList的区别**

- ArrayList：必须开辟连续空间，查询快，增删慢。
- LinkedList：无需开辟连续空间，查询慢，增删快。

![arraylinkedlist.png](https://s1.imagehub.cc/images/2022/03/03/arraylinkedlist.png)

<br/>

## **泛型概述**

---

- Java泛型是JDK1.5中引入的一个新特性，其本质是参数化类型，把类型作为参数传递。
- 常见形式有泛型类、泛型接口、泛型方法。
- 语法：
  - <T,…> T称为类型占位符，表示一种引用类型。
- 好处：
  - 提高代码的重用性。
  - 防止类型转换异常，提高代码的安全性。

<br/>

### **泛型类**

```java
/*
    泛型类
    语法: 类名<T>
    T: 类型占位符, 表示一种引用类型. 当需要多个时需使用逗号隔开
 */
public class MyGenericClass<T> {
    // 1. 使用泛型创建变量
    T t;
    // 不能使用泛型实例化对象
//    T t1 = new T();

    // 2. 使用泛型作为方法的参数
    public void show(T t) {
        System.out.println(t);
    }

    // 3. 使用泛型作为方法的返回类型
    public T getT(){
        return t;
    }
}
```

```java
public static void main(String[] args) {
    // 泛型类的使用
    // 使用泛型创建对象
    MyGenericClass<String> myGeneric1 = new MyGenericClass<>();
    myGeneric1.t = "Generic1";
    myGeneric1.show("Hello");
    System.out.println(myGeneric1.getT());

    MyGenericClass<Integer> myGeneric2 = new MyGenericClass<>();
    myGeneric2.t = 100;
    myGeneric2.show(200);
    System.out.println(myGeneric2.getT());
    }
```

<br/>

### **泛型接口**

```java
/*
    泛型接口
    语法: 接口名<T>
    注意: 不能创建泛型静态常量
 */
public interface MyGenericInterface<T> {
    String name = "abc";

    T genericInterfaceOut(T t);
}
```

```java
/*
    实现接口时确定泛型类
 */
public class GenericInterfaceImplTest1 implements MyGenericInterface<String>{
    @Override
    public String genericInterfaceOut(String s) {
        System.out.println("Interface test1 output: " + s);
        return s;
    }
}
```

```java
/*
    实现接口时不确定泛型类
 */

public class GenericInterfaceImplTest2<T> implements MyGenericInterface<T> {
    @Override
    public T genericInterfaceOut(T t) {
        System.out.println("Interface test2 output: " + t);
        return t;
    }
}
```

```java
// 泛型接口的使用
// 在类实现接口时就指定泛型类型
GenericInterfaceImplTest1 genericInterfaceImplTest1 = new GenericInterfaceImplTest1();
genericInterfaceImplTest1.genericInterfaceOut("success1");

// 在实例化实现接口类时再指定泛型类型
GenericInterfaceImplTest2<Integer> genericInterfaceImplTest2 = new GenericInterfaceImplTest2<>();
genericInterfaceImplTest2.genericInterfaceOut(666);
```

<br/>

### **泛型方法**

---

```java
/*
    泛型方法
    语法: <T>返回值类型
 */

public class MyGenericMethod {
    public <T> T genericMethodOut(T t){
        System.out.println("Method output test: " + t);
        return t;
    }
}
```

```java
// 泛型方法的使用
MyGenericMethod myGenericMethod = new MyGenericMethod();
myGenericMethod.genericMethodOut("success");
myGenericMethod.genericMethodOut("666");
myGenericMethod.genericMethodOut("6.66");
```

<br/>

### **泛型集合**

- **概念**：参数化类型、类型安全的集合，强制集合元素的类型必须一致。

- **特点:** 

  - 编译时即可检查，而非运行时抛出异常。
  - 访问时，不必类型转换（拆箱）。
  - 不同泛型指尖引用不能相互赋值，泛型不存在多态。

  之前我们在创建LinkedList类型对象的时候并没有使用泛型，但是进到它的源码中会发现：

  ```java
  public class LinkedList<E>
      extends AbstractSequentialList<E>
      implements List<E>, Deque<E>, Cloneable, java.io.Serializable{...}
  ```

  它是一个泛型类，而我之前使用的时候并没有传递，说明java语法是允许的，这个时候传递的类型是Object类，虽然它是所有类的父类，可以存储任意的类型，但是在遍历、获取元素时需要原来的类型就要进行强制转换。这个时候就会出现一些问题，假如往链表里存储了许多不同类型的数据，在强转的时候就要判断每一个原来的类型，这样就很容易出现错误。

<br/>

## Set集合概述

---

### **Set子接口**

- **特点**：无序、无下标、元素不可重复。
- **方法**：全部继承自Collection中的方法。

```java
/*
    Set的使用
    特点: 1.无序、无下标  2.不能重复
 */
public class SetTest1 {
    public static void main(String[] args) {
        // 创建集合
        Set<String> set = new HashSet<>();

        // 1. 添加元素
        set.add("Apple");
        set.add("Huawei");
        set.add("Xiaomi");
        set.add("Samsung");
        // 重复元素不会被添加进HashSet
        set.add("Huawei");
        // 输出不会按照添加元素的顺序(无序)
        System.out.println(set + "\nElementNum: " + set.size());

        // 2. 删除元素
        set.remove("Samsung");
        System.out.println(set + "\nElementNum: " + set.size());

        // 3. 遍历[重点]
        // for-each edition (无下标，不能使用for)
        System.out.println("for-each edition: ");
        for(String s : set){
            System.out.println(s);
        }

        //iterator edition
        System.out.println("iterator edition: ");
        Iterator<String> iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }

        // 4. 判断
        System.out.println("The set contains Apple is: " + set.contains("Apple"));
        System.out.println("The set is empty is: " + set.isEmpty());
    }
```

<br/>

#### **Set实现类**

##### **HashSet**

- 基于HashCode计算元素存放位置。
- 当存入元素的哈希码相同时，会调用equals进行确认，如结果为true，则拒绝后者存入。

```java
/*
    人类
 */
public class Person {
    private String name;
    private int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person [name = " + name + ", age = " + age + "]";
    }
}
```

```java
/*
    HashSet的使用
    存储结构：哈希表(数组 + 链表 + 红黑树)
    存储过程：1.根据hashcode计算保存的位置。若此位置为空，则之间保存；否则执行2.
            2.执行equals()方法，若结果为true，则认为是重复元素，不进行存储；否则创建链表。
 */
public class HashSetTest1 {
        public static void main(String[] args) {
            // 创建集合
            HashSet<Person> persons = new HashSet<>();

            // 添加数据
            Person p1 = new Person("a", 20);
            Person p2 = new Person("b", 21);
            Person p3 = new Person("c", 22);
            Person p4 = new Person("d", 23);

            persons.add(p1);
            persons.add(p2);
            persons.add(p3);
            persons.add(p4);
            // 重复元素无法添加
            persons.add(p4);
            // 在add()方法中new一个已经添加过的Person对象可以添加进HashSet
            // 要使该元素无法添加进HashSet，就要重写Person类中的HashCode()方法和equals()方法
            // 重写HashCode()方法让相同名字、年龄的元素所分配的位置相同；重写equals()方法让相同名字、年龄的元素返回true。
            persons.add(new Person("c", 22));
            System.out.println(persons + "\nPersonNum: " + persons.size());

            // 删除数据
            persons.remove(p4);
            System.out.println("After remove: " + persons + "\nPersonNum: " + persons.size());

            // 遍历
            // for-each edition
            System.out.println("for-each edition: ");
            for (Person p : persons){
                System.out.println(p);
            }

            // iterator edition
            System.out.println("iterator edition: ");
            Iterator iterator = persons.iterator();
            while (iterator.hasNext()) {
                System.out.println(iterator.next());
            }

            // 判断
            System.out.println("The HashSet contains d is: " + persons.contains(new Person("b", 21)));
            System.out.println("The HashSet is empty is: " + persons.isEmpty());
        }
```

```java
// 重写hashCode()方法和equals()方法
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Person person = (Person) o;
    return getAge() == person.getAge() && getName().equals(person.getName());
}

@Override
public int hashCode() {
    return Objects.hash(getName(), getAge());
}
```

<br/>

##### **TreeSet**

- 基于**排列 顺序**实现不重复。
- 实现了SortedSet接口，对集合元素自动排序。
- 元素对象的类型必须实现Comparable接口，指定排序规则。
- 通过CompareTo方法确定是否为重复元素。

```java
/*
    TreeSet的使用(元素实现Comparable接口)
    存储结构： 红黑树
    要求： 元素必须实现Comparable接口。当compareTo()方法的返回值为 0 ，则认为是重复元素。
 */
public class TreeSetTest1 {
    public static void main(String[] args) {
        // 创建集合
        TreeSet<Person> treePersons1 = new TreeSet<>();

        // 1. 添加元素
        Person p1 = new Person("a", 20);
        Person p2 = new Person("b", 21);
        Person p3 = new Person("c", 22);
        Person p4 = new Person("c", 23);
        Person p5 = new Person("d", 24);

        // 若添加的元素不实现Comparable接口并重写compareTo()方法，则TreeSet无法比较元素，出现ClassCastException
        treePersons1.add(p1);
        treePersons1.add(p2);
        treePersons1.add(p3);
        treePersons1.add(p4);
        treePersons1.add(p5);

        System.out.println(treePersons1 + "\nPersonNum: " + treePersons1.size());

        // 删除
        treePersons1.remove(p4);
        // compareTo()方法比较的姓名和年龄认为new的对象与p5相同，则能删除。
        treePersons1.remove(new Person("d", 24));

        System.out.println(treePersons1 + "\npersonNum: " + treePersons1.size());

        // 遍历
        // for-each edition
        System.out.println("for-each edition: ");
        for (Person p : treePersons1){
            System.out.println(p);
        }

        // iterator edition
        System.out.println("iterator edition: ");
        Iterator<Person> iterator = treePersons1.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }

        // 判断
        System.out.println("The TreeSet contains d is: " + treePersons1.contains(new Person("b", 21)));
        System.out.println("The TreeSet is empty is: " + treePersons1.isEmpty());
    }
```

Person类实现Comparable接口, 重写compareTo()方法:

```java
// 先按姓名比较，再按年龄比较
@Override
public int compareTo(Person o) {
    int n1 = this.getName().compareTo(o.getName());
    int n2 = this.age - o.getAge();
    return n1 == 0 ? n2 : n1;
}
```

除了实现Comparable接口里的比较方法，TreeSet也提供了一个带比较器Comparator的构造方法，使用lambda来实现它：

```java
/*
    TreeSet的使用(创建集合时实现Comparator接口，指定比较规则)
    存储结构： 红黑树
 */
public class TreeSetTest2 {

    public static void main(String[] args) {
        // 创建集合 指定比较规则
        TreeSet<Person> treePersons2 = new TreeSet<>((o1, o2) -> {
            int n1 = o1.getAge() - o2.getAge();
            int n2 = o1.getName().compareTo(o2.getName());
            return n1 == 0 ? n2 : n1;
        });

        // 添加元素
        Person p1 = new Person("a", 20);
        Person p2 = new Person("b", 21);
        Person p3 = new Person("c", 22);
        Person p4 = new Person("c", 23);
        Person p5 = new Person("d", 24);

        treePersons2.add(p1);
        treePersons2.add(p2);
        treePersons2.add(p3);
        treePersons2.add(p4);
        treePersons2.add(p5);

        System.out.println(treePersons2 + "\nPersonNum: " + treePersons2.size());
    }
```

使用TreeSet集合实现字符串按照长度进行排序:

```java
/*
    使用TreeSet集合实现字符串按照长度进行排序
    list, arrayList, linkedList, set, hashSet, treeSet
 */
public class TreeSetTest3 {
    public static void main(String[] args) {
        TreeSet<String> treeSet= new TreeSet<>((o1, o2) ->{
            int n1 = o1.length() - o2.length();
            int n2 = o1.compareTo(o2);
            return n1 == 0 ? n2 : n1;
        });

        treeSet.add("list");
        treeSet.add("arrayList");
        treeSet.add("linkedList");
        treeSet.add("set");
        treeSet.add("hashSet");
        treeSet.add("treeSet");

        System.out.println("The sorted treeSet: " + treeSet);
    }
}
```

<br/>

## **Map集合概述**

---

### **Map接口的特点：**

1. 用于存储任意键值对(Key-Value)。
2. 键：无序、无下标、不允许重复（唯一）。
3. 值：无序、无下标、允许重复。

![f7246b600c338744ebf82e15ac5fcef9d72a60590a03.png](https://s1.imagehub.cc/images/2022/03/04/f7246b600c338744ebf82e15ac5fcef9d72a60590a03.png)

<br/>

### **Map父接口**

- **方法**：
  - `V put(K key,V value)`//将对象存入到集合中，关联键值。key重复则覆盖原值。
  - `Object get(Object key)`//根据键获取相应的值。

  - `Set<K> keySet()`//返回所有的key
  - `Collection<V> values()`//返回包含所有值的Collection集合。
  - `Set<Map.Entry<K,V>>`//键值匹配的set集合

```java
/*
    Map的使用
    特点: 1.存储键值对; 2.键不能重复,值可以重复; 3.无序
 */
public class MapTest {
    public static void main(String[] args) {
        // 创建集合
        Map<String, String> map = new HashMap<>();

        // 1. 添加元素
        map.put("cn", "China");
        map.put("uk", "British");
        map.put("us", "America");
        // 键重复,则替换
        map.put("uk", "England");

        System.out.println(map + "\nElementNum: " + map.size());

        // 2. 删除
        map.remove("us");
        System.out.println("After remove" + map + "\nElementNum: " + map.size());

        // 3. 遍历
        // keySet edition
        System.out.println("keySet edition: ");
        for (String key : map.keySet()){
            System.out.println(key + " " + map.get(key));
        }

        // entryKey edition [重点]
        System.out.println("entryKey edition: ");
        for (Map.Entry<String, String> entry : map.entrySet()){
            System.out.println(entry.getKey() + " " + entry.getValue());
        }

        // 判断
        System.out.println("The map contains key 'cn' is: " + map.containsKey("cn"));
        System.out.println("The map contains value 'England' is: " + map.containsValue("England"));
        System.out.println("The map is empty is: " + map.isEmpty());
    }
```

<br/>

### **Map子接口**

#### **Map实现类**

##### **HashMap[重点]**

```java
/*
    水果类
 */
public class Fruits {
    private String name;
    private int price;

    public Fruits() {
    }

    public Fruits(String name, int price) {
        this.name = name;
        this.price = price;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getPrice() {
        return price;
    }

    public void setPrice(int price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "Fruits{" + "name='" + name + '\'' + ", price=" + price + '}';
    }
}
```

```java
/*
    HashMap的使用
    存储结构: 哈希表
    特点: 线程不安全，运行效率快；允许用null作为key或是value。
 */
public class HashMapTest {
    public static void main(String[] args) {
        // 创建集合
        HashMap<Fruits, String> hashMap = new HashMap<>();

        // 添加元素
        Fruits f1 = new Fruits("Apple", 5);
        Fruits f2 = new Fruits("pear", 6);
        Fruits f3 = new Fruits("watermelon", 7);
        Fruits f4 = new Fruits("mango", 8);

        hashMap.put(f1, "xinJiang");
        hashMap.put(f2, "dongBei");
        hashMap.put(f3, "shangHai");
        hashMap.put(f4, "xian");

        // new的对象与之前的对象虽然键值都相同,但地址不同. 要想让键值相同的对象不能被添加进hashMap则要重写Fruits类的hashCode和equals方法
        hashMap.put(new Fruits("mango", 8), "xian");

        System.out.println(hashMap + "\nElementNum: " + hashMap.size());

        // 删除
        hashMap.remove(f4);
        System.out.println("After remove: " + hashMap + "\nElementNum: " + hashMap.size());

        // 遍历
        // keySet edition
        System.out.println("keySet edition: ");
        for (Fruits fruits : hashMap.keySet()) {
            System.out.println(fruits.toString() + " " + hashMap.get(fruits));
        }

        // entrySet edition
        System.out.println("entrySet edition: ");
        for(Map.Entry<Fruits, String> entry : hashMap.entrySet()){
            System.out.println(entry.toString());
        }

        // 判断
        System.out.println(hashMap.containsKey(new Fruits("Apple", 5)));
    }
```

注：和之前说过的HashSet类似，重复依据是hashCode和equals方法，重写即可：

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Fruits fruits = (Fruits) o;
    return getPrice() == fruits.getPrice() && Objects.equals(getName(), fruits.getName());
}

@Override
public int hashCode() {
    return Objects.hash(getName(), getPrice());
}
```

<br/>

##### **HashMap源码分析**

- 默认初始化容量：`static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16`
- 数组最大容量：`static final int MAXIMUM_CAPACITY = 1 << 30;`
- 默认加载因子：`static final float DEFAULT_LOAD_FACTOR = 0.75f;`
- 链表调整为红黑树的链表长度阈值:`static final int TREEIFY_THRESHOLD = 8;`
- 红黑树调整为链表的链表长度阈值:`static final int UNTREEIFY_THRESHOLD = 6;`
- 链表调整为红黑树的数组最小阈值:`static final int MIN_TREEIFY_CAPACITY = 64;`
- HashMap存储的数组：`transient Node<K,V>[] table;`
- HashMap存储的元素个数：`transient int size;`

> - 默认加载因子是什么？
>   - 就是判断数组是否扩容的一个因子。假如数组容量为100，如果HashMap的存储元素个数超过了100*0.75=75，那么就会进行扩容。
> - 链表调整为红黑树的链表长度阈值是什么？
>   - 假设在数组中下标为3的位置已经存储了数据，当新增数据时通过哈希码得到的存储位置又是3，那么就会在该位置形成一个链表，当链表过长时就会转换成红黑树以提高执行效率，这个阈值就是链表转换成红黑树的最短链表长度；
> - 红黑树调整为链表的链表长度阈值是什么？
>   - 当红黑树的元素个数小于该阈值时就会转换成链表。
> - 链表调整为红黑树的数组最小阈值是什么？
>   - 并不是只要链表长度大于8就可以转换成红黑树，在前者条件成立的情况下，数组的容量必须大于等于64才会进行转换。

HashMap的数组table存储的就是一个个的Node<K, V>类型，很清晰地看到有一对键值，还有一个指向next的指针（以下只截取了部分源码）：

```java
static class Node<K,V> implements Map.Entry<K,V> {
      final K key;
      V value;
      Node<K,V> next;
  }
```

之前的代码中在new对象时调用的是HashMap的无参构造方法，进入到该构造方法的源码查看一下：

```java
public HashMap() {
      this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
  }
```

只赋值了一个默认加载因子。而源码中 table 和 size 都没有赋予初始值，说明刚创建的HashMap对象没有分配容量，并不拥有默认的16个空间大小，这样做的目的是为了节约空间，此时**table = null，size = 0**。

当我们往对象里添加元素时调用put方法：

```java
public V put(K key, V value) {
      return putVal(hash(key), key, value, false, true);
  }
```

put方法把key和value传给了putVal，同时还传入了一个hash(Key)所返回的值，这是一个产生哈希值的方法，再进入到putVal方法（部分源码）：

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                    boolean evict) {
      Node<K,V>[] tab; Node<K,V> p; int n, i;
      if ((tab = table) == null || (n = tab.length) == 0)
          n = (tab = resize()).length;
      if ((p = tab[i = (n - 1) & hash]) == null)
          tab[i] = newNode(hash, key, value, null);
      else{
          ...
      }
  }
```

这里面创建了一个tab数组和一个Node变量p，第一个if实际是判断table是否为空，而我们现在只关注刚创建HashMap对象时的状态，此时tab和table都为空，满足条件，执行内部代码，这条代码其实就是把resize()所返回的结果赋给tab，n就是tab的长度，resize顾名思义就是重新调整大小。查看resize()源码（部分）：

```java
final Node<K,V>[] resize() {
      Node<K,V>[] oldTab = table;
      int oldCap = (oldTab == null) ? 0 : oldTab.length;
      int oldThr = threshold;
      if (oldCap > 0);
      else if (oldThr > 0);
      else {               // zero initial threshold signifies using defaults
          newCap = DEFAULT_INITIAL_CAPACITY;
          newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
      } 
      @SuppressWarnings({"rawtypes","unchecked"})
      Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
      table = newTab;
      return newTab;
  }
```

该方法首先把table及其长度赋值给oldTab和oldCap；threshold是阈值，此时为0，所以前两个if先不管，最后else里newCap的值为默认初始化容量16；往下创建了一个newCap大小的数组并将其赋给了table，刚创建的HashMap对象就在这里获得了初始容量。然后我们再回到putVal方法，第二个if就是根据哈希码得到的tab中的一个位置是否为空，为空便直接添加元素，此时数组中无元素所以直接添加。至此HashMap对象就完成了第一个元素的添加。当添加的元素超过16*0.75=12时，就会进行扩容：

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict){
      if (++size > threshold)
          resize();
  }
```

扩容的代码如下（部分）：

```java
final Node<K,V>[] resize() {
      int oldCap = (oldTab == null) ? 0 : oldTab.length;
      int newCap;
      if (oldCap > 0) {
          if (oldCap >= MAXIMUM_CAPACITY) {...}
          else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                   oldCap >= DEFAULT_INITIAL_CAPACITY)
      }
  }
```

- 核心部分是else if里的移位操作，**也就是说每次扩容都是原来大小的两倍**。
- **注**：额外说明的一点是在JDK1.8以前链表是头插入，JDK1.8以后链表是尾插入。

<br/>

##### **HashSet源码分析**

HashSet的存储结构就是利用HashMap实现的：

```java
public class HashSet<E>
      extends AbstractSet<E>
      implements Set<E>, Cloneable, java.io.Serializable
  {
      private transient HashMap<E,Object> map;
      private static final Object PRESENT = new Object();
      public HashSet() {
          map = new HashMap<>();
      }
  }
```

HashSet的add()方法也是利用HashMap的put()方法实现：

```java
public boolean add(E e) {
      return map.put(e, PRESENT)==null;
  }
```

利用了map的key来存储HashSet的值。

<br/>

##### **Hashtable**

- JDK1.0版本，线程安全，运行效率慢；不允许null作为key或是value。

- 初始容量11，加载因子0.75。

  这个集合在开发过程中已经不用了，稍微了解即可。

<br/>

##### **Properties**

- Hashtable的子类，要求key和value都是String。通常用于配置文件的读取。

它继承了Hashtable的方法，与流关系密切，在流中详解。

<br/>

##### **TreeMap**

- 实现了SortedMap接口（是Map的子接口），可以对key自动排序。

实现Comparable接口的方法：

```java
/*
    TreeMap的使用
    存储结构: 红黑树
    特点: 与TreeSet相似, TreeMap中的元素也必须实现Comparable接口, 使其之间能够相互比较, 否则会报ClassCastException
 */
public class TreeMapTest1 {
    public static void main(String[] args) {
        // 创建集合
        TreeMap<Fruits, String> treeMap = new TreeMap<>();

        // 添加元素
        Fruits f1 = new Fruits("Apple", 5);
        Fruits f2 = new Fruits("pear", 6);
        Fruits f3 = new Fruits("watermelon", 7);
        Fruits f4 = new Fruits("mango", 8);

        treeMap.put(f1, "xinJiang");
        treeMap.put(f2, "dongBei");
        treeMap.put(f3, "shangHai");
        treeMap.put(f4, "xian");
        // 重写完compareTo方法后会认为下面语句new的对象与f4是相同的,因此不能添加进treeMap
        treeMap.put(new Fruits("mango", 8), "xian");

        System.out.println(treeMap + "\nElementNum: " + treeMap.size());

        // 删除
        treeMap.remove(f4);
        System.out.println("After remove: " + treeMap + "\nElementNum: " + treeMap.size());

        // 遍历
        // keySet edition
        System.out.println("keySet edition: ");
        for (Fruits fruits : treeMap.keySet()) {
            System.out.println(fruits + treeMap.get(fruits));
        }

        // entrySet edition
        System.out.println("entrySet edition: ");
        for(Map.Entry<Fruits, String> entrySet : treeMap.entrySet()) {
            System.out.println(entrySet);
        }
    }
```

Fruits类实现Comparable接口，重写compareTo方法：

```java
@Override
public int compareTo(Fruits o) {
    int n1 = this.getName().compareTo(o.getName());
    int n2 = this.price - o.getPrice();
    return n1 == 0 ? n2 : n1;
}
```

```java
/*
    创建TreeMap集合时实现Comparator接口，指定比较规则
 */
public class TreeMapTest2 {
    public static void main(String[] args) {
        TreeMap<Fruits, String> treeMap2 = new TreeMap<>((o1, o2) -> {
            int n1 = o1.compareTo(o2);
            int n2 = o1.compareTo(o2);
            return n1 == 0 ? n2 : n1;
        });

        Fruits f1 = new Fruits("Apple", 5);
        Fruits f2 = new Fruits("pear", 6);
        Fruits f3 = new Fruits("watermelon", 7);
        Fruits f4 = new Fruits("mango", 8);

        treeMap2.put(f1, "xinJiang");
        treeMap2.put(f2, "dongBei");
        treeMap2.put(f3, "shangHai");
        treeMap2.put(f4, "xian");

        System.out.println(treeMap2 + "\nElementNum: " + treeMap2.size());
    }
}
```

实现Comparator的方法：

```java
/*
    创建TreeMap集合时实现Comparator接口，指定比较规则
 */
public class TreeMapTest2 {
    public static void main(String[] args) {
        TreeMap<Fruits, String> treeMap2 = new TreeMap<>((o1, o2) -> {
            int n1 = o1.compareTo(o2);
            int n2 = o1.compareTo(o2);
            return n1 == 0 ? n2 : n1;
        });

        Fruits f1 = new Fruits("Apple", 5);
        Fruits f2 = new Fruits("pear", 6);
        Fruits f3 = new Fruits("watermelon", 7);
        Fruits f4 = new Fruits("mango", 8);

        treeMap2.put(f1, "xinJiang");
        treeMap2.put(f2, "dongBei");
        treeMap2.put(f3, "shangHai");
        treeMap2.put(f4, "xian");

        System.out.println(treeMap2 + "\nElementNum: " + treeMap2.size());
    }
}
```

<br/>

##### **TreeSet源码分析**

和HashSet类似，TreeSet的存储结构是利用TreeMap实现的：

```java
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable
{
    private transient NavigableMap<E,Object> m;
    private static final Object PRESENT = new Object();
    TreeSet(NavigableMap<E,Object> m) {
        this.m = m;
    }
    public TreeSet() {
        this(new TreeMap<E,Object>());
    }
}
```

TreeSet的add()方法也是利用TreeMap的put()方法实现：

```java
public boolean add(E e) {
    return m.put(e, PRESENT)==null;
}
```

它的add方法调用的就是TreeMap的put方法，将元素作为key传入到存储结构中。

<br/>

## Collections工具类

---

- **概念**：集合工具类，定义了除了存取以外的集合常用方法。

- **方法**：

  - `public static void reverse(List<?> list)`//反转集合中元素的顺序
  - `public static void shuffle(List<?> list)`//随机重置集合元素的顺序
  - `public static void sort(List<T> list)`//升序排序（元素类型必须实现Comparable接口）

  ```java
  /*
      Collections工具类的使用
   */
  public class CollectionsTest {
      public static void main(String[] args) {
          List<Integer> list = new ArrayList<>();
  
          list.add(1);
          list.add(5);
          list.add(30);
          list.add(2);
          list.add(10);
  
          System.out.println();
          System.out.println("Original list: " + list);
  
          // sort排序
          Collections.sort(list);
          System.out.println("Sorted list: " + list);
  
          // binarySearch
          System.out.println("BinarySearch '5' in list: " + Collections.binarySearch(list, 5));
          System.out.println("BinarySearch '3' in list: " + Collections.binarySearch(list, 3));
  
          // copy
          List<Integer> copiedList = new ArrayList<>();
          // 若没有为copiedList添加与list相同长度的值,会报IndexOutOfBoundsException. 因为此时copiedList长度为0
          for (int i = 0; i < list.size(); i++) {
              copiedList.add(0);
          }
          Collections.copy(copiedList, list);
          System.out.println("The copied list: " + copiedList);
  
          // reverse
          Collections.reverse(copiedList);
          System.out.println("The r eversed list: " + copiedList);
  
          // shuffle
          Collections.shuffle(list);
          System.out.println("The shuffled list: " + list);
  
          // toArray
          Integer[] array = list.toArray(new Integer[0]);
          System.out.println("The list change into array: " + Arrays.toString(array));
  
          // asList
          String[] s ={"a", "b", "c"};
          List<String> sList = Arrays.asList(s);
          System.out.println("The array change into list: " + sList);
          // 由数组转换成的集合是一个受限集合,不允许对其添加和删除
          try {
              sList.add("a");
          }catch(UnsupportedOperationException e){
              System.out.println("UnsupportedOperationException!");
          }
  
          // 基本类型转成集合时需要修改为包装类
          Integer[] nums = {100, 200, 300, 400, 500};
          List<Integer> nList = Arrays.asList(nums);
          System.out.println(nList);
      }
  ```



