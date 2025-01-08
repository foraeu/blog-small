---
title: "矩阵合并的优化"
date: 2024-11-13T19:31:18+08:00
draft: false
tags: ["矩阵合并","python","numpy"]
---
在处理数据时，有时需要多次按行合并矩阵。本文将探讨两种不同的矩阵合并方式，并分析哪种方法更高效。

问题是这样的：有1个矩阵，假设其shape为m*n。现在希望对于某一行l1，将其它的行依次添加到该行的后部，但由于列数限制为2n，需要生成m行，每行分别为[l1,l2],[l1,l3],……,[l1,lm]。将这种处理作用于所有的行，最终能够得到一个(m-1)*m行，2n列的矩阵。

## 方法一

创建一个列数为2n的空矩阵`matrix_end`，在循环中，先依次对原矩阵每一行生成m-1行，2n列的矩阵，每行分别为[li,l1],[li,l2],……,[li,lm]，然后将该矩阵与`matrix_end`合并。实现如下：

```python
import numpy as np
import time

# 设置随机种子
np.random.seed(0)

# 记录运行时间
start = time.time()

# 随机生成1000行5列的矩阵init_matrix
init_matrix = np.random.randint(0, 10, size=(1000, 5))

matrix_end = np.empty((0, init_matrix.shape[1]*2))

# 逐行合并
for idx in range(init_matrix.shape[0]):
    neighbor_rows = np.delete(init_matrix, idx, axis=0)
    row = np.concatenate([init_matrix[idx] + 0 * neighbor_rows, neighbor_rows], axis=1)
    matrix_end = np.concatenate([matrix_end, row], axis=0)

end = time.time()
print("Time:", end - start)
```

最终的运行时间为7.4s

## 方法二

创建一个空列表，在循环中，先得到m-1行，2n列的矩阵，将结果存储到列表中，之后在循环外一次性将所有行合并成一个矩阵。实现如下：

```python
import numpy as np
import time

# 设置随机种子
np.random.seed(0)

# 记录运行时间
start = time.time()

# 随机生成1000行5列的矩阵init_matrix
init_matrix = np.random.randint(0, 10, size=(1000, 5))

matrix_list = []

for idx in range(init_matrix.shape[0]):
    neighbor_rows = np.delete(init_matrix, idx, axis=0)
    row = np.concatenate([init_matrix[idx] + 0 * neighbor_rows, neighbor_rows], axis=1)
    matrix_list.append(row)

# 一次性合并所有行
matrix = np.vstack(matrix_list)

end = time.time()
print("Time:", end - start)
```

最终的运行时间为0.05s

为何性能相差如此之大？

对于方法一，由于每次调用`np.concatenate`都会创建一个新的数组，所有的数据（原矩阵 + 新增的行）会被复制到新的数组中，内存需要重新分配。随着矩阵行数的增加，每次合并时的**内存重新分配**和**数据复制**的开销也会随之增加，导致性能下降。

对于方法二，首先是使用`matrix_list.append(row)`将每一行的数据先存储在一个列表中，列表操作是非常高效的，因为它只是将数据引用添加到内存中，而不需要重新分配内存或复制数据。其次是最后通过`np.vstack(matrix_list)`一次性将所有的行合并成一个新的矩阵，`np.vstack`会一次性计算最终矩阵的大小并分配内存，然后将所有的行合并到一起。这比逐行合并要高效得多。

在常规观念里，使用`numpy`进行操作往往比python自带的list,dict更加高效。在这个例子里，我们也看到了list数据结构的优势。虽然`numpy`在处理大规模矩阵和向量运算时有显著的性能优势，因为它是基于底层的C语言实现，并且能够通过向量化操作减少循环的开销，但在某些情况下，使用python的list可以在**内存管理**和**操作的灵活性**上带来额外的好处。

在我们讨论的优化过程中，使用list来积累行数据比每次都通过`np.concatenate`合并矩阵要更加高效。**Python列表的内存管理是动态的，`NumPy`数组的内存管理是静态的，数组的大小在创建时就已经确定**。list的`append()`操作是一个时间复杂度为 `O(1)` 的常数时间原位操作，意味着它的开销非常小。而`np.concatenate`是非原位操作，每次合并矩阵时，都会导致内存重新分配和数据复制，这种操作的时间复杂度是 `O(N)`，尤其是在需要反复进行矩阵拼接时，矩阵也会增大，性能下降非常明显。

因此，在需要多次进行矩阵合并或行拼接时，先将结果存储在`list`中，再使用`numpy`的批量合并操作（如`np.vstack`或`np.concatenate`）来一次性合并，这种方法能够显著提高性能，减少不必要的内存操作和数据复制。