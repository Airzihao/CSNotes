关于JUnit中方法的执行顺序
JUnit4使用Java5中的注解（annotation），以下是JUnit4常用的几个annotation： 

`@Before`：初始化方法   对于每一个测试方法都要执行一次（注意与BeforeClass区别，后者是对于所有方法执行一次）

`@After`：释放资源  对于每一个测试方法都要执行一次（注意与AfterClass区别，后者是对于所有方法执行一次）

`@Test`：测试方法，在这里可以测试期望异常和超时时间 

`@Test`(expected=ArithmeticException.class)检查被测方法是否抛出ArithmeticException异常 

`@Ignore`：忽略的测试方法 

`@BeforeClass`：针对所有测试，只执行一次，且必须为static void 

`@AfterClass`：针对所有测试，只执行一次，且必须为static void 

一个JUnit4的单元测试用例执行顺序为： 

`@BeforeClass` -> `@Before` -> `@Test` -> `@After` -> `@AfterClass`; 

每一个测试方法的调用顺序为： 

`@Before` -> `@Test` -> `@After`; 


JUnit中，类内的方法会在每个test之前执行一次，如果有多个test则会执行多次。下例中，outter和before一样，会被执行两次。
```
class SortTest {

  println("outter")
  
  @Before
  def before(): Unit = {
    println("before")
  }
  @Test
  def test1(): Unit = {
    println("1")
  }
  @Test
  def test2(): Unit = {
    println("2")
  }
}
```

scala中没有static方法，使用伴生对象解决，即将@BeforeClass方法放入与class同名的方法中，例：
```
object SortTest {
  @BeforeClass
  def beforeClass(): Unit ={
    println("beforeClass")
  }
}

class SortTest {

  println("outter")

  @Before
  def before(): Unit = {
    println("before")
  }

  @Test
  def test1(): Unit = {
    println("1")
  }

  @Test
  def test2(): Unit = {
    println("2")
  }
}
```