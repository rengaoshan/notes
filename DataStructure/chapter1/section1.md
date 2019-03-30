# 1.1. 第一次尝试

    import time
    
    start_time = time.time()
    
    # 注意是三重循环
    for a in range(0, 1001):
        for b in range(0, 1001):
            for c in range(0, 1001):
                if a**2 + b**2 == c**2 and a+b+c == 1000:
                    print("a, b, c: %d, %d, %d" % (a, b, c))
    
    end_time = time.time()
    print("elapsed: %f" % (end_time - start_time))
    print("complete!")


运行结果：

    a, b, c: 0, 500, 500
    a, b, c: 200, 375, 425
    a, b, c: 375, 200, 425
    a, b, c: 500, 0, 500
    elapsed: 214.583347
    complete!


**注意运行的时间:214.583347秒**