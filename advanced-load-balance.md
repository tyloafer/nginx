# 负载均衡

## 简介

| key  | value                    |
| ---- | ------------------------ |
| 语法   | ` upstream name { ... }` |
| 默认值  | -                        |
| 上下文  | http                     |



### 调度算法

- 轮询（默认）。每个请求按时间顺序逐一分配到不同的后端服务器，如果后端某台服务器宕机，故障系统被自动剔除，使用户访问不受影响。
- Weight       指定轮询权值，Weight值越大，分配到的访问机率越高，主要用于后端每个服务器性能不均的情况下。
- ip_hash       每个请求按访问IP的hash结果分配，这样来自同一个IP的访客固定访问一个后端服务器，有效解决了动态网页存在的session共享问题。
- least_conn  最小连接数，根据后端的连接数来找出一个连接数最少的服务器进行调度
- least_time    最小延时  根据后端服务器的延时大小，来找出一个延时最小的服务器进行调度

### 状态参数

- max_cons    表示一套服务器的最大连接数， 超出不再连接 默认值0，表示无限制。 
- max_fails     最大的失败次数，超出这个次数后， 会在设置的时间内不再连接，也就是下面的 `c`
- fail_timeout  失败超时连接，在这个时间范围内，连接失败超出次数的服务器，将认为是不可用的，这个时间段内，不再参与调度的工作
- backup    备份服务器  只有主服务器都down的时候，这台服务器才会起作用
- down    宕机服务器，不再起作用



## 例

### 轮询

nginx 的 **http段** 配置如下 

~~~
    upstream weight-load {
        server 127.0.0.1:8000;
        server 127.0.0.1:8001;
    }   
~~~

nginx的 **server段** 配置如下

~~~
         location ~ /weight-load {
             proxy_pass http://weight-load;
         }
~~~

监听端口的配置

~~~
    server {
        server_name 127.0.0.1;
        listen 8000;
        listen 8001;
        listen 8002;
        listen 8003;

        add_header  Content-Type 'text/html; charset=utf-8';
        location / { 
            return 200 $server_port;
        }   
        
    } 
~~~

> GET http://test.tyloafer.cn/weight-load
>
> 结果： 分别返回8000 和 8001， 次数比较平均

## 加权轮询

基于上面的配置，我们只需要修改 **http段** 内的配置即可

~~~
    upstream weight-load {
        server 127.0.0.1:8000;
        server 127.0.0.1:8001 weight=5;
    }   
~~~

> GET http://test.tyloafer.cn/weight-load
>
> 结果： 分别返回8000 和 8001， 8001返回频率提高

## ip_hash

在 **http段** 增加如下配置

~~~
    upstream ip-load {
        ip_hash;
        server 127.0.0.1:8002;
        server 127.0.0.1:8003;
    }
~~~

> GET http://test.tyloafer.cn/ip-load
>
> 结果：电脑： 8002
>
> ​	    本地curl： 8003
>
> ​	    且刷新无变化