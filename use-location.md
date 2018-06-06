# location

## 简介

| key   | value |
| ------- | -------------------------------------- |
| 语法:   | `location operation condition { ... }` |
| 默认值: | —                                      |
| 上下文: | `server`                               |

## operation
- `~`      #波浪线表示执行一个正则匹配，区分大小写
- `~*`    #表示执行一个正则匹配，不区分大小写
- `^~`    #^~表示普通字符匹配，不是正则匹配。如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录
- `=`      #进行普通字符精确匹配
- `@`     #"@" 定义一个命名的 location，使用在内部定向时，例如 error_page, try_files



## 操作符优先级

1. =前缀的指令严格匹配这个查询。如果找到，停止搜索。
2. 所有剩下的常规字符串，最长的匹配。如果这个匹配使用^前缀，搜索停止。
3. 正则表达式，在配置文件中定义的顺序。
4. 如果第3条规则产生匹配的话，结果被使用。否则，如同从第2条规则被使用。

即 ： (location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/) 



# 例

Nginx配置如下

~~~
        location ~ /self-variable(/)(.*)$ {
            set $path 'tyloafer';
            if ($request_uri ~ 'haha') {
                return 200 $1;
            }
            return 200 "path = $path , 1 = $1 , 2 = $2";
        }

        location = /location {
            return 200 '= /location';
        }

        location ~ /location[a-z]+ {
            return 200 '~ /location[a-z]+';
        }

        location ~ /location.*$ {
            return 200 '~ /location.*';
        }

        location ^~ /locationa {
            return 200 ' ^~ /locationa';
        }

        location ~* /location {
            return 200 '*~ /location';
        }

        location /test-location {
            try_files index.php1 @location;
        }

        location @location {
            return 200 '@location';
        }
~~~

> GET http://test.tyloafer.cn/location
>
> 结果： = /location 

>GET http://test.tyloafer.cn/locationa
>
>结果： ^~ /locationa 

> GET http://test.tyloafer.cn/locationa11
>
> 结果： ^~ /locationa

> GET http://test.tyloafer.cn/locationzadasdas
>
> 结果： ~ /location[a-z]+

> GET http://test.tyloafer.cn/Locationzadasdas
>
> 结果： *~ /location

> GET http://test.tyloafer.cn/test-location
>
> 结果： @location 

