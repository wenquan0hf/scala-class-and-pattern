# 模式限定修饰 #

有些时候，需要对定义的模式做某些限制，比如你需要完成表达式简化，比如两个相同的参数相加 e+e ，你想把它简化为e*2。也就是  

    BinOp("+",Var("x"),Var("x"))  
转化为  

    BinOp("\*",Var("x"),Number(2))

你可能试着这么定义规则：

    def simplifyAdd(e:Expr) =e match{
    	case BinOp("+",x,x) => BinOp("*",x,Number(2))
    	case _ =>
    }

但是编译器会报错：

    <console>:13: error: x is already defined as value x
       case BinOp("+",x,x) => BinOp("*",x,Number(2))
    

告诉你变量 x 已经定义过了，也是模式中定义的变量名只能定义一次，那么此时就可以借助于限制修饰，我们重新定义如下：

    def simplifyAdd(e:Expr) =e match{
    	case BinOp("+",x,y) if x==y => BinOp("*",x,Number(2))
    	case _ =>
    }

模式的限定修饰为一 if 语句，if 语句可以使用任意的布尔表达式，通常这个布尔表达式对前面定义的模式变量进行了限制。也就是只有在条件满足的且满足模式匹配时，才执行=>后面的表达式。
  
测试如下：  

    scala> simplifyAdd(BinOp("+",Var("x"),Var("x")))
    res0: Any = BinOp(*,Var(x),Number(2.0))

