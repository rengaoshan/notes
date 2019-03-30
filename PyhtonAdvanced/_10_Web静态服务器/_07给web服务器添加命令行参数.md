# 10.7. 给web服务器添加命令行参数

目标
--

* 知道启动程序获取命令行参数

  import socket
  import re
  import gevent
  from gevent import monkey
  import sys


  # 让gevent能够识别耗时操作，让协程自动切换执行，失败的耗时操作由： recv, accept, time.sleep, 网络请求延时
  monkey.patch_all()


  # 封装的web服务器类
  class HttpWebServer(object):
  ​    def __init__(self, port):
  ​        # 创建tcp服务端套接字
  ​        tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  ​        # 设置socket选项，立即释放端口
  ​        tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
  ​        # 绑定端口号
  ​        tcp_server_socket.bind(("", port))
  ​        # 设置监听
  ​        tcp_server_socket.listen(128)
  ​        # 创建对象提供socket属性
  ​        self.tcp_server_socket = tcp_server_socket

      # 启动服务器
      def start(self):
          # 循环接收客户端的连接请求
          while True:
              service_client_socket, ip_port = self.tcp_server_socket.accept()
              # handle_client_request(service_client_socket)
              # 开辟协程并执行对应的任务
              gevent.spawn(self.handle_client_request, service_client_socket)
              # 不需要加上join，主要原有是我们的进程不会退出
              # g1.join()
      
      # 处理客户端的请求
      @staticmethod
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


  # 主函数
  def main():
  ​    print(sys.argv)
  ​    if len(sys.argv) != 2:
  ​        print("启动命令如下: python3 xxx.py 9090")
  ​        return
  ​    if not sys.argv[1].isdigit():
  ​        print("启动命令如下: python3 xxx.py 9090")
  ​        return
  ​    port = int(sys.argv[1])
  ​    print(port)

      server = HttpWebServer(port)
      server.start()


  if __name__ == '__main__':
  ​    main()


### 小结

终端启动python程序获取命令行参数使用sys.argv