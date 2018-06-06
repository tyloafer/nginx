# rewrite

## 简介

| key     | value                               |
| ------- | ----------------------------------- |
| 语法:   | `rewrite regex replacement [flag];` |
| 默认值: | —                                   |
| 上下文: | `server`, `location`, `if`          |

 ## flag

- `last`       # 停止执行当前这一轮的`ngx_http_rewrite_module`指令集，然后查找匹配改变后URI的新location；
- `break`      # 停止执行当前这一轮的`ngx_http_rewrite_module`指令集；
- `redirect`         # 在replacement字符串未以“`http://`”或“`https://`”开头时，使用返回状态码为302的临时重定向；
- `permanent`        # 返回状态码为301的永久重定向。



## 例

针对于`last` 和 `break` 举例

1. 先测试last

   nginx配置

   ~~~
           location ~ /rewrite/last {
               rewrite .* /rewrite/break.html last;
           }   
   
           location ~ /rewrite/break {
           	retorn 200 'break';
               rewrite .* /last.html break;
           } 
   ~~~

   > GET http://test.tyloafer.cn/rewrite/last
   >
   > 结果： break

   上面结果表明，当我们第一次请求匹配到 `/rewrite/last` 的时候，我们此时应该把uri 重写成了 `http://test.tyloafer.cn/rewrite/break.html`, 然后拿着这个uri后找 nginx解析，从而匹配到了 第二个location

   即， 当我们使用 `last` 的时候，nginx会拿着解析后的uri再次进行解析

2. 再测试last

   nginx配置

   ~~~shell
           location ~ /rewrite/last {
           	return 200 'last';
               rewrite .* /rewrite/break.html last;
           }   
   
           location ~ /rewrite/break {
               rewrite .* /last.html break;
           } 
   ~~~

   last.html

   ~~~html
   this is last.html
   ~~~

   > GET http://test.tyloafer.cn/rewrite/break
   >
   > 结果： this is last.html

   上面结果表明，当我们第一次请求匹配到 `/rewrite/break的时候，我们此时应该把uri 重写成了 `http://test.tyloafer.cn/rewrite/blast.html`, 然后拿着这个uri后， 直接去找相应的文件了

   即， 当我们使用 `break` 的时候，nginx会拿着解析后的uri不在解析，就随他去吧