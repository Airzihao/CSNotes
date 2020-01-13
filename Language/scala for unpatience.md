## 第2章控制结构和函数

### 2.1 条件表达式
```
if(x>0) 1 else -1
val s = if(x>0) 1 else -1
if(x>0) 1 else ()
```

### 2.2 语句终止

分号，同一行写多条语句时必须有，其他情况可有可无。

### 2.3 块表达式和赋值
{}块包含一系列表达式，其结果也是一个表达式。块中最后一个表达式的值就是块的值。
```
val distance = {val dx=x-x0; val dy=y-y0; sqrt(dx*dx + dy*dy)}
```
scala中赋值动作本身返回值是，Unit

### 2.4 输入和输出
输出：
```
print()
println()

//插值器：
printf(f"Hello, $name! In six months, you'll be ${age + 0.5}%7.2f years old.%n)
```
输入：
```
import scala.io
val name = StdIn.readLine("Your name: ")

//其他方法：
readInt(),readDouble()等
```

### 2.5循环
Scala有while没有for。
```
// 下式让i遍历表达式的所有值
for(i <- 表达式)

for(i <- 0 to s.length-1)
for(ch <- "Hello")
```

### 2.6 高级for循环
```
//多个生成器
for(i <- 1 to 3; j <- 1 to 3) println(i+j)

//每个生成器都可以带守卫
for(i <- 1 to 3; j <- 1 to 3 if i!=j) prinln(i+j)

//for推导式
for(i <- 1 to 10) yield i%3
//交出 Vector（1,2,0,1,2,0,1,2,0,1）
```
另：上述生成器、守卫和定义也可写在花括号中。

### 2.7 函数
```
def abs(x: Double) = if (x>=0) x else -x

//对于递归函数，必须指定返回类型。
def fac(n:Int): Int = if(n<=0) 1 else n*fac(n-1)
```

Scala中不提倡使用return。

### 2.8 默认参数和带名参数

带名参数不需要跟参数列表的顺序完全一致。

### 2.9 变长参数
```
def sum(args: Int*) = {
    val result = 0;
    for(arg <- args) result += arg
    result
}

//加上 _* 告诉编译器希望这个参数被当作参数序列使用
val s = sum(1 to 5: _*) 
```

### 2.10 过程
如果函数体包含在花括号中且没有前边的=号，那么返回类型就是Unit，这样的函数被称为过程（procudure）。

```
def box(s: String) = {
    ...
}
//可以显式地声明Unit返回类型。
def box(s: Stromg): Unit = {
    ...
}
```


### 2.11 懒值
在变量首次被调用时取值：

```
lazy val words = scala.io.Source.fromFile("./words").mkString
```
lazy val对于开销较大的初始化语句比较有用。


### 2.12 异常
```
if (x>=0) {
    sqrt(x)
} else throw new IllegalArgumentException("x should not be negative")
```
更通用的异常要放在更具体的异常之后。

try/finally语句可以释放资源，不论有没有异常发生。finally语句不论是否抛出异常都会被执行。


## 第3章. 数组相关操作 
##### 要点：
- Array VS. ArrayBuffer
- 提供具体初始值时不使用new
- 用()访问元素
- for (elem <- arr)
- for (elem <- arr if ...) yield ... #将原数组转型为新数组

### 3.1 定长数组
```
val nums = new Array[Int](10)
val a = new Array[String](10)
val a = Array("Hello","world")
s(0) = "hello"
```

### 3.2 变长数组：数组缓冲
```
import scala.collection.mutable.ArrayBuffer
val b = ArrayBuffer[Int]()
b += 1 //在结尾追加元素
b += (1,2,3) //在尾端添加多个元素
b ++= Array(8,13,21) //在尾端追加集合
b.trimEnd(2) //移除最后两个元素
b.insert(2,6,7,8) //在b(2)插入6，7，8
b.insert(2) //移除b(2)
b.insert(2,3) //从b(2)开始，移除3个元素
b.toArray //将b转换为数组
a.toBuffer //将数组a转换为数组缓冲
```

### 3.3 遍历Array和ArrayBuffer
```
for (i <- 0 until a.length) println(a(i))
for (i <- 0 until a.length by 2) print(a(i) //两个元素一跳
for (elem <- a)
0 until a.length by -1 //从数组的尾端开始
```

until和to方法很像，但是until方法会排除最后一个元素。    

### 3.4 数组转换
```
val a = Array(2,3,5,7,11)
val result = for(elem <- a) yield 2*elem
    // resule = (3,6,10,14,22)

for(elem <- a if elem % 2 == 0) yield 2*elem    
另一种做法：
a.filter(_ % 2 == 0).map(2 * _)
//或
a filter{_ % 2 == 0} map {2 * _}
```
for/yeild循环创建了一个类型与原始集合相同的新集合，并且不影响原始集合。

