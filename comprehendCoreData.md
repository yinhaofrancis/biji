# 理解CoreData
## 访问数据
1. 上下文通过调用executeRequest(_:withContext:)方法把获取请求转交给它的持久化
   存储协调器。**请注意这里上下文将自己作为第二个参数传入 - 它在后面会被使用到。**

2. 持久化存储协调器通过调用每个存储上的executeRequest(_:withContext:)方法将获 取请求转发给所有的持久化存储们 (假如你有多个存储的话)。**再次注意:发起获取请求 的上下文被传递给了持久化存储。**

3. 持久化存储把获取请求转换成一个SQL语句,并把这个SQL语句发送给SQLite。

4. 也就是在相同的托管对象 上下文里,表示相同数据的对象的指针地址也是相等的。

5. 获取请求的includesPendingChanges属性默认值是true,在返回获取请求的结 果之前,上下文会将那些正在等待进行的更改考虑进来,并相应地更新原来的结果
   ![流程](https://raw.githubusercontent.com/yinhaofrancis/notes/master/fetch.png)在这个过程中,最重要的部分是 Core Data 的惰值化和唯一性机制。惰值允许你无需在内存中 实体化所有对象就能处理大数据集;唯一性可以确保对于相同的数据,你总是得到相同的对象, 并且有且仅有一个对象副本

## 对象惰值
1. `NSFetchRequest` 设置 `returnsObjectsAsFaults = true` 返回惰值 `false` 强制填充为惰值
2. `NSFetchRequest` 设置 `includesPropertyValues = true` 行缓存加载对象属性 `false` 只加载ID 属性填充一个用这样的请求取回的对象的惰 值,会导致另一次到 SQLite 的往返,除非这部分数据已经通过另一种方式取回了。这种获取请 求的前期开销比较低,但是使用对象的开销可能会很昂贵。
3. `NSManagedContext` Object invoke refreshObject 传一个 true 值并不 会把对象变成惰值;相反,它会从行缓存里更新那些不变的属性,并保留所有未保存的更改。 这几乎总是你想要做的 (refreshAllObjects() 方法的做法也是如此)。
   如果你指定 mergeChanges 为 false,这个对象会被强转成一个惰值,未保存的更改也会丢失。 所以使用它时需要非常谨慎,尤其要处理的关系上有未保存的更改时。在这种情况下,强行把 一个对象变成惰值可能会在你的数据里**引入参照完整性问题** (referential integrity issue)。 
> 参照完整性是数据库系统确保不同表里的关系保持一致的重要概念。因为一
> 个双向关系的一端可能会因为强制刷新而被破坏掉。

`NSFetchRequest`` shouldRefreshRefetchedObjects` 的选项,它会导致上下文自动刷新所有已 存在于上下文、同时也是获取结果一部分的对象。默认情况下,一个已存在的实体化对象不会被获取请求所改变。设置 shouldRefreshRefetchedObjects 为 true 是一种确保返回的对象具 有持久化存储里最新值的便捷方法
### 获取请求的结果类型
.DictionaryResultType。虽然这个结果类型有点复杂,但是它的功能非常强大。它 的基本思路是:一个使用字典结果类型的获取请求将不再返回一个托管对象的数组来表示你请 求的数据,而是返回一个包含原始数据的字典的数组。这种行为让一些有趣的使用场景成为可 能。

首先,你可以通过设置 propertiesToFetch 来指定只取回实体的某些属性。Core Data 之后只 会把这些特定的属性加载到内存里,如果你正在操作一个非常大的表的话,这样做有益于提升 性能和减小内存占用。

---------
p 86
