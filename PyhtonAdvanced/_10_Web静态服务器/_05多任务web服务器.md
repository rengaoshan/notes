# 10.5. 多任务web服务器

目标
--

*   知道如何实现多任务web服务器

### 多任务web服务器代码

    import socket
    import re
    import gevent
    from gevent import monkey
    
    # 让gevent能够识别耗时操作，让协程自动切换执行，失败的耗时操作由： recv, accept, time.sleep, 网络请求延时
    monkey.patch_all()


​    
    # 处理客户端的请求
    def handle_client_request(service_client_socket):
        # 获取客户端的请求报文数据
        client_request_data = service_client_socket.recv(4096)
        print(client_request_data)
        # GET /index2.html HTTP/1.1xxxxxxx
        client_request_conent = client_request_data.decode("utf-8")
        # 通过正则查找请求的资源路径
        match_obj = re.search("/\S*", client_request_conent)
    
        if not match_obj:
            print("访问路径有误")
            service_client_socket.close()
            return
    
        # 获取匹配结果
        request_path = match_obj.group()
        print(request_path)
    
        if request_path == "/":
            # 如果用户没有指定资源路径那么默认访问的数据是首页的数据
            request_path = "/index.html"
    
        # 读取指定文件数据
        # 使用rb的原因是浏览器也有可能请求的是图片
        try:
            with open("static" + request_path, "rb") as file:
                # 读取文件数据
                file_data = file.read()
        except Exception as e:
            # 准备响应报文数据
            # 响应行
            response_line = "HTTP/1.1 404 Not Found\r\n"
            # 响应头
            response_header = "Server: PWS1.0\r\nContent-Type: text/html;charset=utf-8\r\n"
            # 响应体 -> 打开一个404html数据把数据给浏览器
            response_body = "<h1>非常抱歉，您当前访问的网页已经不存在了</h1>".encode("utf-8")
    
            # 匹配响应报文数据
            response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
            # 发送响应报文数据
            service_client_socket.send(response_data)
        else:
            # 准备响应报文数据
            # 响应行
            response_line = "HTTP/1.1 200 OK\r\n"
            # 响应头
            response_header = "Server: PWS1.0\r\nContent-Type: text/html;charset=utf-8\r\n"
            # 响应体
            response_body = file_data
    
            # 匹配响应报文数据
            response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
            # 发送响应报文数据
            service_client_socket.send(response_data)
        finally:
            service_client_socket.close()


​    
    def main():
        # 创建tcp服务端套接字
        tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        # 设置socket选项，立即释放端口
        tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
        # 绑定端口号
        tcp_server_socket.bind(("", 9090))
        # 设置监听
        tcp_server_socket.listen(128)
        # 循环接收客户端的连接请求
        while True:
            service_client_socket, ip_port = tcp_server_socket.accept()
            # handle_client_request(service_client_socket)
            # 开辟协程并执行对应的任务
            gevent.spawn(handle_client_request, service_client_socket)
            # 不需要加上join，主要原有是我们的进程不会退出
            # g1.join()


​    
    if __name__ == '__main__':
        main()


### 小结

*   使用协程完成多任务web服务器，节省资源，效率比较高
*   gevent开辟协程如果程序一直运行则不需要加上join操作，因为程序没有退出