### 3.5 常用算法
```
val a = Array(1,2,3)
a.sum 
a.max
val aSorted = a.sorted //a的值并没有被改变
val aDescending = a.sortWith(_ > _)
scala.util.Sorting.quickSort(a) //快排
```

```
a.mkString(" and ") //将数组a字符串化，指定分隔
a.mkString("<",",",">") //指定前缀、分隔符、后缀
```  

### 3.7 多维数组
```
val matrix = Array.ofDim[Double](3,4) //三行四列
matrix(row)(column) = 42
```


## 第4章 映射和元组

### 4.1 构造映射
```
//不可变映射
val scores = Map("Alice"-> 10, "Bob"->3)
//可变映射
val scores = scala.collection.mutable.Map("Alice"-> 10, "Bob"->3)
```

### 4.2 获取映射中的值
```
//使用()表示法：如果该键不存在，则抛出异常。
val bobScore = scores("Bob") //类似于Java中的scores.get("Bob")
```
```
# contains方法
scores.contains("Bob")
//如下两个表达式等效
val bobScore = if(scores.contains("Bob")) scores("Bob") eles 0
val bobScore = scores.getOrElse("Bob",0)
```
```
映射.get($key)这样的方法返回一个Option对象，要么是Some（键对应的值），要么是None。
```

### 4.3 更新映射中的值
```
scores("Bob") = 10
scores += ("Bob" -> 10, "Fred" -> 7)
score -= "Alice" //移除某个k-v对
```
如果想修改一个不可变的映射，那就只能获取一个包含所需要的更新的新映射：
```
val newScores = scores + ("Bob" -> 10, "Fred" -> 7)
val newScores = scores - "Alice"
```

### 4.4 迭代映射
可以使用下边的方法迭代处理映射
```
for( (k,v) <- 映射) 处理kv

scores.keySet //返回一个包含所有key值的集合
scores.values //返回所有value的集合

for( (k,v) <- 映射>) yield (v,k) //反转一个映射
```

### 4.5 已排序映射
Scala中映射的实现有哈希表（默认）和平衡树两种。
```
//得到一个不可变的树形映射
val scores = scala.collection.immutable.SortedMap("Alice" -> 10, "Bob" -> 3)
```

### 4.6 与Java的互操作
相互转换：
```
// Java转Scala
import scala.collection.JavaConversion.mapAsScalaMap
val scores: scala.collection.mutable.Map[String, Int] = new java.util.TreeMap[String, Int]

//从Properties到Map[String,String]的转换
import scala.collection.JavaConversion.propertiesAsScalaMap
val props: scala.collection.mutable.Map[String, String] = System.getProperties()
```
```
//Scala 转 Java
import scala.collection.JavaConversion.mapAsJavaMap
import java.awt.font.TextAttribute._
val attrs = Map("FAMILY" -> "Serif")
val font = new java.awt.Font(attrs)
```

### 4.7 元组(Tuple)
元组是不同类型的值的聚集（同一类型都用数组了）
```
#元组下标从1开始，不是从0开始
val t = (1, 3.14, "red")
val second = t._2 //或： val second = t _2

//反过来赋值：
val (first, second, third) = t
val (first, second, _) = t
```
元组可以用于函数需要返回不止一个值的情况

### 4.8 拉链操作
```
val symbols = Array("<", "-", ">")
val counts = Array(2, 10 ,2)
val pairs = symbols.zip(counts)
//输出对偶数组：
Array(("<",2),("-",10),(">",2))
```
用toMap方法可以将对偶的集合转换成映射
```
keys.zip(values).toMap
```

## 第5章 类

### 5.1 简单类和无参方法
在Scala中类不声明为public。Scala源文件可以包含多个类，所有这些类都有公有可见性。
```
class Counter{
    private var value = 0
    def increment(){ value += 1}
    def current() = value
}

val myCounter = new Couner //或 new Counter()
myCounter.increment()
println(myCounter.current)
```
调用无参方法加不加括号都行，通常改值器方法加括号，取值器方法不加括号。

### 5.2 带getter和setter的属性
在Scala中，属性x的getter和setter方法分别叫做 x和x_ = 。

自定义getter和setter方法：
```
class Person{
    private var privateAge = 0
    
    def age = privateAge
    def age_ = (newValue: Int){
        if(newValue>privateAge) privateAge = newValue
    }
    
}
```
如果字段是私有的，则getter和setter方法也是私有的。

如果字段是val，则只有getter方法被生成。

### 5.3 只带getter的属性
在实现属性时，有如下四个选择：
1. var foo: Scala自动生成setter和getter
2. val foo: Scala自动生成一个getter
3. 自定义 foo和foo_= 方法
4. 自定义 foo方法

