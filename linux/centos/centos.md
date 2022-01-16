# 软件安装

## jdk

**安装JDK**

通过安装包安装 [ https://blog.csdn.net/jx_lihuifu/article/details/80761038](https://blog.csdn.net/jx_lihuifu/article/details/80761038)

1、从官网上下载linux版本的JDK并上传到Linux

2、新建一个java文件夹 mkdir /usr/local/java用于解压 tar -zxvf 

3、配置环境变量 

`vim /etc/profile`

```bash
# 在文件末尾追加
export JAVA_HOME=/usr/local/java/jdk1.8.0_301
export JRE_HOME=/usr/local/java/jdk1.8.0_301/jre
export CLASSPATH=.:$JAVA_HOME/lib$:JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin/$JAVA_HOME:$PATH
```

` source /etc/profile` 重新加载环境变量

**卸载JDK**

1、先输入java -version 查看是否安装了jdk

2、如果安装了，检查下安装的路径 which java（查看JDK的安装路径） 

![img](assets/1151633-20190129104204104-278118100.png)

3、卸载 rm -rf JDK地址（卸载JDK） rm -rf /usr/java/jdk/jdk1.8.0_172/

4、vim命令编辑文件profile vim /etc/profile

![img](assets/1151633-20190129104319055-116669734.png)

删除配置的环境变量，至此JDK卸载完毕

5、检查下自带的jdk

命令：

```
rpm -qa |grep java
rpm -qa |grep jdk
rpm -qa |grep gcj
```

如果没有输入信息表示没有安装。

如果安装可以使用rpm -qa | grep java | xargs rpm -e --nodeps 批量卸载所有带有Java的文件 这句命令的关键字是java

## maven

# 常用软件使用

## vim编辑器

在所有的 Linux distributions 上都会有的一套文书编辑器就是 vi ，而且很多软件默认也是使用 vi 做为他们编辑的接口

### vi与vim

- 所有的 Unix Like 系统都会内建 vi 文书编辑器

- 很多软件的编辑接口都会主动调用 vi (例如未来会谈到的 crontab, visudo, edquota 等指令)

- vim 具有程序编辑的能力，可以辨别语法的正确性(shell script或基础配置文件)

### vi的使用

**一般指令模式 (command mode)**

以 vi 打开一个文件就直接进入一般指令模式。

```
使用**上k下j左h右l**按键来移动光标，使用『删除字符』来处理文件内容， 使用『复制、粘贴』处理文件数据。
```

**编辑模式 (insert mode)**

按下『i, I, o, O, a, A, r, R』等任何一个字母之后进入编辑模式。

```
按下这些按键时，在画面的左下方会出现『 INSERT 或 REPLACE 』 的字样，此时才可以进行编辑。按下『Esc』这个按键即可退出编辑模式,进入一般指令模式.
```

**指令行模式 (command-line mode)**

在一般模式当中，输入 **: / ?** 三个中的任何一个按钮，就可以将光标移动到最底下那一列。

```
可以提供『搜索』的动作，而读取、存盘、大量取代字符、离开 vi 、显示行号等等的动作则是在此模式中实现的
```

**三种模式的转换关系**

<img src="assets/clip_image002.jpg" alt="img" style="zoom:67%;" />

**简易执行**

- 使用『 vi filename 』进入一般指令模式

![img](assets/clip_image004.jpg)

![img](assets/clip_image006.jpg)

- 按下 i 进入编辑模式，进入编辑模式

![img](assets/clip_image008.jpg)

- 按下 [ESC] 按钮回到一般指令模式，INSERT消失

- 进入指令列模式， 文件储存并离开 vi 环境

![img](assets/clip_image010.jpg)

### 按键说明

https://www.cnblogs.com/yangjig/p/6014198.html

![http://img.blog.csdn.net/20160712110935064?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center](assets/clip_image012.jpg) 

![https://images2015.cnblogs.com/blog/175824/201611/175824-20161123224659425-328736487.png](assets/clip_image014.gif)

 **一般指令模式可用的按钮**

a.     移动

| 按键                                   | 功能                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| 多次移动: "30j" 或 "30↓ "向下移动 30行 |                                                              |
| [Ctrl] + [f]                           | 屏幕『向下』移动一页，相当于 [Page Down]按键 (常用)          |
| [Ctrl] + [b]                           | 屏幕『向上』移动一页，相当于 [Page Up] 按键 (常用)           |
| [Ctrl] + [d]                           | 屏幕『向下』移动半页                                         |
| [Ctrl] + [u]                           | 屏幕『向上』移动半页                                         |
| +                                      | 下一行行首                                                   |
| -                                      | 上一行行首                                                   |
| n<space>                               | 那个 n 表示『数字』，如 20<space> 则光标会向后面移动 20 个字符距离。 |
| 0 或功能键[Home]                       | 这是数字『 0 』：移动到这一行的最前面字符处 (常用)           |
| $ 或功能键[End]                        | 移动到这一行的最后面字符处(常用)                             |
| H                                      | 光标移动到这个屏幕的最上方那一行的第一个字符                 |
| M                                      | 光标移动到这个屏幕的中央那一行的第一个字符                   |
| L                                      | 光标移动到这个屏幕的最下方那一行的第一个字符                 |
| G                                      | 移动到这个文件的最后一行(常用)                               |
| nG                                     | n 为数字。移动到这个文件的第 n 行。                          |
| gg                                     | 移动到这个文件的第一行，相当于 1G 啊！ (常用)                |
| n<Enter>                               | n 为数字。光标向下移动 n 行(常用)                            |

b.     搜索与替换

| 按键                  | 功能                                                         |
| --------------------- | ------------------------------------------------------------ |
| /word                 | 向光标之下寻找一个名称为 word 的字符 (常用)                  |
| ?word                 | 向光标之上寻找一个字符串名称为 word 的字符串。               |
| n                     | 这个 n 是英文按键。代表『重复前一个搜寻的动作』              |
| N                     | 这个 N 是英文按键。与 n 刚好相反，为『反向』进行前一个搜寻动作。 |
| :n1,n2s/word1/word2/g | 在第 n1 与 n2 行之间寻找 word1 这个字符串，并将该字符串取代为 word2   举例来说，在 100 到 200 行之间搜寻 vbird 并取代为 VBIRD 则：『:100,200s/vbird/VBIRD/g』。 (常用) |
| :1,$s/word1/word2/g   | 从第一行到最后一行寻找 word1 字符串，并将该字符串取代为 word2 ！ (常用) |
| :1,$s/word1/word2/gc  | 从第一行到最后一行寻找 word1 字符串，并将该字符串取代为 word2 ！且在取代前显示提示字符给用户确认 (confirm) 是否需要取代！ (常用) |

c.  删除,复制与粘贴

| 按键     | 功能                                                         |
| -------- | ------------------------------------------------------------ |
| x, X     | x 为向后删除一个字符 ( [del] 按键)， X 为向前删除一个字符(退格键) (常用) |
| nx       | n 为数字，连续向后删除 n 个字符。连续删除 10 个字符: 『10x』。 |
| dd       | 剪切游标所在的那一整列(常用)                                 |
| ndd      | n 为数字。删除光标所在的向下 n 列(常用)                      |
| d1G      | 剪切光标所在到第一列的所有数据                               |
| dG       | 剪切光标所在到最后一列的所有数据                             |
| d$       | 剪切游标所在处，到该列的最后一个字符                         |
| d0       | 剪切游标所在处，到该列的最前面一个字符                       |
| y        | 复制选中区域                                                 |
| yy       | 复制游标所在的那一列(常用)                                   |
| nyy      | n 为数字。复制光标所在的向下 n 列，例如 20yy 则是复制 20 列(常用) |
| y1G      | 复制光标所在列到第一列的所有数据                             |
| yG       | 复制光标所在列到最后一列的所有数据                           |
| y0       | 复制光标所在的那个字符到该列行首的所有数据                   |
| y$       | 复制光标所在的那个字符到该列行尾的所有数据                   |
| p, P     | p 为将已复制的数据在光标下一列贴上， P 则为贴在游标上一列！  |
| J        | 将光标所在列与下一列的数据结合成同一列,中间由空格隔开        |
| ncj      | 重复删除多个数据，例如向下删除 10 列， [ 10cj ]              |
| u        | 复原前一个动作。 (常用)                                      |
| [Ctrl]+r | 重做上一个动作。 (常用)                                      |
| .        | 重复前一个动作 (常用)                                        |

 **一般指令模式切换到编辑模式**

| 按键 | 功能                                                         |
| ---- | ------------------------------------------------------------ |
| i, I | 进入插入模式(Insert  mode)：   i 为『从目前光标所在处插入』， I 为『在目前所在列的第一个非空格符处开始插入』。(常用) |
| a, A | 进入插入模式(Insert  mode)：   a 为『从目前光标所在的下一个字符处开始插入』， A 为『从光标所在列的最后一个字符处开始插入』。 (常用) |
| o, O | 进入插入模式(Insert  mode)：   o 为『在目前光标所在的下一列处插入新的一列』； O 为在目前光标所在处的上一列插入新的一列！ (常用) |
| r, R | 进入取代模式(Replace  mode)：   r 只会取代光标所在的那一个字符一次； R 会一直取代光标所在的文字，直到按下 ESC为止； (常用) |

 **一般指令模式切换到指令列模式**

a.    指令列模式的储存、离开

| 按键                                                        | 功能                                                         |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| :w                                                          | 将编辑的数据写入硬盘文件中(常用)                             |
| :w!                                                         | 若文件属性为『只读』时，强制写入该文件。  到底能不能写入，还是跟你对该文件的文件权限有关啊！ |
| :q                                                          | 离开 vi (常用)                                               |
| :q!                                                         | 若曾修改过文件，又不想储存，使用 ! 为强制离开不储存文件。    |
| 注意一下啊，惊叹号 (!) 在 vi 当中，常常具有『强制』的意思～ |                                                              |
| :wq                                                         | 储存后离开，若为 :wq! 则为强制储存后离开 (常用)              |
| ZZ                                                          | 若文件没有更动，则不储存离开，若文件已经被更动过，则储存后离开！ |
| :w [filename]                                               | 将编辑的数据储存成另一个文件（类似另存新档）                 |
| :r  [filename]                                              | 在编辑的数据中，读入另一个文件的数据。亦即将 『filename』 这个文件内容加到游标所在列后面 |
| :n1,n2 w [filename]                                         | 将 n1 到 n2 的内容储存成 filename 这个文件。                 |
| :!  command                                                 | 暂时跳出vim 到指令列模式下执行 command 的显示结果！  例如『:! ls /home』即可在 vi 当中察看 /home 底下以 ls 输出的文件信息！ |

b.    vim环境变更

| :set nu   | 显示行号，设定之后，会在每一列的前缀显示该列的行号 |
| --------- | -------------------------------------------------- |
| :set nonu | 与 set nu 相反，为取消行号                         |

### vim 的暂存档、救援回复与开启时的警告讯息

**暂存档**

vim具有恢复功能,该功能是通过”暂存档”实现的

使用 vim 编辑时， vim 会在与被编辑的文件的目录下， 再建立一个名为 .filename.swp 的文件。

若文件正常被保存.filename.swp会被自动删除

若文件在编辑时被意外中断,.filename.swp就会被保存下来,用于恢复

![img](assets/clip_image016.jpg)

在 vim 的一般指令模式下按下 [ctrl]-z 的组合按键时，vim 会在后台执行

**查看暂存档**

![img](assets/clip_image018.jpg)

 暂存档为隐藏文件夹,需要ls –a才能显示

 

模拟vim异常中断

![img](assets/clip_image020.jpg)

​     杀死vim进程,模拟vim异常中断,filename.swp仍然存在

 

再次编辑文件

 ![img](assets/clip_image022.jpg)

​     暂存档存在的两种可能

​          可能有其他人在编辑该文件

​          文件在编辑时,异常中断

​     6种对暂存档的操作

[O]pen Read-Only： 打开此文件成为只读档，

(E)dit anyway：用正常的方式打开要文件， 并不会载入暂存盘的内容。容易出现两个使用者互相改变文件等问题！！

(R)ecover：就是加载暂存盘的内容，救回之前未储存的工作。 之后还是要手动自行删除那个暂存档喔！

(D)elete it：开启文件前会先将这个暂存盘删除！

(Q)uit：按下 q 就离开 vim 

(A)bort：忽略这个编辑行为，感觉上与 quit 非常类似！ 也会送你回到命令提示字符！

### 区块选择(Visual Block)

| v        | 字符选择，会将光标经过的地方反白选择！ |
| -------- | -------------------------------------- |
| V        | 列选择，会将光标经过的列反白选择！     |
| [Ctrl]+v | 列编辑，可以用长方形的方式选择资料     |
| y        | 将反白的地方复制起来                   |
| d        | 将反白的地方删除掉                     |
| p        | 将刚刚复制的区块，在游标所在处贴上！   |

**示例**

vim hosts

v字符选择

<img src="assets/clip_image028.jpg" alt="img" style="zoom:67%;" />

V行选择

<img src="assets/clip_image030.jpg" alt="img" style="zoom:67%;" />

[ctrl]+v区块选择

![img](assets/clip_image032.jpg)

- 删除列

```
1.光标定位到要操作的地方。
2.CTRL+v 进入“可视 块”模式，选取这一列操作多少行。
3.d 删除。
```

- 插入列，例如我们在每一行前都插入"() "：

```
1.光标定位到要操作的地方。
2.CTRL+v 进入“可视 块”模式，选取这一列操作多少行。
3.SHIFT+i(I) 输入要插入的内容。
4.ESC 按两次，会在每行的选定的区域出现插入的内容。
```

###  多文件编辑

| :n     | 编辑下一个文件                    |
| ------ | --------------------------------- |
| :N     | 编辑上一个文件                    |
| :files | 列出目前这个 vim 的开启的所有文件 |

vim hosts /etc/hosts开启两个文件

:files查看多文件信息

![img](assets/clip_image034.jpg)

在多文件之间实现复制与粘贴

![img](assets/clip_image036.jpg)

### 多窗口功能

两个特殊的使用场景

- 当我有一个文件非常的大，我查阅到后面的数据时，想要『对照』前面的数据， 是否需要使用 [ctrl]+f 与[ctrl]+b (或 pageup, pagedown 功能键) 来跑前跑后查阅？

- 我有两个需要对照着看的文件，不想使用前一小节提到的多文件编辑功能；

“分区窗口”或”冻结窗口”将一个文件分区成多个窗口展现

| 按键                     | 功能                                                         |
| ------------------------ | ------------------------------------------------------------ |
| :sp [filename]           | 开启一个新窗口，如果有加 filename， 表示在新窗口开启一个新文件 |
| [ctrl]+w+ j   [ctrl]+w+↓ | 按键的按法是：先按下 [ctrl] 不放， 再按下 w 后放开所有的按键，然后再按下 j (或向下箭头键)，则光标可移动到下方的窗口。 |
| [ctrl]+w+ k   [ctrl]+w+↑ | 同上，不过光标移动到上面的窗口。                             |
| [ctrl]+w+ q              | 退出当前窗口                                                 |

sp:水平分屏

![img](assets/clip_image038.jpg)

​     多个窗口之间也能复制粘贴

vsp:垂直分屏

 ![img](assets/clip_image040.jpg)

### vim 的挑字补全功能

不同人的说法不同,我的编辑器也不同

<c-n/p>-文件内容关键字

<c-x><c-n>-当前缓冲区

<c-x><c-f>-包含文件关键字

<c-x><c-]>-标签文件关键字

<c-x><c-k>-字典查找

文件内容补齐

![img](assets/clip_image042.jpg)

### vim 环境设定与记录： ~/.vimrc, ~/.viminfo

vim会主动将用户的行为记录在~/viminfo中

```
1.以 vim 软件来搜寻文件的某个字符串时，这个字符串会被反白；下次我们再次以 vim 编辑这个文件时，该搜寻的字符串反白情况还是存在；在编辑其他文件时， 如果其他文件内也存在这个字符串，还是主动反白
2.当我们重复编辑同一个文件时，当第二次进入该文件时， 光标竟然就在上次离开的那一列上
```

vim的环境设定值放在/etc/vimrc中

| :set nu   :set nonu                  | 设定与取消行号啊！                                           |
| ------------------------------------ | ------------------------------------------------------------ |
| :set  hlsearch   :set nohlsearch     | hlsearch 就是 high light search(高亮度搜寻)。 设定是否将搜寻的字符串反白的设定值。默认值是 hlsearch |
| :set  autoindent   :set noautoindent | 是否自动缩排？ autoindent 就是自动缩排。                     |
| :set backup                          | 是否自动储存备份档？一般是 nobackup 的， 如果设定 backup 的话，更动任何一个文件时，则源文件会被另存成一个档名为 filename~ 的文件。 |
| :set ruler                           | ruler 设置在显示或不显示右下角状态栏                         |
| :set  showmode                       | 是否要显示 --INSERT-- 之类的字眼在左下角的状态栏。           |
| :set  backspace=(012)                | 一般来说， 如果我们按下 i 进入编辑模式后，可以利用退格键 (backspace) 来删除任意字符的。   某些 distribution 则不许如此。 当 backspace 为 2 时，可以删除任意值； 0 或 1 时，仅可删除刚刚输入的字符， 而无法删除原本就已经存在的文字了！ |
| :set all                             | 显示目前所有的环境参数设定值。                               |
| :set                                 | 显示与系统默认值不同的设定参数， 就是你有自行变动过的设定参数啦！ |
| :syntax on   :syntax off             | 是否依据程序相关语法显示不同颜色？                           |
| :set  bg=dark   :set bg=light        | 可用以显示不同的颜色色调，预设是『 light 』。                |

  /etc/vimrc不建议修改,但可以修改~/.vimrc文件,会覆盖默认的环境设定

![img](assets/clip_image044.jpg)

### vim常用指令图

![img](assets/clip_image046.jpg)

### vim 个性化设置

查看vi/vim

使用 vi 后，却看到画面的右下角有显示目前光标所在的行列号码，那么 vi 已经被 vim 所取代

alias:显示简写命令的实际命令(不存在vi=’vim’,仍要使用vim)

![img](assets/clip_image024.jpg)

 

vim /etc/services

![img](assets/clip_image026.jpg)

​     /etc/services 是配置文件，因此 vim 会进行语法检验，深蓝色那一列是以批注符号 (#) 为开头；

### 其他 vim 使用注意事项

**中文编码问题**

文件编码显示的影响因素

Linux 系统默认支持的语系数据：这与 /etc/locale.conf 有关；

终端界面 (bash) 的语系： 这与 LANG, LC_ALL 这几个变数有关；

文件原本的编码；

开启终端机的软件，例如在 GNOME 底下的窗口接口。

若文本原编码与开启终端机软件的编码一致,文件就能以正确的编码显示

​     文件原编码难以修改,修改终端机软件编码更简单

​          LANG=…

​          export LC_ALL=…

**DOS 与 Linux 的断行字符**

DOS的换行符是CR+LF

Linux的换行符是LF

DOS下编辑的文件一般不能直接在Linux下运行,尤其是shell script.需要将文件格式转为Linux格式

安装文件转换软件

![img](assets/clip_image048.jpg)

命令

​     dos2unix [-kn] file [newfile]

​     unix2dos [-kn] file [newfile]

-k ：保留该文件原本的 mtime 时间格式

-n ：保留原本的旧档，将转换后的内容输出到新文件

 **语系编码转换**

`iconv -f 原本编码 -t 新编码 filename [-o newfile]`

--list ：列出 iconv 支持的语系数据

-f ： from ，亦即来源之意，后接原本的编码格式；

-t ： to ，亦即后来的新编码要是什么格式；

-o file：如果要保留原本的文件，那么使用 -o 新档名，可以建立新编码文件。

![img](assets/clip_image050.jpg)

查看文件编码

​     file 文件名

在vim中使用:fileencoding

## shell

### shell简介

**什么是 shell**

- Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。
- Shell 既是一种命令语言，又是一种程序设计语言。
- Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问 Linux 内核的服务。

Ken Thompson 的 sh 是第一种 Unix Shell，Windows Explorer 是一个典型的图形界面 Shell。

**什么是 shell 脚本**

Shell 脚本（shell script），是一种为 shell 编写的脚本程序，一般文件后缀为 `.sh`。

业界所说的 shell 通常都是指 shell 脚本，但 shell 和 shell script 是两个不同的概念。

**Shell 环境**

Shell 编程跟 java、php 编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。

<font color='red'>Shell 的解释器</font>种类众多，常见的有：

- [sh](https://link.juejin.cn?target=https%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fbash%2F) - 即 Bourne Shell。sh 是 Unix 标准默认的 shell。
- [bash](https://link.juejin.cn?target=https%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fbash%2F) - 即 Bourne Again Shell。bash 是 Linux 标准默认的 shell。
- [fish](https://link.juejin.cn?target=https%3A%2F%2Ffishshell.com%2F) - 智能和用户友好的命令行 shell。
- [xiki](https://link.juejin.cn?target=http%3A%2F%2Fxiki.org%2F) - 使 shell 控制台更友好，更强大。
- [zsh](https://link.juejin.cn?target=http%3A%2F%2Fwww.zsh.org%2F) - 功能强大的 shell 与脚本语言。

**指定脚本解释器**

在 shell 脚本，`#!` 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 解释器。`#!` 被称作[shebang（也称为 Hashbang ）](https://link.juejin.cn?target=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FShebang)。

所以，你应该会在 shell 中，见到诸如以下的注释：

- 指定 sh 解释器

```
#!/bin/sh
```

- 指定 bash 解释器

```
#!/bin/bash
```

> **注意**
>
> 上面的指定解释器的方式是比较常见的，但有时候，你可能也会看到下面的方式：
>
> ```
> #!/usr/bin/env bash
> ```
>
> 这样做的好处是，系统会自动在 `PATH` 环境变量中查找你指定的程序（本例中的`bash`）。相比第一种写法，你应该尽量用这种写法，因为程序的路径是不确定的。这样写还有一个好处，操作系统的`PATH`变量有可能被配置为指向程序的另一个版本。比如，安装完新版本的`bash`，我们可能将其路径添加到`PATH`中，来“隐藏”老版本。如果直接用`#!/bin/bash`，那么系统会选择老版本的`bash`来执行脚本，如果用`#!/usr/bin/env bash`，则会使用新版本。

**模式**

shell 有交互和非交互两种模式。

1. 交互模式

> 简单来说，你可以将 shell 的交互模式理解为执行命令行。

看到形如下面的东西，说明 shell 处于交互模式下：

```
user@host:~$
```

接着，便可以输入一系列 Linux 命令，比如 `ls`，`grep`，`cd`，`mkdir`，`rm` 等等。

2. 非交互模式

> 简单来说，你可以将 shell 的非交互模式理解为执行 shell 脚本。

在非交互模式下，shell 从文件或者管道中读取命令并执行。

当 shell 解释器执行完文件中的最后一个命令，shell 进程终止，并回到父进程。

可以使用下面的命令让 shell 以非交互模式运行：

```
sh /path/to/script.sh
bash /path/to/script.sh
source /path/to/script.sh
./path/to/script.sh
```

`script.sh`是一个包含 shell 解释器可以识别并执行的命令的普通文本文件，`sh`和`bash`是 shell 解释器程序。

还可以通过`chmod`命令给文件添加可执行的权限，来直接执行脚本文件：

```
chmod +x /path/to/script.sh #使脚本具有执行权限
/path/to/test.sh
```

这种方式要求脚本文件的第一行必须指明运行该脚本的程序，比如：

**:keyboard: 『示例源码』** [helloworld.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fhelloworld.sh)

```
#!/usr/bin/env bash
echo "Hello, world!"
```

## 2. 基本语法

### 2.1. 解释器

前面虽然两次提到了`#!` ，但是本着重要的事情说三遍的精神，这里再强调一遍：

在 shell 脚本，`#!` 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 解释器。`#!` 被称作[shebang（也称为 Hashbang ）](https://link.juejin.cn?target=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FShebang)。

`#!` 决定了脚本可以像一个独立的可执行文件一样执行，而不用在终端之前输入`sh`, `bash`, `python`, `php`等。

```
# 以下两种方式都可以指定 shell 解释器为 bash，第二种方式更好
#!/bin/bash
#!/usr/bin/env bash
复制代码
```

### 2.2. 注释

注释可以说明你的代码是什么作用，以及为什么这样写。

shell 语法中，注释是特殊的语句，会被 shell 解释器忽略。

- 单行注释 - 以 `#` 开头，到行尾结束。
- 多行注释 - 以 `:<<EOF` 开头，到 `EOF` 结束。

**:keyboard: 『示例源码』** [comment-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fcomment-demo.sh)

```
#--------------------------------------------
# shell 注释示例
# author：zp
#--------------------------------------------

# echo '这是单行注释'

########## 这是分割线 ##########

:<<EOF
echo '这是多行注释'
echo '这是多行注释'
echo '这是多行注释'
EOF
复制代码
```

### 2.3. echo

echo 用于字符串的输出。

输出普通字符串：

```
echo "hello, world"
# Output: hello, world
复制代码
```

输出含变量的字符串：

```
echo "hello, \"zp\""
# Output: hello, "zp"
复制代码
```

输出含变量的字符串：

```
name=zp
echo "hello, \"${name}\""
# Output: hello, "zp"
复制代码
```

输出含换行符的字符串：

```
# 输出含换行符的字符串
echo "YES\nNO"
#  Output: YES\nNO

echo -e "YES\nNO" # -e 开启转义
#  Output:
#  YES
#  NO
复制代码
```

输出含不换行符的字符串：

```
echo "YES"
echo "NO"
#  Output:
#  YES
#  NO

echo -e "YES\c" # -e 开启转义 \c 不换行
echo "NO"
#  Output:
#  YESNO
复制代码
```

输出重定向至文件

```
echo "test" > test.txt
复制代码
```

输出执行结果

```
echo `pwd`
#  Output:(当前目录路径)
复制代码
```

**:keyboard: 『示例源码』** [echo-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fecho-demo.sh)

### 2.4. printf

printf 用于格式化输出字符串。

默认，printf 不会像 echo 一样自动添加换行符，如果需要换行可以手动添加 `\n`。

**:keyboard: 『示例源码』** [printf-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fprintf-demo.sh)

```
# 单引号
printf '%d %s\n' 1 "abc"
#  Output:1 abc

# 双引号
printf "%d %s\n" 1 "abc"
#  Output:1 abc

# 无引号
printf %s abcdef
#  Output: abcdef(并不会换行)

# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出
printf "%s\n" abc def
#  Output:
#  abc
#  def

printf "%s %s %s\n" a b c d e f g h i j
#  Output:
#  a b c
#  d e f
#  g h i
#  j

# 如果没有参数，那么 %s 用 NULL 代替，%d 用 0 代替
printf "%s and %d \n"
#  Output:
#   and 0

# 格式化输出
printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876
#  Output:
#  姓名     性别   体重kg
#  郭靖     男      66.12
#  杨过     男      48.65
#  郭芙     女      47.99
复制代码
```

#### printf 的转义符

| 序列    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| `\a`    | 警告字符，通常为 ASCII 的 BEL 字符                           |
| `\b`    | 后退                                                         |
| `\c`    | 抑制（不显示）输出结果中任何结尾的换行字符（只在%b 格式指示符控制下的参数字符串中有效），而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略 |
| `\f`    | 换页（formfeed）                                             |
| `\n`    | 换行                                                         |
| `\r`    | 回车（Carriage return）                                      |
| `\t`    | 水平制表符                                                   |
| `\v`    | 垂直制表符                                                   |
| `\\`    | 一个字面上的反斜杠字符                                       |
| `\ddd`  | 表示 1 到 3 位数八进制值的字符。仅在格式字符串中有效         |
| `\0ddd` | 表示 1 到 3 位的八进制值字符                                 |

## 3. 变量

跟许多程序设计语言一样，你可以在 bash 中创建变量。

Bash 中没有数据类型，bash 中的变量可以保存一个数字、一个字符、一个字符串等等。同时无需提前声明变量，给变量赋值会直接创建变量。

### 3.1. 变量命名原则

- 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- 中间不能有空格，可以使用下划线（_）。
- 不能使用标点符号。
- 不能使用 bash 里的关键字（可用 help 命令查看保留关键字）。

与java变量名规则相似,只是bash变量名不能使用$

### 3.2. 声明变量

访问变量的语法形式为：`${var}` 和 `$var` 。

变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界，所以推荐加花括号。

```
word="hello"
echo ${word}
# Output: hello
复制代码
```

### 3.3. 只读变量

使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。

```
rword="hello"
echo ${rword}
readonly rword
# rword="bye"  # 如果放开注释，执行时会报错
复制代码
```

### 3.4. 删除变量

使用 unset 命令可以删除变量。变量被删除后不能再次使用。<font color='cornflowerblue'>unset 命令不能删除只读变量</font>。

```
dword="hello"  # 声明变量
echo ${dword}  # 输出变量值
# Output: hello

unset dword    # 删除变量
echo ${dword}
# Output: （空）
复制代码
```

### 3.5. 变量类型

- **局部变量** - 局部变量是仅在某个脚本内部有效的变量。它们不能被其他的程序和脚本访问。
- **环境变量** - 环境变量是对当前 shell 会话内所有的程序或脚本都可见的变量。创建它们跟创建局部变量类似，但使用的是 `export` 关键字，shell 脚本也可以定义环境变量。

常见的环境变量：

| 变量      | 描述                                               |
| --------- | -------------------------------------------------- |
| `$HOME`   | 当前用户的用户目录                                 |
| `$PATH`   | 用分号分隔的目录列表，shell 会到这些目录中查找命令 |
| `$PWD`    | 当前工作目录                                       |
| `$RANDOM` | 0 到 32767 之间的整数                              |
| `$UID`    | 数值类型，当前用户的用户 ID                        |
| `$PS1`    | 主要系统输入提示符                                 |
| `$PS2`    | 次要系统输入提示符                                 |

参数相关变量:

| 变量      | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| $0        | 当前脚本的文件名。                                           |
| $n（n≥1） | 传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是 $1，第二个参数是 $2。 |
| $#        | 传递给脚本或函数的参数个数。                                 |
| $*        | 传递给脚本或函数的所有参数。                                 |
| $@        | 传递给脚本或函数的所有参数。当被双引号`" "`包含时，$@ 与 $* 稍有不同，我们将在《[Shell $*和$@的区别](http://c.biancheng.net/view/vip_4559.html)》一节中详细讲解。 |
| $?        | 上个命令的退出状态，或函数的返回值，我们将在《[Shell $?](http://c.biancheng.net/view/808.html)》一节中详细讲解。 |
| $$        | 当前 Shell 进程 ID。对于 Shell 脚本，就是这些脚本所在的进程 ID。 |

[这里](https://link.juejin.cn?target=http%3A%2F%2Ftldp.org%2FLDP%2FBash-Beginners-Guide%2Fhtml%2Fsect_03_02.html%23%23%23sect_03_02_04) 有一张更全面的 Bash 环境变量列表。

### 3.6. 变量示例源码

**⌨️ 『示例源码』** [variable-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fvariable-demo.sh)

## 4. 字符串

### 4.1. 单引号和双引号

shell 字符串可以用单引号 `''`，也可以用双引号 `“”`，也可以不用引号。

- 单引号的特点
  - 单引号里不识别变量
  - 单引号里不能出现单独的单引号（使用转义符也不行），但可成对出现(<font color='cornflowerblue'>可识别变量</font>)，作为字符串拼接使用。
- 双引号的特点
  - 双引号里识别变量
  - 双引号里可以出现转义字符

综上，推荐使用双引号。

### 4.2. 拼接字符串

```
# 使用单引号拼接
name1='white'
str1='hello, '${name1}''
str2='hello, ${name1}'
echo ${str1}_${str2}
# Output:
# hello, white_hello, ${name1}

# 使用双引号拼接
name2="black"
str3="hello, "${name2}""
str4="hello, ${name2}"
echo ${str3}_${str4}
# Output:
# hello, black_hello, black
复制代码
```

### 4.3. 获取字符串长度

```
text="12345"
echo ${#text}
# Output:
# 5
复制代码
```

### 4.4. 截取子字符串

```
text="12345"
echo ${text:2:2}
# Output:
# 34
复制代码
```

从第 3 个字符开始，截取 2 个字符

### 4.5. 查找子字符串

```
#!/usr/bin/env bash

text="hello"
echo `expr index "${text}" ll`

# Execute: ./str-demo5.sh
# Output:
# 3
复制代码
```

查找 `ll` 子字符在 `hello` 字符串中的起始位置。

### 4.6. 字符串示例源码

**⌨️ 『示例源码』** [string-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fstring-demo.sh)

## 5. 数组

bash 只支持一维数组。

数组下标从 0 开始，下标可以是整数或算术表达式，其值应大于或等于 0。

### 5.1. 创建数组

```
# 创建数组的不同方式
nums=([2]=2 [0]=0 [1]=1)
colors=(red yellow "dark blue")
复制代码
```

### 5.2. 访问数组元素

- **访问数组的单个元素：**

```
echo ${nums[1]}
# Output: 1
复制代码
```

- **访问数组的所有元素：**

```
echo ${colors[*]}
# Output: red yellow dark blue

echo ${colors[@]}
# Output: red yellow dark blue
复制代码
```

上面两行有很重要（也很微妙）的区别：

为了将数组中每个元素单独一行输出，我们用 `printf` 命令：

```
printf "+ %s\n" ${colors[*]}
# Output:
# + red
# + yellow
# + dark
# + blue
复制代码
```

为什么`dark`和`blue`各占了一行？尝试用引号包起来：

```
printf "+ %s\n" "${colors[*]}"
# Output:
# + red yellow dark blue
复制代码
```

现在所有的元素都在一行输出 —— 这不是我们想要的！让我们试试`${colors[@]}`

```
printf "+ %s\n" "${colors[@]}"
# Output:
# + red
# + yellow
# + dark blue
复制代码
```

在引号内，`${colors[@]}`将数组中的每个元素扩展为一个单独的参数；数组元素中的空格得以保留。

- **访问数组的部分元素：**

```
echo ${nums[@]:0:2}
# Output:
# 0 1
复制代码
```

在上面的例子中，`${array[@]}` 扩展为整个数组，`:0:2`取出了数组中从 0 开始，长度为 2 的元素。

### 5.3. 访问数组长度

```
echo ${#nums[*]}
# Output:
# 3
复制代码
```

### 5.4. 向数组中添加元素

向数组中添加元素也非常简单：

```
colors=(white "${colors[@]}" green black)
echo ${colors[@]}
# Output:
# white red yellow dark blue green black
复制代码
```

上面的例子中，`${colors[@]}` 扩展为整个数组，并被置换到复合赋值语句中，接着，对数组`colors`的赋值覆盖了它原来的值。

### 5.5. 从数组中删除元素

用`unset`命令来从数组中删除一个元素：

```
unset nums[0]
echo ${nums[@]}
# Output:
# 1 2
复制代码
```

### 5.6. 数组示例源码

**:keyboard: 『示例源码』** [array-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Farray-demo.sh)

## 6. 运算符

### 6.1. 算术运算符

下表列出了常用的算术运算符，假定变量 x 为 10，变量 y 为 20：

| 运算符 | 说明                                          | 举例                           |
| ------ | --------------------------------------------- | ------------------------------ |
| +      | 加法                                          | `expr $x + $y` 结果为 30。     |
| -      | 减法                                          | `expr $x - $y` 结果为 -10。    |
| *      | 乘法                                          | `expr $x * $y` 结果为 200。    |
| /      | 除法                                          | `expr $y / $x` 结果为 2。      |
| %      | 取余                                          | `expr $y % $x` 结果为 0。      |
| =      | 赋值                                          | `x=$y` 将把变量 y 的值赋给 x。 |
| ==     | 相等。用于比较两个数字，相同则返回 true。     | `[ $x == $y ]` 返回 false。    |
| !=     | 不相等。用于比较两个数字，不相同则返回 true。 | `[ $x != $y ]` 返回 true。     |

**注意：**条件表达式要放在方括号之间，并且要有空格，例如: `[$x==$y]` 是错误的，必须写成 `[ $x == $y ]`。

**:keyboard: 『示例源码』** [operator-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Foperator%2Foperator-demo.sh)

```
x=10
y=20

echo "x=${x}, y=${y}"

val=`expr ${x} + ${y}`
echo "${x} + ${y} = $val"

val=`expr ${x} - ${y}`
echo "${x} - ${y} = $val"

val=`expr ${x} \* ${y}`
echo "${x} * ${y} = $val"

val=`expr ${y} / ${x}`
echo "${y} / ${x} = $val"

val=`expr ${y} % ${x}`
echo "${y} % ${x} = $val"

if [[ ${x} == ${y} ]]
then
  echo "${x} = ${y}"
fi
if [[ ${x} != ${y} ]]
then
  echo "${x} != ${y}"
fi

#  Execute: ./operator-demo.sh
#  Output:
#  x=10, y=20
#  10 + 20 = 30
#  10 - 20 = -10
#  10 * 20 = 200
#  20 / 10 = 2
#  20 % 10 = 0
#  10 != 20
复制代码
```

### 6.2. 关系运算符

关系运算符只支持数字，不支持字符串，除非字符串的值是数字。

下表列出了常用的关系运算符，假定变量 x 为 10，变量 y 为 20：

| 运算符 | 说明                                                  | 举例                         |
| ------ | ----------------------------------------------------- | ---------------------------- |
| `-eq`  | 检测两个数是否相等，相等返回 true。                   | `[ $a -eq $b ]`返回 false。  |
| `-ne`  | 检测两个数是否相等，不相等返回 true。                 | `[ $a -ne $b ]` 返回 true。  |
| `-gt`  | 检测左边的数是否大于右边的，如果是，则返回 true。     | `[ $a -gt $b ]` 返回 false。 |
| `-lt`  | 检测左边的数是否小于右边的，如果是，则返回 true。     | `[ $a -lt $b ]` 返回 true。  |
| `-ge`  | 检测左边的数是否大于等于右边的，如果是，则返回 true。 | `[ $a -ge $b ]` 返回 false。 |
| `-le`  | 检测左边的数是否小于等于右边的，如果是，则返回 true。 | `[ $a -le $b ]`返回 true。   |

**:keyboard: 『示例源码』** [operator-demo2.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Foperator%2Foperator-demo2.sh)

```
x=10
y=20

echo "x=${x}, y=${y}"

if [[ ${x} -eq ${y} ]]; then
   echo "${x} -eq ${y} : x 等于 y"
else
   echo "${x} -eq ${y}: x 不等于 y"
fi

if [[ ${x} -ne ${y} ]]; then
   echo "${x} -ne ${y}: x 不等于 y"
else
   echo "${x} -ne ${y}: x 等于 y"
fi

if [[ ${x} -gt ${y} ]]; then
   echo "${x} -gt ${y}: x 大于 y"
else
   echo "${x} -gt ${y}: x 不大于 y"
fi

if [[ ${x} -lt ${y} ]]; then
   echo "${x} -lt ${y}: x 小于 y"
else
   echo "${x} -lt ${y}: x 不小于 y"
fi

if [[ ${x} -ge ${y} ]]; then
   echo "${x} -ge ${y}: x 大于或等于 y"
else
   echo "${x} -ge ${y}: x 小于 y"
fi

if [[ ${x} -le ${y} ]]; then
   echo "${x} -le ${y}: x 小于或等于 y"
else
   echo "${x} -le ${y}: x 大于 y"
fi

#  Execute: ./operator-demo2.sh
#  Output:
#  x=10, y=20
#  10 -eq 20: x 不等于 y
#  10 -ne 20: x 不等于 y
#  10 -gt 20: x 不大于 y
#  10 -lt 20: x 小于 y
#  10 -ge 20: x 小于 y
#  10 -le 20: x 小于或等于 y
复制代码
```

### 6.3. 布尔运算符

下表列出了常用的布尔运算符，假定变量 x 为 10，变量 y 为 20：

| 运算符 | 说明                                                | 举例                                       |
| ------ | --------------------------------------------------- | ------------------------------------------ |
| `!`    | 非运算，表达式为 true 则返回 false，否则返回 true。 | `[ ! false ]` 返回 true。                  |
| `-o`   | 或运算，有一个表达式为 true 则返回 true。           | `[ $a -lt 20 -o $b -gt 100 ]` 返回 true。  |
| `-a`   | 与运算，两个表达式都为 true 才返回 true。           | `[ $a -lt 20 -a $b -gt 100 ]` 返回 false。 |

**:keyboard: 『示例源码』** [operator-demo3.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Foperator%2Foperator-demo3.sh)

```
x=10
y=20

echo "x=${x}, y=${y}"

if [[ ${x} != ${y} ]]; then
   echo "${x} != ${y} : x 不等于 y"
else
   echo "${x} != ${y}: x 等于 y"
fi

if [[ ${x} -lt 100 && ${y} -gt 15 ]]; then
   echo "${x} 小于 100 且 ${y} 大于 15 : 返回 true"
else
   echo "${x} 小于 100 且 ${y} 大于 15 : 返回 false"
fi

if [[ ${x} -lt 100 || ${y} -gt 100 ]]; then
   echo "${x} 小于 100 或 ${y} 大于 100 : 返回 true"
else
   echo "${x} 小于 100 或 ${y} 大于 100 : 返回 false"
fi

if [[ ${x} -lt 5 || ${y} -gt 100 ]]; then
   echo "${x} 小于 5 或 ${y} 大于 100 : 返回 true"
else
   echo "${x} 小于 5 或 ${y} 大于 100 : 返回 false"
fi

#  Execute: ./operator-demo3.sh
#  Output:
#  x=10, y=20
#  10 != 20 : x 不等于 y
#  10 小于 100 且 20 大于 15 : 返回 true
#  10 小于 100 或 20 大于 100 : 返回 true
#  10 小于 5 或 20 大于 100 : 返回 false
复制代码
```

### 6.4. 逻辑运算符

以下介绍 Shell 的逻辑运算符，假定变量 x 为 10，变量 y 为 20:

| 运算符 | 说明       | 举例                                            |
| ------ | ---------- | ----------------------------------------------- |
| `&&`   | 逻辑的 AND | `[[ ${x} -lt 100 && ${y} -gt 100 ]]` 返回 false |
| `||`   | 逻辑的 OR  | `[[ ${x} -lt 100 || ${y} -gt 100 ]]` 返回 true  |

**:keyboard: 『示例源码』** [operator-demo4.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Foperator%2Foperator-demo4.sh)

```
x=10
y=20

echo "x=${x}, y=${y}"

if [[ ${x} -lt 100 && ${y} -gt 100 ]]
then
   echo "${x} -lt 100 && ${y} -gt 100 返回 true"
else
   echo "${x} -lt 100 && ${y} -gt 100 返回 false"
fi

if [[ ${x} -lt 100 || ${y} -gt 100 ]]
then
   echo "${x} -lt 100 || ${y} -gt 100 返回 true"
else
   echo "${x} -lt 100 || ${y} -gt 100 返回 false"
fi

#  Execute: ./operator-demo4.sh
#  Output:
#  x=10, y=20
#  10 -lt 100 && 20 -gt 100 返回 false
#  10 -lt 100 || 20 -gt 100 返回 true
复制代码
```

### 6.5. 字符串运算符

下表列出了常用的字符串运算符，假定变量 a 为 "abc"，变量 b 为 "efg"：

| 运算符 | 说明                                       | 举例                       |
| ------ | ------------------------------------------ | -------------------------- |
| `=`    | 检测两个字符串是否相等，相等返回 true。    | `[ $a = $b ]` 返回 false。 |
| `!=`   | 检测两个字符串是否相等，不相等返回 true。  | `[ $a != $b ]` 返回 true。 |
| `-z`   | 检测字符串长度是否为 0，为 0 返回 true。   | `[ -z $a ]` 返回 false。   |
| `-n`   | 检测字符串长度是否为 0，不为 0 返回 true。 | `[ -n $a ]` 返回 true。    |
| `str`  | 检测字符串是否为空，不为空返回 true。      | `[ $a ]` 返回 true。       |

**:keyboard: 『示例源码』** [operator-demo5.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Foperator%2Foperator-demo5.sh)

```
x="abc"
y="xyz"


echo "x=${x}, y=${y}"

if [[ ${x} = ${y} ]]; then
   echo "${x} = ${y} : x 等于 y"
else
   echo "${x} = ${y}: x 不等于 y"
fi

if [[ ${x} != ${y} ]]; then
   echo "${x} != ${y} : x 不等于 y"
else
   echo "${x} != ${y}: x 等于 y"
fi

if [[ -z ${x} ]]; then
   echo "-z ${x} : 字符串长度为 0"
else
   echo "-z ${x} : 字符串长度不为 0"
fi

if [[ -n "${x}" ]]; then
   echo "-n ${x} : 字符串长度不为 0"
else
   echo "-n ${x} : 字符串长度为 0"
fi

if [[ ${x} ]]; then
   echo "${x} : 字符串不为空"
else
   echo "${x} : 字符串为空"
fi

#  Execute: ./operator-demo5.sh
#  Output:
#  x=abc, y=xyz
#  abc = xyz: x 不等于 y
#  abc != xyz : x 不等于 y
#  -z abc : 字符串长度不为 0
#  -n abc : 字符串长度不为 0
#  abc : 字符串不为空
复制代码
```

### 6.6. 文件测试运算符

文件测试运算符用于检测 Unix 文件的各种属性。

属性检测描述如下：

| 操作符  | 说明                                                         | 举例                        |
| ------- | ------------------------------------------------------------ | --------------------------- |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。              | `[ -b $file ]` 返回 false。 |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。            | `[ -c $file ]` 返回 false。 |
| -d file | 检测文件是否是目录，如果是，则返回 true。                    | `[ -d $file ]` 返回 false。 |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | `[ -f $file ]` 返回 true。  |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | `[ -g $file ]` 返回 false。 |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | `[ -k $file ]`返回 false。  |
| -p file | 检测文件是否是有名管道，如果是，则返回 true。                | `[ -p $file ]` 返回 false。 |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | `[ -u $file ]` 返回 false。 |
| -r file | 检测文件是否可读，如果是，则返回 true。                      | `[ -r $file ]` 返回 true。  |
| -w file | 检测文件是否可写，如果是，则返回 true。                      | `[ -w $file ]` 返回 true。  |
| -x file | 检测文件是否可执行，如果是，则返回 true。                    | `[ -x $file ]` 返回 true。  |
| -s file | 检测文件是否为空（文件大小是否大于 0），不为空返回 true。    | `[ -s $file ]` 返回 true。  |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。          | `[ -e $file ]` 返回 true。  |

**:keyboard: 『示例源码』** [operator-demo6.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Foperator%2Foperator-demo6.sh)

```
file="/etc/hosts"

if [[ -r ${file} ]]; then
   echo "${file} 文件可读"
else
   echo "${file} 文件不可读"
fi
if [[ -w ${file} ]]; then
   echo "${file} 文件可写"
else
   echo "${file} 文件不可写"
fi
if [[ -x ${file} ]]; then
   echo "${file} 文件可执行"
else
   echo "${file} 文件不可执行"
fi
if [[ -f ${file} ]]; then
   echo "${file} 文件为普通文件"
else
   echo "${file} 文件为特殊文件"
fi
if [[ -d ${file} ]]; then
   echo "${file} 文件是个目录"
else
   echo "${file} 文件不是个目录"
fi
if [[ -s ${file} ]]; then
   echo "${file} 文件不为空"
else
   echo "${file} 文件为空"
fi
if [[ -e ${file} ]]; then
   echo "${file} 文件存在"
else
   echo "${file} 文件不存在"
fi

#  Execute: ./operator-demo6.sh
#  Output:(根据文件的实际情况，输出结果可能不同)
#  /etc/hosts 文件可读
#  /etc/hosts 文件可写
#  /etc/hosts 文件不可执行
#  /etc/hosts 文件为普通文件
#  /etc/hosts 文件不是个目录
#  /etc/hosts 文件不为空
#  /etc/hosts 文件存在
复制代码
```

## 7. 控制语句

### 7.1. 条件语句

跟其它程序设计语言一样，Bash 中的条件语句让我们可以决定一个操作是否被执行。结果取决于一个包在`[[ ]]`里的表达式。

由`[[ ]]`（`sh`中是`[ ]`）包起来的表达式被称作 **检测命令** 或 **基元**。这些表达式帮助我们检测一个条件的结果。这里可以找到有关[bash 中单双中括号区别](https://link.juejin.cn?target=http%3A%2F%2Fserverfault.com%2Fa%2F52050)的答案。

共有两个不同的条件表达式：`if`和`case`。

#### `if`

（1）`if` 语句

`if`在使用上跟其它语言相同。如果中括号里的表达式为真，那么`then`和`fi`之间的代码会被执行。`fi`标志着条件代码块的结束。

```
# 写成一行
if [[ 1 -eq 1 ]]; then echo "1 -eq 1 result is: true"; fi
# Output: 1 -eq 1 result is: true

# 写成多行
if [[ "abc" -eq "abc" ]]
then
  echo ""abc" -eq "abc" result is: true"
fi
# Output: abc -eq abc result is: true
复制代码
```

（2）`if else` 语句

同样，我们可以使用`if..else`语句，例如：

```
if [[ 2 -ne 1 ]]; then
  echo "true"
else
  echo "false"
fi
# Output: true
复制代码
```

（3）`if elif else` 语句

有些时候，`if..else`不能满足我们的要求。别忘了`if..elif..else`，使用起来也很方便。

```
x=10
y=20
if [[ ${x} > ${y} ]]; then
   echo "${x} > ${y}"
elif [[ ${x} < ${y} ]]; then
   echo "${x} < ${y}"
else
   echo "${x} = ${y}"
fi
# Output: 10 < 20
复制代码
```

**:keyboard: 『示例源码』** [if-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fstatement%2Fif-demo.sh)

#### `case`

如果你需要面对很多情况，分别要采取不同的措施，那么使用`case`会比嵌套的`if`更有用。使用`case`来解决复杂的条件判断，看起来像下面这样：

**:keyboard: 『示例源码』** [case-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fstatement%2Fcase-demo.sh)

```
exec
case ${oper} in
  "+")
    val=`expr ${x} + ${y}`
    echo "${x} + ${y} = ${val}"
  ;;
  "-")
    val=`expr ${x} - ${y}`
    echo "${x} - ${y} = ${val}"
  ;;
  "*")
    val=`expr ${x} \* ${y}`
    echo "${x} * ${y} = ${val}"
  ;;
  "/")
    val=`expr ${x} / ${y}`
    echo "${x} / ${y} = ${val}"
  ;;
  *)
    echo "Unknown oper!"
  ;;
esac
复制代码
```

每种情况都是匹配了某个模式的表达式。`|`用来分割多个模式，`)`用来结束一个模式序列。第一个匹配上的模式对应的命令将会被执行。`*`代表任何不匹配以上给定模式的模式。命令块儿之间要用`;;`分隔。

### 循环语句

循环其实不足为奇。跟其它程序设计语言一样，bash 中的循环也是只要控制条件为真就一直迭代执行的代码块。

Bash 中有四种循环：`for`，`while`，`until`和`select`。

#### `for`循环

`for`与它在 C 语言中的姊妹非常像。看起来是这样：

```
for arg in elem1 elem2 ... elemN
do
  ### 语句
done
复制代码
```

在每次循环的过程中，`arg`依次被赋值为从`elem1`到`elemN`。这些值还可以是通配符或者[大括号扩展](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdenysdovhan%2Fbash-handbook%2Fblob%2Fmaster%2Ftranslations%2Fzh-CN%2FREADME.md%23%E5%A4%A7%E6%8B%AC%E5%8F%B7%E6%89%A9%E5%B1%95)。

当然，我们还可以把`for`循环写在一行，但这要求`do`之前要有一个分号，就像下面这样：

```
for i in {1..5}; do echo $i; done
复制代码
```

还有，如果你觉得`for..in..do`对你来说有点奇怪，那么你也可以像 C 语言那样使用`for`，比如：

```
for (( i = 0; i < 10; i++ )); do
  echo $i
done
复制代码
```

当我们想对一个目录下的所有文件做同样的操作时，`for`就很方便了。举个例子，如果我们想把所有的`.bash`文件移动到`script`文件夹中，并给它们可执行权限，我们的脚本可以这样写：

```
DIR=/home/zp
for FILE in ${DIR}/*.sh; do
  mv "$FILE" "${DIR}/scripts"
done
# 将 /home/zp 目录下所有 sh 文件拷贝到 /home/zp/scripts
复制代码
```

**:keyboard: 『示例源码』** [for-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fstatement%2Ffor-demo.sh)

#### `while`循环

`while`循环检测一个条件，只要这个条件为 *真*，就执行一段命令。被检测的条件跟`if..then`中使用的[基元](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdenysdovhan%2Fbash-handbook%2Fblob%2Fmaster%2Ftranslations%2Fzh-CN%2FREADME.md%23%E5%9F%BA%E5%85%83%E5%92%8C%E7%BB%84%E5%90%88%E8%A1%A8%E8%BE%BE%E5%BC%8F)并无二异。因此一个`while`循环看起来会是这样：

```
while [[ condition ]]
do
  ### 语句
done
复制代码
```

跟`for`循环一样，如果我们把`do`和被检测的条件写到一行，那么必须要在`do`之前加一个分号。

比如下面这个例子：

```
### 0到9之间每个数的平方
x=0
while [[ ${x} -lt 10 ]]; do
  echo $((x * x))
  x=$((x + 1))
done
#  Output:
#  0
#  1
#  4
#  9
#  16
#  25
#  36
#  49
#  64
#  81
复制代码
```

**:keyboard: 『示例源码』** [while-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fstatement%2Fwhile-demo.sh)

#### `until`循环

`until`循环跟`while`循环正好相反。它跟`while`一样也需要检测一个测试条件，但不同的是，只要该条件为 *假* 就一直执行循环：

```
x=0
until [[ ${x} -ge 5 ]]; do
  echo ${x}
  x=`expr ${x} + 1`
done
#  Output:
#  0
#  1
#  2
#  3
#  4
复制代码
```

**:keyboard: 『示例源码』** [until-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fstatement%2Funtil-demo.sh)

#### `select`循环

`select`循环帮助我们组织一个用户菜单。它的语法几乎跟`for`循环一致：

```
select answer in elem1 elem2 ... elemN
do
  ### 语句
done
复制代码
```

`select`会打印`elem1..elemN`以及它们的序列号到屏幕上，之后会提示用户输入。通常看到的是`$?`（`PS3`变量）。用户的选择结果会被保存到`answer`中。如果`answer`是一个在`1..N`之间的数字，那么`语句`会被执行，紧接着会进行下一次迭代 —— 如果不想这样的话我们可以使用`break`语句。

一个可能的实例可能会是这样：

```
#!/usr/bin/env bash

PS3="Choose the package manager: "
select ITEM in bower npm gem pip
do
echo -n "Enter the package name: " && read PACKAGE
case ${ITEM} in
  bower) bower install ${PACKAGE} ;;
  npm) npm install ${PACKAGE} ;;
  gem) gem install ${PACKAGE} ;;
  pip) pip install ${PACKAGE} ;;
esac
break # 避免无限循环
done
复制代码
```

这个例子，先询问用户他想使用什么包管理器。接着，又询问了想安装什么包，最后执行安装操作。

运行这个脚本，会得到如下输出：

```
$ ./my_script
1) bower
2) npm
3) gem
4) pip
Choose the package manager: 2
Enter the package name: gitbook-cli
复制代码
```

**:keyboard: 『示例源码』** [select-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fstatement%2Fselect-demo.sh)

#### `break` 和 `continue`

如果想提前结束一个循环或跳过某次循环执行，可以使用 shell 的`break`和`continue`语句来实现。它们可以在任何循环中使用。

> `break`语句用来提前结束当前循环。
>
> `continue`语句用来跳过某次迭代。

**:keyboard: 『示例源码』** [break-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fstatement%2Fbreak-demo.sh)

```
# 查找 10 以内第一个能整除 2 和 3 的正整数
i=1
while [[ ${i} -lt 10 ]]; do
  if [[ $((i % 3)) -eq 0 ]] && [[ $((i % 2)) -eq 0 ]]; then
    echo ${i}
    break;
  fi
  i=`expr ${i} + 1`
done
# Output: 6
复制代码
```

**:keyboard: 『示例源码』** [continue-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fstatement%2Fcontinue-demo.sh)

```
# 打印10以内的奇数
for (( i = 0; i < 10; i ++ )); do
  if [[ $((i % 2)) -eq 0 ]]; then
    continue;
  fi
  echo ${i}
done
#  Output:
#  1
#  3
#  5
#  7
#  9
复制代码
```

## 函数

bash 函数定义语法如下：

```
[ function ] funname [()] {
    action;
    [return int;]
}
复制代码
```

> :bulb: 说明：
>
> 1. 函数定义时，`function` 关键字可有可无。
> 2. 函数返回值 - return 返回函数返回值，返回值类型只能为整数（0-255）。如果不加 return 语句，shell 默认将以最后一条命令的运行结果，作为函数返回值。
> 3. 函数返回值在调用该函数后通过 `$?` 来获得。
> 4. 所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至 shell 解释器首次发现它时，才可以使用。调用函数仅使用其函数名即可。

**:keyboard: 『示例源码』** [function-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2F%2Ffunction%2Ffunction-demo.sh)

```
#!/usr/bin/env bash

calc(){
  PS3="choose the oper: "
  select oper in + - \* / # 生成操作符选择菜单
  do
  echo -n "enter first num: " && read x # 读取输入参数
  echo -n "enter second num: " && read y # 读取输入参数
  exec
  case ${oper} in
    "+")
      return $((${x} + ${y}))
    ;;
    "-")
      return $((${x} - ${y}))
    ;;
    "*")
      return $((${x} * ${y}))
    ;;
    "/")
      return $((${x} / ${y}))
    ;;
    *)
      echo "${oper} is not support!"
      return 0
    ;;
  esac
  break
  done
}
calc
echo "the result is: $?" # $? 获取 calc 函数返回值
复制代码
```

执行结果：

```
$ ./function-demo.sh
1) +
2) -
3) *
4) /
choose the oper: 3
enter first num: 10
enter second num: 10
the result is: 100
复制代码
```

### 位置参数

**位置参数**是在调用一个函数并传给它参数时创建的变量。

位置参数变量表：

| 变量           | 描述                           |
| -------------- | ------------------------------ |
| `$0`           | 脚本名称                       |
| `$1 … $9`      | 第 1 个到第 9 个参数列表       |
| `${10} … ${N}` | 第 10 个到 N 个参数列表        |
| `$*` or `$@`   | 除了`$0`外的所有位置参数       |
| `$#`           | 不包括`$0`在内的位置参数的个数 |
| `$FUNCNAME`    | 函数名称（仅在函数内部有值）   |

**:keyboard: 『示例源码』** [function-demo2.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2F%2Ffunction%2Ffunction-demo2.sh)

```
#!/usr/bin/env bash

x=0
if [[ -n $1 ]]; then
  echo "第一个参数为：$1"
  x=$1
else
  echo "第一个参数为空"
fi

y=0
if [[ -n $2 ]]; then
  echo "第二个参数为：$2"
  y=$2
else
  echo "第二个参数为空"
fi

paramsFunction(){
  echo "函数第一个入参：$1"
  echo "函数第二个入参：$2"
}
paramsFunction ${x} ${y}
复制代码
```

执行结果：

```
$ ./function-demo2.sh
第一个参数为空
第二个参数为空
函数第一个入参：0
函数第二个入参：0

$ ./function-demo2.sh 10 20
第一个参数为：10
第二个参数为：20
函数第一个入参：10
函数第二个入参：20
复制代码
```

执行 `./variable-demo4.sh hello world` ，然后在脚本中通过 `$1`、`$2` ... 读取第 1 个参数、第 2 个参数。。。

### 函数处理参数

另外，还有几个特殊字符用来处理参数：

| 参数处理 | 说明                                             |
| -------- | ------------------------------------------------ |
| `$#`     | 返回参数个数                                     |
| `$*`     | 返回所有参数                                     |
| `?`      | 脚本运行的当前进程 ID 号                         |
| `$!`     | 后台运行的最后一个进程的 ID 号                   |
| `$@`     | 返回所有参数                                     |
| `$-`     | 返回 Shell 使用的当前选项，与 set 命令功能相同。 |
| `$?`     | 函数返回值                                       |

**:keyboard: 『示例源码』** [function-demo3.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Ftree%2Fmaster%2Fcodes%2Fshell%2Fdemos%2F%2Ffunction%2Ffunction-demo3.sh)

```
runner() {
  return 0
}

name=zp
paramsFunction(){
  echo "函数第一个入参：$1"
  echo "函数第二个入参：$2"
  echo "传递到脚本的参数个数：$#"
  echo "所有参数："
  printf "+ %s\n" "$*"
  echo "脚本运行的当前进程 ID 号：?"
  echo "后台运行的最后一个进程的 ID 号：$!"
  echo "所有参数："
  printf "+ %s\n" "$@"
  echo "Shell 使用的当前选项：$-"
  runner
  echo "runner 函数的返回值：$?"
}
paramsFunction 1 "abc" "hello, \"zp\""
#  Output:
#  函数第一个入参：1
#  函数第二个入参：abc
#  传递到脚本的参数个数：3
#  所有参数：
#  + 1 abc hello, "zp"
#  脚本运行的当前进程 ID 号：26400
#  后台运行的最后一个进程的 ID 号：
#  所有参数：
#  + 1
#  + abc
#  + hello, "zp"
#  Shell 使用的当前选项：hB
#  runner 函数的返回值：0
复制代码
```

## Shell 扩展

*扩展* 发生在一行命令被分成一个个的 *记号（tokens）* 之后。换言之，扩展是一种执行数学运算的机制，还可以用来保存命令的执行结果，等等。

感兴趣的话可以阅读[关于 shell 扩展的更多细节](https://link.juejin.cn?target=https%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fbash%2Fmanual%2Fbash.html%23%23%23Shell-Expansions)。

#### 大括号扩展

大括号扩展让生成任意的字符串成为可能。它跟 *文件名扩展* 很类似，举个例子：

```
echo beg{i,a,u}n ### begin began begun
复制代码
```

大括号扩展还可以用来创建一个可被循环迭代的区间。

```
echo {0..5} ### 0 1 2 3 4 5
echo {00..8..2} ### 00 02 04 06 08
复制代码
```

#### 命令置换

命令置换允许我们对一个命令求值，并将其值置换到另一个命令或者变量赋值表达式中。当一个命令被``或$()包围时，命令置换将会执行。举个例子：

```
now=`date +%T`
### or
now=$(date +%T)

echo $now ### 19:08:26
复制代码
```

#### 算数扩展

在 bash 中，执行算数运算是非常方便的。算数表达式必须包在`$(( ))`中。算数扩展的格式为：

```
result=$(( ((10 + 5*3) - 7) / 2 ))
echo $result ### 9
复制代码
```

在算数表达式中，使用变量无需带上`$`前缀：

```
x=4
y=7
echo $(( x + y ))     ### 11
echo $(( ++x + y++ )) ### 12
echo $(( x + y ))     ### 13
复制代码
```

#### 单引号和双引号

单引号和双引号之间有很重要的区别。<font color='red'>在双引号中，变量引用或者命令置换是会被展开的</font>。在单引号中是不会的。举个例子：

```
echo "Your home: $HOME" ### Your home: /Users/<username>
echo 'Your home: $HOME' ### Your home: $HOME
复制代码
```

当局部变量和环境变量包含空格时，它们在引号中的扩展要格外注意。随便举个例子，假如我们用`echo`来输出用户的输入：

```
INPUT="A string  with   strange    whitespace."
echo $INPUT   ### A string with strange whitespace.
echo "$INPUT" ### A string  with   strange    whitespace.
复制代码
```

调用第一个`echo`时给了它 5 个单独的参数 —— `$INPUT` 被分成了单独的词，`echo`在每个词之间打印了一个空格。第二种情况，调用`echo`时只给了它一个参数（整个$INPUT 的值，包括其中的空格）。

来看一个更严肃的例子：

```
FILE="Favorite Things.txt"
cat $FILE   ### 尝试输出两个文件: `Favorite` 和 `Things.txt`
cat "$FILE" ### 输出一个文件: `Favorite Things.txt`
复制代码
```

尽管这个问题可以通过把 FILE 重命名成`Favorite-Things.txt`来解决，但是，假如这个值来自某个环境变量，来自一个位置参数，或者来自其它命令（`find`, `cat`, 等等）呢。因此，如果输入 *可能* 包含空格，务必要用引号把表达式包起来。

## 流和重定向

Bash 有很强大的工具来处理程序之间的协同工作。使用流，我们能将一个程序的输出发送到另一个程序或文件，因此，我们能方便地记录日志或做一些其它我们想做的事。

管道给了我们创建传送带的机会，控制程序的执行成为可能。

学习如何使用这些强大的、高级的工具是非常非常重要的。

### 输入、输出流

Bash 接收输入，并以字符序列或 **字符流** 的形式产生输出。这些流能被重定向到文件或另一个流中。

有三个文件描述符：

| 代码 | 描述符   | 描述         |
| ---- | -------- | ------------ |
| `0`  | `stdin`  | 标准输入     |
| `1`  | `stdout` | 标准输出     |
| `2`  | `stderr` | 标准错误输出 |

### 重定向

重定向让我们可以控制一个命令的输入来自哪里，输出结果到什么地方。这些运算符在控制流的重定向时会被用到：

| Operator | Description                                                  |
| -------- | ------------------------------------------------------------ |
| `>`      | 重定向输出                                                   |
| `&>`     | 重定向输出和错误输出                                         |
| `&>>`    | 以附加的形式重定向输出和错误输出                             |
| `<`      | 重定向输入                                                   |
| `<<`     | [Here 文档](https://link.juejin.cn?target=http%3A%2F%2Ftldp.org%2FLDP%2Fabs%2Fhtml%2Fhere-docs.html) 语法 |
| `<<<`    | [Here 字符串](https://link.juejin.cn?target=http%3A%2F%2Fwww.tldp.org%2FLDP%2Fabs%2Fhtml%2Fx17837.html) |

以下是一些使用重定向的例子：

```
### ls的结果将会被写到list.txt中
ls -l > list.txt

### 将输出附加到list.txt中
ls -a >> list.txt

### 所有的错误信息会被写到errors.txt中
grep da * 2> errors.txt

### 从errors.txt中读取输入
less < errors.txt
复制代码
```

### `/dev/null` 文件

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null：

```
$ command > /dev/null
复制代码
```

/dev/null 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 /dev/null 文件非常有用，将命令的输出重定向到它，会起到"禁止输出"的效果。

如果希望屏蔽 stdout 和 stderr，可以这样写：

```
$ command > /dev/null 2>&1
//https://blog.csdn.net/weixin_41988331/article/details/86215802
```

## Debug

shell 提供了用于 debug 脚本的工具。

如果想采用 debug 模式运行某脚本，可以在其 shebang 中使用一个特殊的选项：

```
#!/bin/bash options
复制代码
```

options 是一些可以改变 shell 行为的选项。下表是一些可能对你有用的选项：

| Short | Name        | Description                                                |
| ----- | ----------- | ---------------------------------------------------------- |
| `-f`  | noglob      | 禁止文件名展开（globbing）                                 |
| `-i`  | interactive | 让脚本以 *交互* 模式运行                                   |
| `-n`  | noexec      | 读取命令，但不执行（语法检查）                             |
| `-t`  | —           | 执行完第一条命令后退出                                     |
| `-v`  | verbose     | 在执行每条命令前，向`stderr`输出该命令                     |
| `-x`  | xtrace      | 在执行每条命令前，向`stderr`输出该命令以及该命令的扩展参数 |

举个例子，如果我们在脚本中指定了`-x`例如：

```
#!/bin/bash -x

for (( i = 0; i < 3; i++ )); do
  echo $i
done
复制代码
```

这会向`stdout`打印出变量的值和一些其它有用的信息：

```
$ ./my_script
+ (( i = 0 ))
+ (( i < 3 ))
+ echo 0
0
+ (( i++  ))
+ (( i < 3 ))
+ echo 1
1
+ (( i++  ))
+ (( i < 3 ))
+ echo 2
2
+ (( i++  ))
+ (( i < 3 ))
复制代码
```

有时我们值需要 debug 脚本的一部分。这种情况下，使用`set`命令会很方便。这个命令可以启用或禁用选项。使用`-`启用选项，`+`禁用选项：

**:keyboard: 『示例源码』** [debug-demo.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fos-tutorial%2Fblob%2Fmaster%2Fcodes%2Fshell%2Fdemos%2Fdebug-demo.sh)

```
# 开启 debug
set -x
for (( i = 0; i < 3; i++ )); do
  printf ${i}
done
# 关闭 debug
set +x
#  Output:
#  + (( i = 0 ))
#  + (( i < 3 ))
#  + printf 0
#  0+ (( i++  ))
#  + (( i < 3 ))
#  + printf 1
#  1+ (( i++  ))
#  + (( i < 3 ))
#  + printf 2
#  2+ (( i++  ))
#  + (( i < 3 ))
#  + set +x

for i in {1..5}; do printf ${i}; done
printf "\n"
#  Output: 12345
复制代码
```

## 12. 更多内容

> :notebook: 本文已归档到：「[blog](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdunwu%2Fblog)」

- [awesome-shell](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Falebcay%2Fawesome-shell)，shell 资源列表
- [awesome-bash](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fawesome-lists%2Fawesome-bash)，bash 资源列表
- [bash-handbook](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fdenysdovhan%2Fbash-handbook)
- [bash-guide](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fvuuihc%2Fbash-guide) ，bash 基本用法指南
- [bash-it](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FBash-it%2Fbash-it)，为你日常使用，开发以及维护 shell 脚本和自定义命令提供了一个可靠的框架
- [dotfiles.github.io](https://link.juejin.cn?target=http%3A%2F%2Fdotfiles.github.io%2F)，上面有 bash 和其它 shell 的各种 dotfiles 集合以及 shell 框架的链接
- [Runoob Shell 教程](https://link.juejin.cn?target=http%3A%2F%2Fwww.runoob.com%2Flinux%2Flinux-shell.html)
- [shellcheck](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fkoalaman%2Fshellcheck) 一个静态 shell 脚本分析工具，本质上是 bash／sh／zsh 的 lint。

最后，Stack Overflow 上 [bash 标签下](https://link.juejin.cn?target=https%3A%2F%2Fstackoverflow.com%2Fquestions%2Ftagged%2Fbash)有很多你可以学习的问题，当你遇到问题时，也是一个提问的好地方。


