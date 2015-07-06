# 模式的种类(四) #
**变量绑定**

除了独立的变量模式外，你还可以把一个变量添加到另外的模式中，你简单的定义一个变量，然后再添加一个 @ 符号，然后再写其它的模式。 这就定义了一个变量绑定过的模式。这意味着还是按照标准的模式匹配来匹配输入对象，如果匹配成功，匹配成功的对象会赋值到定义的变量中。  
例如：

    expr match {
    	case UnOp("abs",e @ UnOp("abs",_)) => e
    	case _ =>
    }


这里定义了一绑定到变量的模式，e @ UnOp(“abs”,\_)，而整个模式是匹配了运用了两次 “abs” 操作的对象，如果匹配成功，e 将赋值为匹配 UnOp(“abs”,\_) 部分：比如：

    scala>  val expr = UnOp("abs",UnOp("abs",Number(5)))
    expr: UnOp = UnOp(abs,UnOp(abs,Number(5.0)))
    
    scala> expr match {
     | case UnOp("abs",e @ UnOp("abs",_)) => e
     | case _ =>
     |   }
    res0: Any = UnOp(abs,Number(5.0))



可以看到匹配 UnOp(“abs”,UnOp(“abs”,Number(5))) 成功后，e 赋值为 UnOp(abs,Number(5.0))。
