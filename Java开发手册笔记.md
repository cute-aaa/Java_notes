Object 的 equals 方法容易抛空指针异常，应使用常量或确定有值的对象来调用
equals。
正例："test".equals(object);
反例：object.equals("test");



所有整型包装类对象之间值的比较，全部使用 equals 方法比较。
说明：对于 Integer var = ? 在-128 至 127 范围内的赋值，Integer 对象是在 IntegerCache.cache 产
生，会复用已有对象，这个区间内的 Integer 值可以直接使用==进行判断，但是这个区间之外的所有数
据，都会在堆上产生，并不会复用已有对象，这是一个大坑，推荐使用 equals 方法进行判断。



浮点数之间的等值判断，基本数据类型不能用==来比较，包装数据类型不能用
equals 来判断。
说明：浮点数采用“尾数+阶码”的编码方式，类似于科学计数法的“有效数字+指数”的表示方式。二进制无法精确表示大部分的十进制小数，具体原理参考《码出高效》。
反例：

```
 float a = 1.0f - 0.9f;
 float b = 0.9f - 0.8f;
 if (a == b) {
     // 预期进入此代码快，执行其它业务逻辑
     // 但事实上 a==b 的结果为 false
 }
 Float x = Float.valueOf(a);
 Float y = Float.valueOf(b);
 if (x.equals(y)) {
     // 预期进入此代码快，执行其它业务逻辑
     // 但事实上 equals 的结果为 false
 } 
```

正例：
(1) 指定一个误差范围，两个浮点数的差值在此范围之内，则认为是相等的。

```
 float a = 1.0f - 0.9f;
 float b = 0.9f - 0.8f;
 float diff = 1e-6f;
 if (Math.abs(a - b) < diff) {
 	System.out.println("true");
 }
```

(2) 使用 BigDecimal 来定义值，再进行浮点数的运算操作。

```
 BigDecimal a = new BigDecimal("1.0");
 BigDecimal b = new BigDecimal("0.9");
 BigDecimal c = new BigDecimal("0.8");
 BigDecimal x = a.subtract(b);
 BigDecimal y = b.subtract(c);
 if (x.equals(y)) {
 	System.out.println("true");
 }
```





为了防止精度损失，禁止使用构造方法 BigDecimal(double)的方式把 double 值转
化为 BigDecimal 对象。
说明：BigDecimal(double)存在精度损失风险，在精确计算或值比较的场景中可能会导致业务逻辑异常。
如：BigDecimal g = new BigDecimal(0.1f); 实际的存储值为：0.10000000149
正例：优先推荐入参为 String 的构造方法，或使用 BigDecimal 的 valueOf 方法，此方法内部其实执行了
Double 的 toString，而 Double 的 toString 按 double 的实际能表达的精度对尾数进行了截断。

```java
 BigDecimal recommend1 = new BigDecimal("0.1");
 BigDecimal recommend2 = BigDecimal.valueOf(0.1);
```





关于基本数据类型与包装数据类型的使用标准如下：
1） 【强制】所有的 POJO 类属性必须使用包装数据类型。
2） 【强制】RPC 方法的返回值和参数必须使用包装数据类型。
3） 【推荐】所有的局部变量使用基本数据类型。
说明：POJO 类属性没有初值是提醒使用者在需要使用时，必须自己显式地进行赋值，任何 NPE 问题，或
者入库检查，都由使用者来保证。
正例：数据库的查询结果可能是 null，因为自动拆箱，用基本数据类型接收有 NPE 风险。
反例：比如显示成交总额涨跌情况，即正负 x%，x 为基本数据类型，调用的 RPC 服务，调用不成功时，
返回的是默认值，页面显示为 0%，这是不合理的，应该显示成中划线。所以包装数据类型的 null 值，能
够表示额外的信息，如：远程调用失败，异常退出。



【强制】定义 DO/DTO/VO 等 POJO 类时，不要设定任何属性默认值。
反例：POJO 类的 createTime 默认值为 new Date()，但是这个属性在数据提取时并没有置入具体值，在
更新其它字段时又附带更新了此字段，导致创建时间被修改成当前时间。





构造方法里面禁止加入任何业务逻辑，如果有初始化逻辑，请放在 init 方法中。





禁止在 POJO 类中，同时存在对应属性 xxx 的 isXxx()和 getXxx()方法。
说明：框架在调用属性 xxx 的提取方法时，并不能确定哪个方法一定是被优先调用到。





