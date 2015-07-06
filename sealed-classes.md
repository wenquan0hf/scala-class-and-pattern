# Sealed Classes 

前面说过，在写模式匹配时，你必须保证你所写的可选项覆盖了全部的可能性，因此常常你必须加上一个缺省通配符选项。但这种情况只适应于缺省通配符有意义的情况。如果对于一些没有缺省项的情况，你怎么才能保证你写的可选项是完全的呢？

实际上，你可以借助于 Scala 编译器来帮忙，要做到这一点，编译器需要预先知道所有可能的匹配项，这种通常情况下是不可能的。比如你总可以派生出新的子类，然后再可选项中添加这个新创建的子类的模式。

一种可能的实现是为基类添加上 Sealed 关键字，一个 sealed 的类只能在定义它的同一个文件中定义它的子类。这样你就只需要关心已经定义的子类，如果你使用这些子类做为模式定义，如果可选项不去全的话，编译器会自动警告。

我们还是使用之前定义的表达式的例子：

```
    sealed abstract class Expr
    case class Var(name:String) extends Expr
    case class Number(num:Double) extends Expr
    case class UnOp(operator:String, arg:Expr) extends Expr
    case class BinOp(operator:String,left:Expr,right:Expr) extends Expr
```

下面我们定义一个不完全的模式匹配：

```
    def describe(e:Expr) :String =e match{
    	case Number(_) => "a number"
    	case Var(_) => "a variable"
    }
    
    <console>:12: warning: match may not be exhaustive.
    It would fail on the following inputs: BinOp(_, _, _), UnOp(_, _)
       def describe(e:Expr) :String =e match{
     ^
    describe: (e: Expr)String
```

编译器给出警告，表示你的定义可能会抛出 MatchError 异常，因为 BinOp 和 UnOp 没有定义在模式定义中。

当有的时候，你可能只需要匹配部分模式，一是添加一个缺省匹配，比如通配符模式，例如：

```
    def describe(e:Expr) :String =e match{
    	case Number(_) => "a number"
    	case Var(_) => "a variable"
    	case _ => throw new RuntimeException
    }
```

为简洁起见，Scala 支持使用标注 (annotation) 的方法暂时取消编译器检查模式定义是否完备，为变量添加 @unchecked 标注后，编译器不再给出警告：

```
    def describe(e:Expr) :String =(e: @unchecked) match{
    	case Number(_) => "a number"
    	case Var(_) => "a variable"
    }
```

@unchecked 在模式匹配中具有特殊意义，如果模式匹配的变量使用该标准，Scala 编译器不对该模式进行完备性检查。
