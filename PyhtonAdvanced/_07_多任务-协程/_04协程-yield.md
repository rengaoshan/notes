# 7.4. 协程-yield

目标
--

*   知道协程完成多任务的特点

### 1.协程的概念

> 协程，又称微线程，纤程,也称为用户级线程，在不开辟线程的基础上完成多任务，也就是在单线程的情况下完成多任务，多个任务按照一定顺序交替执行 通俗理解只要在def里面只看到一个yield关键字表示就是协程

**协程是也是实现多任务的一种方式**

### 协程yield的代码实现

### 简单实现协程

    import time
    
    def work1():
        while True:
            print("----work1---")
            yield
            time.sleep(0.5)
    
    def work2():
        while True:
            print("----work2---")
            yield
            time.sleep(0.5)
    
    def main():
        w1 = work1()
        w2 = work2()
        while True:
            next(w1)
            next(w2)
    
    if __name__ == "__main__":
        main()


运行结果：

    ----work1---
    ----work2---
    ----work1---
    ----work2---
    ----work1---
    ----work2---
    ----work1---
    ----work2---
    ----work1---
    ----work2---
    ----work1---
    ----work2---
    ...省略...


### 小结

协程之间执行任务按照一定顺序交替执行