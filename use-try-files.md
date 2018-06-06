# try_files

## 简介

| key     | value                                                 |
| ------- | ----------------------------------------------------- |
| 语法:   | `try_files file ... uri 或 try_files file ... = code` |
| 默认值: | —                                                     |
| 上下文: | `server`, `location`                                  |

其作用是按顺序检查文件是否存在，返回第一个找到的文件或文件夹(结尾加斜线表示为文件夹)，如果所有的文件或文件夹都找不到，会进行一个内部重定向到最后一个参数。 

需要注意的是，只有最后一个参数可以引起一个内部重定向，之前的参数只设置内部URI的指向。最后一个参数是回退URI且必须存在，否则会出现内部500错误。命名的location也可以使用在最后一个参数中。与rewrite指令不同，如果回退URI不是命名的location那么$args不会自动保留，如果你想保留$args，则必须明确声明。 



## 例

nginx配置

~~~
        location ~ /try-files {
            try_files /index.html /index.php =404 @location;
        }
        location @location {
            return 200 '@location';
        }
~~~

> GET http://test.tyloafer.cn/rewrite/try-files
>
> 结果： 
>
> ​	如果index.html存在， 返回index.html
>
> ​	如果index.html不存在，index.php 存在，会直接返回index.php的源码，而不解析，因为不是放在最后一个
>
> ​	如果前两个都不存在，则去寻找设置的 404.html
>
> ​	如果前三个页面都没有， 最后返回 @location