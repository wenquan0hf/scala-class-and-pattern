# 模式的种类(一) 

上个例子显示了几种不同的模式：常量模式，通配模式，变量模式等，本篇逐个介绍模式的种类。

## 通配模式

通配符“_”可以用来匹配任意对象，通常在模式匹配中作为最后一个匹配项，匹配其它所有的输入对象。比如：

```
    expr match {
    	case BinOp(op,left,right) => println( expr + " is a binary operation")
    	case _ =>
    }
```

通配模式也可以用来忽略一些你不打算处理的对象，比如本例，这关心输入是否为一二元操作，其它的直接忽略。如果你不关心具体的操作符，左右操作符，可以直接使用通配符忽略这些部分，如：

```
    expr match {
    	case BinOp(_,_,_) => println( expr + " is a binary operation")
    	case _ =>
    }
```

## 常量模式

常量模式可以匹配和常量值本身相同的输入。任意的字面量都可以作为常量模式。此外，所有的单例(singleton) 也可以作为常量模式。比如 Nil 可以匹配空列表。

```
    def describe(x:Any) =x match {
    	case 5 => "five"
    	case true => "truth"
    	case "hello" => "hi!"
    	case Nil => "the empty list"
    	case _ => "something else"
    }
```

本例定义了多个常量模式，我们来看看一些测试结果：

```
    scala> describe (5)
    res0: String = five
    
    scala> describe(true)
    res1: String = truth
    
    scala> describe ("hello")
    res2: String = hi!
    
    scala> describe(Nil)
    res3: String = the empty list
    
    scala> describe(List(1,3,4))
    res4: String = something else
```

## 变量模式

一个变量模式可以匹配任意对象，在这一点上和通配符”_”一样，但和通配符不同的是，Scala 将这个变量绑定到输入的对象上，然后你在后面定义的表示中可以引用这个变量。比如下面代码匹配 0，对于其它的对象使用了变量模式，这其后的表示中可以引用这个变量：

```
    def isZero(x:Any) = x match{
    	case 0 => "zero"
    	case somethingElse => "not zero:" + somethingElse
    }
    
    scala> isZero(0)
    res5: String = zero
    
    scala> isZero(1)
    res6: String = not zero:1
```

## 常量模式还是变量模式

常量模式可以采用符号名称，比如前面定义的 Nil（它也是作为常量模式），这里给出另外一个相关的例子：

```
    E match {
      case Pi => "strange math? Pi =" + Pi
     case _ =>"OK"
    }
    
    
    scala> E match {
     | case Pi => "strange math? Pi =" + Pi
     |  case _ =>"OK"
     | }
    res7: String = OK
```

E 显然不是 Pi，那么 Scala 编译器如何知道 Pi 是个常量，而不是一个变量呢？ Scala 编译器使用一个简单的规则来判断：如果一个变量使用小写字母开始，那么它作为一个变量模式，其它则做为常量模式。因此变量首字符的大小写显得非常重要。

如果我们重新定义 pi (小写 p)

```
    scala> val pi=math.Pi
    pi: Double = 3.141592653589793
    
    scala> E match {
     | case pi => "strange math? Pi =" + Pi
     | }
    res9: String = strange math? Pi =3.141592653589793
```

次数 pi 作为变量模式，它可以匹配任意输入，因此可以匹配 E。 由于变量模式可以匹配任意的输入，如果此时你再使用通配符，那么通配符不会被执行到，因此系统会报错。

```
    scala> E match {
      case pi => "strange math? Pi =" + Pi
    		  case _ => "OK"
    
      }
    <console>:10: warning: patterns after a variable pattern cannot match (SLS 8.1.1)
    case pi => "strange math? Pi =" + Pi
     ^
    <console>:11: warning: unreachable code due to variable pattern 'pi' on line 10
    case _ => "OK"
```

如果你还是想使用小写字符开始的符号作为常量模式，有两个方法，如果这个变量是某个对象的属性，可以在这个变量前使用前缀，或者使用反单引号`，比如修改代码如下：

```
    scala> E match {
      case `pi` => "strange math? Pi =" + Pi
    		  case _ => "OK"
    
      }
    	  
    res11: String = OK
```

使用了“的 pi 又作为常量模式来匹配输入值。
