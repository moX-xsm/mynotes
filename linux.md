vimtutor学习手册

# 一.linux发展历史

![image-20200310194205656](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192126.png)

# 二.SHELL编程

## 1.认识shell

- console：大型设备的工作台
- terminal：一个向电脑输入数据的电子设备、一个显示接受到数据的交互系统
- shell：人机接口，是指“提供使用者使用界面的软件”，是一种命令解释器，GUI也是一种shell

### 1.1terminal、shell和bash三者的关系

![image-20200426223747258](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192130.png)

terminal里面关联一个shell，shell一共有三种表现形式：

- bash
- zsh
- sh

ubuntu默认情况下是bash，macos最新为zsh

### 1.2全局设置与个人设置

![image-20200426224109731](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192133.png)

linux是多用户多任务的系统，用户之间是隔离的，用户间的用户程序也是隔离的

- 全局设置放在系统的根目录
- 个人设置放在自己用户的家目录

大多数软件是放在系统根目录，有两个配置文件，一个全局配置文件，一个个人的配置信息，这样比较方便

### 1.3关于bash的配置文件

#### 1.全局配置

![image-20200426224539585](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192138.png)

linux系统全局的配置文件在/etc

- /etc/bash.bashrc：登陆时的shell就是bash
- /etc/bash.profile:用户登陆时会执行
- /etc/bash.bash.logout：用户退出时会执行

#### 2.个人配置

![image-20200426225122497](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192141.png)

个人配置文件在用户家目录下

- ~/.bashrc
- ~/.bash_profile
- ~/.bash_logout
- ~/.input:用户输入时会执行

## 2.shell脚本编程

shell中所有的变量当做字符处理 只有加$才会进行取值 不加一律当字符处理！！(())内进行数学运算时除外

### 2.1变量、运算符等

```shell
#!/bin/bash
#用的是bash解释器
echo "Hello HaiZei" #这是注释

#执行脚本文件的两种方法
bash a.sh#用bash执行a.sh

chmod +x a.sh
a.sh#将a.sh加上可执行权限
```

shell是弱类型语言，没有强制规定变量类型

![image-20200312192918955](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192146.png)

- ``命令替换符:将后面命令的输出赋值给变量

- $变量引用：要用到变量的值要在变量前加上，可以用{}来限制范围，如`${FileName}`

- `a=$a:a`：在a后拼接上:a

  ```shell
  mox@mox-ubuntu:~$ a=`pwd`
  mox@mox-ubuntu:~$ echo $a
  /home/mox
  #我们可以看到a为/home/mox
  #打印a时需在a前家$
  mox@mox-ubuntu:~$ a=$a:a
  mox@mox-ubuntu:~$ echo $a
  /home/mox:a
  #可以看到a后面加上了:a
  ```

  ![image-20200312193328861](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192152.png)

  eg：脚本0.sh

  ```shell
  #!/bin/bash
  echo $#
  echo $1
  echo $2
  ```

  ![image-20200426231047806](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192155.png)

  #可以求变量长度

  ![image-20200312194101420](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192159.png)

  ![image-20200312194136262](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192202.png)

### 2.2输入输出

```shell
#read var 输入
mox@mox-ubuntu:~read$ read a
asdfsdafsadf

#echo和printf用于输出
mox@mox-ubuntu:~$ echo $a
asdfsdafsadf
mox@mox-ubuntu:~$ printf "hello world\n"
hello world

```

![image-20200312200129649](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192205.png)

![image-20200312200520313](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192209.png)

![image-20200312200618427](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192212.png)



### 2.3语法

#### 1.函数

```shell
#函数定义的3种方法
#函数定义中的{}与前面需要加空格
function print1 {
    echo $1
    return 
}
print2() {
    echo $1
    return
}
function print3() {
    echo $1
    return
}
#函数调用
print1 "function 1"
print2 "function 2"
print2 "function 3"

```

![image-20200427091233317](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192216.png)

#### 2.流程控制-IF

加了(())表示对括号内的内容进行数学运算（+-*/、比较大小等）

```shell
if [[ contion ]];then
    #statements
fi
#########################
if [[ contion ]];then
    #statements
else
    #statements
fi
########################
if [[ contion ]];then
    #statements
elif [[ contion ]];then
    #statements
else
    #statements
fi

#######################
if ((i < 5));then
	#dosomething
fi
```