当在Scala中出现字段时，它默认是一个私有字段，加上了getter方法和setter方法。

### 5.4 对象私有字段
```
private[this] var value = 0 //访问someObject.value将不被允许
```
用private声明的字段可以用someObject.value的方法访问，用private[this]声明的字段则只可以被该对象访问。

### 5.5 Bean属性
```
class Person{
    @BeanProperty var name: String = _
}
```
将会生成四个方法：
1. name: String
2. name_: Unit
3. getName(): String
4. setName(newValue: String): Unit

### 5.6 辅助构造器
1. 辅助构造器的名称为this
2. 每一个辅助构造器必须以对一个先前已定义的辅助构造器或主构造器的调用开始

例：
```
class Person {
    private var name = ""
    private vat age = 0
    
    def this(name: String) {  //一个辅助构造器
        this()  //调用主构造器
        this.name = name
    }
    
    def this(name:String, age:Int) {
        this(name) //调用前一个辅助构造器
        this.age = age
    }
    
}

```

### 5.7 主构造器


1. 主构造器的参数直接放置在类名之后。
```
class Person(val name: String, val age: Int) {
    // ()中的内容就是主构造器的参数
}
```
2. 主构造器会执行类定义中的所有语句。


一个类如果没有显式定义主构造器，则自动拥有一个无参的主构造器。这样的一个构造器仅仅执行类体中的所有语句。

### 5.8 嵌套类
在类中可以定义类。要构建一个新的内部对象，只需要new这个类名。


## 第6章 对象
##### 本章要点：
1. 用对象作为单例或存放工具方法
2. 类可以拥有一个同名的伴生对象
3. 对象可以扩展类或特质
4. 对象的apply方法通常用来构造伴生类的新实例
5. 可以用扩展App特质的对象来避免显式定义main方法
6. 通过扩展Ebyneration对象来实现枚举。

### 6.1 单例对象
```
object Accounts{
    private var lastNumber=0;
    def newUniqueNumber() = {  lastNumber += 1;  lastNumber}
}
```
对象的构造器在该对象**第一次被使用时调用**。对象本质上可以拥有类的所有特性，甚至可以扩展其他类或特质，但是不能提供构造器参数。

### 6.2 伴生对象
既有实例方法又有静态方法的类/


## 第7章 包和引入
##### 本章要点：
1. 包也可以像内部类那样嵌套
2. 包路径不是绝对路径
3. 包声明链x.y.z不自动将中间包变为可见
4. 位于文件顶部不带花括号的包声明在整个文件范围内有效
5. 包对象可以持有函数和变量
6. 引入语句可以引入package,class,pbject
7. 引入语句可以出现在任何地方位置（但最好写在文件最上方）
8. 引入语句可以重命名和隐藏特定成员

### 7.1 包
同一个包可以定义在多个文件当中，也可以在同一个文件中为多个包贡献内存。

### 7.2 作用域规则

尽量避免用scala、java、com、org等名称来命名嵌套的包，防止发生歧义

### 7.3 串联式包语句
### 7.4 文件顶部标记法
常用的写法
package com.horstmann,impatient
package people

### 7.5 包对象
package 可以包含class、object和trait，但不能包含函数或变量的定义（这是Java虚拟机的局限）。

尽量把工具函数或常量添加到包而不是某个Utils对象，这样比较合理。

每个包都可以有一个包对象，需要在其父包中定义，且名称与子包一致，如：
```
package com.horstmann.impatient

package object people{
    val defaultName = "John"
}

package people {
    class Person {
        var name = defaultName //从包对象拿到的常量
    }
}
```
注意defaultName不需要加限定词，因为它们位于同一个包内。在其他位置，这个常量可以用完全路径(com.horstmann.impatient.people.defaultName)访问到。

包对象被编译成有静态方法和字段的JVM类，名为package.class，

### 7.6 包可见性

没有被声明为public、private或protected的类成员在包含该类的包中可见

以下方法在它自己的包中可见：
```
package com.horstmann.impatient.people

class Person{
    private[people] def description = "A person with name" + name
}
// 上边方法的可见度可以延展到上层包
class Person{
    private[impatient] def description = "A person with name" + name
}

```

### 7.7 引入
引入某个包的全部成员：
`import java.awt._`

引入语句可以在任何地方不仅限于文件顶部，import语句的效果一直延伸到**包含该语句的块末尾**。

如果想要引入包中的几个成员，可以使用selector：
`import java.awt.{Color, Font}`

此方法允许重命名选择的成员：
```
import java.util.{HashMap => JavaHashMap}
import scala.collection.mutable._
```
这样JavaHashMap对应java.util.HashMap，而HashMap则对应scala.collection.mutable.HashMap。

用 => _ 将某个成员隐藏：
```
import java.util.{HashMap => _, _}
import scala.collection.mutable._
```


