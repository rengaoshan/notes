# 7.5. 协程-greenlet

目标
--

*   知道greenlet模块可以完成多任务

### 1.greentlet的介绍

为了更好使用协程来完成多任务，python中的greenlet模块对其封装，从而使得切换任务变的更加简单

使用如下命令安装greenlet模块:

    pip3 install greenlet


    import time
    import greenlet


​    
    # 任务1
    def work1():
        for i in range(5):
            print("work1...")
            time.sleep(0.2)
            # 切换到协程2里面执行对应的任务
            g2.switch()


​    
    # 任务2
    def work2():
        for i in range(5):
            print("work2...")
            time.sleep(0.2)
            # 切换到第一个协程执行对应的任务
            g1.switch()


​    
    if __name__ == '__main__':
        # 创建协程指定对应的任务
        g1 = greenlet.greenlet(work1)
        g2 = greenlet.greenlet(work2)
    
        # 切换到第一个协程执行对应的任务
        g1.switch()


运行效果
----

    work1...
    work2...
    work1...
    work2...
    work1...
    work2...
    work1...
    work2...
    work1...
    work2...