[[ contion ]]是test表达式，用来测试条件是否成立，man test查看

```shell
       INTEGER1 -eq INTEGER2
              INTEGER1 is equal to INTEGER2

       INTEGER1 -ge INTEGER2
              INTEGER1 is greater than or equal to INTEGER2

       INTEGER1 -gt INTEGER2
              INTEGER1 is greater than INTEGER2

       INTEGER1 -le INTEGER2
              INTEGER1 is less than or equal to INTEGER2

       INTEGER1 -lt INTEGER2
              INTEGER1 is less than INTEGER2

       INTEGER1 -ne INTEGER2
              INTEGER1 is not equal to INTEGER2

```

eg:

```shell
if [[ $1 -eq $2 ]];then
    echo "$1 = $2"
elif [[ $1 -lt $2 ]];then
    echo "$1 < $2"
else
    echo "$1 > $2"
fi

```

![image-20200427092204477](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192222.png)

```bash
i=6
if [[ i -lt 5 ]];then
    echo "$i < 5"
else
    echo "$i >= 5"
fi

if ((i < 5));then
    echo "$i < 5"
fi
```



#### 3.流程控制-FOR

```shell
for i in var;do
    #statements
done
###########################
for (( i = 0; i < 10; i++ ));do
    #statements
done
```

eg:创建10个test.cpp文件

![image-20200312202743175](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192225.png)

```bash
#!/bin/bash
#遍历数组
declare -a arr
arr=( 1 2 3 4 5 6 7 )
for i in ${arr[@]};do
    echo $i
done

printf "\n"
for (( i=0; i<7; i++ ));do
    echo ${arr[$i]}
done

```



#### 4.流程控制-UNTIL

until 直到condition前一直执行

```shell
until [[ condition ]];do
    #statements
done

until ((i < 5));do
    #statements
done
```

打印0-5：

```bash
#!/bin/bash
i=0

until ((i > 5));do
    echo $i
    ((i++))
done

```



#### 5.流程控制-CASE

case跟c语言的switch作用相同

```shell
case word in
    pattern )
        #statements
esac
```



```bash
#!/bin/bash
read i
case $i in 10 ) 
    echo $i
esac

```

#### 6.数组

```shell
#数组声明
declare -a arr
#数组使用
arr[i]=val
arr=(val1 val2 val3)
```

![image-20200312204714522](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192229.png)



#### 7.四则运算

```shell
#四则运算时需要加$[]或者$(())
a=100
b=5
c=$[$a/$b]###
d=$(($a+$b))###在（（））内的数学运算可以省略$
echo $c $d
```

#### 8.变量参数展开和字符串操作

![image-20200312195004357](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192234.png)

![image-20200312195112832](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192238.png)



### 2.4用shell编程实现线性筛：1-1000以内所有素数和

```shell
#!/bin/bash
#shell编程可以不用初始化


declare -a prime
End=1000
Sum=0

#初始化数组
function init(){
    S=$1
    E=$2
    for (( i = $S; i <= $E; i++));do
        prime[$i]=0
    done
}
init 0 1000

for (( i = 2; i <= ${End}; i++ ));do
    if [[ ${prime[$i]} -eq 0 ]];then
    #if [[ ${prime[$i]}x == x ]];then#用字符串的方法判断是否为空  用这句不能加数组的初始化
        prime[0]=$[${prime[0]}+1]
        prime[${prime[0]}]=$i
        Sum=$[${Sum}+$i]
    fi
    for (( j = 1; j <= ${prime[0]}; j++ ));do
        if [[ $[$i*${prime[$j]}] -gt ${End} ]];then
            break
        fi
        prime[$[$i*${prime[$j]}]]=1
        if [[ $[$i%${prime[$j]}] -eq 0 ]];then
            break
        fi
    done
done
echo $Sum

```

![image-20200427094222339](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192242.png)

### 2.5用shell编成实现随机点名

```shell
#!/bin/bashi
#source 2.suijishu.sh是将脚本在当前的shell进程运行一次，在终端中运行rand即可运行rand函数

if [[ $# -ne 1 ]];then
    echo "Usage: bash 2.suijishu.sh num"
    exit
fi

Cnt=$1

Names=(`cat names`)#names为同路径下的文件 将所有人员姓名按行放在names文件中 ()表示数组！！
function rand(){
    Min=1
    Max=${#Names[*]}
    Num=`cat /dev/urandom | head -n 10 | cksum | cut -d ' ' -f 1`#获取随机数
    #Num=`date +%s`
    echo $[$Num%$Max]#用echo来充当返回值
}

for i in `seq 1 $Cnt`;do
    RandNum=`rand`
    echo "${Names[${RandNum}]}"
done

```

