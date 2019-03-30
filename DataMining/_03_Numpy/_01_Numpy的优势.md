# 3.1. Numpy的优势

学习目标
----

*   目标
    *   了解Numpy运算速度上的优势
    *   知道Numpy的数组内存块风格
    *   知道Numpy的并行化运算
*   应用
    *   机器学习,深度学习各种框架的基础库

3.1.1 Numpy介绍
-------------

![Numpy](../images/Numpy.png)

Numpy（Numerical Python）是一个开源的Python科学计算库，用于快速处理任意维度的数组。

Numpy支持常见的数组和矩阵操作。对于同样的数值计算任务，使用Numpy比直接使用Python要简洁的多。

Numpy使用ndarray对象来处理多维数组，该对象是一个快速而灵活的大数据容器。

3.1.2 ndarray介绍
---------------

    NumPy provides an N-dimensional array type, the ndarray, which describes a collection of “items” of the same type.


NumPy提供了一个**N维数组类型ndarray**，它描述了**相同类型**的“items”的集合。

![学生成绩数据](../images/学生成绩数据.png)

用ndarray进行存储：

    import numpy as np
    
    # 创建ndarray
    score = np.array([[80, 89, 86, 67, 79],
    [78, 97, 89, 67, 81],
    [90, 94, 78, 67, 74],
    [91, 91, 90, 67, 69],
    [76, 87, 75, 67, 86],
    [70, 79, 84, 67, 84],
    [94, 92, 93, 67, 64],
    [86, 85, 83, 67, 80]])
    
    score


返回结果：

    array([[80, 89, 86, 67, 79],
           [78, 97, 89, 67, 81],
           [90, 94, 78, 67, 74],
           [91, 91, 90, 67, 69],
           [76, 87, 75, 67, 86],
           [70, 79, 84, 67, 84],
           [94, 92, 93, 67, 64],
           [86, 85, 83, 67, 80]])


使用Python列表可以存储一维数组，通过列表的嵌套可以实现多维数组，那么为什么还需要使用Numpy的ndarray呢？

3.1.3 ndarray与Python原生list运算效率对比
--------------------------------

在这里我们通过一段带运行来体会到ndarray的好处

    import random
    import time
    import numpy as np
    a = []
    for i in range(100000000):
        a.append(random.random())
    t1 = time.time()
    sum1=sum(a)
    t2=time.time()
    
    b=np.array(a)
    t4=time.time()
    sum3=np.sum(b)
    t5=time.time()
    print(t2-t1, t5-t4)


t2-t1为使用python自带的求和函数消耗的时间，t5-t4为使用numpy求和消耗的时间，结果为：

![sumtest](../images/sumtest.png)

从中我们看到ndarray的计算速度要快很多，节约了时间。

**机器学习的最大特点就是大量的数据运算**，那么如果没有一个快速的解决方案，那可能现在python也在机器学习领域达不到好的效果。

![计算量大](../images/计算量大.png)

Numpy专门针对ndarray的操作和运算进行了设计，所以数组的存储效率和输入输出性能远优于Python中的嵌套列表，数组越大，Numpy的优势就越明显。

**思考：ndarray为什么可以这么快？**

3.1.4 ndarray的优势
----------------

#### 1 内存块风格

ndarray到底跟原生python列表有什么不同呢，请看一张图：

![numpy内存地址](../images/numpy内存地址.png)

从图中我们可以看出ndarray在存储数据的时候，数据与数据的地址都是连续的，这样就给使得批量操作数组元素时速度更快。

这是因为ndarray中的所有元素的类型都是相同的，而Python列表中的元素类型是任意的，所以ndarray在存储元素时内存可以连续，而python原生lis就t只能通过寻址方式找到下一个元素，这虽然也导致了在通用性能方面Numpy的ndarray不及Python原生list，但在科学计算中，Numpy的ndarray就可以省掉很多循环语句，代码使用方面比Python原生list简单的多。

#### 2 ndarray支持并行化运算（向量化运算）

#### 3 Numpy底层使用C语言编写，内部解除了GIL（全局解释器锁），其对数组的操作速度不受Python解释器的限制，效率远高于纯Python代码。