## 第8章 继承
本章要点：
1. extends、final关键字与Java中相同。
2. 重写方法时必须用override
3. 只有主构造器可以调用超类的主构造器
4. 可以重写字段

### 8.1 扩展类
```
class Employee extends Person{
    val salary =0.0
}
```
在定义中给出子类需要而超类没有的字段和方法（或者重写）。

将类、方法或字段声明为final，它就不能被重写。（不同于Java，Java中final字段是不可变的。）

### 8.2 重写方法
重写非抽象方法必须使用override
```
public class Person{
    ...
    override def toString = getClass.getName + "[name=+ + name + "]"
}
```
override可在如下情况中给出有用的错误提示：
1. 拼错了要重写的方法名。
2. 在新方法中使用了错误的参数类型。
3. 在超类中引入了新的方法，这个方法与子类的方法相冲突。

上述第三点实际是**易违约基类问题**的体现：程序员甲写了基类A，程序员乙在甲不知情的情况下，定义了子类B，在B中实现了一个名为a的方法。后来甲又在A中添加了名为a的方法。当乙运行的时候，很可能出错。

### 8.3 类型检查和转换
isInstanceOf和asInstanceOf
```
if (p.isInstanceOf[Employee]){
    val s = p.asInstanceOfp[Employee] //s的类型为Employee
}
```

条件 |isInstanceOf | asInstanceOf
---|---|---
Employee类及其子类 | true | 正常
null | false | null
不是Employee | false | Execption

判断p指向的是一个Employee对象，且不是其子类：
```
if (p.getClass == classOf[Employee])
// classOf方法定义在scala.Predef对象中，自动引入。
```
更好的方法是使用模式匹配而非类型检查与转换：
```
p match{
    case s: Employee => ... // 将s作为Employee
    case _ => //p不是Employee
}
```

### 8.4 受保护字段和方法
1. protected成员可以被任何子类访问到（同Java）。
2. protedced成员对类所属的包成员不可见（不同于Java）。
3. protected[this]可以将访问权限限定在当前的对象。

### 8.5 超类的构造

类中每个辅助构造器都必须以对先前定义的辅助构造器或主构造器的调用开始。这样做就导致：辅助构造器**永远不可能**直接调用超类的构造器。子类的辅助构造器都会直接和间接地调用主构造器，只有主构造器可以调用超类的构造器。Scala的构造器中，不能调用super(param)。

主构造器和类定义结合在一起，子类调用超类构造器的方法也结合在一起，例如：
```
class Employee(name: String, age: Int, val salary: Double) extends Person(name, age)
// 这部分定义了一个子类，以及一个调用超类构造器的主构造器
```
同样功能的Java代码：
```
public class Employee extends Person {
    private double salary;
    public Employee(String name, int age, double salary){
        super(name,age);
        this.salary = salary;
    }
}
```

Scala类可以扩展Java类。这种情况下它的主构造器必须调用Java超类的某一个构造方法。如：
```
class Square(x: Int, y: Int, width: Int) extends java.awt.Rectangle(x,y,width,width)
```

### 8.6 重写字段
Scala的字段由一个private字段和getter/setter方法构成。可以用另一个同名的val字段重写一个val（或不带参数的def）。子类有一个私有字段和一个公有的getter方法，这个getter方法重写了超类的getter方法。


目的 | 用val | 用def | 用var
---|---|---|---|
重写val | 子类有一个私有字段（与超类的字段名字相同） | 错误 | 错误
重写def | - 子类有一个私有字段 - getter方法重写超类的方法 | 和Java一样 | var可以重写getter/setter对，但只重写getter会报错。
重写var | 错误 | 错误 | 仅当超类的var是抽象时才可以。

从上表中可以看出写var，影响扩展性。

### 8.7 匿名子类
可以用包含定义或重写代码块的方式创建一个匿名的子类。
```
val alien = new Person("Fred"){
    def greeting = "Greetings! My name is Fred."
}
```
上述对象将创建一个结构类型的对象，该类型标记为Person{def greeting: String}。可以使用如下类型作为参数类型的定义：
```
def meet(p: Person{def greeting: String}){
    println(p.name + "says: "+p.greeting)
}
```

### 8.8 抽象类
有些类不能实例化，因为某些方法暂不能完整定义，此时用abstract标记。
```
abstratc class Person(val name: String){
    def id: Int //没有方法体--这是一个抽象方法。
}
```
1. 如果某个类存在一个或多个抽象方法，必须将该类声明为abstract。（同Java）
2. 不需要对抽象方法加abstract关键字，省去方法体即可。（不同于Java）
3. 在子类中重写超类的抽象方法是，不需要使用override关键字。

