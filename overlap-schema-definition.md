# 重叠模式定义 #
Scala 在匹配模式时，按照模式定义的顺序依次检查，因此越特定的规则越要先定义，而通用的规则后定义。比如我们修改之前的

    def simplifyTop(expr :Expr) :Expr = expr match {
     case UnOp("-",UnOp("-",e))=>e
     case BinOp("+",e,Number(0))=>e
     case BinOp("*",e,Number(1))=>e
     case _ => expr
       
     }

使它可以简化任意层次的表达式，我们需要添加两个通用的规则，定义如下：

    def simplifyAll(expr :Expr) :Expr = expr match {
     case UnOp("-",UnOp("-",e))=>e
     case BinOp("+",e,Number(0))=>e
     case BinOp("*",e,Number(1))=>e
     case UnOp(op,e) => UnOp(op,simplifyAll(e))
     case BinOp(op,l,r)=>BinOp(op,simplifyAll(l),simplifyAll(r))
     case _ => expr
       
     }

simplifyAll 规则 1 是规则 4 的特例，而规则 2，3 是规则 5 的特例，因此 1，2，3 需要定义在 4，5 之前，而最后的缺省规则需要放在最后，如果顺序反了，比如把通用的规则放在特例之前，编译器会给出警告，因为当 Scala 做匹配时，按照规则定义的顺序，首先匹配通用规则，规则匹配成功，后面的特例没有机会匹配，比如：

    def simplifyBad(expr:Expr):Expr = expr match{
    	case UnOp(op,e) => UnOp(op,simplifyBad(e))
    	case UnOp("-",UnOp("-",e))=>e
    }
    
    
    <console>:12: warning: unreachable code
       case UnOp("-",UnOp("-",e))=>e
       ^
    simplifyBad: (expr: Expr)Expr

