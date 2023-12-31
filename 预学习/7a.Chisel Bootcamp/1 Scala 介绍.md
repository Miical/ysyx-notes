
[Chisel Bootcamp](https://mybinder.org/v2/gh/freechipsproject/chisel-bootcamp/master)

# ----- 理解Scala ----- 

<!-- CARD BEGIN -->

Scala是另一种支持通用编程范式的编程语言。我们选择使用它有几个原因:

- 它是承载嵌入式DSL的好语言。
- 它有一个强大而优雅的库，用于操作各种数据集合。
- 它有一个严格的类型系统，可以帮助在开发周期的早期(即编译时)捕获大量的错误。 
- 它具有强大的表达和传递函数的方式。
- Chipel, Chijel和Chicel的发音都不如Chisel那么好听。

当我们稍后讨论Chisel时，所有这些要点都将变得清晰，但现在，我们将专注于阅读和编写Scala代码的基础知识。

# 变量和常量 - var 和 val

```scala
var numberOfKittens = 6
val kittensPerHouse = 101
val alphabet = "abcdefghijklmnopqrstuvwxyz"
var done = false

numberOfKittens: Int = 6
kittensPerHouse: Int = 101
alphabet: String = "abcdefghijklmnopqrstuvwxyz"
done: Boolean = false
```

Scala通常不需要在语句末尾使用分号。当有换行时，Scala会推断分号。

`var` 可以被再赋值，而 `val` 一旦被赋值则不可改变

# 条件

使用条件和其他语言一样

```scala
// A simple conditional; by the way, this is a comment
if (numberOfKittens > kittensPerHouse) { 
    println("Too many kittens!!!") 
}
// The braces are not required when all branches are one liners. However, the 
// Scala Style Guide prefers brace omission only if an "else" clause is included.
// (Preferably not this, even though it compiles...)
if (numberOfKittens > kittensPerHouse) 
    println("Too many kittens!!!")

// ifs have else clauses, of course
// This is where you can omit braces!
if (done) 
    println("we are done")
else 
    numberOfKittens += 1

// And else ifs
// For style, keep braces because not all branches are one liners. 
if (done) {
    println("we are done")
}
else if (numberOfKittens < kittensPerHouse) {
    println("more kittens!")
    numberOfKittens += 1
}
else {
    done = true
}
```

但是 Scala 中，`if` 将会返回一个值，这个值是执行代码块的最后一行

```scala
val likelyCharactersSet = if (alphabet.length == 26)
    "english"
else 
    "not english"

println(likelyCharactersSet)
```

# 方法（函数）

使用 `def` 定义。函数实参(或形参)在逗号分隔的列表中指定，该列表指定实参的名称、类型以及可选的默认值。为了清晰起见，应该指定返回类型。

没有任何参数的Scala函数不需要空括号。这使得类成员可以是一个函数，引用时发生计算

按照惯例，**没有副作用的无参数函数不使用括号，而有副作用的函数(也许它们改变了类变量或打印了一些东西)应该需要括号**。

```scala
// Simple scaling function with an input argument, e.g., times2(3) returns 6
// Curly braces can be omitted for short one-line functions.
def times2(x: Int): Int = 2 * x

// More complicated function
def distance(x: Int, y: Int, returnPositive: Boolean): Int = {
    val xy = x * y
    if (returnPositive) xy.abs else -xy.abs
}
```


## 重载函数

```scala
// Overloaded function
def times2(x: Int): Int = 2 * x
def times2(x: String): Int = 2 * x.toInt

times2(5)
times2("7")
```

## 递归和嵌套函数

```scala
/** Prints a triangle made of "X"s
  * This is another style of comment
  */
def asciiTriangle(rows: Int) {
    
    // This is cute: multiplying "X" makes a string with many copies of "X"
    def printRow(columns: Int): Unit = println("X" * columns)
    
    if(rows > 0) {
        printRow(rows)
        asciiTriangle(rows - 1) // Here is the recursive call
    }
}

// printRow(1) // This would not work, since we're calling printRow outside its scope
asciiTriangle(6)
```

# Lists

```scala
val x = 7
val y = 14
val list1 = List(1, 2, 3)
val list2 = x :: y :: y :: Nil       // An alternate notation for assembling a list

val list3 = list1 ++ list2           // Appends the second list to the first list
val m = list2.length
val s = list2.size

val headOfList = list1.head          // Gets the first element of the list
val restOfList = list1.tail          // Get a new list with first element removed

val third = list1(2)                 // Gets the third element of a list (0-indexed)
```

# for 表达式

```scala
for (i <- 0 to 7) { print(i + " ") }
println()

// 使用 until 不包括最后一个数 0-6

for (i <- 0 until 7) { print(i + " ") }
println()

// 修改步长

for(i <- 0 to 10 by 2) { print(i + " ") }
println()

// 迭代器

val randomList = List(scala.util.Random.nextInt(), scala.util.Random.nextInt(), scala.util.Random.nextInt(), scala.util.Random.nextInt())
var listSum = 0
for (value <- randomList) {
  listSum += value
}
println("sum is " + listSum)
```

<!-- CARD END -->
<!--ID: 1699538838378-->

# ----- 阅读 Scala -----

<!-- CARD BEGIN -->

能够阅读Scala代码并理解常见的命名约定、设计模式和最佳实践是成为一名高效的Chisel设计师的重要一步。代码重用的潜力是Chisel的优点之一，但是如果您不能阅读其他人的代码，那么重用是很困难的。有效地解析其他人的代码也使寻求帮助变得更容易，特别是从像StackOverflow这样的资源中。

下面几节将展示您将看到的常见代码模式。

# Packages 和 Imports

```scala
pakage mytools
class Tool1 { ... }

// 当从外部引用包含上述代码行的文件中定义的代码时，应该使用:

import mytools.Tool1

// 包名应该与目录层次结构匹配。这不是强制性的，但是不遵守这个准则可能会产生一些不寻常的和难以诊断的问题。
// 按照约定，包名是小写的，并且不包含下划线等分隔符。这有时会使好的描述性名称变得困难
```

# Scala是面向对象的语言

## Class 定义

```scala
// WrapCounter counts up to a max value based on a bit size
class WrapCounter(counterBits: Int) {

  val max: Long = (1 << counterBits) - 1
  var counter = 0L
    
  def inc(): Long = {
    counter = counter + 1
    if (counter > max) {
        counter = 0
    }
    counter
  }
  println(s"counter created with max value $max")
}
```

- counter——代码块的最后一行很重要。 表示为代码块最后一行的任何值都被认为是该代码块的返回值。返回值可以被调用语句使用，也可以被忽略。
- `println(s"counter created with max value $max")` 将字符串打印到标准输出。因为println直接在定义代码块中，它是类初始化代码的一部分，并且在每次创建该类的实例时运行，即打印出字符串。
- 在这种情况下打印的字符串是一个插值字符串。 
	- 第一个双引号前的前导s将其标识为插入字符串。 
	- 如果$后面跟着一个代码块，则可以在该代码块中使用任意Scala。例如，`println(s"double max is ${max + max}")`。

## Class 实例

```scala
val x = new WrapCounter(2)

x.inc() // Increments the counter

// Member variables of the instance x are visible to the outside, unless they are declared private
if(x.counter == x.max) {              
    println("counter is about to wrap")
}

x inc() // Scala allows the dots to be omitted; this can be useful for making embedded DSL's look more natural
```

# 代码块

代码块由大括号分隔。一个块可以包含零行或多行Scala代码。Scala代码的最后一行成为代码块的返回值(可以忽略)。没有行的代码块将返回一个特殊的类似null的对象Unit。

代码块在Scala中被广泛使用:它们是类定义的主体，它们构成函数和方法定义，它们是if语句的子句，它们是for和许多其他Scala操作符的主体。

**参数化代码块**

代码块可以接受参数。在类和方法定义的情况下，这些参数与大多数常规编程语言中的参数类似。在下面的例子中，c和s是代码块的参数。

```scala
// A one-line code block doesn't need to be enclosed in {}
def add1(c: Int): Int = c + 1

class RepeatString(s: String) {
  val repeatedString = s + s
}
```

**参数化的另外一种方式**

```scala
val intList = List(1, 2, 3)
val stringList = intList.map { i =>
  i.toString
}
```

代码块被传递给类List的方法映射。map方法要求它的代码块只有一个参数。为列表的每个成员调用代码块，代码块返回转换为String的成员。Scala几乎过度地接受这种语法的变体。你可能会看到很多不同的写法。这种类型的代码块称为匿名函数，关于匿名函数的更多细节将在后面的模块中提供。

# 命名参数和参数默认值

```scala
def myMethod(count: Int, wrap: Boolean, wrapValue: Int = 24): Unit ={…}

// 在调用该方法时，您经常会看到参数名称以及传入的值。
myMethod(count = 10, wrap = false, wrapValue = 23)

//  使用命名参数，您甚至可以以不同的顺序调用函数。
myMethod(wrapValue = 23, wrap = false, count = 10)
```

当某些参数具有默认值(不需要重写)时，调用者只需要(按名称)传递不使用默认值的特定参数。注意，参数wrapValue的默认值是24。因此,

```scala
myMethod(wrap = false, count = 10)
```

<!-- CARD END -->
<!--ID: 1699538856343-->



