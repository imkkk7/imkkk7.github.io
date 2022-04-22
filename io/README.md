# **流的概念**

---

- **概念**：内存与存储设备之间传输数据的通道。

![dbb44aed2e738bd4b31c6ba6adda90d6277f9e2f5350.png](https://s1.imagehub.cc/images/2022/03/06/dbb44aed2e738bd4b31c6ba6adda90d6277f9e2f5350.png)

- 水借助管道传输；数据借助流传输。





# **流的分类**

---

## **按方向[重点]**

- **输入流**：将<存储设备>中的内容读入到<内存>中。
- **输出流**：将<内存>中的内容写入到<存储设备>中。

![1e30e924b899a9014c0896a111c41d7b02087bf45f50.png](https://s1.imagehub.cc/images/2022/03/06/1e30e924b899a9014c0896a111c41d7b02087bf45f50.png)

## **按单位**

- **字节流**：以字节为单位，可以读写所有数据。
- **字符流**：以字符为单位，只能读写文本数据。

## **按功能**

- **字节流**：具有实际传输数据的读写功能。
- **过滤流**：在节点流的基础之上增强功能。





# **字节流**

---

![QQ20220306163205.png](https://s1.imagehub.cc/images/2022/03/06/QQ20220306163205.png)



## **字节流的父类(抽象类)**

- InputStream

  字节输入流

  - `public int read(){}`
  - `public int read(byte[] b){}`
  - `public int read(byte[] b,int off,int len){}`

- OutputStream

  字节输出流

  - `public void write(int n){}`
  - `public void write(byte[] b){}`
  - `public void write(byte[] b,int off,int len){}`



## **字节流的子类**

### **文件字节流**

- **FileInputStream**
  - `public int read()`//从输入流中读取一个字节数据，返回读到的字节数据，如果达到文件末尾，返回-1。
  - `public int read(byte[] b)`//从输入流中读取字节数组长度的字节数据存入数组中，返回实际读到的字节数；如果达到文件的尾部，则返回-1。
- **FileOutputStream**
  - `public void write(int b)`//将指定字节写入输出流。
  - `public void write(bute[] b)`//一次写多个字节，将b数组中所有字节，写入输出流。

```java
/*
    FileInputStream的使用
 */
public class FileInputStreamTest {
    public static void main(String[] args) throws IOException {
        // 创建对象流并指定文件路径
        FileInputStream fileInputStream = new FileInputStream("F:\\Study\\JavaSE\\src\\ioFrameWork\\fileIOStream\\fileInputStreamTest.txt");

        // 读取文件
        // 单个字节读取
        int data = 0;
        while((data = fileInputStream.read()) != -1){
            System.out.print((char)data);
        }
        System.out.println("\nRead completely");

        // 多个字节读取(注意将单个字节读取的代码注释掉,否则指针已经指向文件末尾导致下面多字节读取无法读取到数据)
        byte [] buffer = new byte[1024];
        int count = 0;
        while((count = fileInputStream.read(buffer)) != -1){
            System.out.println(new String(buffer, 0, count));
        }

        // 关闭流
        fileInputStream.close();
        System.out.println("Stream closed");
    }
}
```

在用`FileInputStream`读取文件时需要用一个`int`类型的数据来存储读到的值。如果在读取时仅仅使用：

```java
while(fileInputStream.read() != -1){
            System.out.print((char)fileInputStram.read());
        }
```

则会隔数据输出：第一次读取到第一个数据进入循环，但在输出时又向下读取了一个数据并输出。

```java
/*
    FileOutputStream的使用
 */
public class FileOutputStreamTest {
    public static void main(String[] args) throws IOException {
        // 创建对象流
        FileOutputStream fileOutputStream = new FileOutputStream("F:\\Study\\JavaSE\\src\\ioFrameWork\\fileIOStream\\fileOutputStreamTest.txt");
//        使用 FileOutputStream(String name, boolean append)构造方法可以使文件数据不被覆盖.
//        FileOutputStream fileOutputStream = new FileOutputStream("F:\\Study\\JavaSE\\src\\ioFrameWork\\fileIOStream\\fileOutputStreamTest.txt", true);

        // 写入文件
        // 单个字节写入
        fileOutputStream.write(97);
        fileOutputStream.write('b');
        fileOutputStream.write('c');

        // 多个字节写入
        byte [] data = {'d', 'e', 'f', 'g'};
        int count = 0;
        while(count < data.length){
            fileOutputStream.write(data[count]);
            count++;
        }

        String s = "hijkl";
        fileOutputStream.write(s.getBytes());

        // 关闭流
        fileOutputStream.close();
        System.out.println("Stream Closed");
    }
}
```

```java
/*
    使用文件字节流实现文件的复制
 */
public class fileIOStreamTest {
    public static void main(String[] args) throws IOException {
        // 创建输入输出流对象
        FileInputStream fileInputStream = new FileInputStream("F:\\Study\\JavaSE\\src\\ioFrameWork\\fileIOStream\\20220306.jpg");
        FileOutputStream fileOutputStream = new FileOutputStream("F:\\Study\\JavaSE\\src\\ioFrameWork\\fileIOStream\\new20220306.jpg");

        // 边读边写
        byte[] buffer = new byte[1024];
        int count = 0;
        while ((count = fileInputStream.read(buffer)) != -1){
            fileOutputStream.write(buffer, 0, count);
        }

        // 关闭流
        fileInputStream.close();
        fileOutputStream.close();
        System.out.println("Copy Completely");
    }
}
```



### **字节缓存流**

**缓冲流**：BufferedInputStream/BufferedOutputStream

- 提高IO效率，减少硬盘访问次数。
- 数据存储在缓冲区中，用`flush()`方法将缓冲区的内容写入文件中，也可以直接`close()`。
- 是`FileInputStream/FileOutputStream`的增强。

```java
/*
    BufferedInputString的使用(字节缓冲流)
 */
public class BufferedInputStreamTest {
    public static void main(String[] args) throws IOException {
        // 创建对象流
        FileInputStream fileInputStream = new FileInputStream("F:\\Study\\JavaSE\\src\\ioFrameWork\\bufferedIOStream\\bufferedInputStreamTest.txt");
        // BufferedInputStream是FileInputStream的增强
        BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream);

        // 读取文件
        int data = 0;
        while ((data = bufferedInputStream.read()) != -1){
            System.out.print((char)data);
        }

        // 关闭流(直接关闭bufferedInputStream就可以)
        bufferedInputStream.close();

    }
}
```

```java
/*
    BufferedOutputStream的使用
 */
public class BufferedOutputStreamTest {
    public static void main(String[] args) throws IOException {
        // 创建对象流
        FileOutputStream fileOutputStream = new FileOutputStream("F:\\Study\\JavaSE\\src\\ioFrameWork\\bufferedIOStream\\bufferedOutStreamTest.txt");
        BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(fileOutputStream);

        // 写入文件
        for (int i = 0; i < 10; i++) {
            bufferedOutputStream.write("BufferedOutputStream\n".getBytes());    // 写入缓冲区(8K)
            bufferedOutputStream.flush();   // 刷新到硬盘(写在for循环中效率低但可以防止数据丢失)
        }

        // 关闭流(内部调用flush()方法)
        bufferedOutputStream.close();
    }
}
```



### **对象流**

- **对象流**：ObjectOutputStream/ObjectInputStream

  - 增加了缓冲区功能。
  - 增强了读写8种基本数据类型和字符串功能。
  - 增强了读写对象的功能：
    - `readObject()`	//从流中读取一个对象。
    - `writeObject(Object obj)`    //向流中写入一个对象。

  **使用流传输对象的过程称为序列化、反序列化。**



#### **序列化（ObjectOutputStream）**

```java
/**
 * 学生类
 */
public class Student {
    private String name;
    private int age;
    public Student(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    @Override
    public String toString() {
        return "Student [name=" + name + ", age=" + age + "]";
    }
}
```

```java
/*
    使用ObjectOutputStream实现对象的序列化
    要求: 1.序列化的类必须要实现Serializable接口 2.序列化的类中对象的属性也要实现Serializable接口
 */
public class ObjectOutputStreamTest {
    public static void main(String[] args) throws IOException {
        // 创建对象流
        // 这个文件后缀名表示二进制文件，可以写成其他如obj等任意后缀
        FileOutputStream fileOutputStream = new FileOutputStream("F:\\Study\\JavaSE\\src\\ioFrameWork\\objectIOStream\\stu.bin");
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(fileOutputStream);

        // 序列化(写入操作)
        Student s1 = new Student("abc", 22);
        objectOutputStream.writeObject(s1);

        // 关闭流
        objectOutputStream.close();
        System.out.println("WriteObject Completely");
    }

}
```

**注**：执行上述代码后IDE会抛出`java.io.NotSerializableException`，意思是Student类不能被序列化。需要实现Serializable接口。

```java
COPY//不需要实现任何方法
public class Student implements Serializable{ }
```

Serializable其实是一个标志接口，用来标志该类是否可以被序列化。该接口不含任何属性和抽象方法。

```java
COPYpublic interface Serializable {
}
```



#### **反序列化（ObjectInputStream）**

```java
/*
    使用ObjectOutputStream实现对象的反序列化(读取重构成对象)
 */
public class ObjectInputStreamTest {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        // 创建对象流
        FileInputStream fileInputStream = new FileInputStream("F:\\Study\\JavaSE\\src\\ioFrameWork\\objectIOStream\\stu.bin");
        ObjectInputStream objectInputStream = new ObjectInputStream(fileInputStream);

        // 反序列化(读取操作)
        Student s = (Student) objectInputStream.readObject();

        // 关闭流
        objectInputStream.close();
        System.out.println("ReadObject Completely: " + s.toString());
    }
}
```



#### **序列化和反序列化注意事项**

- **序列化类必须实现`Serializable`接口**。

- **序列化类中的对象属性也要求实现`Serializable`接口**。也就是说如果`Student`类中有一个`Parents`类型的属性`private Parents info;`那么`Parents`这个类也要实现`Serializable`接口。

- **序列化类中可以添加序列化版本号ID，以保证序列化的类和被序列化的类是同一个类**。在上面的代码中没有添加序列号版本，虽然IDE没有报错，但是会显示一个警告，提示我添加序列化版本号（串行版本标识）。我们可以在`Student`类中添加：

  ```java
  private static final long serialVersionUID = 100L;
  ```

  此时再运行`ObjectInputStreamTest`就会报一个无效类的异常：

  ```java
  java.io.InvalidClassException: ioFrameWork.objectIOStream.Student; 
  local class incompatible: stream classdesc serialVersionUID = 6434269056674371883, local class serialVersionUID = 100
  ```

  **这是因为之前序列化的`Student`和现在添加完`serialVersionUID = 100L`的序列化版本号不同**。当未添加序列版本号时，会自动生成了。我们再运行`ObjectOutputStreamTest`将`Student`重新序列化后，再反序列化就可以正常执行了。

- **使用`transient`（短暂的）修饰属性，可以避免该属性被序列化**。如用它来修饰`age`：

  ```java
  private transient int age;
  ```

  把`s1`这个对象序列化后再反序列化，这个对象的`age`属性就变成了 0。

- **静态属性不能被序列化**。

- **可以利用集合来序列化多个对象**：

  ```java
  ArrayList<Student> arrayList=new ArrayList<Student>();
  arrayList.add(s1);
  arrayList.add(s2);
  arrayList.add(s3);
  objectOutputStream.writeObject(arrayList);
  ```

  ```java
  ArrayList<Student> list=(ArrayList<Student>)objectInputStream.readObject();
  ```



# **常见字符编码**

---

- IOS-8859-1

  收录除ASCII外，还包括西欧、希腊语、泰语、阿拉伯语、希伯来语对应的文字符号。采用1个字节来表示，最多只能表示256个字符。

- UTF-8

  针对Unicode码表的可变长度字符编码。国际上使用的编码，也称为“万国码”，收录了几乎所有国家的常用字符。采用1至3个字节来表示一个字符。

- GB2312

  简体中文，采用1个或2个字节来表示字符，95年之前所采用的编码。

- GBK

  简体中文的扩充，GB2312的升级版本。

- BIG5

  台湾，繁体中文。

**当编码方式和解码方式不一致时，会出现乱码。**如果在`FileInputStreamTest`中的文件`fileInputStreamTest.txt`中存储的是：“中文中文”这样的中文字符，那么用`FileInputStreamTest`读取出来的数据就会是乱码。这是因为在`fileInputStreamTest.txt`文件中，中文是**UTF-8**编码方式，这种编码方式中中文一个字占**3个**字节，而字节流按字节读取。在读取的时候一个字节一个字节的读取就会使读取出来的信息出现乱码。

因此，对于中文就应该使用**字符流**对其进行读取和写入。



# **字符流**

---

[![QQ20220306163353.png](https://s1.imagehub.cc/images/2022/03/06/QQ20220306163353.png)](https://www.imagehub.cc/image/l7614)



## **字符流的父类（抽象类）**

- **Reader：**字符输入流

  - `public int read()`

    从流中读取单个字符，用整型来返回读取的字符；当读到流底部时返回-1。

  - `public int read(char[] c)`

    从流中读取字符保存到c数组中，返回读取的字符个数，当读到流底部时返回-1。

  - `public int read(char[] cbuf,int off,int len){}`

    抽象方法。

- **Writer：**字符输出流

  - `public void write(int n)`

    写入单个字符，只能写入包含16位低阶字节的整型数值，16位高阶字节将会被忽略。

  - `public void write(String str)`

    写入一个字符串。

  - `public void write(char[] cbuf)`

    写入一个字符数组。

## **字符流的子类**

### **文件字符流**

- **FileReader：**

  - `public int read()`

    继承自InputStreamReader类。读取单个字符，返回读取的字符，当读到流底部时返回-1。

  - `public int read(char[] c)`

    继承自Reader类。

  - `public int read(char[] cbuf,int offset,int length)`

    继承自InputStreamReader类。从流中读取部分字符到cbuf中指定位置，返回读取到的字符个数，当读到流底部时返回-1。

- **FileWriter:**

  - `public void write(int c)`

    继承自OutputStreamWriter类，写入一个字符。

  - `public void write(String str)`

    继承自Writer类。

  - `public void Write(char[] cbuf)`

    继承自Writer类。





### **字符缓冲流**

