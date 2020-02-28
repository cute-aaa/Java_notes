**结论：**

- 字符串常量存储在堆的常量池中，字符串对象存储在堆的非常量池中
- 除了字符串对象之外，其他字符串都存储在常量池中。
- 字符串对象相加产生另一个字符串对象。



**字符串：**

```java
String name = "hello";
```

变量名name存储在jvm栈，值"hello"存储在堆中的常量池中



**字符串对象：**

```java
String name2 = new String("hello");
```

变量名name2存储在jvm栈，值"hello"存储在堆的非常量池中，并且每个new出来的String对象的地址都是不同的



使用代码查看他们的地址是否相同：

```java
String str1 = "abc";
String str2 = new String("abc");
System.out.println("常量与对象：" + str1 == str2);

//false
```



不过，可以把字符串对象的值从堆中挪到常量池中：

```java
str2 = str2.intern();
System.out.println("字符串与intern后的字符串对象：" + (str1 == str2));

//true
```



**其他字符串产生方式：**

还有各种其他方式产生的字符串：

字符串常量（final）、静态字符串（static）、字符串相加、字符串对象相加、未引用的字符串、方法中的字符串等

```java
final String str3 = "abc";
System.out.println("字符串与字符串常量：" + (str1 == str3));

//true
```

```java
System.out.println("未引用字符串相加与常量：" + ("ab" + "c" == str3));

//true
```

```java
System.out.println("未引用字符串与字符串常量：" + ("abc" == str3));

//true
```

```java
System.out.println("未引用字符串与引用字符串：" + ("abc" == str1));

//true
```

```java
static final String str4 = "abc";	//类中定义

System.out.println("未引用字符串与静态字符串：" + ("abc" == str4));	//main中对比

//true
```

```java
void returnAbc(){
	String str5 = "abc";
	System.out.println("方法中的字符串与静态字符串：" + (str5 == str4));
}
```



还有个特殊的，字符串对象相加：

```java
String ab = new String("ab");
String c = new String("c");
String abc = ab + c;
System.out.println("对象相加与字符串：" + (abc == str1));
System.out.println("对象相加与对象：" + (abc == str2));

//false
//false
```

可以看到，相加之后的对象与在常量池中的str1地址不同，所以他也是个字符串对象。

前面说到，字符串对象的地址都是不同的，因此跟另一个字符串对象str2对比结果也是false。



如果让相加产生的字符串对象也调用intern方法，再与intern后的str2对比：

```java
abc = abc.intern();
System.out.println("intern后的两个对象：" + (str2 == abc));

//true
```

得出结论：

字符串常量存储在堆的常量池中，字符串对象存储在堆的非常量池中 

除了字符串对象之外，其他字符串都存储在常量池中。

字符串对象相加产生另一个字符串对象。



结果图：

![image-20191128184805811](D:/Typora/imgs/Java字符串的存储方式/image-20191128184805811.png)



