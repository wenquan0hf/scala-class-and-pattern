# 模式的种类(三) #
**类型模式**

你可以使用类型模式匹配来代替类型检查和类型转换。比如：

    def generalSize(x:Any) = x match{
    	case s:String => s.length
    	case m:Map[_,_] =m.size
    	case _ => -1
    }

函数 generalSize 返回某些对象长度或是大小。它的参数类型为 Any，因此可以传入任意类型的数据。 模式 s:String 为一类型模式，匹配任意类型为 String 的非空对象。变量 s 为匹配的字符串。

第二个匹配 m:Map[_,_] 匹配任意类型的 Map 对象，这里我们不关心具体的 key 和 value，因此使用通配符\_，如果你需要在后面表达式中使用 key，value，可以使用 key，value 替换掉\_。

对于 Map 类型的数据，我们能否匹配指定 Key 或 value 类型的数据呢，比如：

    def  isIntIntMap(x:Any) = x match {
    	case m:Map[Int,Int]=>true
    	case _ => false
    }


此时编译器会给出警告：

    <console>:9: warning: non-variable type argument Int in type pattern Map[Int,Int] is unchecked since it is eliminated by erasure
       case m:Map[Int,Int]=>true
      ^
    isIntIntMap: (x: Any)Boolean

Scala 和 Java 类似对于 generic 类采用了 ”type erasure”，也就是说运行时不保存 Map 的 Key 和 Value 的类型，因此我们无法匹配指定类型 Key 或 Value 的 Map 对象。  
我们可以看到 

    scala> isIntIntMap(Map(1->1))
    res14: Boolean = true
    
    scala> isIntIntMap(Map("a"->"b"))
    res15: Boolean = true

这两个都返回 true，这个和预期不同，因此对于这种情况，编译器会给出警告，pattern Map[Int,Int] 中的类型不起作用。

但有一个特例，数组和一般的 generic 处理不同，它支持匹配元素类型。比如：

    def isStringArray(x:Any) = x match{
    	case a:Array[String]=>"yes"
    	case _ => "no"
    }

测试结果如下：  

    scala> val as =Array("abc")
    as: Array[String] = Array(abc)
    
    scala> isStringArray(as)
    res16: String = yes
    
    scala> val ai = Array(1,2,3)
    ai: Array[Int] = Array(1, 2, 3)
    
    scala> isStringArray(ai)
    res17: String = no
    
