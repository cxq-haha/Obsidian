# 结构化命令
## 结构化命令
## if语句
```shell
#!/bin/bash
# testing multiple commands in the then section #
testuser=Christine #
if grep $testuser /etc/passwd then
	echo "This is my first command" 
	echo "This is my second command"
	echo "I can even put in other commands besides echo:" ls -a /home/$testuser/.b*
fi
```

if-then-else语句
```shell
if command then
	commands 
else 
	commands
fi
```

if条件的是否根据命令的退出状态码来判断，0（正确执行）为true，非0为false。
如果if语句要使用其他条件而不是shell命令，test命令提供了在if-then语句中测试不同条件的途径。如果test命令中列出的条件成立，test命令就会退出并返回退出状态码0
```shell
if test condition then
commands fi

# bash shell提供了另外一种方法：可以使用中括号代替test指令，注意中括号与条件之间有空格
if [ condition ] then
commands fi
```


条件语句中比较数值：
![[Pasted image 20220720011701.png]]

条件语句中比较字符串：
![[Pasted image 20220720011718.png]]

判断字符串长度是否为0：
```shell
# 判断val1变量是否长度非0
if [ -n $val1 ]

# 判断val2变量是否长度为0
if [ -z $var2 ]
```

条件语句中比较文件：
![[Pasted image 20220720012603.png]]

if-then语句的高级特性：
- 用于数学表达式的双括号 
![[Pasted image 20220720013139.png]]
```shell
#!/bin/bash
# using double parenthesis #
val1=10 #
if (( $val1 ** 2 > 90 )) then 
	(( val2 = $val1 ** 2 )) 
	echo "The square of $val1 is $val2"
fi
```

- 用于高级字符串处理功能的双方括号

双方括号里的expression使用了test命令中采用的标准字符串比较。但它提供了test命令未提供的另一个特性——模式匹配（pattern matching）。可以定义一些正则表达式类匹配字符串
```shell
# [[ expression ]]

#!/bin/bash
# using pattern matching #
if [[ $USER == r* ]] then
echo "Hello $USER" else echo "Sorry, I do not know you"
fi
```

## case语句
```shell
#!/bin/bash
case variable in
pattern1 | pattern2) 
	commands1;; 
pattern3) 
	commands2;;
*) 
	default commands;; 
esac
```

## for命令
```shell
for var in list 
do
	commands 
done


# 读取列表值

#!/bin/bash 
# basic for command 
for test in Alabama Alaska Arizona Arkansas California Colorado 
do 
	echo The next state is $test 
done 
```

### 更改字段分隔符
造成这个问题的原因是特殊的环境变量IFS，叫作内部字段分隔符（internal field separator）。IFS环境变量定义了bash shell用作字段分隔符的一系列字符。默认是使用空格作为分隔符，如果要修改成换行符作为分隔符，可以这样做：
```shell
# 注意这里需要使用美元符号，如果没有使用会把"\"和"n"作为分隔符
IFS=$'\n'
```

### C语言风格的for命令
```shell
for (( variable assignment ; condition ; iteration process ))

# 例子
for (( i=1; i <= 10; i++ )) 
do
	echo "The next number is $i" 
done
```

## while命令
```shell
while test command 
do
	other commands 
done

# 例子
#!/bin/bash
var1=10
while [ $var1 -gt 0 ] 
do
	echo $var1 
	var1=$[ $var1 - 1 ] 
done
```

while可以使用多个test命令，只有最后一个test命令的退出状态码会被用来决定什么时候结束循环

## until命令
```shell
until test commands 
do
	other commands
done
```

until命令和while命令工作的方式完全相反。until命令要求你指定一个通常返回非零退出状态码的测试命令

## 处理循环的输出
在shell脚本中，可以对循环的输出使用管道或进行重定向
```shell
for file in /home/rich/* 
do
	if [ -d "$file" ] then
		echo "$file is a directory" 
	elif 
		echo "$file is a file" 
	fi
done > output.txt
```

# 用户输入
## 通过脚本参数的形式输入
用户脚本的参数在脚本中可以使用`$1`到`$9`表示，就像使用普通参数一样
如果不止9个参数，可以加上大括号`${10}`，`${11}`，...
`$0`表示当前脚本名称
`$#`代表脚本运行时携带的命令行参数的个数，要使用脚本的最后一个参数可以使用`${!#}`，由于在`${}`中不能使用美元符号，需要用`!`代替
`$*`和`S@`代表所有的参数。`$*`和`$@`的区别在于S*将所有的参数当做一个单词保存，是一个整体，`$@`将提供多的参数当做一个字符串中的多个单词保存
`$$`代表Linux系统分配给该脚本的PID


