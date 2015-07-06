# 模式的种类(二) 

## 构造器模式 

构造器模式功能非常强大，比如一个构造器模式可以定义为 BinOp(“+”,e,Number(0))。它由一个名称  BinOp（某个 case class 的名称）和一系列有括号分开的模式构成（“+”，e 和 Number(0))。这个模式首先检查输入对象是否是 BinOp 类型的对象，然后检查构造器参数是否匹配输入的对象。

这些额外的模式表示 Scala 支持深度匹配，这些模式不仅仅检查最高层次的匹配，并且检查其内部内容的匹配，同时这些额外的模式自身还可以说构造器模式，因此你可以构造嵌套任意层次的构造器模式。
比如

```
    expr match{
    	case BinOp("+",e,Number(0)) => println(" a deep match")
    	case _ =>
    }
```

它的第三个参数 Number(0) 自身也是一个构造器模式，其参数又匹配 0，因此有三个层次的匹配。

## 序列模式

你也可以匹配如 List 和数组等序列类型的数据。和匹配 case class 语法类似，但你可以指明序列中任意数量的元素。比如：

下面的例子匹配含三个元素，其中首元素为 0 的列表：

```
    scala> List(0,2,4) match{
     | case List(0,_,_) => print ("found it " )
     | case _ =>
     | }
    found it 
```

如果你需要匹配一个未指明长度的序列，可以使用“\_\*” 作为模式的后一元素，这个“\_\*” 可以匹配任意数目的元素（包括 0 个元素）

```
    expr match{
      case List(0,_*) => print ("found it")
      case _ =>
      }
```

## 多元组模式

除序列外，你可以匹配多元组，比如：

```
    scala> def tupleDemo(expr:Any) =
     | expr match{
     |   case (a,b,c) => print("matched " + a +":" +b +":"+c )
     |   case _ =>
     | }
    tupleDemo: (expr: Any)Unit
    
    scala> tupleDemo(2,3,4)
    matched 2:3:4
```
