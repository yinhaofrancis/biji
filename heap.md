# （二叉）堆

​	(二叉)堆数据结构是数组对象由数组来创建。它被看做是完全二叉树 每个节点都有 父节点左右两个孩子节点 当前节点的小标为 i 数组起始下标为0

````C
 	//父节点
 	parent = Floor((i - 1)/2)
 	//左孩子
     left = i * 2 + 1
     //右孩子
     right = i * 2 + 2
````
​	二叉堆分为两种 一种 叫最小堆 一种叫最大堆  最大堆的堆顶元素为对中最大值, 最小堆的对顶元素为最小值

````C
	//最大堆的性质
	heap[parent] >= heap[i]
	//最小堆的性质
 	heap[parent] <= heap[i]
````

## 保持堆的性质

​	算法描述，从最后一个节点的parent开始倒序遍历到根节点确保每个节点和他的子节点保持堆的性质

````swift
        if let p = parent(end){
            for i in 0 ... p{
                let current = p - i
                if let l = leftChild(current){
                    if filter(a: array[l],b: array[current]){
                        exchange(current, index1: l)
                    }
                }
                if let r = rightChild(current){
                    if r <= end{
                        if filter(a: array[r],b: array[current]){
                            exchange(current, index1: r)
                        }
                    }
                }
            }
        }
````

## 堆排序

​	从堆的末尾开始遍历到小标为1的节点 遍历的当前元素依次和堆顶元素进行交换(一次交换之后，堆的性质会被破坏,需要重新恢复遍历下标之前的子堆的性质堆性质 eg. 当遍历的下标 i = 10 时 交换之后 恢复 0 … 9 堆的性质)

````swift
   func sort(){
        let n = self.array.count - 1
        for i in 0 ... n{
            let j = n - i
            self.exchange(0, index1: j)
            if j - 1 == 0{
                break
            }
            self.heapfy(j - 1, filter: filter)
        }
    }
````

