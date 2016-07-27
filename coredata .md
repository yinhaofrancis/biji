# core data 笔记 

> objc.io Core Data

## 可选值的约定

Swift 提供了 Optional 数据类型,这迫使我们显式地思考和处理没有值的情况
避免使用 Swift 的 ! 操作符来强制解包
唯一的例外是那些必须设置但又无法在初始化时设置的属性。比如 Interface Builder 的 outlets 或必要的代理 (delegate) 属性等。在这些情况下,使用隐式解包的可选值符合 “尽早崩 溃” 原则
## 错误处理的约定
Core Data 中好些方法会抛出错误。基于它们是不同类型的错误的这一基本事实,我们可以分 类处理这些错误。我们将区分逻辑错误和其他错误。
逻辑错误是指程序员犯错的结果。它们应该从代码层面上修复而不应该尝试动态恢复程序的运
行。

一个例子,当你尝试读取应用程序包里的一个文件的时候,因为应用程序包是只读的,那么一
个文件要么存在要么不存在,而且它的内容永远不会变。所以如果我们无法打开或者解析应用
程序包里的文件,这就是一个逻辑错误。

对于这些类型的错误,我们使用 Swift 的 try ! 或 fatalError () 来尽可能早地让应用程序崩溃。

同样的思想可以适用于 as! 操作符的强制类型转换: 如果我们知道一个对象必须是某种类型,转
换失败的唯一原因会是逻辑错误,这种时候我们实际上是希望应用程序崩溃的。
很多时候我们用 Swift 的 guard 关键字来更好地表达哪些地方出错了。例如 fetched results controller 返回的类型是 NSManagedObject 的对象,我们知道它必须是一个特定的子类,我 们使用 guard 来保证向下转换,并在出错的时候使用 fatal error 来终止程序:
````swift
func objectAtIndexPath(indexPath: NSIndexPath) -> Object {
	guard let result = fetchedResultsController.objectAtIndexPath(indexPath) as? Object else
	{
		fatalError("Unexpected object at \(indexPath)")
	}
	return result
}
````
## 数据建模
### 实体与属性
Core Data 自身就支持很多数据类型:数值类型 (整数和不同大小的浮点数,以及十进制数值), 字符串,布尔值,日期,二进制数据,以及存储着实现了 NSCoding 协议的对象或者是提供了 自定义值转换器 (value transformer) 的对象的可转换类型。
### 属性选项

- 可选属性


- 必选属性
  必选属性必须要赋给它们恰当的值,才能保存这些数据。把一个属性标记为可索引时,Core Data 会在底层 SQLite 数据库表里创建一个索引。索引可以加速这个属性的搜索和排序,但代 价是插入数据时的性能下降和额外的存储空间。在我们的例子里,我们会以 mood 对象的时间 来排序,所以把 date 属性标记为可索引是有意义的。我们后面会在性能和性能分析这些章节里
  深入探讨这个主题。
------

p(22页)