![image-20200427094802782](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192246.png)



# 三.命令系统

## 1.man手册

man手册包括以下几部分：

![image-20200314153743877](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192256.png)

man手册中搜索/+搜索内容

man -f 是什么

man -k 什么与其相关

+| grep reboot 标记reboot

![image-20200314154456736](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192300.png)

![image-20200314154601957](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192302.png)

## 2.tldr

tldr是一个简易版的man，是i从网络上整理的

too long don't read

eg:tldr ls

rm -rf /home/mox/.tldr/  删除目录的命令

## 3.升级python方法

当python版本过低时

which python   先看是哪个文件

sudo rm -f /usr/bin/python删除此文件

安装更新版本的python  在/usr/bin/文件下查看版本

sudo ln -s /usr/bin/python3.6 /usr/bin/python 建立新的软链接 

usr目录下的文件都是系统文件，注意尽量不要删除

- 软连接 -s： 建立一个指向特定文件的文件 改变任何文件另一个都会修改  删除主文件软连接文件失效 删除软连接文件主文件无影响
- 硬链接 -f：copy一份文件 文件名不一样 其余一样 当删除两个文件时，文件才会失效



## 4.通配符

| 符号 |     意思     |
| :--: | :----------: |
|  ?   | 通配单个字符 |
|  *   | 任意多个字符 |

![image-20200314162604056](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192308.png)





## 5.任务管理

|  符号  |                             作用                             |
| :----: | :----------------------------------------------------------: |
|   &    |                      command &后台执行                       |
|   ；   |                      cmd1；cmd2顺序执行                      |
|   &&   |              cmd1 && cmd2第一个成功，执行第二个              |
|  \|\|  |                   第一个不成功才执行第二个                   |
| `cmd`  |               命令替换符，替换为当前命令的结果               |
| crtl+z | 挂起，系统不执行了,对系统无负载；fg继续执行；挂起多个时jobs查看挂起的任务，fg+序号 |
|   bg   |                         转为后台运行                         |
|   fg   |                         转为前台运行                         |
|  jobs  |                        查看挂起的任务                        |

%在bash相当于fg



## 6.管道、重定向

a|b|c将命令变成一条通路,a执行完的数据给b，b处理完后给c

|  符号   |                             作用                             |
| :-----: | :----------------------------------------------------------: |
| a\|b\|c |   通道，将命令变成一条通路,a执行完的数据给b，b处理完后给c    |
|    >    | cat > a.txt,重定向,重写,将a.txt的内容重写为cat的输出，命令>文件 |
|   >>    |                             追加                             |
|    <    |   ./a.out < a.txt把cata.txt数据作为a.out的输入，文件>命令    |
|   <<    | 把标准输入流输出到屏幕  cat > a.log  << xxx,定义当读到xxx时结束cat命令 |

EOF文件结束标志  crtl+d表示EOF

## 7.linux系统信息的获取

![image-20200314180920969](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192314.png)

ntp用来维护基准时间

重点 uptime w last uname date



# 四.linux的目录树

## 1.目录树

![image-20200314185933277](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192318.png)

现在的根目录是/

/etc/network 第一个/表示根目录

./file .表示当前目录   /表示目录分割符

|  目录  |                             作用                             |
| :----: | :----------------------------------------------------------: |
|  /usr  |                        用户级别的程序                        |
| /boot  |                    引导系统启动，内核文件                    |
|  /dev  |                           设备文件                           |
|  /etc  |                   配置文件（全局配置文件）                   |
| /home  | 家目录不是/home，/home是所有用户的聚集地  /home是一栋楼，/home/mox才是家目录，root的家目录是/root，并不是所有用户的家目录都在/home     HOME存放的用户的家目录，cd $HOME回家 |
|  /lib  |                          library库                           |
| /media |                    挂载光盘，自动识别光驱                    |
|  /mnt  |                       挂载u盘，硬盘等                        |
|  /opt  |                       可选的，一般为空                       |