### 8.9 抽象字段
抽象字段就是没有初始值的字段。
1. 对于val，默认有抽象的getter方法。
2. 对于var，默认有抽象的getter/setter方法。
3. 生成的Java类不带字段。

具体的子类中，必须提供具体的字段。在子类中重写超类中的抽象字段时，不需要override关键字。

### 8.9 构造顺序和提前定义
```
class Creature{
    val range: Int = 10
    val env: Array[Int] = new Array[Int](range)

}

class Ant extends Creature{
    override val range = 2
}
```
超类构造器先于子类的构造器运行。上述代码运行时，创建一个Ant对象，将得到长度为0的env,因为输出Ant类的range字段值时会重写Creature中的range字段，所以range方法返回0，env被设为长度为0的数组。
因此：**在构造器中不应该依赖val的值**
被调用的方法可能被子类重写，事实上初始化env的时候调用了range的getter方法。

上述问题有如下解决方式：
- 将val声明为final，安全但不灵活。
- 在超类中将val声明为lazy，安全但不高效。
- 在子类中使用提前定义语法。

"提前定义"语法使用**with**关键词可以在超类的构造器执行之前初始化子类的val字段。如：
```
class Ant extends{
    override val range = 2
} with Creature
// 提前定义的等号右侧只能引用之前已有的定义，不能使用类中的其他字段或方法。
```

### 8.11 Scala继承层级
暂略。

### 8.12 对象相等性
AnyRef下的eq方法检查两个引用是否指向同一个对象。实现一个类时，应该考虑重写该类的equals方法，以提供与实际情况相称的相等性判断。

为了保证相等判断的对称性，将方法定义在父类中，并且标记为final。

定义equals时，同时也定义hashCode。

在应用程序中，通常使用==操作符即可，不需直接调用eq或equals，对于引用类型而言，在做完必要的null检查后，会调用equals方法。

## 第9章 文件和正则表达式
要点：
1. Source.fromFile(...).getLines.toArray 输出文件的所有行。
2. Source.fromeFile(...).mkString 以字符串形式输出文件内容。
3. str.toInt, str.toDouble
4. PrintWrinter


## 第10章 特质 （Trait）
要点：
1. 类可以实现任意数量的trait。
2. trait可以要求实现它们的类具备特定的字段、方法或超类。
3. Scala的trait可以提供方法和字段的实现。（不同于Java接口）
4. 多个trait叠加在一起时应该注意顺序，其方法先被执行的trait排在后面。

### 10.1 没有多重继承
多重继承代价较高，可能需要解决方法冲突等问。因此Scala和Java一样不支持多重继承。

trait可以同时拥有抽象方法和具体方法，类可以实现多个特质。

### 10.2 当作interface使用的trait
特质中未被实现的方法默认是抽象的，无需声明为abstract。
```
trait Loggeer{
    def log(msg: String)
}

class ConsoleLogger extends Logger { //用extends而不是implements
    def log(msg: String){ //重写trait的抽象方法时，不需要使用override关键字
        println(msg)
    }
}
```
如果需要多个trait，可以用with关键字添加额外的trait：
`class ConsoleLogger extends Logger with Cloneable with Serializable`

### 10.3 带有具体实现的trait
trait中的方法不一定是抽象的，可以做具体实现。

如下代码声明并使用一个特质：
```
trait ConsoleLogger{
    def log(msg: String) { printl(msg)}
}

class SavingsAccount extends Account with ConsoleLogger {
    def withdraw(amount: Double){
        if(amount > balace) log("Insufficient funds")
    }
}
```

### 10.4 带有trait的对象
```
trait Logged {
    def log(msg: String){ }
}

class SavingAccount extends Account with Logged {
    def withdraw(amount: Double){
        if(amount: Double){
            if(amount > balance) log("Insufficient funds")
            else ...
        }
        ...
    }
}

trait ConsoleLogger extends Logged {
    override def log(msg: String){  println(msg)    }
}

//在构造不同对象的时候加入不同的trait
val acct = new SavingAccount with ConsoleLogger

val acct2 = new SavingsAccount with FileLogger

```

### 10.5 叠加在一起的trait
类或对象可以添加多个互相调用的trait，其调用顺序从最后一个开始。

### 10.6 在trait中重写抽象方法

### 10.7 当作富接口使用的trait

## 第11章 操作符
要点：
1. 一元和二元操作符实际上是方法调用。
2. 操作符优先级取决于第一个字符，而结合性取决于最后一个字符。
3. 提取器从输入中提取元组或值的序列。

### 11.1 标识符
包括变量、函数、类的名称。

Scala在选择标识符时比Java有更多选择，但最好还是传统一点（个人喜好）。

设计标识符时要尽量易读，易于输入。

### 11.2 中置操作符
`a 标识符 b`

实际上是一个方法调用 ：
```
1 to 10
1.to(10)
```