18.【推荐】当一个类有多个构造方法，或者多个同名方法，这些方法应该按顺序放置在一起，
便于阅读，此条规则优先于下一条。
19.【推荐】 类内方法定义的顺序依次是：公有方法或保护方法 > 私有方法 > getter / setter
方法。
说明：公有方法是类的调用者和维护者最关心的方法，首屏展示最好；保护方法虽然只是子类关心，也可能是“模板设计模式”下的核心方法；而私有方法外部一般不需要特别关心，是一个黑盒实现；因为承载的信息价值较低，所有 Service 和 DAO 的 getter/setter 方法放在类体最后。





循环体内，字符串的连接方式，使用 StringBuilder 的 append 方法进行扩展。
说明：下例中，反编译出的字节码文件显示每次循环都会 new 出一个 StringBuilder 对象，然后进行
append 操作，最后通过 toString 方法返回 String 对象，造成内存资源浪费。
反例：

```
String str = "start"; 
for (int i = 0; i < 100; i++) { 
str = str + "hello"; 
} 
```





final 可以声明类、成员变量、方法、以及本地变量，下列情况使用 final 关键字：
1） 不允许被继承的类，如：String 类。
2） 不允许修改引用的域对象。
3） 不允许被覆写的方法，如：POJO 类的 setter 方法。
4） 不允许运行过程中重新赋值的局部变量。
5） 避免上下文重复使用一个变量，使用 final 可以强制重新定义一个变量，方便更好地进行重构。





类成员与方法访问控制从严：
1） 如果不允许外部直接通过 new 来创建对象，那么构造方法必须是 private。
2） 工具类不允许有 public 或 default 构造方法。
3） 类非 static 成员变量并且与子类共享，必须是 protected。
4） 类非 static 成员变量并且仅在本类使用，必须是 private。
5） 类 static 成员变量如果仅在本类使用，必须是 private。
6） 若是 static 成员变量，考虑是否为 final。
7） 类成员方法只供类内部调用，必须是 private。
8） 类成员方法只对继承类公开，那么限制为 protected。
说明：任何类、方法、参数、变量，严控访问范围。过于宽泛的访问范围，不利于模块解耦。思考：如果是一个 private 的方法，想删除就删除，可是一个 public 的 service 成员方法或成员变量，删除一下，不得手心冒点汗吗？变量像自己的小孩，尽量在自己的视线内，变量作用域太大，无限制的到处跑，那么你会担心的。





使用集合转数组的方法，必须使用集合的 toArray(T[] array)，传入的是类型完全一
致、长度为 0 的空数组。
反例：直接使用 toArray 无参方法存在问题，此方法返回值只能是 Object[]类，若强转其它类型数组将出现 ClassCastException 错误。
正例：

```
List<String> list = new ArrayList<>(2);
list.add("guan");
list.add("bao");
String[] array = list.toArray(new String[0]);
```

 说明：使用 toArray 带参方法，数组空间大小的 length：
1） 等于 0，动态创建与 size 相同的数组，性能最好。
2） 大于 0 但小于 size，重新创建大小等于 size 的数组，增加 GC 负担。
3） 等于 size，在高并发情况下，数组创建完成之后，size 正在变大的情况下，负面影响与上相同。
4） 大于 size，空间浪费，且在 size 处插入 null 值，存在 NPE 隐患。





在使用 Collection 接口任何实现类的 addAll()方法时，都要对输入的集合参数进行
NPE 判断。
说明：在 ArrayList#addAll 方法的第一行代码即 Object[] a = c.toArray(); 其中 c 为输入集合参数，如果
为 null，则直接抛出异常。





使用工具类 Arrays.asList()把数组转换成集合时，不能使用其修改集合相关的方
法，它的 add/remove/clear 方法会抛出 UnsupportedOperationException 异常。
说明：asList 的返回对象是一个 Arrays 内部类，并没有实现集合的修改方法。Arrays.asList 体现的是适
配器模式，只是转换接口，后台的数据仍是数组。

```
 String[] str = new String[] { "yang", "hao" };
 List list = Arrays.asList(str);
```

第一种情况：list.add("yangguanbao"); 运行时异常。
第二种情况：str[0] = "changed"; 也会随之修改，反之亦然。