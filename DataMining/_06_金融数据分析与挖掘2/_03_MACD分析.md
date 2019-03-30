# 6.3. MACD分析

学习目标
----

*   目标
    *   说明MACD的三种交易信号分析
    *   应用talib.MACD实现指标计算与结果分析
*   应用
    *   无

什么是MACD指标呢，我们可以通过行情软件，来看一看

![MACD图](../images/MACD图.png)

6.3.1 MACD指标介绍
--------------

指数平滑异同移动平均线（Moving Average Convergence /Divergence, MACD）是股票交易中一种常见的技术分析工具，由Gerald Appel于1970年代提出，用于**研判股票价格变化的强度、方向、能量，以及趋势周期，以便把握股票买进和卖出的时机。**

6.3.2 MACD的原理以及计算公式
-------------------

### 6.3.2.1 原理

原理：**MACD的意义和双移动平均线基本相同，即由快、慢均线的离散、聚合表征当前的多空状态和股价可能的发展变化趋势**，但阅读起来更方便

### 6.3.2.2 计算公式

*   **1、MACD首先行计算出快速（一般选12日）移动平均值与慢速（一般选26日）移动平均值**
*   **2、12日EMA数值减去26日EMA数值得到，差离值DIF**
*   **3、根据离差值计算其9日的EMA，即离差平均值，是所求的MACD值。为了不与指标原名相混淆，又名DEA或DEM(讯号线)**
*   **4、DIF与DEA的差值，为MACD柱状图**

### 6.3.2.3 交易信号种类

*   差离值（DIF值）与讯号线（DEA值，又称MACD值）相交；
*   差离值与坐标轴相交；
*   股价与差离值的背离。

### 6.3.2.4 交易信号分析

* 差离值值和讯号线

  差离值（DIF）形成“快线”（短期线），讯号线（DEM）形成“慢线”（长期线）。
  当差离值（DIF）从下而上穿过讯号线（DEM），为买进讯号(金叉)；相反若从上而下穿越，为卖出讯号。(死叉)

* 差离值（MACD柱状图）

  *   1、当红柱状持续放大时，表明投资市场处于牛市行情中，价格走势将继续上涨，这时应持仓待涨或短线买入投资品种，直到红柱无法再放大时才考虑卖出
  *   2、当绿柱状持续放大时，表明投资市场处于熊市行情之中，价格走势将继续下跌，这时应持币观望或卖出投资品种，直到绿柱开始缩小时才可以考虑少量买入投资品种。
  *   3、当红柱状开始缩小时，表明投资市场牛市即将结束（或要进入调整期），价格走势将大幅下跌，这时应卖出大部分投资品种而不能买入投资品种。
  *   4、当绿柱状开始收缩时，表明投资市场的大跌行情即将结束，价格走势将止跌向上（或进入盘整），这时可以少量进行长期战略建仓而不要轻易卖出投资品种。
  *   5、当红柱开始消失、绿柱开始放出时，这是投资市场转市信号之一，表明投资市场的上涨行情（或高位盘整行情）即将结束，价格走势将开始加速下跌，这时应开始卖出大部分投资品种而不能买入投资品种。
  *   6、当绿柱开始消失、红柱开始放出时，这也是投资市场转市信号之一，表明投资市场的下跌行情（或低位盘整）已经结束，价格走势将开始加速上升，这时应开始加码买入投资品种或持仓待涨。

6.3.3 TA-Lib库进行指标运算
-------------------

![talib](../images/talib.png)

![TA-lib函数图](../images/TA-lib函数图.png)

6.3.4 案例： 计算MACD指标、画出图形
-----------------------

效果：

![MACD画图](../images/MACD画图.png)

### 6.3.4.0 导入包

    import pandas as pd
    import talib
    import matplotlib.pyplot as plt
    import numpy as np
    from mpl_finance import candlestick_ochl


### 6.3.4.1 获取股票日线数据

实现思路和步骤是:

1. 从文件读取股票数据
2. 按照日期索引排序
3. 增加一列index索引数据, 后续不需要日期索引
4. 抽取index, open, close, high, low五列数据, 后续用来计算MACD值, 其他的值不需要

   # 读取日线的数据
   stock_day = pd.read_csv("./data/stock_day/stock_day.csv")
   stock_day = stock_day.sort_index()[:200]
   stock_day['index'] = [i for i in range(stock_day.shape[0])]
   arr = stock_day[['index', 'open', 'close', 'high', 'low']].values


### 6.3.4.2 分析

*   计算MACD值的TA-Lib函数

![MACD函数使用](../images/MACD函数使用.png)

*   传入的参数序列必须是Numpy序列
*   返回的值
    *   macd:差离值
    *   macdsignal:信号线
    *   macdhist:MACD柱状图值

方法: 使用股票的收盘价格(close)去计算MACD指标数据 快速移动周期为12，慢速移动周期为26，DIF的移动平均线的周期为9日

    # dif: 12， 与26日的差别
    # dea:dif的9日以移动平均线
    dif, dea, macd_hist = talib.MACD(stock_day['close'].values, fastperiod=12, slowperiod=26, signalperiod=9)


* 通过画图展示MACD值
* 步骤:
  *   构造画布，里面包含了一个axes
  *   产生一个x的单维数组
  *   画出DIF差离值线
  *   画出MACD柱状图
  *   显示一下K线图对比MACD指标图

  # 构造画布，里面包含了一个axes
  fig, axes = plt.subplots(nrows=1, ncols=1, figsize=(20, 8), dpi=100)

  # 产生一个x的单维数组
  index = [i for i in range(200)]

  # 画出dif这根差离值线
  plt.plot(index, dif, color='y', label="差离值 DIF")
  plt.plot(index, dea, color='b', label="讯号线 DEA")

  # 画出MACD柱状图
  # 分开正负的柱状图去画出来
  # 画第一个bar， macd_hist，如果大于0， 保留当前值，如果小于0，变为0，得出一个red_hist
  # 画出第二个bar，macd_hisr，如果小于0， 保留当前值，如果大于0，直接变为0
  red_hist = np.where(macd_hist > 0 , macd_hist, 0)
  green_hist = np.where(macd_hist < 0 , macd_hist, 0)

  plt.bar(index, red_hist, label="红色MACD值", color='r')
  plt.bar(index, green_hist, label="绿色MACD值", color='g')

  # 显示一下K线图对比MACD指标图
  candlestick_ochl(axes, arr, width=0.2, colorup='r', colordown='g')

  plt.legend(loc="best")
  plt.show()

* 背离分析（了解）

当股价创新低，但MACD并没有相应创新低（牛市背离或顶背离），视为**利多**讯息，股价跌势或将完结。相反，若股价创新高，但MACD并没有相应创新高（熊市背离或底背离），视为**利空**讯息。

![背离分析1](../images/%E8%83%8C%E7%A6%BB%E5%88%86%E6%9E%901.png)

![背离分析2](../images/%E8%83%8C%E7%A6%BB%E5%88%86%E6%9E%902.png)

![背离分析3](../images/背离分析3.png)

6.3.5 MACD总结
------------

MACD是一种中长线的研判指标。当股市强烈震荡或股价变化巨大（如送配股拆细等）时，可能会给出错误的信号。所以在决定股票操作时，应该谨慎参考其他指标，以及市场状况，不能完全信任差离值的单一研判，避免造成损失。