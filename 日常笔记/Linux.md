

## 一、 常用命令

### 1. &

​	在命令后面加上 ==&== 会使该命令在后台运行，注意需要用户交互的命令不要放在后台运行，常配合输出重定向符 ==>== 将该命令的输出重定向到某个文件中，如

```shell
java -jar test.jar > log.file &

java -jar test.jar > log.file 2>&1 &
```

> 在shell脚本中，默认情况下，总是有三个文件处于打开状态，标准输入(键盘输入)、标准输出（输出到屏幕）、标准错误（也是输出到屏幕），它们分别对应的**文件描述符**是0，1，2 。
>
> 2>&1 表示将错误输出也重定向合并到标准输出中 
>
> 附：输入重定向符 < , 输出重定向符 > , 追加输出重定向符 >>

### 2. nohup

​	==nohup== 是 no hang up 的缩写，意为不挂起，经常与 ==&== 一起使用，当仅使用 ==&== 命令使程序在后台运行时，一旦退出登陆shell所在的用户，该程序或命令也会停止运行，这时候就可以使用==nohup== 命令使程序挂起，即退出登陆后程序依旧运行，如

```shell 
nohup java -jar test.jar > log.file 2>&1 &
```

> 如果没有设置输出参数，则默认输出到当前目录的 nohup.out 文件中



### 3. |

​	==**|**== 称为管道命令，它会将左侧命令的标准输出作为右侧命令的标准输入，如

```shell
cat .zshrc | grep alias

# 等同于
grep alias .zshrc
```



### 4. xargs

​	然而，很多命令都不接受标准输入作为参数，这时就需要用到 ==xargs== 命令了，==xargs== 命令作用就是通过管道接收字符串，并将接收到的字符串通过空格分割成许多参数(默认情况下是通过空格符和换行符分割) 然后将参数传递给其后面的命令，作为后面命令的命令行参数，如

```shell
# echo 命令不接受标准输入，所以不会有结果
echo "hello world!" | echo

# xargs 将标准输入转化为命令行参数,这时就会有输出了
echo "hello worod!" | xargs echo
```

> 注：xargs 后面有一个默认命令 echo ，即 xargs 单独使用 等同于 xargs echo



参数：

`-d` 修改分隔符，如

```shell
echo "1 | 2 | 3" | xargs -d "|"
# 输出：1 2 3
```



`-p` 与 `-t` 输出实际执行的命令

​		==-p== 会打印出实际执行都命令并需要用户手动输入 y 来确认执行，==-t== 会打印出实际执行命令并立即执行，不需要确认，如

```shell
echo "1 | 2 | 3" | xargs -d "|" -p
echo 1   2   3
 ?...y
1   2   3

echo "1 | 2 | 3" | xargs -d "|" -t
echo 1   2   3
 
1   2   3
```



`-L` 指定行数分隔

​		如果标准输入包含多行，==-L== 参数指定多少行作为一个命令行参数，每分隔一次就执行一次命令，如

```shell
liam@liam-Ali ~> echo "hello
                 world
                 and
                 you" | xargs
hello world and you
liam@liam-Ali ~> echo "hello
                 world
                 and
                 you" | xargs -L 1
hello
world
and
you
liam@liam-Ali ~> echo "hello
                 world
                 and
                 you" | xargs -L2
hello world
and you
```



`-n` 指定项数分隔

​		如果标准输入包含多行，==-n== 参数指定多少项作为一个命令行参数，每分隔一次就执行一次命令，如

```shell
liam@liam-Ali:~$ echo {0..9}
0 1 2 3 4 5 6 7 8 9
liam@liam-Ali:~$ echo {0..9} | xargs -n1
0
1
2
3
4
5
6
7
8
9
```





### 5. find

​		==find==命令用来查找文件，格式为 ==find  <目录>. <条件>. <动作>==，其中目录默认为当前目录(包括子目录)

条件有：

​	`-name` 最常使用的条件，根据文件名来查找文件，如

```shell
# 查找家目录下以rc结尾的隐藏文件
find ~ -name ".*rc" -maxdepth 1 

# ! 取反
find ~ ! -name ".*rc" -maxdepth 1

# i 忽略大小写
find ~ -iname ".*RC" -depth 1
```