### 11.3 一元操作符
`a 标识符`等同于a.标识符()，如：
```
a toString
1.toString()
```

`+`,`-`,`!`,`~`可以作为前置操作符出现在参数之前。

### 11.4 赋值操作符
```
a 操作符= b
a = a 操作符 b

a += b
a = a + b
```

### 11.5 优先级
后置操作符的优先级低于中置操作符。


### 11.6 结合性

## 第13章 集合
- 所以集合都扩展自Iterable特质。
- 集合有三大类，分别是序列、集和映射。
- 对于几乎所有集合类，都提供了可变和不可变的版本。
- Scala列表要么是空的，要么拥有一头一尾，其中尾部本身又是一个列表。
- 集是无先后次序的集合。
- 用LinkedHashSet来保留插入顺序，或者用SortedSet来按顺序进行迭代。
- +将元素添加到无先后次序的集合中，+:和:+向前或向后追加到序列；++将两个集合串接在一起；-和--移除元素。

### 13.1 主要的集合特质
Seq有先后次序，如数组或列表。

Set是没有先后次序的，在SortedSet中，元素以某种排过序的顺序被访问。

Map是一组（键，值）对偶。

### 13.2 可变和不可变集合
- **可变**：scala.collection.mutable._ 
- **不可变**： scala.collection.immutable._
同时继承自scala.collection._

### 13.3 序列
##### 不可变序列
- Vector
- Range
- List
- Stream
- Stack
- Queue

##### 可变序列：
- ArrayBuffer
- Stack
- Queue
- Priority Queue
- LinkedList
- Double LinkedList

Vector是ArrayBuffer的不可变版本：一个带下标的序列，支持快速随机访问。Vector是以树形结构实现的。

Range表示整数序列，只存储起始值、结束值和增值，可以用to和until方法构造Range对象。

### 13.4 列表
在Scala中，列表要么是Nil，要么是一个head加上一个tail，而tail又是一个列表。
如：
```
val digits = List(4,2) 
//digits.head = 4
//digits.tail = List(2)
//digits.tail.tail = Nil
```
`::`操作符放在头部或尾部可以得到新的列表。并且遍历列表使用递归方法会非常自然，如：
```
9::List(4,2)

def sum(lst: List[Int]): Int = {
    if(lst == Nil) 0 else lst.head + sum(lst.tail)
}
```
### 13.5 可变列表
```
// set negative to 0
val lst = scala.collection.mutable.LinkedList(1, -2, 7, -9)
var cur = lst
while(cur != Nil) {
    if(cur.elem < 0 cur.elem = 0)
    cur = cur.next
}

// 隔一个删除
var cur = lst
while(cur != Nil && cur.next != Nil) {
    cur.next = cur.next.next
    cur = cur.next
}
```
Tips: 如果想把列表中的某个节点变为列表的最后一个节点，不能将next引用设为Nil或null，应该设为**LinkedList.empty**。

### 13.6 Set
缺省情况下，Set是以哈希集实现的，其元素根据hashCode方法的值进行组织。Set不保持元素的顺序，因为在哈希集中查找元素比在数组和列表中快得多。

LinkedHashSet可以记住元素插入的顺序，它会维护一个链表来实现。
如果想按照已排序的顺序来访问集合中的元素，可以用SortedSet，它是用红黑树实现的:
```
val weekdays = scala.collection,mutable,LinkedHashSet("Mo","Tu","We","Th","Fr")
val scala.collection.immutable.SortedSet(1, 2, 3, 4, 5, 6)
```
一些常见的集合操作：`union`、`intersect`、`diff`
一般而言，`+`用于将元素添加到无先后次序的集合，而`+:`和`:+`将元素添加到有先后次序的集合的开头或末尾。

### 13.7 常用操作符：
operator | description | Collection Type
---|---|---
coll :+ elem | 追加 | Seq
coll + elem  | 添加 | Set、Map
coll - elem | | Set、Map、ArrayBuffer
coll ++ coll2 | 与coll同类型的集合，包含两个集合的元素 | Iterable
coll -- coll2 | | Set、Map、ArrayBuffer
elem::lst | 被向前追加了元素或给定列表的列表 | List
list ::: list2 | 等同于list ++： list2 | List
set \| set2  set & set2 set &~ set2 | 两个集合的并、交、差 | Set
coll += elem  coll += (e1,e2,...) coll ++= coll2(`+`可以换成`-`)| 添加或移除指定元素 | 可变集合
elem +=: coll coll2 ++=: coll|向前追加给定元素或集合修改coll | ArrayBuffer

一般而言，`+`用于将元素添加到无先后次序的集合，而`+:`和`:+`分别将元素添加到有先后次序的集合的开头或末尾。

**右结合**的操作符都返回新的集合，不会修改原有的集合，而可变集使用的是左结合的操作元。

