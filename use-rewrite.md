# rewrite

## 简介

| 语法:   | `rewrite regex replacement [flag];` |
| ------- | ----------------------------------- |
| 默认值: | —                                   |
| 上下文: | `server`, `location`, `if`          |

 ## flag

- `last`       # 停止执行当前这一轮的`ngx_http_rewrite_module`指令集，然后查找匹配改变后URI的新location；
- `break`      # 停止执行当前这一轮的`ngx_http_rewrite_module`指令集；
- `redirect`         # 在replacement字符串未以“`http://`”或“`https://`”开头时，使用返回状态码为302的临时重定向；
- `permanent`        # 返回状态码为301的永久重定向。

