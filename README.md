# shell
shell简介
#基础
首先shell其实就是一堆与系统交互的命令集合，所谓shell脚本就是把多个shell命令放入文件中作为程序执行。

##shell版本之分
 
    shell是有不同的版本的，我们最常用的是许多Linux发行版中默认的shell——Bash，它的全称是Bourne again shell。除了bash以外，还有ash、korn、tcsh、dash、zsh等，一般这些shell都在｀/bin/｀目录当中，要修改当前系统的shell也是可以的，使用chsh命令。之所以提到这件事情是因为有些命令的用法在不同的shell里是不兼容的，所以有可能在一台机器上可以正常执行的脚本在另外一台机器上就不能执行了，我讲的都是在Bash环境下运行的一些命令。
 shebang
 
shell脚本通常会以类似的作为文件启示行：
#!/bin/bash

这一行有个奇葩的名字叫shebang，这个名字的由来是这样的，在Unix行话里用sharp或hash称呼#，然后用bang称呼!，俩加起来就成了shebang。然后跟在#!俩字符后边的是Bash的解释器的路径。
shebang的作用是什么呢？脚本文件可以作为命令行参数运行：
bash script.sh

但也可以利用shebang实现脚本独立运行，但是这个脚本文件必须有可执行的权限，可以像下面这样给文件加权限：
chmod a+x script.sh

这样子就能像下面这样运行脚本：
./script.sh

系统内核会首先读取脚本首行并注意到shebang为#!/bin/bash，它识别出/bin/bash并在内部像这样执行脚本：

/bin/bash script.sh

shebang不仅仅只能使用/bin/bash，也可以使用其他shell/bin/zsh，甚至可以是/usr/bin/java /usr/bin/node等可执行文件，这里的路径只有一个限制就是必须存在这个可执行文件。因为我们可以使用各式各样的命令去解释执行我们的脚本，所以.sh其实并不是必须的，而且在linux中这些文件的后缀名系统是不认的，我们可以把script.sh重命名为script完全不会影响它的执行。
#环境变量

为什么我们运行java、node的命令从来没有写全路径？因为系统会遍历PATH环境变量指定的路径去寻找这个可执行文件，找到第一个就结束。Linux系统有一堆环境变量，但一般我们最关心的也就是PATH这一个环境变量，如果实在好奇可以使用printenv查看一下当前系统的全局环境变量，用set查看一下当前系统的局部环境变量。
PATH这个环境变量设置错误会产生很严重的错误，比如说我希望在PATH路径底下加入一些我自己常用的一些小脚本：
```c
export PATH=$HOME/.bin/
```
一下子在运行之前很熟悉的命令比如ls cd，都会类似的错误:
-bash: ls: command not found

PATH有非常多的地方可以设置，所以经常的会被混淆，这是因为不同情况下shell读取的配置文件是不同的。shell启动方式有三种:
登录默认启动的shell，这时候的shell读取配置文件的顺序是/etc/profile、$HOME/.bash_profile、$HOME/.bash_login、$HOME/.profile

交互式的shell，这种启动方式是指你在命令提示符下敲入bash的时候启动的。这种情况读取的配置文件会是$HOME/.bashrc

非交互式shell，执行脚本的时候就是这种shell，这种情况下会有BASH_ENV环境变量指定启动文件
我们开发打开的终端属于第二种，所以我们的环境变量一般都会在$HOME/.bashrc配置。
#常用命令
 man
 
Unix系统下的命令很多，参数也非常多，所以要想全部讲了是不可能的，所以学习Unix命令的人第一个要学的都是man这个命令，这个命令是用来查看命令文档的。如果并不需要查看所有详细的文档，可以直接在命令行输入这个命令回车，一些非交互式的命令会打印出它可以使用的参数，不过强烈使用man。
```javascript
echo printf
echo "Welcome to Bash"
echo Welcome to Bash
echo 'text in quotes'


echo "Hello world !"
echo Hello world !
echo 'Hello world !'
echo "Hello world \!"

printf "Hello world"
printf  "%-5s %-10s %-4s\n" No Name  Mark
printf  "%-5s %-10s %-4.2f\n" 3 Jeff 77.564

echo -n "Hello world"
echo -e "\e[1;31m This is red text \e[0m" # 重置=0，黑色=40，红色=41，绿色=42，黄色=43，蓝色=44，洋红=45，青色=46，白色=47

echo -n
echo '-n'
echo "-n"
echo \-n
echo -e '\055n'
```
回显命令
#ls
* 列出文件夹中的文件，可以使用通配符
* head tail cat more
* 这4个命令可以一起讲，用来查看文件的。
* cat file1 file2 file3 # 将作为命令行参数的文件内容拼接在一起
* echo 'Text through stdin' | cat - file.txt # -被作为stdin文本的文件名
* cat -s file # 删除多余的空白行
* cat -T file.py # -T选项能够将制表符标记成^|
* cat -n lines.txt # -n选项会在输出的每一行内容之前加上行号