| 目录  |                        作用                         |
| :---: | :-------------------------------------------------: |
| /tmp  | 临时文件 重启之后丢失  只有文件的创建者才可删除文件 |
| /bin  |                  二进制可执行程序                   |
| /sbin |           超级管理员用的二进制可执行文件            |
| /var  |         动态数据数据，存放日志，缓存信息等          |

## 2.挂载？？！！

u盘系统挂载在/mnt下   绿色为u盘系统

sshfs 远程安全终端 文件系统   将远端目录挂载到本地

umount ./mnt 解挂

df -h 查看磁盘挂载

![image-20200314191939803](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192322.png)

执行完挂载后，/mnt原先的目录临时被屏蔽

解挂后会恢复



## 3.启动流程

![image-20200314193245429](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192326.png)

linux启动快 尽可能启动更少的进程并且并行启动，目前用systemd并发启动

![image-20200314194001332](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192329.png)

env查看当前的环境变量

# 五.文件与目录

## 1.目录

![image-20200314200233450](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192332.png)

绝对路径  /home/mox/......

相对路径  .指当前目录   ..当前目录的上层目录

### 1.1cd切换工作目录

![image-20200315102557834](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192335.png)

cd - = cd $OLDPWD

### 1.2pwd打印工作目录

![image-20200315102851458](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192336.png)

| 参数 | 作用                         |
| ---- | ---------------------------- |
| -L   | 显示逻辑工作目录             |
| -P   | 显示物理工作目录（绝对位置） |

### 1.3mkdir创建目录

![image-20200315103053977](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192338.png)

| 参数 | 作用                                                         |
| ---- | ------------------------------------------------------------ |
| -p   | 自动创建父目录（mkdir -p ./test/test/test时，当.下没有test时自动创建） |
| -m   | 设置权限                                                     |

### 1.4rmdir删除目录

![image-20200314201148235](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192341.png)

## 2.文件与目录的管理

![image-20200314201304609](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192343.png)

### 2.1cp拷贝

![image-20200315103707967](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192345.png)

| 参数 | 作用                       |
| ---- | -------------------------- |
| -i   | 交互模式，询问用户是否拷贝 |
| -s   | 软链接                     |
| -l   | 硬链接                     |

### 2.2rm删除

![image-20200315103852445](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192349.png)

| 参数 | 作用                                      |
| ---- | ----------------------------------------- |
| -i   | 交互模式                                  |
| -r   | 递归删除（慎用）                          |
| -f   | 暴力删除force（f一般有force，file等意思） |

### 2.3mv移动

![image-20200315104030085](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192356.png)

### 2.4basename和dirname

|          | 作用     |
| -------- | -------- |
| basename | 取文件名 |
| dirname  | 取目录名 |

## 3.文件内容的查阅

![image-20200315104415348](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192401.png)

wc -l求行数

###  3.1cat正向连续读/tac反向连续读（按行正向/反向）

![image-20200315104254477](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192403.png)

tac与cat相反，从最后一行开始打印

### 3.2NL输出行号显示文件

![image-20200315104517048](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192406.png)

| 参数                 | 选项        | 作用                |
| -------------------- | ----------- | ------------------- |
| -b指定行号方式       | a           | 空行标行号          |
|                      | t           | 空行不标行号        |
| -n列出行号的表示方式 | ln          | 行号左对齐          |
|                      | rn          | 行号右对齐          |
|                      | rz          | 行号右对齐，前面补0 |
| -w                   | num（数字） | 行号占num位         |

### 3.3more/less

![image-20200315105319956](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192409.png)

![image-20200315105325991](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192411.png)



### 3.4head/tail

![image-20200315105542738](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192413.png)

| 函数 | 参数    | 作用            |
| ---- | ------- | --------------- |
| head | -n num  | 显示前num行     |
|      | -n -num | 除后num行都显示 |
| tail | -n num  | 显示后num行     |
|      | -n +num | 除前num行都显示 |

eg：显示文档的101-120行

man ls | nl -b a -n rz -w 3 | head -n 120 | tail -n 20

![image-20200315113133918](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192427.png)

##  4.修改文件

![image-20200315143649315](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192428.png)

![image-20200315143709591](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192431.png)

![image-20200315143253597](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192434.png)

![image-20200315144035859](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192436.png)

| 选项 | 作用                       |
| ---- | -------------------------- |
| a    | 只能增加，如log文件        |
| i    | 文件不能作任何操作，只能读 |
| s    | 直接从磁盘里删除           |
| u    | 从系统层面删除             |

