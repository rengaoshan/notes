# 5.9. 案例-多任务版udp聊天器

目标
--

*   使用多线程完成多任务，实现聊天器收发小时同时进行

#### 说明

> *   编写一个有2个线程的程序
> *   线程1用来接收数据然后显示
> *   线程2用来检测键盘数据然后通过udp发送数据

#### 要求

> 1.  实现上述要求
> 2.  总结多任务程序的特点

#### 参考代码:

    import socket
    import threading


​    
    # 发送数据的功能函数
    def send_msg(udp_socket):
        # 接收用户输入的数据
        send_content = input("请输入您要发送的数据:")
        # 对数据进行gbk编码
        send_data = send_content.encode("gbk")
        # 接收对方的ip地址和端口号
        dest_ip = input("请输入对方的ip地址:")
        dest_port = int(input("请输入对方的端口号:"))
        # 发送数据
        udp_socket.sendto(send_data, (dest_ip, dest_port))


​    
    # 接收数据的功能函数
    def recv_msg(udp_socket):
        while True:
            # 接收数据 1024:表示每次接收数据的最大字节数
            recv_data, ip_port = udp_socket.recvfrom(1024)
            # 解码数据
            recv_content = recv_data.decode("gbk")
            print(recv_content, ip_port)


​    
    if __name__ == '__main__':
        # 创建udpsocket
        udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
        # 绑定端口， 提示：客户端不强制要求绑定端口
        # 在同一个电脑上端口号不能重复
        udp_socket.bind(("", 8080))
        # 创建接收数据的线程
        recv_thread = threading.Thread(target=recv_msg, args=(udp_socket,))
        # 设置成为守护主线程，主线程退出后子线程直接销毁
        recv_thread.setDaemon(True)
        recv_thread.start()
    
        while True:
            # 接收用户的指令
            option = input("请输入功能选项 1. 发送 2. 退出:")
            if option == "1":
                # 发送数据
                send_msg(udp_socket)
    
            elif option == "3":
                break
    
        # 关闭socket
        udp_socket.close()


### 小结

*   子线程任务执行不完程序退出不了，解决办法设置子线程守护主线程