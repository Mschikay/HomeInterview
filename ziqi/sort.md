# 堆排序

1. 从最后一个非叶子节点开始，找最大的数，使它到堆顶
2. 然后与最后一个叶子结点交换（此时最后一个已排序）
3. 堆的尺寸减小1（堆尾减一），重复1


[排序原理](https://www.cnblogs.com/onepixel/p/7674659.html)
[py源码](http://python.jobbole.com/82270/)

# 快排
```python
Python
def quick_sort(lists, left, right):
    # 快速排序
def quick_sort(lists, left, right):
    # 快速排序
    if left >= right:
        return lists
    key = lists[left]
    low = left
    high = right
    while left < right:
        while left < right and lists[right] >= key:
            right -= 1
        lists[left] = lists[right]
        while left < right and lists[left] <= key:
            left += 1
        lists[right] = lists[left]
    lists[right] = key
    quick_sort(lists, low, left - 1)
    quick_sort(lists, left + 1, high)
    return lists
```