shift命令还可以带一个参数，设置每次移动的长度

## shell程序的标准参数功能实现
### 实现参数和选项分离
这个例子实现了选项和参数的分开输出

![[Pasted image 20220724005027.png]]

**shift命令**：
shift命令可以将脚本参数整体向左移动一位，`$0`仍然是脚本名称，向左移动到小于1的参数会永久被移除，不能恢复。
可以利用shift命令遍历所有的参数：
```shell
# 例子
#!/bin/bash
count=1
while [ -n "$1" ] do
	echo "Parameter #$count = $1" 
	count=$[ $count + 1 ] 
	shift
done

# 运行结果 
# $ ./test13.sh rich barbara katie jessica
# Parameter #1 = rich 
# Parameter #2 = barbara 
# Parameter #3 = katie 
# Parameter #4 = jessica 
# $
```

**分隔符 --**
Linux通过`--`分割选项什么时候结束和脚本参数什么时候开始

### 使用getopt命令实现参数的合并输入
实现-ab 相当于 -a -b的效果

![[Pasted image 20220724005618.png]]

**getopt命令**
getopt命令是一个在处理命令行选项和参数时非常方便的工具。它能够识别命令行参数，从而在脚本中解析它们时更方便

命令格式：
```shell
getopt optstring parameters
```

![[Pasted image 20220724005916.png]]

b后面有个冒号表示b选项需要一个参数值
如果指定了一个不在optstring中的选项，默认情况下，getopt命令会产生一条错误消息，如果想忽略这条错误消息，可以在命令后加-q选项

![[Pasted image 20220724010122.png]]

set命令有一个选项，双破折线（--），它会将命令行参数替换成set命令的命令行值

`set -- $(getopt -q ab:cd "$@")`代码使原始的命令行参数变量的值被getopt命令的输出替换，而getopt已经为我们格式化
好了命令行参数

### 使用getopts命令
上面的代码中还存在一个缺陷，不能处理选择项值中存在空格的情况
`./test.sh -a -b test1 -cd "test2 test3" test4`
-cd选项中的值用引号的方式表示不支持

getopts命令（注意是复数）内建于bash shell。它跟近亲getopt看起来很像，但多了一些扩展功能。它一次只处理命令行上检测到的一个参数。处理完所有的参数后，它会退出并返回一个大于0的退出状态码

```shell
getopts optstring variable
```

有效的选项字母都会列在optstring中，如果选项字母要求有个参数值，就加一个冒号。要去掉错误消息的话，可以在整个optstring之前加一个 冒号（类似于getopt中的-q选项）
getopts将正在处理的参数位置保存在OPTIND环境变量。这样就能在处理完选项之后继续处理其他命令行参数了

![[Pasted image 20220724011040.png]]

### Linux命令常用选项代表的含义
![[Pasted image 20220724011114.png]]

## 使用read获取用户输入
**read命令**从标准输入（键盘）或另一个文件描述符中接受输入。在收到输入后，read命令会将数据放进一个变量
```shell
read var_name

# 可以使用-p选项输入提示符
read -p "请输入密码" password

# 可以不指定变量var_name ，用户输入会存储在环境变量REPLY中

# 可以指定多个变量存放输入的值。输入的每个 数据值都会分配给变量列表中的下一个变量。如果变量数量不够，剩下的数据就全部分配给最后 一个变量
read -p "请输入姓名" firshname lastname

```

`-t` 选项指定了read命令等待 输入的秒数。当计时器过期后，`read`命令会返回一个非零退出状态码。
```shell
read -t 5 var_name
```

-n选项和数字1一起使用，告诉read命令在接受单个字符后退出
```shell
read -n1 "Do you want to continue [Y/N]?"
```

`-s` 选项使输入不显示到显示器上
```shell
read -s -p "Enter your password: " pass
```

read命令还可以读取文件中保存的数据
```shell
cat test | while read line do
	# ...
done
```

# 输出
## 重定向
```shell
# 标准输出重定向到test2.txt，如果命令发生错误还是输出到屏幕上
cat test1.txt > test2.txt
```

将错误信息和数据都重定向到文件
```shell
cat test1.txt 1>text2.txt 2>error.txt

# 使用&>符，命令生成的所有输出都会发送到同一位置，包括数据和错误
cat test1.txt &>ret.txt
```

