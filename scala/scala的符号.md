### 1. <- 

用于：

- 迭代

```scala
scala> val arr = Array(1,2,3)
arr: Array[Int] = Array(1, 2, 3)

scala> for(a <- arr) { println(a) }
1
2
3

scala> for(i <- 1 to 3) { println(i) }
1
2
3

scala> for (i <- 1 to 3) {
     |   for (j <- 1.to(3)) {
     |     println(i * j)
     |   }
     | }
1
2
3
2
4
6
3
6
9
// 比较高级的多层嵌套for循环
scala> for (i <- 1 to 3; j <- 1 to 3) { println(i*j) }
1
2
3
2
4
6
3
6
9

```

### 2. ->

用于map创建键值对

```scala
scala> val m:Map[String, Int] = Map("a" -> 1, "b" -> 2)
m: Map[String,Int] = Map(a -> 1, b -> 2)

scala> val b = Map("a" -> 1, "b" -> 2)
b: scala.collection.immutable.Map[String,Int] = Map(a -> 1, b -> 2)
```

### 3. =>

用于：

- Scala的匿名函数
- 模式匹配



```scala
// 匿名函数
scala> val f = (x:Int, y:Int) => x + y
f: (Int, Int) => Int = <function2>

scala> f(1,2)
res50: Int = 3

// or
scala> val l = List(1,2,3)
l: List[Int] = List(1, 2, 3)

scala> l.map(x => x * 2)
res51: List[Int] = List(2, 4, 6)

// 模式匹配
scala> val b = Map("a" -> 1, "b" -> 2)
b: scala.collection.immutable.Map[String,Int] = Map(a -> 1, b -> 2)

scala> def mt(o: Option[Int]) = {
     |   o match {
     |     case Some(o) => o
     |     case None => "None"
     |   }
     | }
mt: (o: Option[Int])Any

scala> mt(b.get("a"))
res56: Any = 1

scala> mt(b.get("x"))
res57: Any = None
```



### 4 :Type= {}

用于定义函数返回值, 如 `def max(x: Int) :Int = { // ... }`

![image-20190409215740343](http://limbo.oss-cn-beijing.aliyuncs.com/2019-04-09-135740.png)

### 5 _

- 作为“通配符”，类似Java中的`*`。如`import scala.math._`
- `:_*`作为一个整体，告诉编译器你希望将某个参数当作参数序列处理, 例如val s = sum(1 to 5:_*)就是将1 to 5当作参数序列处理。
- 指代一个集合中的每个元素
- 在元组中，可以用方法_1, _2, _3访问组员

```scala
// 例如我们要在一个Array l 中筛出偶数，并乘以2，可以用以下办法：
scala> val l = List(1,3,4,6,7,8)
l: List[Int] = List(1, 3, 4, 6, 7, 8)

scala> l.filter(_ % 2 == 0).map(2 * _)
res70: List[Int] = List(8, 12, 16)

// 累加
val l = List(1, 3, 5, 11, -1, -3, -5)
l.foldLeft(0)(_ + _) // same as l.foldLeft(0)((a,b) => a + b)
```

当`_`用于参数占位符时，`_`不仅能表示单个参数，也能表示整个参数列表

```scala
scala> val largest = (Integer.MIN_VALUE /: arr) {Math.max(_, _)}
largest: Int = 30

scala> val largest = (Integer.MIN_VALUE /: arr) {Math.max _}
largest: Int = 30

val largest = (Integer.MIN_VALUE /: arr) {Math.max}
```

> `_`表示整个参数列表，也就是(**参数**`1`**, 参数**`2`)。如果只是为了按照同样的顺序将接收到的参数传递给底层的方法，我们甚至不需要`_`这种形式。 

《Scala实用指南》总结：

![image-20190513135403716](http://limbo.oss-cn-beijing.aliyuncs.com/2019-05-13-055404.png)

很屌呀 .....太多魔术了 .. 

### 6.可选项

```scala
scala> class Counter {
     |   private var value = 0
  				// 改值器
     |   def inc() { value += 1}
     |   def dec() = { value -= 1}
          // 取值器
     |   def cur() = value
     | }
defined class Counter

// 约定，对于改值器方法（即改变对象状态的方法）用()，而对于取值器方法（不改变对象状态的方法）去掉()

scala> val c = new Counter
c: Counter = Counter@28131d33

scala> c.inc()

scala> c.inc

scala> c.dec

scala> c.cur
res80: Int = 1
```

>- 调用无参数方法或创建无参数对象时，可以写圆括号，也可不写
>- 方法返回值如果不写默认为unit

```scala
scala> def inc() {}
inc: ()Unit

scala> def inc() = {}
inc: ()Unit

scala> def inc():Unit = {}
inc: ()Unit

// 如果方法体只有一个表达式或值，则{}可省略
scala> def inc() {print("..")}
inc: ()Unit

scala> def inc() = print("...")
inc: ()Unit
```

## 7./:

`/:` 等效于`foldLeft`

在scala源码中可以看到

![image-20190511141041084](http://limbo.oss-cn-beijing.aliyuncs.com/2019-05-11-061129.png)

如下求最大值

```scala
scala> val arr = Array(1,30,2,23)
arr: Array[Int] = Array(1, 30, 2, 23)

scala> val sum = arr.foldLeft(0) { (sum, elem) => sum + elem }
sum: Int = 56

// 使用(0 /: arr) 代替 arr.foldLeft(0)
scala> val sum = (0 /: arr) { (sum, elem) => sum + elem }
sum: Int = 56
// 还可以是()
scala> val sum = (0 /: arr) ( (sum, elem) => sum + elem )
sum: Int = 56

// 使用参数法占位符更加简洁
// 在单个变量只出现一次时, 用 _ 更加合适， Python, Golang都是这样设计的
scala> val sum = (0 /: arr) ( _ + _ )
sum: Int = 56
```

在kafka源码中我们应该看到这样的用法：

```scala
//kafka.api.MockTime.scala
// make sure that sum of all partitions to all consumers equals total number of partitions
val totalPartitionsInAssignments = (0 /: assignments) (_ + _.size)
```

上面关于`foldLeft` 用法

![image-20190511141240184](http://limbo.oss-cn-beijing.aliyuncs.com/2019-05-11-061241.png)

*图片来源:<https://blog.csdn.net/qq_29677083/article/details/84436462>*



## 8 :\

`:\` 是 foldRight用法

## More ...

- [scala中的奇怪的符号](https://www.jianshu.com/p/f22b8dee0dec) 补充了一些其他的符合
- [Scala 特殊符号操作符汇总](https://notes.mengxin.science/2018/09/07/scala-special-symbol-usage/)