### 13.8 常用方法

##### 表13-2 Iterable特质的常用方法
Method | Description
---|---
head, last, headOption, lastOption | 返回第一个或最后一个元素，或用Option形式返回
tail, init | 返回除第一个或最后一个元素以外的部分
length, isEmpty | 略
map(f), foreach(f), flatMap(f), collect(pf) | 将函数应用到所有元素
reduceLeft(op), reduceRight(op), foldLeft(init)(op), foldRight(init)(op) | 以给定顺序将二元操作应用到所有元素
reduce(op), fold(init)(op), aggregate(init)(op,combineOp) |以非特定顺序将二元操作符应用到所有元素
sum, product, max, min | 返回该值（前提是可以隐式转换）
count(pred), forall(pred), exists(pred) | 返回满足前提表达式的元素计数； Boolean值； Boolean值；
filter(pred), filterNot(pred), partition(pred) | 返回所有满足前提表达式的元素；所有不满足前提表达式的元素； 这两组元素组成的对偶；
takeWhile(pred), dropWhile(pred), span(pred) | 返回满足前提表达式的一组元素（直到遇见第一个不满足的元素）；所有其他元素；或者这两组元素组成的对偶。
take(n), drop(n), splitAt(n) | 返回前n个元素； 所有其他元素；这两组元素组成的对偶。
takeRight(n), dropRight(n) | 返回最后n个元素；或者其他所有元素。
slice(from, to)|返回从from到to区间内的所有元素。
zip(coll2)、zipAll(coll2, fill, fill2)、 zipWityIndex| 返回由本集合的元素和另一个集合的元素组成的对偶。
grouped(n)、sliding(n) | 返回长度为n的子集合迭代器；grouped产出下标为0 until n 的元素，然后是下标为n until 2*n的元素，以此类推；sliding产出下标为0 until n的元素，然后是下标为1 until n+1 的元素，以此类推。
mkString(before, between, after)、addString(sb, before, between, after) | 由所有元素组成的字符串，将给定字符串分别添加到首个元素之前、每个元素之间，以及最后一个元素之后。第二个方法将字符串追加到stringbuilder当中
toIterable, toSeq, toIndexedSeq, toArray, toList, toStream, toSet, toMap | 将集合转换成指定类型的集合
copyToArray(arr), copyToArray(arr, start, length), copyToBuffer(buf) | 将元素拷贝到数组或缓冲当中

##### Seq在Iterable基础上添加的方法
Method | Description
---| ---
contains(elem), containsSlice(seq), startsWith(seq), endsWith(seq) | 返回布尔值
indexOf(elem), lastIndexOf(elem), indexOfSlice(seq), lastIndexOfSlice(seq) | 返回对应下标
indexWhere(pred) | 返回满足pred的首个元素的下标
prefixLength(pred), segmentLength(pred,n)|返回满足pred的最长元素序列的长度，从当前序列的下标0或n开始查找
padTo(n, fill) | 返回当前序列的一个拷贝，将fill的内容向后追加，知道新序列的长度达到n
intersect(seq), diff(seq)|返回多重集合的交集，或序列之间的差异
reverse | 当前序列的反向
sorted, sortWith(less), sortBy(f) 使用元素本身的大小，二元函数less，或者将每个元素映射成一个带先后次序的类型的值的函数f，对当前序列进行排序后的新序列。
permutations, combinations(n) | 返回一个遍历所有排列或组合的迭代器

这些方法都不改变原有集合，返回一个与原集合类型相同的新集合。

### 13.9 将函数映射到集合
`map`,`flatMap`
```
names.map(_.toUpperCase)
names,flatMap(ulcase)
如果使用flatMap并传入返回Option的函数的话，最终返回的集合将包括所有的值v，前提是函数返回Some(v)
```
`collect`方法用于oartial function --- 那些并没有对所有可能的输入进行定义的函数，他产出被定义的所有参数的函数值的集合。

### 13.10 化简、折叠和扫描
```
c.reduceLeft(op)
c.reduceRight(op)
c.foldLeft(init)(op)
scanLeft
scanRight
```

### 13.11 拉链操作
```
val prices = List(5.0, 20.0, 9.95)
val quantities = List(10, 2, 1)
prices zip quantities
得到：
List[(Double, Int)] = List((5.0, 10), (20.0, 2), (9.95, 1))

// 其他用法：
(prices zip quantities) map {p => p._1 * p._2}
((prices zip quatities) map {p => p._1 * p._2}).sum
//如果一个集合比另一个短，那么集合中的对偶数量与较短的集合中元素数量相同。
//zipAll方法可以指定较短列表中的缺省值。
List((5.0, 20.0, 9.95).zipAll(List(10,2), 0.0, 1)
//将得到：
List((5.0, 10), (20.0, 2), (9.95, 1))
zipWithIndex 方法返回对偶的列表，每个对偶中第二个组成部分是每个元素的下标。
```
### 13.12 迭代器
Scala并不提倡使用迭代器，通过前两节的方法可以更容易地获取结果。（是否便于Spark化？）

