# Java基础总结
**面向对象的设计原则：**

- 单一职责原则：减少耦合；提高复用
- 接口隔离原则：避免一个类对另外一个类的依赖
- 开放封闭原则：模块行为开放，模块功能封闭（对功能进行扩展的时候，排除最少的影响）
- 替换原则：之类可以替换父类
- 依赖倒置原则：解耦

**&与&&**

- &&：具有短路功能，当第一个条件为False时，不会在进行下一步运算。

**装箱与拆箱**

- 装箱：自动将基本类型数据转换成包装器类型（Int--->Integer）。
- 拆箱：自动将包装器类型的数据转换成基本数据类型（Integer--->Int）。

**==与equals**

- ==
    - 基本数据类型比较的时候，判断的是数值的大小。
    - 非基本类型的数据比较的时候，比较的是变量内存中存放对象的内存地址，用来判断两个对象的内存地址是否相同，即是否指向同一个对象。

- equals
    - 基本数据类型：不能用来比较。
    - 非基本数据类型：比较的是两个对象的内容是否相同。

- 代码描述

```java
int a = 1;
int b = 1;
a==b (true)
String a = "a";
String b = "a";

a==b (true)(String具有常量池功能，当执行String a = "a";的时候，常量池里已经有”a“了，当去执行String b = "a";因为常量池里已经有”a“了，所以b就直接去常量池里取值了，这样a和b的地址也就相同了)。

a.equals(b)（true）
String a = new String("ab");
String b = new String("ab");
a==b(false)
a.equals(b)（true）
```

**补充**

**String 类和常量池**
- String 对象的两种创建方式：
String str1 = "abcd";//先检查字符串常量池中有没有"abcd"，如果字符串常量池中没有，则创建一个，然后 str1 指向字符串常量池中的对象，如果有，则直接将 str1 指向"abcd""；

```java
String str2 = new String("abcd");//堆中创建一个新的对象。
String str3 = new String("abcd");//堆中创建一个新的对象。
System.out.println(str1==str2);//false 
System.out.println(str2==str3);//false
System.out.println(str1.equals(str2));//true
System.out.println(str2.equals(str3));//true
```

- 这两种不同的创建方法是有差别的。第一种方式是在常量池中拿对象；第二种方式是直接在堆内存空间创建一个新的对象。

- 记住一点：只要使用 new 方法，便需要创建新的对象。

String 类型的常量池比较特殊。它的主要使用方法有两种：

直接使用双引号声明出来的 String 对象会直接存储在常量池中。

如果不是用双引号声明的 String 对象，可以使用 String 提供的 intern 方法。String.intern() 是一个 Native 方法，它的作用是：如果运行时常量池中已经包含一个等于此 String 对象内容的字符串，则返回常量池中该字符串的引用；如果没有，JDK1.7之前（不包含1.7）的处理方式是在常量池中创建与此 String 内容相同的字符串，并返回常量池中创建的字符串的引用，JDK1.7以及之后的处理方式是在常量池中记录此字符串的引用，并返回该引用。

```java
String s1 = new String("计算机");         
String s2 = s1.intern();           
String s3 = "计算机";           
System.out.println(s2);//计算机           
System.out.println(s1 == s2);//false，因为一个是堆内存中的 String 对象一个是常量池中的 String 对象，           
System.out.println(s3 == s2);//true，因为两个都是常量池中的 String 对象。
字符串拼接:
String str1 = "str";           
String str2 = "ing";            
String str3 = "str" + "ing";//常量池中的对象           
String str4 = str1 + str2; //在堆上创建的新的对象                 
String str5 = "string";//常量池中的对象           
System.out.println(str3 == str4);//false           
System.out.println(str3 == str5);//true           
System.out.println(str4 == str5);//false
```

尽量避免多个字符串拼接，因为这样会重新创建对象。如果需要改变字符串的话，可以使用 StringBuilder 或者 StringBuffer。

**String s1 = new String("abc");这句话创建了几个字符串对象？**

将创建 1 或 2 个字符串。如果池中已存在字符串常量“abc”，则只会在堆空间创建一个字符串常量“abc”。如果池中没有字符串常量“abc”，那么它将首先在池中创建，然后在堆空间中创建，因此将创建总共 2 个字符串对象。

- 验证：

```java
String s1 = new String("abc");// 堆内存的地址值         
String s2 = "abc";        
System.out.println(s1 == s2);// 输出 false,因为一个是堆内存，一个是常量池的内存，故两者是不同的。         
System.out.println(s1.equals(s2));// 输出 true
a=a+b与a+=b
a+=b,会进行隐式的自动类型转换
a=a+b,不会进行隐式的类型转换
```

