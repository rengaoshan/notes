# 4.4. Pandas画图

学习目标
----

*   目标
    *   了解DataFrame的画图函数
    *   了解Series的画图函数
*   应用
    *   股票每日数据的统计

4.4.1 pandas.DataFrame.plot
---------------------------

*   `DataFrame.plot`(_x=None_, _y=None_, _kind='line'_)

    *   x : label or position, default None
    *   y : label, position or list of label, positions, default None
        *   Allows plotting of one column versus another
    *   kind : str
        *   ‘line’ : line plot (default)
        *   ‘bar’ : vertical bar plot
        *   ‘barh’ : horizontal bar plot
        *   ‘hist’ : histogram
        *   ‘pie’ : pie plot
        *   ‘scatter’ : scatter plot

> 更多参数细节：[https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.plot.html?highlight=plot#pandas.DataFrame.plot](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.plot.html?highlight=plot#pandas.DataFrame.plot)

4.4.2 pandas.Series.plot
------------------------

> 更多参数细节：[https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.plot.html?highlight=plot#pandas.Series.plot](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.plot.html?highlight=plot#pandas.Series.plot)