#find
这个命令用来查找文件的
```javascript
find . -print

find ~/node/b2bweb -name "*.ts" -print
find . -iname "purchase*" -print
find ~/node/b2bweb -path "*/components/*" -print
find . -iregex ".*\(\.ts\|\.js\)$" # linux
find -E ./ -iregex '.*(ts|js)$'
find . ! -name "*.js" -print

find . -maxdepth 1 -name "*.json" -print

find . -type d -print
find . -type f -print
find . -type l -print

# 访问时间（-atime）：用户最近一次访问文件的时间; 
# 修改时间（-mtime）：文件内容最后一次被修改的时间
# 变化时间（-ctime）：文件元数据（例如权限或所有权）最后一次改变的时间。
# -atime、-mtime以及-ctime都是基于时间的参数
# -mmin、-mmin、-cmin基于分钟
find . -type f -atime -7 -print # 最近7天内被访问过的所有文件
find . -type f -atime 7 -print # 恰好在7天前被访问过的所有文件
find . -type f -atime +7 -print # 打印出访问时间超过7天的所有文件
find . -type f -newer file.txt -print # 找出比file.txt修改时间更近的所有文件

find . -type f -name "*.swp" -delete # 删除当前目录下所有的 .swp文件
find . -type f -perm 644 -print # # 打印出权限为644的文件

# {}表示一个匹配。对于任何匹配的文件名，{}均会被该文件名所替换
find . -type f -name "*.txt" -exec cat {} \;>all_files.txt # 文件拼接起来写入单个文件all_files.txt
find . -type f -mtime +10 -name "*.txt" -exec cp {} OLD  \; # 将10天前的 .txt文件复制到OLD目录中

 find . -name "node_modules" -prune -o -type f -name "*.js" -print # 跳过特定的目录
 ```
#tr
转换命令
```javascript
echo "HELLO WHO IS THIS" | tr 'A-Z' 'a-z'
echo 12345 | tr '0-9' '9876543210'
echo 87654 | tr '9876543210' '0-9'
echo "Hello world" | tr 'a-zA-Z' 'n-za-mN-ZA-M' # 著名的ROT13加密算法

tr '\t' ' ' < file.txt # 将制表符转换成空格
echo "Hello 123 world 456" | tr -d '0-9'
echo hello 1 char 2 next 4 | tr -d -c '0-9 \n' # 从输入文本中将不在补集中的所有字符全部删除
echo "GNU is       not     UNIX. Recursive   right ?" | tr -s ' ' # 连续的重复字符应该压缩成单个字符
```
#sort
排序命令
```javascript
sort file1.txt file2.txt > sorted.txt
sort file1.txt file2.txt -o sorted.txt

sort -n file.txt # 按照数字顺序进行排序
sort -r file.txt # 按照逆序进行排序
sort -m sorted1 sorted2 # 合并两个已排序过的文件
sort file1.txt file2.txt | uniq # 找出已排序文件中不重复的行
```
# grep
过滤命令
```javascript
echo -e "this is a word\nnext line" | grep word
grep "match_text" file1 file2 file3 ...
grep word filename --color=auto

grep -nr "text" ./
echo hello world | grep -i "HELLO" # 忽略样式中的大小写
echo this is a line of text | grep -e "this" -e "line" -o # 以用选项-e来指定多个匹配样式
grep -nr "Purchase" --include=*.tsx --include=*.js ./ # 递归搜索所有的 .tsx和 .js文件
grep "something" . -r --exclude "README" # 排除所有的README文件

seq 10 | grep 5 -A 3 # 打印匹配某个结果之后的3行
seq 10 | grep 5 -B 3 # 打印匹配某个结果之前的3行
seq 10 | grep 5 -C 3 # 打印匹配某个结果之前以及之后的3行
echo -e "a\nb\nc\na\nb\nc" | grep a -A 1 # 如果有多个匹配，会使用--作为各部分之间的定界符
```
#tar
压缩命令
```javascript
tar zcvf output.tar.gz file1 file2 file3 folder1
tar zxvf output.tar.gz
```
#其他
当一个命令发生错误并退回时，它会返回一个非0的退出状态；而当命令成功完成后，它会返回数字0。退出状态可以从特殊变量$?中获得（在命令执行之后立刻运行echo $?，就可以打印出退出状态）。
```javascript
ls +
ls + > out.txt
ls + 2> out.txt #正常运行

cmd 2>stderr.txt 1>stdout.txt # 将stderr单独重定向到一个文件，将stdout重定向到另一个文件
cmd 2>&1 output.txt # 将stderr转换成stdout，使得stderr和stdout都被重定向到同一个文件中
cmd &> output.txt
cmd 2>/dev/null
```
别名就是一种便捷方式，以省去用户输入一长串命令序列的麻烦。下面我们会看到如何使用alias命令创建别名.
```javascript
alias install='sudo apt-get install'
alias rm='cp $@ ~/backup && rm $@'
```
用wget可以下载网页或远程文件
```javascript
wget https://www.baidu.com/
```
cURL支持包括HTTP、HTTPS、FTP在内的众多协议。它还支持POST、cookie、认证、从指定偏移处下载部分文件、参照页（referer）、用户代理字符串、扩展头部、限速、文件大小限制、进度条等特性。
```javascript
curl http://www.baidu.com -o index.html --progress
```
可以通过lsof列出系统中的开放端口以及运行在端口上的服务的详细信息
```javascript
lsof -i
lsof -i :3000
```
可以通过ps查看进程

ps aux 
