# 5.10. 案例-多任务版TCP服务端

目标
--

*   知道使用多线程实现多个客户端同时连接服务端

#### 功能要求

*   使用多任务实现多个客户端同时连接TCP服务端

### 1\. 多任务版TCP服务端代码

    import socket
    import threading
    
    # 接收客户端信息
    def recv_msg(service_client_socket, ip_port):
        while True:
            # 接收客户端的数据
            recv_data = service_client_socket.recv(1024)
            if recv_data:
                # 解码
                recv_content = recv_data.decode("gbk")
                print(recv_content)
                service_client_socket.send("ok,问题正在处理中....".encode("gbk"))
            else:
                print(ip_port, "客户端断开连接了")
                break
        # 终止和客户端的通信
        service_client_socket.close()


​    
    if __name__ == '__main__':
        # 创建tcp服务端socket
        tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        # 程序退出立即释放端口
        tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
        # 绑定端口
        tcp_server_socket.bind(("", 7891))
        # 设置监听，把主动套接字改变被动套接字，只能接收客户端的连接请求
        tcp_server_socket.listen(128)
        # 循环接收客户端的连接请求
        while True:
            # 接收客户端的连接请求
            service_client_socket, ip_port = tcp_server_socket.accept()
            print(ip_port)
    
            # 创建接收数据的子线程
            recv_thread = threading.Thread(target=recv_msg, args=(service_client_socket, ip_port))
            # 设置守护主线程，主线程退出子线程直接销毁
            recv_thread.setDaemon(True)
            # 启动接收数据的子线程
            recv_thread.start()
        # 关闭服务端socket，不接收客户端的连接请求
        tcp_server_socket.close()


### 小结

接收客户端数据的代码没有在主线程操作，而是开辟了子线程，这样就可以做到同时服务于多个客户端了。