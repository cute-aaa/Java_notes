# Java参数传递——值传递还是址传递？

## 首先是变量的存储方式

各种变量的存储方式：



**基本数据类型：**

```java
int age = 20;
```

变量名age和值20都存储在jvm栈中





**引用数据类型：**

字符串见 https://blog.csdn.net/oooo2316/article/details/103298996 

懒得看的话：字符串常量存储在堆的常量池中，字符串对象存储在堆的非常量池中



对象：

对象本身存储在堆中，包含了对象的成员变量和成员函数。

![image-20191128190900501](D:/Typora/imgs/Java参数传递——值传递还是址传递？/image-20191128190900501.png)

对象的地址x0123作为引用，赋值给对象变量obj。

因此，可以将x0123看作obj的值，这样一来，obj的变量名和值都是存储在jvm栈中了，只不过它的值是对象的地址。

程序运行时调用main方法，于是将mian方法的各种东西压入栈桢。

现在，假设main中新建了**基本类型变量a和对象类型变量b**，对象b中包含一个基本类型变量 `i` 。

```java
int a = 5;
B b = new B();
b.setI(10);
```

还有一个可以将传入的 `a` 进行平方的方法 `square(int a)` 和将 `b.i` 进行平方的方法 `square(B b)` 。

```java
void squareTest(int a){
	a = a*a;
}

void squareTest(B obj){
	obj.setI(obj.getI() * obj.getI());
}
```

然后调用square()方法，把a和b传入，

调用square()时，jvm再将square()方法压入栈帧，并**从局部变量表中找出a和b的值复制一份**过去。

而**a是基本数据类型，b是引用数据类型**

这样就导致，**a传过去的是变量a的真实值，而b传过去的是b在jvm栈中的值，也就是b引用的对象的地址。**

细心的小伙伴可能已经发现了：

在square()方法中，a的值被复制过来了，方法中操作的是a的克隆，对克隆a的操作是不会影响原来的a的； 

而b复制过来的是地址，相当于C++中的址传递，对 `b` 进行操作时，发现它是一个地址，然后就顺着地址，找到了藏在堆中的源对象，并更改了 `b.i` 的值，可以看出，main函数和square()函数操作的是同一个地址，所以在square()中的更改在main函数中也可以看到。

```java
int a = 5;
B b = new B();
b.setI(10);

//传入值
test.squareTest(a);
System.out.println(a);  //5
test.squareTest(b.getI());
System.out.println(b.getI());   //10

//传入对象
test.squareTest(b);
System.out.println(b.getI());   //100
```

运行结果：

![image-20191128212603941](D:/Typora/imgs/Java参数传递——值传递还是址传递？/image-20191128212603941.png)

如果我们把 square(B b)方法改动一下：

```java
void squareTest(B obj){
    obj = new B();
	obj.setI(obj.getI() * obj.getI());
}
```

执行 square() 方法之后main方法中的 `b.i` 的值会不会变呢？

运行结果：

![image-20191128212526915](D:/Typora/imgs/Java参数传递——值传递还是址传递？/image-20191128212526915.png)



这是因为，在更改之后的 square() 方法中，更改了变量指向的引用，从堆中新建了一个B对象，并把它的地址赋给了 square() 方法中的obj变量，但obj变量只是main函数中b变量的拷贝，所以b变量仍然指向原来的地址。

就像你又一个情敌，后来执行 `obj = new B()` 之后情敌喜欢别的人了，而你还是喜欢原来的。

![image-20191128214336035](D:/Typora/imgs/Java参数传递——值传递还是址传递？/image-20191128214336035.png)



当然， square() 执行完之后情敌和他喜欢的人都挂了。





如果将传入的对象交换呢：

```java
void swapB(B a, B b){
    B temp = a;
    a = b;
    b = temp;
}
```

main中的引用也不会变，就像main中的m，n，情敌分别是a和b，后来a和b互换了喜欢的人，但m和n仍然是喜欢原来的。此时变成了a和n是情敌，b和m是情敌。