### 在脚本中重定向输出
将数据重定向到标准错误输出，需要使用`&`符号，并且`>&2`之间不能存在空格
```shell
cat test >&2
```

如果脚本中有大量数据需要重定向，重定向每个echo语句就会很烦琐。取而代之，可以用exec命令告诉shell在脚本执行期间重定向某个特定文件描述符。

<u>exec命令会启动一个新shell并将STDOUT文件描述符重定向到文件。脚本中发给STDOUT的所有输出会被重定向到文件</u>。

```shell
#!/bin/bash
exec 1>testout.txt
echo "this is a test of redirecting all output"
echo "from a script to another file."
echo "without having to redirect every individual line"
```

### 在脚本中重定向输入
将testfile设置为脚本的标准输入，read读取标准输入的信息编程读取testfile的信息
```shell
#!/bin/bash
exec 0< testfile count=1
while read line do
	echo "Line #$count: $line" 
	count=$[ $count + 1 ]
done
```

### 暂存标准输入输出
暂存标准输出
```shell
#!/bin/bash
exec 3>&1                                      # 文件描述符3指向标准输出
exec 1>testout                                 # 文件描述符1指向testout

echo "this should store in the output file"    # 输出到文件testout
echo "along with this line"

exec 1>&3                                      # 文件描述符1指向标准输出
echo "Now things should be be back narmal"     # 输出到控制台
```

暂存表述输入
```shell
#!/bin/bash
exec 6<&0                                      # 文件描述符6指向标准输入
exec 0< testfile                               # 文件描述符0指向testfile

count =1 
while read line                                # 读取testfile中的内容
do
	echo "Line $count: $line"
	count=$[ $count + 1 ]
done

exec 0<&6                                      # 文件描述符0指向标准输入
read -p "Are you done now?" answer             # 从控制台获取输入
case $answer in 
Y|y) echo "Goodbye";;                          
N|n) echo "Sorry ,this is the end.";;
esac
```

### 关闭文件描述符
将文件描述符重定向到`-`
```
exec 3>&-
```

关闭文件描述符后，如果再将文件描述符3重定向到一个文件，这个文件会被重新创建

## 创建临时文件和目录mktemp
在本地目录中创建一个临时文件
```shell
mktemp testing.xxxxxx
# xxxxxx会自动生成6个字符吗，保证文件的唯一性
```

在/tmp目录中创建临时文件
```shell
mktemp -t testing.xxxxxx
# 返回创建的文件的全路径
```

创建临时目录
```shell
mktemp -d directory
```

## 记录消息tee命令
tee命令相当于管道的一个T型接头。它将从STDIN过来的数据同时发往两处

```shell
tee testfile

date | tee testfile # 输出到标准输出和testfile（重定向到testfile时会清楚原来的内容）

date -a | tee testfile # 以附加的形式重定向到testfile
```

## 例子：将csv文件转换为SQL文件
```shell
#!/bin/bash
# read file and create INSERT statement for MYSQL

outfile="mem.sql"
IFS=","
while read lname fname address city state zip
do
  cat >> $outfile << EOF
  INSERT INTO members (lname, fname, address, city, state, zip) VALUES
  ('$lname', '$filename', '$address', '$city', '$state', '$zip');
EOF
done < ${1}

```

# 控制脚本
## 信号处理
### 常见的Linux信号
![[Pasted image 20220730165610.png]]
Ctrl+C生成SIGINT信号
Ctrl+Z生成SIGSTP信号
kill命令发送一个SIGKILL信号

### 捕获信号trap
trap命令允许你来指定shell脚本要监看并从shell中拦截的Linux信号。如果脚本收到了trap命令中列出的信号，该信号不再 由shell处理，而是交由本地处理。
```shell
trap command signals
```

捕获SIGINT信号
```shell
# 捕获SIGINT信号
trap "echo ' Sorry... Ctrl-C is trapped.'" SIGINT

# 修改捕获信号只需要再一次写捕获信号的内容即可
trap "echo ' I modified the trap!'" SIGINT

# 删除捕获信号使用双破折号
trap -- SIGINT

# 恢复信号的默认行为使用单破折号
trap -- SIGINT
```

## 脚本后台运行
当**&**符放到命令后时，它会将命令和bash shell分离开来，将命令作为系统中的一个独立的后台进程运行。
```shell
./test.sh &
```

每一个后台进程都和 终端会话（pts/0）终端联系在一起。如果终端会话退出，那么后台进程也会随之退出。

