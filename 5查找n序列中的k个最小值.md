### 查找最小的k 个元素
题目：输入n 个整数，输出其中最小的k 个。

#### 思路:

- 用选择或交换排序，即遍历n个数，先把最先遍历到得k个数存入大小为k的数组之中，对这k个数，利用选择或交换排序，找到k个数中的最大数kmax（kmax设为k个元素的数组中最大元素），用时O（k）（你应该知道，插入或选择排序查找操作需要O（k）的时间），后再继续遍历后n-k个数，x与kmax比较：如果x<kmax，则x代替kmax，并再次重新找出k个元素的数组中最大元素kmax‘（多谢kk791159796 提醒修正）；如果x>kmax，则不更新数组。这样，每次更新或不更新数组的所用的时间为O（k）或O（0），整趟下来，总的时间复杂度平均下来为：n*O（k）=**O（n\*k）**

### 代码:

```python
def find_max(li):
    max_value = li[0]
    index = 0
    for i in range(1, len(li)):
        if li[i] > max_value:
            max_value = li[i]
            index = i
    return max_value, index


def k_small(li_n, k):
    li_k = li_n[0:k]
    for i in range(k, len(li_n)):
        k_max, index = find_max(li_k)
        if li_n[i] < k_max:
            li_k[index] = li_n[i]
    return li_k


def main():
    li_n = [3, 1, 5, 2, 4, 1, 6, 213, 51, 5, 5, -1, 45, -5]
    k = int(input('请输入最小值个数: '))
    li_k = k_small(li_n, k)
    print(li_k)


if __name__ == '__main__':
    main()
```

#### 运行结果:

```python
请输入最小值个数: 5
[-5, 1, 1, 2, -1]
```



#### 思路参考:

- [CSDN: littlestream9527](https://blog.csdn.net/littlestream9527/article/details/8104731)



#### python数据结构: 堆

```python
class PriorityQueueBase:
    '''优先队列-堆 抽象基类'''

    class Item:
        # 轻量级组合来存储堆项目
        __slots__ = '_key', '_value'

        def __init__(self, key, value):
            self._key = key
            self._value = value

        def __lt__(self, other):
            # 比较大小
            return self._key < other._key

        def is_empty(self):
            return len(self) == 0

        def __str__(self):
            return str(self._key)


class HeapPriorityQueue(PriorityQueueBase):
    """堆结构"""
    def __init__(self):
        self._data = []

    def __len__(self):
        return len(self._data)

    def is_empty(self):
        # 判断是否为空
        return len(self) == 0

    def add(self, key, value):
        # 在后面加上然后加上
        self._data.append(self.Item(key, value))
        self._upheap(len(self._data) - 1)

    def min(self):
        if self.is_empty():
            raise ValueError("Priority queue is empty.")
        item = self._data[0]
        return (item._key, item._value)

    def remove_min(self):
        if self.is_empty():
            raise ValueError("Priority queue is empty.")
        self._swap(0, len(self._data) - 1)
        item = self._data.pop()
        self._downheap(0)
        return (item._key, item._value)

    def _parent(self, j):
        '''
            0
           / \
          1   2
        :param j: 子节点下标
        :return: 父节点下标
        '''
        return (j - 1) // 2

    def _left(self, j):
        '''
            0
           / \
          1   2
        :param j: 节点下标
        :return: 左子节点小标
        '''
        return 2 * j + 1

    def _right(self, j):
        '''
            0
           / \
          1   2
        :param j: 节点下标
        :return: 右子节点小标
        '''
        return 2 * j + 2

    def _has_left(self, j):
        return self._left(j) < len(self._data)

    def _has_right(self, j):
        return self._right(j) < len(self._data)

    def _swap(self, i, j):
        self._data[i], self._data[j] = self._data[j], self._data[i]

    def _upheap(self, j):
        # 往上交换
        parent = self._parent(j)
        if j > 0 and self._data[j] < self._data[parent]:
            self._swap(j, parent)
            self._upheap(parent)

    def _downheap(self, j):
        # 往下交换, 递归比较三个值
        # 判断左子树是否存在
        if self._has_left(j):
            # 得到左子节点的下标
            left = self._left(j)
            # 初始化最小值的下标为左子节点的下标
            small_child = left
            # 判断右子树是否存在
            if self._has_right(j):
                # 得到右子树下标
                right = self._right(j)
                # 比较左右字节点的大小, 如果右子树比左子树小则重新赋值最小值下标为右子树下标
                if self._data[right] < self._data[left]:
                    small_child = right
            # 比较根节点和最小子节点的大小, 如果根节点比最小子节点小则与最小子节点交换
            if self._data[small_child] < self._data[j]:
                self._swap(j, small_child)
                # 继续下沉最小子节点, 直至到合适的位置
                self._downheap(small_child)
                

def main():
	heap = HeapPriorityQueue()
	heap.add(4, "D")
	heap.add(3, "C")
	heap.add(1, "A")
	heap.add(5, "E")
	heap.add(2, "B")
	heap.add(7, "G")
	heap.add(6, "F")
	heap.add(26, "Z")

	for item in heap._data:
    	print(item)

	print("min is: ")
	print(heap.min())
	print()

	print("remove min: ")
	print(heap.remove_min())
	print("Now min is: ")
	print(heap.min())
	print()

	print("remove min: ")
	print(heap.remove_min())
	print("Now min is: ")
	print(heap.min())
	print()

	heap.add(1, "A")
	print("Now min is: ")
	print(heap.min())
	print()
    
if __name__ == '__main__':
    main()
```

运行结果:

```python
1
2
3
5
4
7
6
26
min is: 
(1, 'A')

remove min: 
(1, 'A')
Now min is: 
(2, 'B')

remove min: 
(2, 'B')
Now min is: 
(3, 'C')

Now min is: 
(1, 'A')
```

#### 知识点参考:

- [博客园: kumata](https://www.cnblogs.com/kumata/p/9201571.html)