文件删除时，只是操作系统层面的删除，磁盘上的内容还在，当要往磁盘里写东西时，把删除的内容覆盖掉就能做到真正的删除。这是为了使删除更方便更快，从磁盘删除时间会变慢。所以误删除后要想找回原来的文件，先关机

![image-20200315144725263](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192439.png)

chattr修改的是基本的属性，与所有的用户无关

chmod修改的是用户对于文件的属性

## 5.文件的特殊权限

![image-20200315150131638](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192442.png)

uid用户权限

用u+s时将u的x权限位置改为s

当既有s和x时u+s，当只有s时u+S

![image-20200315150339997](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192445.png)

sx    s

s-    S

![image-20210722101858211](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210722101906.png)

修改的是user的权限，但是这个权限是文件的属性，s权限不止user拥有

一个电脑上的所有用户密码文件为同一个文件/etc/shadow，当用户修改密码时，很明显要访问这个文件，但是如果访问这个文件，那么user能访问到其他用户的密码，所以不行，这时需要用到s权限--在

>  s，表示set UID或set GID。位于user或group权限组的第三位置。如果在user权限组中设置了s位，则当文件被执行时，该文件是以文件所有者UID而不是用户UID 执行程序。如果在group权限组中设置了s位，当文件被执行时，该文件是以文件所有者GID而不是用户GID执行程序。s权限位是一个敏感的权限位，容 易造成系统的安全问题。请在设置时小心，并注意系统中已有的SUID或SGID文件和目录。
>
>   举个简单的例子，某个可执行文件foo，如果起所有者为root，在其权限为普通的x的时候，该文件被执行的时候，是以执行该文件的用户权限在执行。但是将其设置为s的时候，该文件被执行就是以root权限来执行了。
>
>   这在一些需要超级权限的系统服务中很有用处

![image-20200315151055825](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192446.png)

set_gid只针对目录（文件夹）



cd ~mox快速回到mox的家目录

![image-20200315151633250](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192449.png)

suyelu在198目录加了set_gid权限，虽然meng在198创建了e.sh，但是所属组为suyelu

用户 组 其他人

u+   g+    o+

/tmp中有黏着位权限t，在/tmp下，用户只能删除自己创建的文件

![image-20210722102920205](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210722102928.png)

![image-20200315152150696](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192451.png)

亚马逊aws申请虚拟机

## 6.ext4文件系统

详见操作系统笔记中的文件系统

linux文件系统为ext4

superblocks存储下面两种分区的大小，是否用了，启动项信息等，有多个，防止数据丢失

inode文件节点，存储文件的基本信息，时间、特性等，属性权限信息放在这里，blocks信息

block存放文件内容

软链接相当于快捷方式   硬链接两个文件的inode是一样的 cp是不一样的

