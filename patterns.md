# 模式无处不在 

Scala 程序很多地方都可以使用模式，而不仅仅用在模式匹配( match 表达式)，本篇给出几种使用模式的情况。

## 变量定义

任何时候你使用 val 或 var 定义变量时，你都可以使用模式定义多个变量，此时你定义元组，分别赋值到不同的变量。

```
    scala> val myTuple=(134,"abc")
    myTuple: (Int, String) = (134,abc)
    
    scala> var(number,string)= myTuple
    number: Int = 134
    string: String = abc
```

这个情况也适应 Case classes。 如果你知道某个值的具体形式，你可以利用模式来分解单个项：

```
    scala> val exp=new BinOp("*",Number(5),Number(1))
    exp: BinOp = BinOp(*,Number(5.0),Number(1.0))
    
    scala> val BinOp(op,left,right)=exp
    op: String = *
    left: Expr = Number(5.0)
    right: Expr = Number(1.0)
```

## Case 序列定义部分方程

一个 Case 序列（ case squence，也成为可选项）为包含在{}的代码，它可以用在可以使用任何方程字面量的地方。从根本上来说 case 序列也是一个函数字面量，只是更一般化的函数。通常的函数只有一个入口点和一组参数。一个 Case 序列可以有多个入口点和多组参数。每个 Case 可选项都是函数的入口点，而它对于模式为参数定义。其函数体为 Case 序列的右边部分。

这里给出一个简单的例子：

```
    val withDefault: Option[Int] => Int ={
    	case Some(x) =>x
    	case None => 0
    }
```

这个函数定义了两个可选项，第一个选项匹配 Some 对象，第二个选项匹配 None。

```
    scala> withDefault(Some(5))
    res0: Int = 5
    
    scala> withDefault(None)
    res1: Int = 0
```

此外需要注意的是，一个 Case 序列定义了一个部分函数( partial function )，如果你传入一个该函数不支持的参数，代码会给出 Runtime 异常。比如我们定义下面一个部分函数：

```
    val second: List[Int] => Int = {
     case x::y::_ => y
    }
```

返回列表的第二个元素，系统会给出如下警告：

```
    <console>:7: warning: match may not be exhaustive.
    It would fail on the following input: List(_)
       val second: List[Int] => Int = {
      ^
    second: List[Int] => Int = <function1>
```

系统警告匹配不完全，比如函数无法匹配 List(1)，List() 等，测试如下：

```
    scala> second(List(1,2))
    res7: Int = 2
    
    scala> second(List(1))
    scala.MatchError: List(1) (of class scala.collection.immutable.$colon$colon)
    at $anonfun$1.apply(<console>:7)
    at $anonfun$1.apply(<console>:7)
```

如果你需要测试某个部分函数是否定义，你就需要告诉编译器你在使用部分函数。而类型 List[Int] => Int 代表了所有由列表到整数的变换。而如果我们需要定义由 List[int] 到 int 的部分函数，需要使用 PartialFunction 来定义，例如：

```
    val second:PartialFunction[List[Int],Int] = {
    	case x::y::_ => y
    }
```

PartialFunction 定义了一个 isDefinedAt 方法可以用来测试某种类型的部分函数是否定了。例如：

```
    scala> second.isDefinedAt(List(5,6,7))
    res0: Boolean = true
    
    scala> second.isDefinedAt(List(1))
    res1: Boolean = false
```

## for 表达式中使用模式

你也可以在 for 表示式中使用模式，比如我们之前定义的

```
    val capitals = Map("France"->"Paris", "Japan"->"Tokyo","China"->"Beijing")
```

我们使用 for 表示式来枚举国家和首都

```
    for((country,city) <- capitals) 
    	println("The captical of " + country + " is " + city)
    	
    The captical of France is Paris
    The captical of Japan is Tokyo
    The captical of China is Beijing
```

这个例子的 (county，city) 匹配都会成功，因为 capitals 的每个元素都是一个二元组。如果某些匹配不成功，则这些元素自动跳过。比如：

```
    val results=List(Some("apple"),None,Some("Orange"))
    
    scala> for(Some(fruit) <- results) println (fruit)
    apple
    Orange
```
