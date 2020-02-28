### Java反射笔记

反射是Java的特征之一，它允许运行中的Java程序获取自身的信息，并可以操作类或对象的内部属性。

通过反射，可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。程序中一般的对象的类型都是在编译期就确定下来的，而 Java 反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，即使这个对象的类型在编译期是未知的。

反射的核心是JVM在运行时才动态加载类或调用方法/访问属性，它不需要事先（写代码的时候或编译期）知道运行对象是谁。

Java 反射主要提供以下功能：

- 在运行时判断任意一个对象所属的类；

- 在运行时构造任意一个类的对象；

- 在运行时判断任意一个类所具有的成员变量和方法（通过反射甚至可以调用private方法）；

- 在运行时调用任意一个对象的方法

  重点：**是运行时而不是编译时**

### 反射的主要用途：

如IDE中，输入一个对象并想调用它的属性或方法时，编译器会自动列出他的属性或方法，这里就会用到反射。

**反射的最重要的用途就是开发各种通用框架** 很多框架都是配置话的（比如Spring通过XML文件配置Bean），为了保证框架的通用性，他们可能需要根据配置文件加载不同的类或对象，调用不同的方法，这时候就必须要用到反射，运行时动态加载需要加载的对象。

如：在 `struts.xml` 里去配置 `Action`

```xml
	   <action name="login"
               class="org.ScZyhSoft.test.action.SimpleLoginAction"
               method="execute">
           <result>/shop/shop-index.jsp</result>
           <result name="error">login.jsp</result>
       </action>
```

配置文件与action建立的一种映射关系，当view层发出请求时，请求会被filter拦截，然后filter会去动态地创建action实例，比图我们请求loginl.action，那么filter就会去解析struct.xml文件，检索action中name为login的action，并根据class属性创建SimpleLoginAction实例，并用invoke方法来调用execute方法，这个过程离不开反射。

## 反射的使用

#### 获取Class对象：

方法有三种：

1.  **直接获取某个对象的class:**

    ```java
    Class<?> class = int.class;
    Class<?> classInt = Integer.TYPE;
    ```

2.  **使用Class类的 forName 静态方法：**

    ```java
    函数原型：public static Class<?> forName(String className)
    使用：Class.forName(driver);
    ```

3.  **调用某个对象的 getClass() 方法：**

    ```java
    StringBuilder str = new StringBuilder("123");
    Class<?> class = str.getClass();
    ```

**区别：**

​	".class"不执行操作，"forName"将类加载到内存（执行静态方法），"getClass"将类加载到内存并实例化对象





F:\Projects\Java\HelloWorld\applicationClientModule\com\ReflectTest.java