想在终端会话中启动shell脚本，让脚本一直以后台模式运行到结束，即使退出了终端会话。这可以用**nohup命令**来实现

```shell
nohup ./test.sh &
```

由于nohup命令会解除终端与进程的关联，进程也就不再同STDOUT和STDERR联系在一起。为了保存该命令产生的输出，nohup命令会自动将STDOUT和STDERR的消息重定向到一个名为 nohup.out的文件中。注意，相同目录下的脚本通过nohup运行的输出会重定向到同一个文件！

## 作业控制
### 查看作业jobs命令
jobs命令用于查看当前正在处理的作业

| 选项 | 含义                                        |
| ---- | ------------------------------------------- |
| -l   | 列出进程的PID以及作业号                     |
| -n   | 只列出上次shell发出的通知后改变了状态的作业 |
| -p   | 只列出作业的PID                             |
| -r   | 只列出运行中的作业                          |
| -s   | 只列出已停止的作业                          |

![[Pasted image 20220731155741.png]]

带加号的作业会被当做默认作业。在使用作业控制命令时，如果未在命令行指定任何作业号，该作业会被当成作业控制命令的操作对象。

### 重启作业

#### bg命令：将作业以后台运行的形式重启

```shell
bg 作业号 
# 作业号为job命令中括号中的编号，不是进程号
# 不指定作业号表示重启默认作业
```

#### fg命令：将后台作业调到前台运行

```shell
fg 作业号
```

### 调度优先级
在Linux中，内核负责将CPU时间分配给系统上运行的每个进程。**调度优先级**（scheduling priority）是内核分配给进程的CPU时间（相对于其他进程）。在Linux系统 中，由shell启动的所有进程的调度优先级默认都是相同的。
<u>调度优先级是个整数值，从-20（最高优先级）到+19（最低优先级）</u>。默认情况下，bash shell以优先级0来启动所有进程。

#### nice命令：设置命令启动时的调度优先级

```shell
nice -n 10 ./test.sh > ./test.out
```

nice命令不允许普通用户提高优先级

#### renice命令：修改已运行命令的优先级
```shell
renice -n 10 -p 进程号
```

- 只能对属于你的进程执行renice； 
- 只能通过renice降低进程的优先级；
- root用户可以通过renice来任意调整进程的优先级。

### 定时运行作业
#### 计划执行作业at命令
作用：
at命令允许置顶Linux系统何时运行脚本。at命令会将作业提交到队列中，指定shell何时运行该作业。

原理：
有一个守护进程atd，会检查系统上的`/var/spool/at`来获取at命令提交的作业。默认情况下60秒会检查一次这个目录。如果有作业的运行时间与当前时间匹配，就运行该作业

```shell
at command time

at [-f filename] time
```

可以这样来指定时间：
![[Pasted image 20220803105654.png]]

使用at命令执行的作业会将执行用户的电子邮件地址作为标准输出和标准错误输出，任何发到标准输出和标准错误输出都会通过邮件系统发送给用户。要获任务的输出可以将输出从定向到指定文件

#### 查看作业
```
atq
```

#### 删除作业
```shell
atrm 作业号
```

