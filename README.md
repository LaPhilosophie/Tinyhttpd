
# 函数调用分析
- main函数调用了start函数进行服务器端的监听套接字绑定
  - start函数包含了套接字的创建、设置、绑定等，牵扯到网络编程的一些知识点，比如sockaddr_in结构体、bind函数等
- 主函数进入死循环，使用accept函数阻塞等待客户端的网络连接请求
- 使用pthread_creat函数创建线程
- accept_request函数判断http请求是post还是get，从而决定是直接向client传送文件还是执行cgi脚本
- execute_cgi函数是该项目的精髓，包含了匿名管道、进程间通信、fork进程、环境变量等知识
- **对代码的注释的repository：** https://github.com/LaPhilosophie/Tinyhttpd

# 一些分析图
![函数调用链分析](https://gls.show/image/Snipaste_2022-08-23_23-22-55.png)

![管道的分析图](https://gls.show/image/tinyhttpd-work-flow1.png)

# 网络编程与套接字
- 套接字A用于监听和接受客户端请求，绑定监听的ip地址和端口，阻塞等待客户端连接请求
- 如果套接字A接收到了客户端的连接请求，那么服务器会创建出新的套接字B，用于和客户端的通信，而之前的监听套接字A继续监听，如果有别的来自客户端的连接请求，那么会继续创建出和客户端通信的套接字C、D、E...
- 服务器与客户端的通信模型图：

![](https://docs.oracle.com/cd/E38902_01/html/E38880/figures/7099.png)

# 进程相关知识

```
int pthread_create(pthread_t *thread,
                   const pthread_attr_t *attr,
                   void *(*start_routine) (void *),
                   void *arg);

```
上述函数的四个参数分别为：
- 线程的地址
- 手动设置新建线程的属性，若为NULL，那么会采用系统的默认属性值设置线程属性
- 以函数指针的方式指明新建线程需要执行的函数，形参和返回值的类型都必须为 void* 类型
- arg是函数的参数

线程创建成功时返回0，失败为非0

# http协议请求头格式
```
{method} {path} {http_version}(CRLF)
{header_name}: {header_value}(CRLF)
...more header info(CRLF)
(CRLF)
{content}
```
第一行（消息行）包含 HTTP 方法和目标，例：
```
GET /mysite/mydirectory/index.html HTTP/1.1\r\n
```
格式中的变量说明
```
method: GET,POST,PUT,DELETE 等等
path: URL路径部分，如：/index.html
version：如：http/1.1
header_name: 如：Host
header_value: 如： www.xxx.com
```

# 抓包
可以通过chrome浏览器调试，先后顺序分别为：f12、network、preserve log、name、header


# 参考
- https://blog.csdn.net/wenqian1991/article/details/46011357
- https://phenix3443.github.io/notebook/c/tinyhttpd-analysis.html
![](/image/tinyhttpd-work-flow.png)


![](/image/20150527211620041.png)