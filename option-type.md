# Option 类型 #
Scala 语言中包含一个标准类型 Option 类型，代表可选值，Option 类型的值可以有两个可能的值，一个为 some(x) 其中 x 为有效值，另外一个为 None 对象，代表空值。

Option 类型的值通常作为 Scala 集合类型（List，Map 等）操作的返回类型。 比如 Map 的 get 方法：

    scala> val capitals = Map("France"->"Paris", "Japan"->"Tokyo","China"->"Beijing")
    capitals: scala.collection.immutable.Map[String,String] = Map(France -> Paris, Japan -> Tokyo, China -> Beijing)
    
    scala> capitals get "France"
    res0: Option[String] = Some(Paris)
    
    scala> capitals get "North Pole"
    res1: Option[String] = None

将 Option 类型的值放开的一种常见的方法是使用模式匹配：

    scala> def show(x:Option[String]) = x match{
    	case Some(s) => s
    	case None => "?"
    }
    
    show: (x: Option[String])String
    
    scala> show (capitals get "China")
    res3: String = Beijing
    
    scala> show (capitals get "France")
    res4: String = Paris
    
    scala> show (capitals get "North Pole")
    res5: String = ?

Scala 程序使用 Option 非常频繁，在 Java 中使用 null 来表示空值，代码中很多地方都要添加 Null 检测，不然很容易出现 NullPointException。 因此 Java 程序需要关心那些变量可能是 Null，而这些变量出现 Null 的可能性很低，但一但出现，很难查出为什么出现NullPointerException。

Scala 的 Option 类型可以避免这种情况，因此 Scala 应用推荐使用 Option 类型来代表一些可选值。使用 Option 类型，读者一眼就可以看出这种类型的值可能为 None。

`注`： Option 类型是 Monoid，通俗的理解可以认为是一种设计模式。