### 定期运行作业
#### 定期运行作业cron程序
cron程序可以安排要定期执行的作业，会在后台运行并检查一个cron时间表，获知已安排执行的作业
cron时间表的格式如下：
> min hour dayofmonth month dayofweek command
> 
> 每个月每天执行命令
> 15 10 * * * command
> 
> 每个月第一天中午12点执行命令
> 00 12 1 * * command
> 
> 如果要每个月的最后一天执行命令，由于月份的总天数会有不同，可以使用下面方式
> 00 12 * * * if [\`date +%d -d tomorrow\` = 01 ] ; then ; command


#### cron时间表操作
查看cron时间表内容
```shell
crontab -l
```

编辑cron时间表
```shell
crontab -e # 会打开一个文本编辑器
```

如果想要脚本每时，每周，每月，或者是每日执行，可以将脚本放在`/etc/cron.*ly`目录下，daily目录对应每天执行，weekly目录对应每周执行...
![[Pasted image 20220803130448.png]]

# 函数
## 函数的使用
```shell
#!/bin/bash

function fun1 {
	echo "Hello world"
}

fun1

```

函数名必须唯一，第二次定义相同的函数会覆盖前面定义的函数，不会报错。必须先定义了函数才能使用函数


返回值：
- bash shell会把函数当作一个小型脚本，运行结束时会返回一个退出状态码。有3种不同的方法来为函数生成退出状态码
- 可以使用`return 状态码`的方式返回一个整数值
- 要返回浮点数或者字符串，可以使用echo将要返回的结果输出到标准输出，然后使用`$()`调用函数
```shell
function fun {
	echo "string value"
}
echo $(fun)
```

参数：
- 函数的参数类似于shell脚本的参数

变量：
- 全局变量：在任何地方都能使用。默认情况下，你在脚本中定义的任何变量都是全局变量
- 局部变量：只需在函数内部使用的变量。定义局部变量要在声明语句的前面加上`local`关键字

## 使用数组
函数中使用数组作为参数要将数组转换成多个参数的形式，在函数内部再将多个参数转换成数组
![[Pasted image 20220804175046.png]]

使用数组作为返回值：将要返回的内容以多个元素的形式输出，调用函数后将多个元素组合成数组
![[Pasted image 20220804175613.png]]

## 函数递归
求阶乘
```shell
#!/bin/bash

function factorial {
        if [ $1 -eq 1 ]
        then
                echo 1
        else
                echo $[ $1 * $(factorial $[ $1 - 1 ]) ]
        fi
}

read -p "Please Enter a number: " num
ret=$( factorial num )
echo "The factorial of this number is zero: $ret"
```

## 创建库
### 创建库
库文件`mufuncs.sh`
```shell
#!/bin/bash

function addem {
        echo $[ $1 + $2 ]
}

function multem {
        echo $[ $1 * $2 ]
}

function divem {
        if [ $2 -ne 0 ]
        then
                echo $[ $1 / $2 ]
        else
                echo -1
        fi
}
```

可以使用source引用其他shell脚本中的函数，也可以使用句点号`.`
```shell
#!/bin/bash

#. ./myfuncs.sh
source ./myfuncs.sh

result=$(addem 10 15)
echo "The result is $result"

```

### 在命令行上创建函数
在命令行上创建的函数在这个系统都可以使用，不需要担心脚本是不是在PATH环境变量里
可以通过下面两种方式在命令行定义函数：
方式一：单行方式定义
```shell
# 花括号中每个语句需要用分号分割
function addtem { echo $[ $1 + $2 ]; }
```

方式二：多行方式定义
```shell
# 直接在命令行定义函数
function addtem {
echo $[ $1 + $2 ]
}
```

如果需要定义的函数在每次关机重启后还能正常使用，可以在`~/.bashrc`文件中定义

# 模拟图形化界面
## 菜单选项
```shell
#!/bin/bash
function diskspace {
        clear
        df -k
}

function whoseon {
        clear
        who
}

function memusage {
        clear
        cat /proc/meminfo
}

function menu {
        clear
        echo
        echo -e "\t\t\tSys Admin Menu\n"
        echo -e "\t1. Display disk space"
        echo -e "\t2. Display logged on users"
        echo -e "\t3. Display memory usage"
        echo -e "\t0. Exit program\n\n"
        echo -en "\t\tEnter optin: "
        read -n 1 option
}

while [ 1 ]
do
        menu
        case $option in
                0)break;;
                1)diskspace;;
                2)whoseon;;
                3)memusage;;
                *)clear
                        echo "Sorry, wrong selection"
        esac
        echo -en "\n\n\t\t\tHit any key to continue"
        read -n 1 line
done

clear
```

### select命令
select命令只需要一条命令就可以创建出菜单，然后获取输入的答案并自动处理。
格式如下：
```shell
# 类似于for循环
select variable in list do
	commands
done
```

```shell
#!/bin/bash
function diskspace {
        clear
        df -k
}

function whoseon {
        clear
        who
}

function memusage {
        clear
        cat /proc/meminfo
}

PS3="Enter option: "
select option in "Display disk space" "Display logged on users" "Display memory usage" "Exit program"
do
        case $option in
                "Exit program")
                        break;;
                "Display disk space")
                        diskspace;;
                "Display logged on users")
                        whoseon;;
                "Display memory usage")
                        memusage;;
                *)
                        clear
                        echo "Sorry, wrong selection"
        esac
done

clear
```

## dialog包的使用
安装
```shell
apt-get install dialog
```
### dialog部件
使用方法：
```shell
# widget是部件的名称，如textbox，yesno，menu等等
# parameters为部件的宽高和部件需要的文本
dialog --widget parameters
```

如常用部件的使用方式：
```shell
dialog --msgbox text height width

dialog --title "Please answer" --yesno "Is this thing on?" 10 20

dialog --inputbox "Enter your age:" 10 20 2>age.txt

