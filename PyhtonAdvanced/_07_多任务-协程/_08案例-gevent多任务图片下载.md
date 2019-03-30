# 7.8. 案例-gevent多任务图片下载

目标
--

*   能够使用gevent完成多任务图片下载

### 1\. 多任务图片下载的示例代码

    import gevent
    import urllib.request # 网络请求模块
    from gevent import monkey
    
    # 打补丁： 让gevent使用网络请求的耗时操作，让协程自动切换执行对应的下载任务
    monkey.patch_all()
    
    # 根据图片地址下载对应的图片
    def download_img(img_url, img_name):
        try:
            print(img_url)
            # 根据图片地址打开网络资源数据
            response = urllib.request.urlopen(img_url)
            # 创建文件把数据写入到指定文件里面
            with open(img_name, "wb") as img_file:
                while True:
                    # 读取网络图片数据
                    img_data = response.read(1024)
                    if img_data:
                        # 把数据写入到指定文件里面
                        img_file.write(img_data)
                    else:
                        break
        except Exception as e:
            print("图片下载异常:", e)
        else:
            print("图片下载成功: %s" % img_name)


​    
    if __name__ == '__main__':
        # 准备图片地址
        img_url1 = "https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=551346117,2593226454&fm=27&gp=0.jpg"
        img_url2 = "https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=829730016,3409799239&fm=27&gp=0.jpg"
        img_url3 = "https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1815077192,817368579&fm=27&gp=0.jpg"
    
        # 创建协程指派对应的任务
        g1 = gevent.spawn(download_img, img_url1, "1.jpg")
        g2 = gevent.spawn(download_img, img_url2, "2.jpg")
        g3 = gevent.spawn(download_img, img_url3, "3.jpg")
    
        # 主线程等待所有的协程执行完成以后程序再退出
        gevent.joinall([g1, g2, g3])


运行结果


    https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=551346117,2593226454&fm=27&gp=0.jpg
    https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=829730016,3409799239&fm=27&gp=0.jpg
    https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1815077192,817368579&fm=27&gp=0.jpg
    图片下载成功: 3.jpg
    图片下载成功: 1.jpg
    图片下载成功: 2.jpg


**从上面的结果可以看到依次根据图片地址去下载，但是收到数据的先后顺序不一定与发送顺序相同，这也就体现出了异步，即不确定什么时候会收到数据，顺序不一定**

思考
--

请把上面的url换为自己需要下载音乐地址完成多任务下载.