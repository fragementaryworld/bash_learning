## <font color=gray>CATALOG</font>

[TOC]

---
### shell通配符
**\*** 匹配任意多个字符 `rm -rf *`
**?** 匹配一个字符 `ll l?ve`
**[]** 匹配括号中任意一个字符`[0-9],[a-z],[^A-Z]`
**()** 在子shell中执行`(umask 077;touch test.txt)`
**{}** 集合 `touch file{1..9}.txt`
**\\** 转义符
> * `echo -e "a\nb"`, 让echo带有转义功能
> * `echo -e "\e[1;31mThis is a red text.\e[0m"`echo带有颜色输出，m前表示颜色，1表示高亮，0表示透明，3X表示前景色，4X表示背景色，m后为输出文本

---
### shell 变量
* **`${#var}`** 取变量长度
* **export** 将局域变量(脚本运行shell)转变为全局变量(所有shell)`export PATH=$PATH:/usr/bin`
* **unset** 取消变量
* **env** 查看全局变量(环境变量)
> * `./test.sh` or `bash test.sh` 在子shell中执行
> * `. test.sh` or `source test.sh` 在当前shell中执行

* **位置变量**：`$1 $2 $3 ... ${10}`
* **预定义变量**
> `$0` 脚本名
> `$*` 所有参数
> `$@` 所有参数，与`$*`的区别在于`$@`的参数是分割开来的，`￥*`的所有参数是一体的，这在`for`语句中将会体现
> `$#` 参数的个数
> `$$` 当前进程的PID
> `$!` 上一个后台进程的PID
> `$?` 上一个命令的返回值

* **变量赋值** `read -p "promot" var`
* **变量运算** 
    * `expr $num1 + $num2`
    * `sum=$(($num1+$num2))`
    * `sum=$[$num1+$num2]`
    * `let sum=$num1+$num2`

> `i++`:先赋值，再自加1
> `++i`:先自加1，再赋值

---
### 变量的删除与替换
* 变量的删除
>  `url=www.baidu.com`
>  `echo ${url#*.}` =======> `baidu.com`   
>  `echo ${url##*.}` =======>  `com`
>  `echo ${url%.*}`  =======>  `www.baidu`   
>  `echo ${url%%.*}` =======> `www`
>  `echo ${url:2:3}` =======> `w.b`
* 变量替换
`${var-rep}`:若变量不存在，则替换之，否则为原变量
`${var:-rep}`:若变量不存在或为空，则替换之；否则为原变量

---
### if
```bash
if [ $? -eq 0 ];then
    ...
elif [ $? -eq 1 ];then
    ...
else
    ...
fi
```
**if**语句中的 **[ ]** 为条件测试 **test**
> `[ -e dir|file ]` 文件或目录是否存在
> `[ -f file ]` 是否为文件
> `[ -d dir ]`
> `[ -r file ]` 当前用户是否对该文件有可读权限
> `[ -w file ]`
> `[ -x file ]`
> `[ -L file ]`
---
### case
```bash
case $i in
mode1)
    ...
    ;;
mode2)
    ...
    ;;
mode3)
    ...
    ;;
*)
    ...
esac
```

---
### for
```bash
for i in {1..10}
do
    ...
    echo $i
done
```
**for**语句以默认的**IFS(inner field seporater)** 来区分变量个数，即**tab,space,换行符**，例如`for line in $(cat xxx.txt)`可能不会达到以选取行为变量的目的，此时，需要重新定义**IFS**为换行符`IFS=$'\n'`

---
### while
```bash
while read line
do
    ...
    echo $line
done < xxx.txt
```

---
### expect
> `spawn` : 启动进程 `spawn ssh username@ip`
> `send` : 向进程发送字符串
> `expect` : 从进程接受字符串，并进行匹配 
> `exp_continue` : 接续进行expect匹配
> **expect**脚本必须以`interact`或`expect eof`结尾，`interact`表示停留在`spawn`的进程中进行交互，而`expect eof`表示结束expect脚本
```bash
#!/user/bin/expect
#set ip [lindex $argv 0]
set ip 192.168.1.1
set user ubuntu
set timeout 5
spawn ssh $user@$ip
expect{
    "yes/no" {send "yes/r"; exp_continue}
    "passwd" {send "xxxxxx\r"};
}
interact
```

---
### 文件描述符fd
进程中的每一个文件描叙符与进程中打开的每一个文件一一对应，可以用`ll /proc/$$/fd`来查看当前进程拥有的文件描述符，不同进程的文件描述符与打开文件的对应关系各不相同，但所有进程都用相同的文件描述符0,1,2
|fd    |   stdio  |
|:----:|:---------|
|0     |  stdin   |
|1     |  stdout  |
|2     |  stderr  |

![img](https://img-blog.csdn.net/20140831224917875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY3l3b3Nw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
> * 在进程A中，文件描述符1和30都指向了同一个打开的文件句柄（标号23）。这可能是通过调用dup()、dup2()、fcntl()或者对同一个文件多次调用了open()函数而形成的。
> * 进程A的文件描述符2和进程B的文件描述符2都指向了同一个打开的文件句柄（标号73）。这种情形可能是在调用fork()后出现的（即，进程A、B是父子进程关系），或者当某进程通过UNIX域套接字将一个打开的文件描述符传递给另一个进程时，也会发生。再者是不同的进程独自去调用open函数打开了同一个文件，此时进程内部的描述符正好分配到与其他进程打开该文件的描述符一样。
> * 此外，进程A的描述符0和进程B的描述符3分别指向不同的打开文件句柄，但这些句柄均指向i-node表的相同条目（1976），换言之，指向同一个文件。发生这种情况是因为每个进程各自对同一个文件发起了open()调用。同一个进程两次打开同一个文件，也会发生类似情况。

可用`exec 6<> test.txt`命令将test.txt与当前进程的文件描述符6绑定，相当于一个链接，若在不释放文件描述符的条件下删除test.txt,文件描述符不会消失，甚至删除的文件还可通过`cp /proc/$$/6 test.txt`来恢复文件，若不需该文件描述符，可用`exec 6<&-`来释放该文件描述符

---
### 管道pipe
* 匿名管道 **|**： `ps aux | grep $pid`
* 命名管道 **mkfifo** : `mkfifo /tmp/tmpfifo`
> 命名管道可跨终端传输数据，本质上为一种特殊文件，具有先进先出的特性，传进管道的数据读取后就消失
> /dev/pts/0 : grep 'sda' /tmp/tmpfifo
> /dev/pts/1 : ll /dev > /tmp/tmpfifo
> 通过上述方法不同时跨终端传输数据  

**并非控制**
```bash
#!/bin/bash
thread=5
tmp_fifo=/tmp/$$.fifo

mkfifo $tmp_fifo
exec 8<>  $tmp_fifo
rm $tmp_fifo

for i in `seq $thread`
do
    echo >&8
done

for i in {1..255}
do
    read -u 8
    {
    ip=192.168.111.$i
    ping -c1 -W1 $ip &> /dev/null
    if [ $? -eq 0 ];then
        echo "$ip is up"
    else
        echo "$ip is dowwn"
    fi
    echo >&8
    }&
done
wait 
echo "finish..."
```

---
### 数组
* **普通数组**：