dialog --textbox /etc/passwd 15 45

dialog --menu "Sys Admin Menu" 20 30 10 1 "Display disk space" 2 "Display users" 3 "Display memory usage" 4 "Exit" 2> test.txt

dialog --title "Select a file" --fselect $HOME/ 10 50 2>file.txt
# ...
```

dialog有如下部件：
![[Pasted image 20220811171317.png]]

### 常用选项
```plain
--title <title>         | 指定将在对话框的上方显示的标题字符串
--colors                | 解读嵌入式“\ Z”的对话框中的特殊文本序列，序列由下面的字符 0-7, b  B, u, U等，恢复正常的设置使用“\Zn”。
--no-shadow             | 禁止阴影出现在每个对话框的底部
--shadow                | 应该是出现阴影效果
--insecure              | 输入部件的密码时，明文显示不安全，使用星号来代表每个字符
--no-cancel             | 设置在输入框，菜单，和复选框中，不显示“cancel”项
--clear                 | 完成清屏操作。在框体显示结束后，清除框体。这个参数只能单独使用，不能和别的参数联合使用。
--ok-label <str>        | 覆盖使用“OK”按钮的标签，换做其他字符。
--cancel-label <str>    | 功能同上
--backtitle <backtitle> | 指定的backtitle字符串显示在背景顶端。
--begin <y> <x>         | 指定对话框左上角在屏幕的上的做坐标
--timeout <secs>        | 超时（返回的错误代码），如果用户在指定的时间内没有给出相应动作，就按超时处理
--defaultno             | 使的是默认值 yes/no，使用no
--sleep <secs>          | zz
--stderr                | 以标准错误方式输出
--stdout                | 以标准方式输出
--default-item <str>    | 设置在一份清单，表格或菜单中的默认项目。通常在框中的第一项是默认
```

### dialog实现菜单选项功能
```shell
#!/bin/bash

temp=$(mktemp -t test.XXXXXX)

temp2=$(mktemp -t test2.XXXXXX)

function diskspace {
        df -k > $temp
        dialog --textbox $temp 20 60
}

function whoseon {
        who > $temp
        dialog --textbox $temp 20 60
}

function memusage {
        cat /proc/meminfo > $temp
        dialog --textbox $temp 20 50
}

while [ 1 ]
do
        dialog --menu "Sys Admin Menu" 20 30 10 1 "Display disk space" 2 "Display users" 3 "Display memory usage" 0 "Exit" 2> $temp2
        if [ $? -eq 1 ]
        then
                break
        fi

        selection=$(cat $temp2)

        case $selection in
                1)
                        diskspace;;
                2)
                        whoseon;;
                3)
                        memusage;;
                0)
                        break;;
                *)
                        dialog --msgbox "Sorry, invalid selection" 10 30
        esac
done

rm -f $temp 2> /dev/null
rm -f $temp2 2> /dev/null
```

# 正则表达式
Linux中的不同应用程序可能会使用不同类型的正则表达式。包括编程语言（Java、Perl和Python）、Linux实用工具（比 如sed编辑器、gawk程序和grep工具）以及主流应用（比如MySQL和PostgreSQL数据库服务器）。

正则表达式是通过正则表达式引擎实现的，Linux有两种流行的正则表达式引擎
- POSIX基础正则表达式引擎
- POSIX扩展正则表达式引擎


锁定行首：`^`
锁定行尾：`$`
点号字符：必须匹配一个字符
字符组：`[]` 只要字符是中括号定义的字符，就匹配。在定义的字符组开头加上`^`表示不包含。如：`[^abc]`
特殊字符串：

| 组            | 描述                                           |
| ------------- | ---------------------------------------------- |
| `[[:alpha:]]` | 匹配任意字母字符，不管是大写还是小写           |
| `[[:alnum:]]` | 匹配任意字母数字字符0-9,A-Z,a-z                |
| `[[:blank:]]` | 匹配空格或制表符                               |
| `[[:digit:]]`   | 匹配0-9之间的数字                              |
| `[[:lower:]]`   | 匹配小写字母字符a-z                            |
| `[[:print:]]`   | 匹配任意可打印字符                             |
| `[[:punct:]]`   | 匹配标点符号                                   |
| `[[:space:]]`   | 匹配任意空白字符：空格，制表符，NL，FF，VT和CR |
| `[[:upper:]]`   | 匹配任意大写字母字符A-Z                        |

文本出现0次或多次：`*`
文本出现1次或多次：`+`
文本出现0次或一次：`?`



