### 13.13 流
流是一个尾部被懒计算的不可变列表，只有需要时才会被计算。和迭代器一样，只有在需要的时候才去取元素，不会付出计算剩余元素的代价。
```
def numsFrom(n: BigInt): Stream[BigInt] = n #:: numsFrom(n + 1)
```
`#::`操作符很像是列表的`::`操作符，不过它构建出的是一个流。

当调用 val tenOrMore = numsFrom(10)时，得到的是一个被显示为Stream(10, ?) 的流对象。其尾部未被求值。

### 13.14 懒视图
略

### 13.15 与Java集合的互操作
```
import scala.collection.JavaConversions._
val props: scala.collection.mutable.Map[String, String] = System.getProperties()
```

### 13.16 线程安全的集合
`SynchronizedBuffer`,`SynchronizedMap`,`SynchronizedPriorityQueue`,`SynchronizedSet`,`SynchronizedStack`

通常说来，如果想保证线程安全，可以用java.util.concurrent包中的某个类。比如，多个线程共享一个Map, 就用`ConcurrentHashMap`或`ConcurrentSkipListMap`这比用同步方式执行所有方法的映射更为高效。

### 13.17 并行集合
Scala为并行提供了很多方便，比如对一个大型集合coll求和，只需要`coll.par.sum`即可。

并不是所有的方法都可以并行化。

## 第14章 模式匹配和样例类
本章要点：
- match表达式不会意外掉入下一个分支。
- 如果没有成功匹配，会抛出MatchError，可以用case_避免。
- 模式可以包含一个随意定义的条件，称作守卫。
- 可以对表达式的类型进行匹配，而且应该优先选择模式匹配而不是isInstanceOf/asInstanceOf。
- 可以匹配数组、元组和样例类的模式，然后将匹配到的不同部分绑定到变量。
- **在for表达式中，不能匹配的情况会被安静地跳过。**
- 样例类是编译器会为之自动产出模式匹配所需要的方法的类。
- 样例类继承层级中的公共超类应该是sealed的。
- 用Option来存放那些不确定是否存在的值，这会比null更安全。

### 14.1 match
在类C语言中，必须在每个分支的末尾显式地使用break语句来退出switch，否则将掉入下一分支。
```
sign = ch match {
    case '+' => 1
    case '-' => -1
    case _ => 0
}
```

### 14.2 守卫
Scala的模式中可以包括随意定义的条件,这叫做守卫，守卫可以是任何Boolean条件：
```
ch match{
    case '+' => sign = 1
    case _ if Character.isDigit(ch) -> digit = Character.digit(ch, 10) 
}
```

### 14.3 模式中的变量
如果case关键字后边跟着一个变量名，那么匹配的表达式会被赋给那个变量：
```
str(i) match {
    case ch => digit = Character.digit(ch, 10)
}
```
另，scala中变量必须以小写字母开头，如果某个变量以小写字母开头，则应该将它包在反括号里，如： \`pi\`

### 14.4 类型模式
可以用于替代isInstanceOf:
```
obj match {
    case x: Int => x
    case s: String => Integer.parseInt(s)
    case _: BigInt => Int.MaxValue
    case + => 0
}
```

当匹配类型的时候必须给出变量名，否则将会用对象本身去匹配。

### 14.5 匹配数组、列表和元组
要匹配数组的内容，可以在模式中使用Array表达式，List和元组同理。
```
arr match {
    //匹配包含0的数组
    case Array(0) => "0"

    //匹配带有两个元素的数组
    case Array(x, y) => x + " " + y
    
    //匹配任何以0开始的数组
    case Array(0, _*) => "0 ..."
    case + => "something else"
}

lst match {
    case 0 :: Nil => "0"
    case x :: y :: Nil => x + " " + y
    case 0 :: tail => "0 ..."
    case _ => "something else"
}

pair match {
    case (0, _) => "0 ..."
    case (y, 0 ) => y + " 0"
    case _ => "neither is 0"
}
```

### 14.6 提取器

### 14.7 变量声明中的模式
```
val (x, y) = (1, 2)
val (q, r) = BigInt(10) /% 3
```

### 14.8 for表达式中的模式
在for表达式中，失败的匹配将会被安静地忽略。
```
for ((k, "") <- System.getProperties())
    println(k)
```

### 14.9 样例类
样例类是一种特殊的类，经过优化以被用于模式匹配。
```
case class Dollar(value: Double) extends Amount
case object Nothing extends Amount //可以用针对单例的样例对象
```
- 构造器中的每一个参数
