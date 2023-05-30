## 命令1：| 管道

将前一个命令的输出作为后一个命令的输入

```
cat xxxx.log | grep error
```

如果错误信息没在这一行

```
less xxx.log
/traceId 
```



## 命令2：tail 

动态查看文件内容

```
tail -f xxx.log (动态查看日志文件后面n多行命令)
tail -500 xxx.log (查看日志文件后500行)
tail -100f xxx.log (动态查看日志文件后100行)
```



## 命令3：cat

查看静态文件内容

```
cat xxx.sh
```



## 命令4: wc

用于计算字数

-   -c, --bytes：统计字节数。
-   -m, --chars：统计字符数。
-   -w, --words：统计字数。
-   -l, --lines：统计行数。
-   -L, --max-line-length：统计最长行的长度

```
wc -l xxx.sh (统计文件一共有多少行)
```



## 命令5：awk

一种行处理程序，执行awk时，它依次对输入文件中的每一行执行花括号中的代码

```
awk '{print}' xxx.txt (打印txt文件的每一行)
awk '{print $3}' xxx.txt (打印txt文件每一行的第三字段)
```



## 命令6：sort