ext4文件系统参考[https://www.cnblogs.com/justmine/p/9128730.html](https://www.cnblogs.com/justmine/p/9128730.html)



777=111 111 111,u+g+o的权限都是rwx

chmod 777 a.log

chmod 755 b.log

![image-20200315160045011](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192455.png)

d开头是指目录    l开头是指链接文件 

u+g+o的权限 链接数(链接数为0说明删除)  所属用户 所属组 文件大小 mtime 文件名



## 7.文件的查询

![image-20200315160705713](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192458.png)

![image-20200315160903135](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192503.png)

![image-20200315160824229](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192504.png)

![image-20200315161054676](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192508.png)

sudo updatedb 升级电脑里的数据库信息

![image-20200315161520376](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192512.png)

.

![image-20200315161905989](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192514.png)

{}存放find命令找到的内容 ，-exec ls -l {} \  接着执行ls -l 命令

wc -l求行数

-o或者的意思

```shell
find ~ -name "*.c" -o -name "*.cpp" -o -name "*.h" -o -name "*.sh" | wc -l
```

求一共有多少个.c .cpp .h .sh文件

```shell
find ~ -name "*.c" -o -name "*.cpp" -o -name "*.h" -o -name "*.sh" | xargs cat
find ~ -name "*.c" -o -name "*.cpp" -o -name "*.h" -o -name "*.sh" |  cat 
#上面两条语句的区别  
#第二条直接打印各个文件的名字
#第一条是将前面find的文件作为参数给cat，输出每个文件的内容     xargs
```

kill -l查看系统的信号



# 六、数据提取操作

![image-20200315193214894](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192520.png)

![image-20200315193311344](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192524.png)

![image-20200315193412206](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192526.png)

![image-20200315194308347](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192528.png)

![image-20200315194614037](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192532.png)

uniq -c去重计数

tee双向重导向

![image-20200315195432377](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192535.png)

![image-20200315200024603](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192537.png)

用来把管道前面的标准输出转变为后面命令的参数而非标准输入流

![image-20200315200515534](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192540.png)

文件中的词频统计：

![image-20200315201345022](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192545.png)



# 七.用户管理

![image-20210310163439006](https://gitee.com/xsm970228/images2020.9.5/raw/master/4Ki7cb2uToE1GJM.png)

![image-20210310163627366](https://gitee.com/xsm970228/images2020.9.5/raw/master/vdtJXiTMlaImOU4.png)

![image-20210310163711323](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310163711323.png)

![image-20210310163725507](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310163725507.png)

![image-20210310163740214](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310163740214.png)

![image-20210310163801684](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310163801684.png)

![image-20210310163902582](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310163902582.png)

![image-20210310163917314](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310163917314.png)



![image-20210310163850129](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310163917314.png)

![image-20210310164505474](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164505474.png)

![image-20210310164519219](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164519219.png)

![image-20210310164532762](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164532762.png)

![image-20210310164550003](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164550003.png)

![image-20210310164607391](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164607391.png)

![image-20210310164617154](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164617154.png)

![image-20210310164634609](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164634609.png)

# 八.进程管理

![image-20210310164748284](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164748284.png)

![image-20210310164804194](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164804194.png)

![image-20210310164813004](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164813004.png)

![image-20210310164825063](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164825063.png)

![image-20210310164834528](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164834528.png)

![image-20210310164843084](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164843084.png)

![image-20210310164851238](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164851238.png)

![image-20210310164908320](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164908320.png)

![image-20210310164918960](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310164918960.png)

# 九.任务管理

![image-20210310165253698](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310165253698.png)

![image-20210310165305400](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310165305400.png)

[https://www.cnblogs.com/xiao-lei/p/11084578.html](https://www.cnblogs.com/xiao-lei/p/11084578.html)

![image-20210310170101865](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310170101865.png)

![image-20210310170148874](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310170148874.png)

![image-20210310170742988](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210310170742988.png)

ctrl + d结束



# 十.linux三剑客（grep awk sed）

awk

![image-20200425144354867](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210306192640.png)



awk:[https://www.runoob.com/linux/linux-comm-awk.html](https://www.runoob.com/linux/linux-comm-awk.html)



三剑客可以读懂正则表达式

grep擅长查找功能，sed擅长取行和替换。awk擅长取列。

linux三剑客：[https://blog.csdn.net/sj349781478/article/details/82930982](https://blog.csdn.net/sj349781478/article/details/82930982)

sed：[https://www.runoob.com/linux/linux-comm-sed.html](https://www.runoob.com/linux/linux-comm-sed.html)

# 十一.shell编程实例

## 1.获取cpu信息

![image-20210722191751583](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210722191752.png)

eval:声明变量 `tldr eval`查看

bc:计算器 可以计算浮点数  $[[]]/$(())只能用于整数的计算

```bash
#!/bin/bash

Time=`date +"%Y-%m-%d__%H:%M:%S"`
LoadAvg=`cut -d " " -f 1-3 /proc/loadavg`
Temp=`cat /sys/class/thermal/thermal_zone0/temp`
Temp=`echo "scale=2;$Temp / 1000" | bc`#scale=2;表示保留两位小数
eval `head -n 1 /proc/stat | awk -v sum=0 '{for (i=2;i<=11;i++){sum = sum + $i} printf("sum1=%d;idle1=%d\n", sum, $5)}'`
sleep 0.5
eval `head -n 1 /proc/stat | awk -v sum=0 '{for (i=2;i<=11;i++){sum = sum + $i} printf("sum2=%d;idle2=%d\n", sum, $5)}'`
CpuUsedPerc=`echo "scale=2;(1 - (($idle1 + $idle2) / ($sum1 + $sum2))) * 100" | bc`

WarnLevel="normal"

if [[ `echo "$Temp >= 70" | bc -l` -eq 1 ]];then
    WarnLevel="warning"
elif [[ `echo "$Temp >= 50" | bc -l` -eq 1 ]];then
    WarnLevel="note"
fi

echo "$Time $LoadAvg $CpuUsedPerc% $Temp℃  $WarnLevel"

```

## 2.获取内存信息

![image-20210722203325382](https://gitee.com/xsm970228/images/raw/master/image-20210722203325382.png)

free -m:以M大小显示内存信息

![image-20210722204218078](https://gitee.com/xsm970228/images/raw/master/image-20210722204218078.png)

```bash
#!/bin/bash

if (($# < 1));then
    echo "Usage: $0 DyAver"
fi

Time=`date +"%Y:%m:%d__%H:%M:%S"`

DyAver=$1

if [[ ${DyAver}x == x ]];then
    exit 1
fi

eval `free -m | head -n 2 | tail -n 1 | awk '{printf("total=%s;used=%s\n", $2, $3 + $6)}'`
MemUsedPrec=`echo "scale=1;$used / $total * 100" | bc`

Free=$(( $total - $used ))

DyAver=`echo "scale=1;$DyAver * 0.3 + $MemUsedPrec * 0.7" | bc`

echo "$Time ${total}M ${Free}M $MemUsedPrec% $DyAver%"

```

## 3.获取硬盘信息

![image-20210722205647501](https://gitee.com/xsm970228/images/raw/master/image-20210722205647501.png)

df -T:显示文件系统信息

df -m：以M大小显示

df -x tmpfs：去掉tmpfs的行

```bash
#!/bin/bash
Time=`date +"%Y:%m:%d__%H:%M:%S"`
#df -T显示文件系统类型 -x去掉某些类型 Pname["NR"] NR为awk内置参数 行
eval `df -T -m -x tmpfs -x devtmpfs -x squashfs | tail -n +2 | awk -v DiskSum=0 -v DiskLeft=0 '{DiskSum+=$3; DiskLeft+=$5; printf("Pname["NR"]=%s;Psum["NR"]=%d;Pleft["NR"]=%d;PusePerc["NR"]=%s\n", $7, $3, $5, $6)} END {printf("DiskSum=%d;DiskLeft=%d;Pnum=%d\n", DiskSum, DiskLeft, NR)}'`

for (( i=1; i<=$Pnum; i++ ));do
    echo "$Time 1 ${Pname[$i]} ${Psum[$i]}M ${Pleft[$i]}M ${PusePerc[$i]}"
done

UsePerc=`echo "scale=4;(1 - $DiskLeft / $DiskSum) * 100" | bc`#以4精度计算
UsePerc=`printf "%.1f" "$UsePerc"`#转换为1精度

echo "$Time 0 disks ${DiskSum}M ${DiskLeft}M ${UsePerc}%"

```

## 4.获取用户信息

![image-20210722213503461](https://gitee.com/xsm970228/images/raw/master/image-20210722213503461.png)

用户号1000以前的都是系统用户 1000以后的才是自己的用户

判断一个用户是否是管理员权限：

- 是否在sudo组中
- 是否在/etc/sudoers文件中定义

```bash
#!/bin/bash
Time=`date +"%Y:%m:%d__%H:%M:%S"`
#-F: 每行以：分割
eval ` awk -F: -v sum=0 '{if ($3 >= 115 && $3 != 65534){sum+=1}} END {printf("UserSum=%s\n", sum)}' /etc/passwd`


MostActiiveUser=(`last -w | cut -d " " -f 1 | grep -v reboot | grep -v wtmp | grep -v ^$ | sort | uniq -c | sort -k 1 -n -r | awk -v num=3 '{if(num > 0) {printf("%s ", $2); num--}}'`)

#eval `awk -F: '{if($3 == 1000){printf("UserWithRoot=%s\n", $1)}}' /etc/passwd`

UserWithRoot=(`cat /etc/group | grep sudo | cut -d : -f 4 | tr ',' ' '`)


UserLogedIn=(`w -h | awk '{printf(",%s_%s_%s", $1, $3, $2)}' | cut -c 2-`)


echo "$Time $UserSum [${MostActiiveUser[*]}] [${UserWithRoot[*]}] [${UserLogedIn[*]}]"

```

## 5.恶意进程检测

![image-20210723100929545](https://gitee.com/xsm970228/images/raw/master/image-20210723100929545.png)

ps -af：显示所有进程

ps -aux：显示进程 且显示cpu和mem占用率

ps -aux --sort=%cpu：显示进程 按%cpu这一列数据排序

ps -aux --sort=-%cpu：显示进程 按%cpu这一列数据逆序排序

```bash
#!/bin/bash
#--sort=%cpu 按cpu这一列排序 -%cpu 逆序排列
#ps -aux 会显示cpu和mem占用率
eval `ps -aux --sort=-%cpu -h | awk -v num=0 '{if($3 < 50) {exit} else{num++; printf("cpupid["num"]=%d\n", $2)}} END {printf("cpunum=%d\n", num)}'`

eval `ps -aux --sort=-%cpu -h | awk -v num=0 '{if($4 < 50) {exit} else{num++; printf("mempid["num"]=%d\n", $2)}} END {printf("memnum=%d\n", num)}'`

#echo $cpunum

if [[ $cpunum -gt 0 || $memnum -gt 0 ]];then
    sleep 5
else
    exit 0
fi

Time=`date +"%Y-%m-%d__%H:%M:%S"`

if [[ $cpunum -gt 0 ]];then
    cnt=0
    for i in ${cpupid[*]};do
    #-h 去最上面的解释栏 -q 寻找
        eval `ps -aux -h -q $i | awk -v  num=$cnt \
        '{if($3 < 50){exit} else {printf("pname["num"]=%s;ppid["num"]=%d;puser["num"]=%s;pcpu["num"]=%.2f;pmem["num"]=%.2f\n", $11, $2, $1, $3, $4)}}'`
        cnt=$[$cnt+1]
    done
fi

if [[ $memnum -gt 0 ]];then
    for i in ${mempid[*]};do
        eval `ps -aux -h -q $i | awk -v  num=$cnt \
        '{if($4 < 50){exit} else {printf("pname["num"]=%s;ppid["num"]=%d;puser["num"]=%s;pcpu["num"]=%.2f;pmem["num"]=%.2f\n", $11, $2, $1, $3, $4)}}'`
        cnt=$[$cnt+1]
    done
fi

for ((i = 0; i < $cnt; i++));do
    echo "$Time ${pname[$i]} ${ppid[$i]} ${puser[$i]} ${pcpu[$i]}% ${pmem[$i]}%"
done

```

## 6.获取系统信息

![image-20210723105633630](https://gitee.com/xsm970228/images/raw/master/image-20210723105633630.png)

```bash
#!/bin/bash
Time=`date +"%Y-%m-%d__%H:%M:%S"`

Hostname=`hostname`
OsType=`cat /etc/issue.net | tr " " "_"`
KernelVersion=`uname -r`
LoadAvg=`cat /proc/loadavg | cut -d " " -f 1-3`
Uptime=`uptime -p | tr " " "_"`
eval `df -T -m --total -x tmpfs -x devtmpfs -x squashfs | tail -n 1 | awk '{printf("DiskTotal=%d;DiskUsedP=%d\n", $3, $6)}'`

DiskWarning="normal"
if [[ $DiskUsedP -gt 90 ]];then
    DiskWarning="warning"
elif [[ $DiskUsedP -gt 80 ]];then
    DiskWarning="note"
fi


eval `free -m | head -n 2 | tail -n 1 | awk '{printf("MemTotal=%s;MeMUsed=%s\n", $2, $3)}'`
MeMUsedP=$[ $MeMUsed * 100 / $MemTotal ]
MemWarning="normal"
if [[ $MeMUsedP -gt 80 ]];then
    MemWarning="warning"
elif [[ $MeMUsedP -gt 70 ]];then
    MemWarning="note"
fi

CpuTemp=`cat /sys/class/thermal/thermal_zone0/temp`
CpuTemp=`echo "scale=2;$CpuTemp/1000" | bc`
CpuWarning="normal"

if [[ `echo "$CpuTemp >= 70" | bc -l` -eq 1 ]];then
    CpuWarning="warning"
elif [[ `echo "$CpuTemp >= 50" | bc -l` -eq 1 ]];then
    CpuWarning="note"
fi

echo "$Time $Hostname $OsType $KernelVersion $Uptime $LoadAvg $DiskTotal $DiskUsedP% $MemTotal $MeMUsedP% $CpuTemp℃  $DiskWarning $MemWarning $CpuWarning"

```



