参考https://juejin.im/post/5a4379d4f265da432003874c



==判断两个引用是不是指向同一个对象

equals用来判断两个对象的值/内容是否相等

### 无需覆写equals的情况

- **类的每个实例本质上是唯一的**：强调活动实体的而不关心值得，比如Thread，我们在乎的是哪一个线程，这时候用equals就可以比较了。
- **不关心类是否提供了逻辑相等的测试功能**：有的类的使用者不会用到它的比较值得功能，比如Random类，基本没人会去比较两个随机值吧
- **超类已经覆盖了equals，子类也只需要用到超类的行为**：比如AbstractMap里已经覆写了equals，那么继承的子类行为上也就需要这个功能，那也不需要再实现了。
- **类是私有的或者包级私有的，那也用不到equals方法**：这时候需要覆写equals方法来禁用它：`@Override public boolean equals(Object obj) { throw new AssertionError();}`



### 覆写equals的准则

#### 五准则

- **自反性**：对于任何非空引用值 x，x.equals(x) 都应返回 true。

- **对称性**：对于任何非空引用值 x 和 y，当且仅当 y.equals(x) 返回 true 时，x.equals(y) 才应返回 true。

- **传递性**：对于任何非空引用值 x、y 和 z，如果 x.equals(y) 返回 true， 并且 y.equals(z) 返回 true，那么 x.equals(z) 应返回 true。

- **一致性**：对于任何非空引用值 x 和 y，多次调用 x.equals(y) 始终返回 true 或始终返回 false， 前提是对象上 equals 比较中所用的信息没有被修改。

- **非空性**：对于任何非空引用值 x，x.equals(null) 都应返回 false。

可以简单地理解为，**在非空引用的条件下**：

- **自反性**：自己跟自己比较，返回true

- **对称性**：只有y.equals(x)返回true时，x.equals(y)才应该返回true

- **传递性**：x与y equals相等，y与z相等，则x.equals(z)应该返回true

- **一致性**：只要内容没有修改，则x.equals(y)应始终返回true/false，不能第一次true第二次就false了

- **非空性**：x.equals(null) 返回 false

#### 覆盖equals时一定要覆盖hashCode

#### equals函数里面一定要是Object类型作为参数

#### equals方法本身不要过于智能，只要判断一些值相等即可



#### 违反对称性和传递性的例子

##### 违反对称性

比如类StrTest重写了equals方法，实现了类似String的equalsIgnoreCase()的功能

```java
StrTest strTest = new StrTest("Case");
strTest.equals("case");	//true
"case".equals(strTest);	//false
```

所以有个准则，一般在覆写equals**只兼容同类型的变量**。

**违反传递性**

比如有父类Cat，子类ColorCat，父类有height和weight两种属性，子类多了一个color属性，且子类在与父类比较时，因为父类没有定义color所以只比较height和weight，相同则返回true，子类互相比较时会比较color。则：

```java
ColorCat whiteCat = new ColorCat(1,2,"white");
Cat cat = new Cat(1,2);
ColorCat blackCat = new ColorCat(1,2,"black");

whiteCat.equals(cat);	//true
cat.equals(blackCat);	//true
whiteCat.equals(blackCat);	//false
```

这里同样违反了只兼容同类型变量的准则，因而违反了五大准则，所以覆写equals时一定要遵守五大准则。



### 覆写equals的诀窍

- **使用==操作符检查“参数是否为这个对象的引用”**：如果是对象本身，则直接返回
- **使用instanceof操作符检查“参数是否是正确的类型”**：只兼容同类型的变量或该类实现的接口的类型，比如Set、List、Map这些集合接口。
- **把参数转化为正确的类型**： 将y与x转换成相同的类型进行比较
- **对于该类中的“关键域”，检查参数中的域是否与对象中的对应域相等**：基本类型的域就用`==`比较，float域用Float.compare方法，double域用Double.compare方法，至于别的引用域，我们一般递归调用它们的equals方法比较，加上判空检查和对自身引用的检查，一般会写成这样：`(field == o.field || (field != null && field.equals(o.field)))`,如String转换成char数组，把数组中的每一位拿出来比较。
- **编写完成后思考是否满足上面提到的对称性，传递性，一致性等等**。



### hashCode的作用

hashCode用于返回对象的hash值，主要用于查找的快捷性，因为hashCode也是在Object对象中就有的，所以所有Java对象都有hashCode，在HashTable和HashMap这一类的散列结构中，都是通过hashCode来查找在散列表中的位置的。

```java
HashSet hs = new HashSet();
hs.add(new Student(1, "zhangsan"));
hs.add(new Student(1, "zhangsan"));	//都可以放进去
```

不重写hashCode时，因为new出来的对象都是彼此不同的，使用hashcode()对两个对象进行比较时生成的hashcode也是不同的，所以hashset把他们当作了不同的对象，此时equals也返回false。

重写后，使同样的值生成同样的hashcode，如 num * str.hashcode()，此时hashset就不再添加重复值。

**如果两个对象equals，那么它们的hashCode必然相等，但是hashCode相等，equals不一定相等。**

以HashMap为例，使用的是链地址法来处理散列，假设有一个长度为8的散列表

```
0 1 2 3 4 5 6 7
```

那么，当往里面插数据时，是以hashCode作为key插入的，一般hashCode%8得到所在的索引，如果所在索引处有元素了，则使用一个链表，把多的元素不断链接到该位置，这边也就是大概提一下HashMap原理。所以hashCode的作用就是找到索引的位置，然后再用equals去比较元素是不是相等，形象一点就是先找到桶（bucket），然后再在里面找东西。



### 覆写hashCode的诀窍

一个好的hashCode的方法的目标：**为不相等的对象产生不相等的散列码**，同样的，相等的对象必须拥有相等的散列码。

好的散列函数要把实例均匀的分布到所有散列值上，结合前人的经验可以采取以下方法：

> 引自Effective Java

1. 把某个非零的常数值，比如17，保存在一个int型的result中；

2. 对于每个关键值f（equals方法中设计到的每个值），做以下操作：

   **a**. 为该值计算int类型的散列码；

   1. 如果该值是boolean类型：则计算(f?1:0)
   2. byte,char,short或者int类型：计算(int)f
   3. long类型：计算(int)(f^(f>>>32))
   4. float类型：计算Float.floatToIntBits(f)
   5. double类型：计算Double.doubleToLongBits(f),然后再计算long型的hash值
   6. 对象引用：递归的调用值的hashCode，如果是更复杂的比较，则需要为这个值计算一个范式，然后针对范式调用hashCode，如果为null，返回0
   7. 数组：把每一个元素当成一个单独的域来处理。

   **b**.result = 31 * result + c;

3. 返回result

4. 编写单元测试验证有没有实现所有相等的实例都有相等的散列码。

这里再说下2.b中为什么采用`31*result + c`,乘法使hash值依赖于域的顺序，如果没有乘法那么所有顺序不同的字符串String对象都会有一样的hash值，而31是一个奇素数，如果是偶数，并且乘法溢出的话，信息会丢失，31有个很好的特性是`31*i ==(i<<5)-i`,即2的5次方减1，虚拟机会优化乘法操作为移位操作的。