> 注：-maxdepth 1 意思是最大查找深度为 1 ，即仅查找一层目录，不递归查找子文件夹；同理，-depth 指定查找深度，如 -depth 2 指定查找第二层目录（会忽略掉第一层目录）；还有 -mindepth 。。。



​		`-perm`  根据权限来查找文件，如

```shell
find ~ -perm 644 -maxdepth 1
```



​		`-type` 根据类型来查找，如

```shell
# b - 块设备文件
# d - 目录
# c - 字符设备文件
# p - 管道文件
# l - 符号链接文件
# f - 普通文件

find ~ -type d -depth 1
```



​		`-prume` 指定忽略查找的目录，如

```shell
# 这是个蛇皮参数，还没搞明白，待修改
find . -path ./download -prune -o -name test.file
```



​		其它不常用参数，如

```shell
# -delete     删除查找到的文件
# -user       按照文件属主来查找文件
# -nouser     查找无主文件，即原属主已不存在
# -group      按照文件所属的组来查找文件
# -nogroup    查找无组文件
# -size       按照文件大小来查找，如 -size 10k, -size +10k
# -follow     如果find命令遇到符号链接文件，就跟踪至链接所指向的文件
# -empty      列出所有长度为零的文件
# -atime      访问时间，-7 表示7天内
# -mtime      修改时间
# -ctime      变化时间，如权限修改不会导致修改时间变化，但会导致变化时间变动
```



### 6. cut

​		==cut==用于从文本中取出某些列，这里的列有三种模式，如

```shell
-b     # 以字节作为标准取出列
-c     # 以字符作为标准取出列
-f     # 以域(field)作为标准取出列
```



​		`-b `以字节为单位，一个英文字符或数字占一个字节，空格一般也算一个字节，如

```shell
$ jobs -l              
[1]    54412 running    vue ui
[2]  - 54448 running    vue ui
[3]  + 54489 running    vue ui > vue.log

# 取出8-12列的进程id
# 用途：jobs -l | cut-b 8-12 | xargs kill -9
$ jobs -l | cut -b 8-12
 54412
 54448
 54489
```

>注：在不确定空格的情况下可以首先测试一下，如 jobs -l | cut -b 8



​		`-c` 以字符为单位，有中文时使用，一般中文为1个字符，3个字节，如

```shell
$ cat test.file  
春眠不觉晓，
处处闻啼鸟，
夜来风雨声，
花落知多少。

# 以字节为单位，终端为UTF-8编码
$ cut -b 1-6 test.file
春眠
处处
夜来
花落

# 以字符为单位
$ cut -c 1-6 test.file
春眠不觉晓，
处处闻啼鸟，
夜来风雨声，
花落知多少。
```



​		`-f` 域模式可以指定输出哪几个域，常配合`-d(指定分隔符)` 使用，注意分隔符也会被输出，如

```shell
~$ jobs -l
[1]- 11370 Stopped (tty output)    vim .profile
[2]+ 11371 Stopped (tty output)    vim .profile

# 以空格为分隔符输出第2个域
~$ jobs -l | cut -d ' ' -f 2
11370
11371
```



​		`--complement` 取补集，如

```shell
$ jobs -l
[1]- 11370 Stopped (tty output)    vim .profile
[2]+ 11371 Stopped (tty output)    vim .profile

$ jobs -l | cut -d ' ' -f 2
11370
11371

$ jobs -l | cut -d ' ' -f 2 --complement
[1]- Stopped (tty output)    vim .profile
[2]+ Stopped (tty output)    vim .profile
```

> 注：-d. --complement  参数在mac上不适用



> 附：
>
> 1，ASCII码：一个英文字母（不分大小写）占一个字节的空间，一个中文汉字占两个字度节的空间。
>
> 2，UTF-8编码：一个英文字符等于一个字节，一个中文（含繁体）等于三个字节。中文标点占三个字节，英文标点占一个字节
>
> 3，Unicode编码：一个英文等于两个字节，一个中文（含繁体）等于两个字节。中文标点占两个字节，英文标点占两个字节





## 二、随缘命令

1. `echo $0` 查看当前shell
2. `jobs -l`查看当前终端的后台进程，常配合 ==&== 使用



## 三、常用网络工具

### 1. tcpdump

### 2. nc

### 3. nmap