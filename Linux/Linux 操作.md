大家好，我是小菜，一个渴望在互联网行业做到蔡不菜的小菜。可柔可刚，点赞则柔，白嫖则刚！
`死鬼~看完记得给我来个三连哦！`
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly90dmF4NC5zaW5haW1nLmNuL2xhcmdlLzg4NTZlYWM3Z3kxZmttaHd6cDZ5c2oyMDczMDV3Z2xqLmpwZw?x-oss-process=image/format,png#pic_center)

> 本文主要介绍 Linux环境下常用的命令
> 如有需要，可以参考
> 如有帮助，不忘 **点赞** ❥
> 创作不易，白嫖无义！
### 前文导读
> Linux 是一个开源、免费的操作系统，在服务器领域的应用是最强的。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvYTk5OTBkNDNmZjkxOTZjMzNhZjk4ZDAyY2VhMzcyYzQuZ2lm#pic_center)


#### Linux 的目录结构
![目录结构图](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWNzLmltYWdlcy5hYy5jbi9pbWFnZS81ZWFlYzVmMzY0YzQ1Lmh0bWw?x-oss-process=image/format,png#pic_center)
- `/bin` **重点**
是 Binary 的缩写，这个目录存放着最经常使用过的命令
- `/sbin` （/usr/sbin、/usr/local/sbin）
s 就是 Super User 的意思，这里存放的是系统管理员使用的系统管理程序
- `/home` **重点**
存放普通用户的主目录，在 Linux 中每个用户都有一个自己的目录，一般该目录是以用户的账号命名的
- `/root `**重点**
该目录为系统管理员，也称为超级权限者的用户主目录
- `/lib `
系统开机所需要最基本的动态连接共享库，其作用类似于 Windows 里的DLL文件。几乎所有的应用程序都需要用到这些共享库
- `/lost+found`
这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件
- `/etc` **重点**
所有的系统管理所需要的配置问津和子目录 my.conf
- `/usr` **重点**
这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于 windows 下的 program files 目录
- `/boot` **重点**
存放的是启动 Linux 时使用的一些核心文件，包括一些连接文件以及镜像文件
- `/proc`
这个目录是一个虚拟的目录，它是系统内存的映射，访问这个目录来获取系统信息
- `/srv`
service 的缩写，该目录存放一些服务启动之后需要提取的数据
- `/sys`
这是 Linux2.6 内核的一个很大变化，该目录下安装了 2.6内核中新出现的一个文件系统
- `/tmp`
这个目录是用来存放一些临时文件的
- `/dev`
类似于 windows 的设备管理器，把所有的硬件用文件的形式存储
- `/media` **重点**
Linux 系统会自动识别一些设备，例如U盘、光驱等等，当识别后，Linux 会把识别的设备挂载到这个目录下。
- `/mnt` **重点**
系统提供该目录是为了让用户临时挂载别的文件系统，我们可以将外部的存储挂载在 /mnt/ 上，然后进入该目录就可以查看里面的内容了
- `/opt`
这是给主机额外 `安装软件` 所摆放的目录
- `/usr/local` **重点**
这是另一个给主机安装软件所 `安装的目录`。一般是通过编译源码方式安装的程序
- `/var` **重点**
这个目录中存放着在不断扩充着的东西，习惯将经常被修改的目录放在这个目录下，包括各种日志文件。
### 真操实练
![ ](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cuNTJkb3V0dS5jbi9zdGF0aWMvdGVtcC9waWMvZGUzOTcwZTQ3YjhhNWYzNDIwOWJkNzlhNTFlOWFiNDcuanBn?x-oss-process=image/format,png#pic_center)


#### 一、vi 和 vim 
- ##### **基本介绍**
> 所有的 Linux 系统都会内建 ***vi 文本编辑器***
> vim 具有程序编辑的能力，可以看做 ***vi的增强版本***，可以主动的以字体颜色辨别语法的正确性，方便程序设计。代码补全、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用。

- ##### **三种模式**
	- 正常模式
使用 vim 打开文本就是直接进入正常模式状态下了。
	- 插入/编辑模式
可以输入内容，按  ***i/I***,***o/O***,***a/A***,***r/R*** 便可以进入编辑模式，常见就是按 i 即可。
	- 命令行模式
在这个模式当中，可以提供你相关指令，完成读取、存盘、替换、离开 vim、显示行号等动作。
![三大模式](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWNzLmltYWdlcy5hYy5jbi9pbWFnZS81ZWFkNmJjNmQwYzM2Lmh0bWw?x-oss-process=image/format,png)

- ##### **常用快捷键**
	- ***yy***
拷贝当前行，5yy 便是拷贝当前行向下5行
	- ***dd***
删除当前行，5dd 便是删除当前行向下5行
	- ***/关键字***
查找某个单词，例如 java：在命令行下输入 **/java** ，回车查找，输入 **n** 便是查找下一个
	- ***:set nu 和 :set nonu***
设置文件的行号，和取消文件的行号
	- ***G 和 gg***
**正常模式下** GG：到文档的最末行 gg：到文档的最首行
	- ***shift+g***
通过 **:set nu** 显示行号，再输入 10，最后输入 shift+g ，便可到达第20行

#### 二、开机&重启
- **shutdown**
`shutdown -h now`：表示立即关机
`shutdown -h 1`：表示1分钟后关机
`shutdown -r now`：立即重启
- **halt**
效果等价于关机
- **reboot**
重启系统
- **syn**
把内存的数据同步到磁盘

`注意事项：`
当我们关机或者重启时，都应该先执行一下sync指令，把内存的数据写入磁盘，防止数据丢失。
#### 三、用户管理
- **useradd**
可以通过 `useradd 用户名` 来创建一个新用户
用户创建成功后，会自动的创建和用户同名的家目录
也可以使用 `useradd  -d /home/cbuc 用户名` 来给新创建的用户指定家目录
- **passwd**
可以通过`passwd 用户名`来给用户指定或修改密码
- **userdel**
可以通过`userdel 用户名`来删除用户，此命令会保留家目录
可以通过`userdel -r 用户名`来删除用户及家目录
- **id**
可以通过`id 用户名`来查询相对用户的信息，当用户不存在时，会返回“无此用户”
- **su**
可以用过`su - 用户名` 来切换用户
在操作 Linux 时，如果当前用户的权限不够，可以通过 su - 命令，切换到高权限的用户，例如 root
***从权限高的用户切换权限低的用户，不需要输入密码，反之需要，当需要返回原来用户时，可以使用*** `exit`
#### 四、常用命令
- **pwd**
显示当前工作目录的绝对路径
- **ls**
`ls -a`：显示当前目录的所有文件和目录，包括隐藏的
`ls -i`：以列表的方式显示信息
- **cd**
切换到指定目录
- **mkdir**
创建目录（make directory）
`mkdir -p /home/cbuc1/cbuc2`：创建多级目录，就是说在 ***home*** 的目录下创建了 ***cbuc1*** ，接着在 ***cbuc1*** 的目录下又创建了 ***cbuc2***
- **rmdir**
删除的是空目录，如果该目录下有内容是无法删除的
可以使用`rm -rf 文件名`来删除非空目录或文件
- **touch**
创建单个空文件：`touch cbuc1.txt`
创建多个空文件：`touch cbuc1.txt cbuc2.txt ...`
- **cp**
拷贝文件到指定目录
***eg：*** 
将 home 目录下的 cbuc.txt 文件拷贝到 tmp 目录下的 test 目录
`cp /home/cbuc.txt /tmp/test/`
***常用选项*：**
 `-r` ：递归复制整个文件夹
 ***扩展：***
  `\cp`：强制覆盖原来的文件
- **rm**
移除（删除）文件或目录
***常用选项：***
`-r`：递归删除整个文件夹
`-f`：强制删除不提示
`-rf`：上面两者的结合
- **mv**
移动文件与目录 或者 重命名
***重命名***： `mv oldFileName newFileName`
***移动文件***：`mv  /home/cbuc.txt /tmp`
- **cat**
查看文件内容，以只读的方式打开
***常用选项：*** `-n`：显示行号
`cat -n /etc/profile | more[分页浏览]`
- **more**
该指令是一个基于 VI 编辑器的文本过滤器，***它以全屏幕的方式按页显示文本文件的内容***。more 指令中也内置了若干快捷键。

| 操作     | 功能说明                           |
| -------- | ---------------------------------- |
| 空格键   | 向下翻一页                         |
| 回车键   | 向下翻一行                         |
| q        | 立刻离开 more ，不再显示该文件内容 |
| ctrl + F | 向下滚动一屏                       |
| ctrl + B | 返回上一屏                         |
| =        | 输出当前的行号                     |
| :f       | 输出文件名和当前的行号             |
- **less**
该指令用来 ***分屏查看文件内容*** ，它的功能与 `more` 相似，但是比 `more` 更加强大，支持各种显示终端。`less` 指令在显示文件内容时，并不是一次将整个文件加载之后才显示，而是根据显示需要加载内容，对于 ***显示大型文件具有较高的效率***。

| 操作     | 功能说明                                          |
| -------- | ------------------------------------------------- |
| 空格键   | 向下翻一页                                        |
| 回车键   | 向下翻一行                                        |
| q        | 立刻离开 less，不再显示该文件内容                 |
| pagedown | 向下滚动一屏                                      |
| pageup   | 向上滚动一屏                                      |
| /字串    | 向下搜寻【字串】的功能：n：向下查找 ；N：向上查找 |
| ？字串   | 向上搜寻【字串】的功能：n：向下查找 ；N：向上查找 |
- **echo**
输出内容到控制台
`echo $PATH`：输出当前的环境变量
- **head**
用于显示文件的开头部分内容，默认情况下 ***head*** 指令显示文件的前10行内容
`head -n 5 /etc/profile`:查看文件头5行内容，【5】可以是任意行数
- **tail**
用于输出文件中尾部的内容，默认情况下 ***tail*** 显示文件的后10行内容
`tail 文件`：查看文件后10行内容
`tail -n 5 文件` ：查看文件后5行内容，【5】可以是任意行数
`tail -f 文件`：实时追踪该文档的所有更新
- **history**
用于查看已经执行过历史命令，也可以执行历史指令
`history 10`：显示最近使用过的10个指令
`！ 5`：执行历史编号为5的指令
#### 五、时间日期类
- **date**
显示当前日期
***常见用法：***
	- `date`：显示当前时间
	- `date+%Y`：显示当前年份
	- `date+%m`：显示当前月份
	- `date+%d`：显示当前是哪一天
	- `date "+%Y-%m-%d %H:%M:%S"`：显示年月日时分秒
- **cal**
查看日历指令
`cal`：显示当前日历
`cal 2020`：显示2020年日历
#### 六、搜索查找类
- **find**
将从指定目录下递归地遍历其各个子目录，将满足条件的文件或者目录显示在终端

| 选项  | 功能                             |
| ----- | -------------------------------- |
| -name | 按照指定的文件名查找模式查找文件 |
| -user | 查找属于指定用户名所有文件       |
| -size | 按照指定的文件大小查找文件       |
`find  /home  -name cbuc.txt`：在 /home 的目录下查找 cbuc.txt 文件
`find  /opt -user cbuc`：在 /opt 的目录下查找用户名为 cbuc 的文件
`find /home -size +20M`：在 /home 的目录下查找大于 20M 的文件
`find /home -size -20M`：在 /home 的目录下查找小于 20M 的文件
`find /home -size 20M`：在 /home 的目录下查找等于 20M 的文件

- **grep & |**
grep 过滤查找，管道符：“|” 表示将前一个命令的处理结果输出传递给后面的命令处理

| 选项 | 功能             |
| ---- | ---------------- |
| -n   | 显示匹配行及行号 |
| -i   | 忽略字母大小写   |
#### 七、压缩和解压类
- **gzip/gunzip**
`gzip 文件`：用于压缩文件，只能将文件压缩为*.gz文件
`gunzip XXX.gz`：用于解压文件
***说明***：使用gzip对文件压缩后，不会保留原来的文件
- **zip/unzip**
`zip 压缩内容`：压缩文件和目录的命令
`unzip XXX.zip`：解压缩文件
***常用选项：***
`-r`：递归压缩，即压缩目录
***例子：***
`zip -r cbuc.zip /home/`：将 /home 下的所有文件进行压缩成 cbuc.zip
`unzip -d /opt/tmp/ cbuc.zip`：将 cbuc.zip 解压到 /opt/tmp 目录下
- **tar**
打包指令，最后打包后的文件是 .tar.gz

| 选项 | 功能               |
| ---- | ------------------ |
| -c   | 产生 .tar 打包文件 |
| -v   | 显示详细信息       |
| -f   | 指定压缩后的文件名 |
| -z   | 打包同时压缩       |
| -x   | 解包 .tar文件      |
`tar -zcvf cbuc.tar.gz cbuc1`：将 cbuc1 目录压缩成 cbuc.tar.gz
`tar -zcvf cbuc.tar.gz cbuc1 cbuc2`：将 cbuc1 和 cbuc2 目录压缩成 cbuc.tar.gz
`tar -zxcf cbuc.tar.gz`：将 cbuc.tar.gz 解压缩在当前目录下
`tar -zxvf cbuc.tar.gz /opt/tmp`：将 cbuc.tar.gz 解压缩在 /opt/tmp 目录下
***注意：*** 解压缩到的那个目录要事先存在，不然会报错
#### 八、进程管理
- **ps**
用来查看进行中的进程
`ps -a`：显示当前终端的所有进程信息
`ps -u`：以用户的格式显示进程信息
`ps -x`：显示后台进程运行的参数
***ps 显示的信息选项***

| 字段  | 说明                               |
| ----- | ---------------------------------- |
| USER  | 用户名称                           |
| PID   | 进程识别号                         |
| %CPU  | 进程占用 CPU 的百分比              |
| %MEM  | 进程占用物理内存的百分比           |
| VSZ   | 进程占用的虚拟内存大小（单位：KB） |
| RSS   | 进程占用的物理内存大小（单位：KB） |
| TTY   | 终端机号                           |
| STAT  | 进程状态                           |
| START | 进程的启动时间                     |
| TIME  | 此进程所消耗的 CPU 时间            |
| CMD   | 启动进程所用的命令和参数           |

`ps -ef`：以全格式显示当前所有的进程
`-e`：显示所有进程
`-f`：全格式
常用：`ps -ef | grep XXX`来查找某个进程
- **kill**
若是某个进程执行一八需要停止时，或是已消耗了很大的系统资源时，此时可以考虑停止该线程。使用 kill 命令来完成此项任务。
`kill 进程号`：通过进程号来杀死进程
`kill 进程名称`：通过进程名称杀死进程，也***支持通配符***，这是系统因负载过大而变得很慢时很有用
`kill  -9  xxx`：表示强迫进程立即停止
#### 九、服务管理
- **service**
`service  服务名  [start | stop | restart | reload | status]`
***注：*** 在CentOS 7.0之后，不再使用 service，而是 systemctl
`service iptables status`：查看防火墙状态
`service iptables stop`：停止防火墙
`service iptables start`：开启防火墙
#### 十、动态监控进程
- **top**
top 与 ps 命令很相似，它们都用来显示正在执行的进程。top 与 ps 最大的不同之处在于 top 在执行一段时间可以更新正在运行的进程。
***选项说明***

| 选项    | 功能                                                         |
| ------- | ------------------------------------------------------------ |
| -d 秒数 | 指定top命令每隔几秒更新，默认是3秒在top命令的交互模式当中可以执行的命令 |
| -i      | 使 top 不显示任何闲置或者僵死的进程                          |
| -p      | 通过指定监控进程 ID 来监控某个进程的状态                     |
***例子***
`监视特点用户`
1. 输入 **top** 回车，查看执行的进程
2. 输入 **u** 回车，再**输入用户名**

`终止指定的进程`
1. 输入 **top** 回车，查看执行的进程
2. 输入 **k**  回车，再 **输入要结束进程的 ID 号**

`指定系统状态更新的时间（每隔10秒自动更新）`
1. 输入**top -d 10**

- **netstat**
查看系统网络情况
`netstat -an `：按一定顺序排列输出
`netstat -p`：显哪个进程在调用
`netstat -anp | more`：查看所有的网络服务
`netstat -anp | grep XXX`：查看对应服务的信息

![看完不赞，都是坏蛋](https://mmbiz.qlogo.cn/mmbiz_png/P7WuIzkp9iaVqg4wKjOlo2cia6ibCCRkwD1Rv4C3icjAnDGgz8dzqpBpjDKfE6OGiaCWI6jBR0XOfcMsJjIibRZBPicXw/0?wx_fmt=png)

>  今天的你多努力一点，明天的你就能少说一句求人的话！
>  `很久很久之前，有个传说，据说：`
>  **看完不赞，都是坏蛋**