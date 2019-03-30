# 7.6. 协程-gevent

目标
--

*   能够使用gevent完成多任务

### 1\. gevent的介绍

greenlet已经实现了协程，但是这个还要人工切换，这里介绍一个比greenlet更强大而且能够自动切换任务的第三方库，那就是gevent。

gevent内部封装的greenlet，其原理是当一个greenlet遇到IO(指的是input output 输入输出，比如网络、文件操作等)操作时，比如访问网络，就自动切换到其他的greenlet，等到IO操作完成，再在适当的时候切换回来继续执行。

由于IO操作非常耗时，经常使程序处于等待状态，有了gevent为我们自动切换协程，就保证总有greenlet在运行，而不是等待IO

### 安装

    pip3 install gevent


### 1\. gevent的使用

    import gevent
    
    def work(n):
        for i in range(n):
            # 获取当前协程
            print(gevent.getcurrent(), i)
    
    g1 = gevent.spawn(work, 5)
    g2 = gevent.spawn(work, 5)
    g3 = gevent.spawn(work, 5)
    g1.join()
    g2.join()
    g3.join()


运行结果

    <Greenlet at 0x10e49f550: f(5)> 0
    <Greenlet at 0x10e49f550: f(5)> 1
    <Greenlet at 0x10e49f550: f(5)> 2
    <Greenlet at 0x10e49f550: f(5)> 3
    <Greenlet at 0x10e49f550: f(5)> 4
    <Greenlet at 0x10e49f910: f(5)> 0
    <Greenlet at 0x10e49f910: f(5)> 1
    <Greenlet at 0x10e49f910: f(5)> 2
    <Greenlet at 0x10e49f910: f(5)> 3
    <Greenlet at 0x10e49f910: f(5)> 4
    <Greenlet at 0x10e49f4b0: f(5)> 0
    <Greenlet at 0x10e49f4b0: f(5)> 1
    <Greenlet at 0x10e49f4b0: f(5)> 2
    <Greenlet at 0x10e49f4b0: f(5)> 3
    <Greenlet at 0x10e49f4b0: f(5)> 4


可以看到，3个greenlet是依次运行而不是交替运行

### 2\. gevent切换执行


    import gevent
    
    def work(n):
        for i in range(n):
            # 获取当前协程
            print(gevent.getcurrent(), i)
            #用来模拟一个耗时操作，注意不是time模块中的sleep
            gevent.sleep(1)
    
    g1 = gevent.spawn(work, 5)
    g2 = gevent.spawn(work, 5)
    g3 = gevent.spawn(work, 5)
    g1.join()
    g2.join()
    g3.join()


运行结果

    <Greenlet at 0x7fa70ffa1c30: f(5)> 0
    <Greenlet at 0x7fa70ffa1870: f(5)> 0
    <Greenlet at 0x7fa70ffa1eb0: f(5)> 0
    <Greenlet at 0x7fa70ffa1c30: f(5)> 1
    <Greenlet at 0x7fa70ffa1870: f(5)> 1
    <Greenlet at 0x7fa70ffa1eb0: f(5)> 1
    <Greenlet at 0x7fa70ffa1c30: f(5)> 2
    <Greenlet at 0x7fa70ffa1870: f(5)> 2
    <Greenlet at 0x7fa70ffa1eb0: f(5)> 2
    <Greenlet at 0x7fa70ffa1c30: f(5)> 3
    <Greenlet at 0x7fa70ffa1870: f(5)> 3
    <Greenlet at 0x7fa70ffa1eb0: f(5)> 3
    <Greenlet at 0x7fa70ffa1c30: f(5)> 4
    <Greenlet at 0x7fa70ffa1870: f(5)> 4
    <Greenlet at 0x7fa70ffa1eb0: f(5)> 4


### 3\. 给程序打补丁

    import gevent
    import time
    from gevent import monkey
    
    # 打补丁，让gevent框架识别耗时操作，比如：time.sleep，网络请求延时
    monkey.patch_all()


​    
    # 任务1
    def work1(num):
        for i in range(num):
            print("work1....")
            time.sleep(0.2)
            # gevent.sleep(0.2)
    
    # 任务1
    def work2(num):
        for i in range(num):
            print("work2....")
            time.sleep(0.2)
            # gevent.sleep(0.2)


​    
​    
    if __name__ == '__main__':
        # 创建协程指定对应的任务
        g1 = gevent.spawn(work1, 3)
        g2 = gevent.spawn(work2, 3)
    
        # 主线程等待协程执行完成以后程序再退出
        g1.join()
        g2.join()


运行结果

    work1....
    work2....
    work1....
    work2....
    work1....
    work2....


### 4\. 注意

*   当前程序是一个死循环并且还能有耗时操作，就不需要加上join方法了,因为程序需要一直运行不会退出

示例代码

    import gevent
    import time
    from gevent import monkey
    
    # 打补丁，让gevent框架识别耗时操作，比如：time.sleep，网络请求延时
    monkey.patch_all()


​    
    # 任务1
    def work1(num):
        for i in range(num):
            print("work1....")
            time.sleep(0.2)
            # gevent.sleep(0.2)
    
    # 任务1
    def work2(num):
        for i in range(num):
            print("work2....")
            time.sleep(0.2)
            # gevent.sleep(0.2)


​    
​    
    if __name__ == '__main__':
        # 创建协程指定对应的任务
        g1 = gevent.spawn(work1, 3)
        g2 = gevent.spawn(work2, 3)
    
        while True:
            print("主线程中执行")
            time.sleep(0.5)


执行结果:

    主线程中执行
    work1....
    work2....
    work1....
    work2....
    work1....
    work2....
    主线程中执行
    主线程中执行
    主线程中执行
    ..省略..
