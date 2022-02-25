---
title: Mac terminal入门篇
date: 2018-01-25 21:36:26
tags: tool
categories: 工具
---
## 了解mac terminal（终端）

``` bash
system_profiler    #显示Mac的硬件和软件信息
sw_vers            #显示OSX系统版本
uname -av          #显示OS X Darwin内核版本
```
简单来说：  
- unix是最早的出来的多任务、多用户系统，是对源代码实行知识产权保护的传统商业软件，是收费的。 
- linux是一个牛人为了让人们用到免费的类unix系统，开发出了linux的内核，是开放源代码的自由软件。
- mac os x是由Unix的一个分支(BSD)发展过来的，核心叫darwin(达尔文)。

在unix 内核的操作系统中,包括衍生出来的分支linux、OS X等，所有文件都挂在跟目录 / 下面，没有Windows下的盘符概念。

<!--more-->

位置       | 路径   | 备注
-----------|--------|------------------- 
根目录     | /mach_kernel              | 内核就在这
驱动所在   | /Systme/Library/Extensions|
用户文件夹(home folder) | /User/用户名 |简写：~
桌面       | /User/用户名/Desktop      |
当前目录   |.                        |
父目录     |..                        |

> 注意：在 Unix系统中是区别大小写字符的，并且对于文件名，必须包括扩展名。

## 快速打开终端

1. spotlight方法：首先用快捷键打开spotlight（command+空格），输入ter，自动显示出终端全称，按enter即可打开终端。  
2. Finder打开：Finder——应用程序——实用工具——终端  
3. 快捷键完成当前文件夹打开终端：打开“系统偏好设置”——键盘——快捷键——服务，找到“新建位于文件夹位置的终端窗口”打钩。之后，只要在文件夹上 右键——服务——“新建位于文件夹位置的终端窗口” 即可。


## 终级Shell--zsh
shell 是我们和系统内核指令打交道的一座桥梁,我们通过键盘输入一种自己容易记忆识别的符号标识(shell 命令)，然后 shell 解析这种命令再反馈给内核去执行一系列操作。

**可以通过以下命令查看安装了哪些shell（OS X 系统预装了zsh）**

``` bash
cat /etc/shells
```

**查看系统默认的shell**
``` bash
echo $SHELL
```

**将默认shell改成zsh**
``` bash
chsh -s /bin/zsh
```
> zsh是shell的一种，由于配置过于复杂，以至于很多人并不常用他。所以大多数Linux产品默认使用的都是bash shell。直到一个开源项目的出现[oh my zsh](http://ohmyz.sh)，github来管理zsh的配置和支持更多插件，使得zsh配置变得更容易，使用起来更加强大。以至于他有了个外号—**终极shell**。

**安装oh my zsh**
``` bash
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

**配置文件路径：** ~/.zshrc

``` bash
大部分配置都可以在配置文件中进行设置，这里大概翻译一下。

# ZSH的环境变量
export ZSH=/Users/dawang/.oh-my-zsh
# 主题设置
# 主题列表在 ~/.oh-my-zsh/themes/
# [主题列表](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)
# 如果设置为 "random", 每次开启都会是不同的主题
ZSH_THEME="robbyrussell"
# 如果想要大小写敏感，可以取消注释下面的一行
# CASE_SENSITIVE="true"
# 如果想要连接符不敏感，可以取消注释下面的一行。_ 和 - 将可以互换
# HYPHEN_INSENSITIVE="true"
# 如果不想要自动更新，可以取消注释下面的一行
# DISABLE_AUTO_UPDATE="true"
# 自动更新的时间间隔，单位是天，这里设置 30 天更新一次
export UPDATE_ZSH_DAYS=30
# 如果不想要 ls 命令输出带颜色，可以取消注释下面的一行
# DISABLE_LS_COLORS="true"
# 是否禁止更改终端标题,不要禁止,不然所有终端tab只显示zsh了,而不随着目录的改变而改变显示
# DISABLE_AUTO_TITLE="true"
# 自动纠正命令,不启用,不怎么好用
# ENABLE_CORRECTION="true"
# 按tab键补全命令的时候,如果没什么可补全的就会出现三个红点
# COMPLETION_WAITING_DOTS="true"
# Uncomment the following line if you want to disable marking untracked files
# under VCS as dirty. This makes repository status check for large repositories
# much, much faster.
# 不要在意这些细节，不需要改动
# DISABLE_UNTRACKED_FILES_DIRTY="true"
# 历史命令日期显示格式
# 有三种方式: "mm/dd/yyyy"|"dd.mm.yyyy"|"yyyy-mm-dd"
# HIST_STAMPS="yyyy-mm-dd"
# Would you like to use another custom folder than $ZSH/custom?
# ZSH_CUSTOM=/path/to/new-custom-folder
# Which plugins would you like to load? (plugins can be found in ~/.oh-my-zsh/plugins/*)
# Custom plugins may be added to ~/.oh-my-zsh/custom/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# 插件设置，如果添加太多启动速度会比较慢
plugins=(git autojump)
[[ -s ~/.autojump/etc/profile.d/autojump.zsh ]] && . ~/.autojump/etc/profile.d/autojump.zsh
# 剩下部分比较不常改动 
# User configuration
export PATH="/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/Library/TeX/texbin"
# export MANPATH="/usr/local/man:$MANPATH"
source $ZSH/oh-my-zsh.sh
# You may need to manually set your language environment
# export LANG=en_US.UTF-8
# Preferred editor for local and remote sessions
# if [[ -n $SSH_CONNECTION ]]; then
#   export EDITOR='vim'
# else
#   export EDITOR='mvim'
# fi
# Compilation flags
# export ARCHFLAGS="-arch x86_64"
# ssh
# export SSH_KEY_PATH="~/.ssh/dsa_id"
# Set personal aliases, overriding those provided by oh-my-zsh libs,
# plugins, and themes. Aliases can be placed here, though oh-my-zsh
# users are encouraged to define aliases within the ZSH_CUSTOM folder.
# For a full list of active aliases, run `alias`.
#
# Example aliases
# alias zshconfig="mate ~/.zshrc"
# alias ohmyzsh="mate ~/.oh-my-zsh"
```
## 使用技巧
1. 插件相关的文件在~/.oh-my-zsh/plugins目录下，默认提供了100多种。插件也是在.zshrc里配置，找到plugins关键字，系统默认加载git与autojump，可以在后面追加启用插件。
- git：当你处于一个 git 受控的目录下时，Shell 会明确显示 「git」和 branch。另外对git 很多命令进行了简化，例如 gco=’git checkout’、gst=’git status’、g=’git’等等，熟练使用可以大大减少 git 的命令长度，命令内容可以参考~/.oh-my-zsh/plugins/git/git.plugin.zsh

- 安装了autojump之后，zsh 会自动记录你访问过的目录，通过 j + 目录名 可以直接进行目录跳转，而且目录名支持模糊匹配和自动补全。

2. 配置文件底部可以设置 alias（通用别名），还能针对文件类型设置对应的打开程序。

3. 连按两次Tab会列出所有的补全列表并直接开始选择，补全项可以使用 ctrl+n/p/f/b 或 方向键上下左右切换。

4. 在zsh中只需要键入 ls -<tab> 就会列出所有的选项和帮助说明。  

5. 命令参数补全。键入 kill <tab> 就会列出所有的进程名和对应的进程号。

6. 更智能的历史命令。在用或者方向上键查找历史命令时，zsh支持限制查找。比如，输入ls,然后再按方向上键，则只会查找用过的ls命令。

7. 多个终端会话共享历史记录。

8. 目录浏览和跳转：输入 d，即可列出你在这个会话里访问的目录列表，输入列表前的序号，即可直接跳转。

9. 在当前目录下输入 .. 或 ... ，或直接输入当前目录名都可以跳转，你甚至不再需要输入 cd 命令了。在你知道路径的情况下，比如 /usr/local/bin 你可以输入 cd /u/l/b 然后按进行补全快速输入。

10. 通配符搜索：例如“ls -l \*\*/\*.sh”，可以递归显示当前目录下的 shell 文件，文件少时可以代替 find。使用 \*\*/ 来递归搜索。

11. 如何在命令里面打回车： \ 后面接回车

12. 一句话执行两个命令： xxx; yyy

13. 一句话执行两个命令： xxx&& yyy

### 快捷键
- 光标左移：Ctrl + B
- 光标右移：Ctrl + F
- 光标至行首：Ctrl + A
- 光标至行尾：Ctrl + E
- 删除至行首：Ctrl + U
- 删除至行尾：Ctrl + K
- 删除至单词开始：Ctrl + W
- 清屏：Ctrl + L
- 插入最近删除：Ctrl + Y
- 历史命令：↑ 或 ↓箭头，或 ⌃ + P、Ctrl + N
- 搜索历史命令：Ctrl + R
- 中止执行：Ctrl + C
- 按住 ⌘，单击路径可以在 Finder 中打开

### 缩写
命令      | 全写           |缩写   
----------|----------------|----------------|
创建目录  | make directory | mkdir 
删除      | remove         | rm
移动/重命名| move          | mv
复制      | copy           | cp
罗列      | list           | ls
链接      | link           | ln *



### 常用的通配符

- 星号（＊，Asterisk）——代表任何长度的任何字符。例如*.tiff代表所有格式为tiff的文件。
- 问号（?，Question mark）——代表任何单个字符。例如b?ok匹配 book 但是不匹配 brook。
- 方括号（[]，Square brackets）——定义一定范围的字符，例如[Dd]ocument匹配 Document 以及 document；doc[1-9]匹配doc1, doc2, …, doc9。  


### 处理特殊字符
- 如果目录中有特殊字符（空格，括号，引号，[]，!，$，&，*，;，|，\），那么直接输入空格会造成系统识别困难，必须使用特殊的语法来表示这些字符。例如上例中，空格前添加反斜杠“\”（back slash）即可：cd Punlic/Drop\ Box/。除了反斜杠，也可以用引号的方法：cd "Public/Drop Box"。

- 如果不想手动输入，也可以把文件从 Finder 拖到 Terminal 窗口来创建绝对路径，这会方便一些，因为上面提到的所有特殊字符在拖动后都会自动变成系统可识别的表示方法。

- 更有效率的解决方案是使用 Tab Complete 功能。连按两次Tab进行选择。Tab complete 同样会处理那些特殊字符。



## 基本命令
### 列出文件  

``` bash
ls 参数 目录名        
```
以下为常用参数，可组合使用。

参数     | 含义
---|---------------|
-a |显示所有档案及目录（ls内定将档案名或目录名称为“.”的视为影藏，不会列出）。 
-A |显示除影藏文件“.”和“..”以外的所有文件列表。 
-d |仅显示目录名，而不显示目录下的内容列表。显示符号链接文件本身，而不显示其所指向的目录列表。 
-l |以长格式显示目录下的内容列表。输出的信息从左到右依次包括文件名，文件类型、权限模式、硬连接数、所有者、组、文件大小和文件的最后修改时间等。 
-R |递归处理，将指定目录下的所有文件及子目录一并处理； --full-time：列出完整的日期与时间； --color[=WHEN]：使用不同的颜色高亮显示不同类型的。 


### 转换目录     
``` bash
pwd     #含义是“print working directory”，会显示当前目录的绝对路径。
cd 路径  #改变当前目录到你指定的目录。不指定则会返回 home folder
```


### 打开文件目录或程序
``` bash
open /Applications/Safari.app/  #打开应用
open .  #打开当前目录。
```


### 建立新目录  

``` bash
mkdir 目录名
```
参数  | 含义
------|-----------
-p |递归创建目录，如果文件不存在，则逐层创建目录。
-v |显示目录创建的信息。
 
 
### 新建文件
``` bash
touch 文件名   #主要功能与时间戳有关，用来更新文件的访问和修改时间。如果文件不存在，会创建相应的空文件
```

### 拷贝文件

``` bash
cp 参数 源文件 目标文件

cp file /usr/men/tmp/file1  #将文件file复制到目录/usr/men/tmp下，并改名为file1

cp -r /usr/men /usr/zh      #将目录/usr/men下的所有文件及其子目录复制到目录/usr/zh中

cp -i /usr/men m*.c /usr/zh #交互式地将目录/usr/men中的以m打头的所有.c文件复制到目录/usr/zh中（覆盖之前询问用户）
```

### 删除文件  

``` bash
rm  foo.txt        #删除一般文件  
rm  -r  foofolder  #删除目录
rm  -r  *          #删除当前目录下的所有文件及目录
```

参数| 说明
------|-----
-r    |删除目录时必需参数；  
-i    |删除前逐一询问确认；  
－rf  |表示递归和强制，千万要小心使用，如果执行了 rm -rf / 就全没了

### 移动文件

``` bash
mv file1 file2      #（若目标是文件名，则相当于文件改名）将文件 file1 改名为 file2。
mv file1 file2 /tmp #将文件 file1 和文件 file2 移动到目录 /tmp 下
```
参数 | 说明
-----|----
- i  | 在覆盖已存在文件时作提示，若回答 y 则覆盖，其他则中止
- f  |覆盖前不作任何提示


### 文本编辑  

编辑器|特点
------|----
vi    |老式的简单文本编辑器
vim   |复杂的文本编辑器，相当于vi的升级版
nano  |小巧自由的舒适型编辑器

#### [nano](http://man.linuxde.net/nano)

``` bash
nano 路径+文件名 #若文件存在将打开这个文件；不存在则会创建新文件并打开。
```

- 复制一整行：Alt+6
- 剪贴一整行：Ctrl+K
- 粘贴：Ctrl+U
- 搜索: Ctrl+W，然后输入你要搜索的关键字，回车确定，定位到第一个匹配的文本，接着可用Alt+W来定位到下一个匹配的文本。
- 翻页到上一页：Ctrl+Y
- 翻页到下一页：Ctrl+V
- 保存：Ctrl+O 保存所做的修改
- 退出：Ctrl+X。如果你修改了文件，下面会询问你是否需要保存修改。输入Y确认保存，输入N不保存，按Ctrl+C取消返回。如果输入了Y，下一步会让你输入想要保存的文件名。如果不需要修改文件名直接回车就行；若想要保存成别的名字（也就是另存为）则输入新名称然后确 定。这个时候也可用Ctrl+C来取消返回。

#### [vi](http://man.linuxde.net/vi)

> vi命令是UNIX操作系统和类UNIX操作系统中最通用的全屏幕纯文本编辑器。Linux中的vi编辑器叫vim，它是vi的增强版（vi Improved），与vi编辑器完全兼容，而且实现了很多增强功能。

vi编辑器提供了丰富的内置命令，有些内置命令使用键盘组合键即可完成，有些内置命令则需要以冒号“：”开头输入。常用内置命令如下：

命令 | 说明
------|------
Ctrl+u |向文件首翻半屏； 
Ctrl+d |向文件尾翻半屏； 
Ctrl+f |向文件尾翻一屏； 
Ctrl+b |向文件首翻一屏； 
Esc    |从编辑模式切换到命令模式；
ZZ     |命令模式下保存当前文件所做的修改后退出vi；
:行号   |光标跳转到指定行的行首；
:wq    |在命令模式下，执行存盘退出操作；
:w     |在命令模式下，执行存盘操作；
:w!    |在命令模式下，执行强制存盘操作；
:q     |在命令模式下，执行退出vi操作；
:q!    |在命令模式下，执行强制退出vi操作；
:set number|在命令模式下用于在最左端显示行号；
:set nonumber|在命令模式下用于在最左端不显示行号；

### 读取文件并输出到 Terminal 窗口

``` bash
cat foo.txt                        #显示文件全部内容
cat > filename                     #创建文件并进入编辑模式
cat foo.txt bar.txt > foobar.txt   #合并文件内容
```

### 查看命令的详细帮助

``` bash
man 命令名
```
命令   | 说明
-------|-----
空格键 |向文件尾部翻一屏
b      |向文件首部翻一屏
/      |后跟一个字符串和[Enter]来查找字符串
n      |发现本次查找的下一个匹配
N      |发现本次查找的上一个匹配
q      |退出

### 定位某个命令的文件路径

``` bash
which 命令
```
这个命令会定位某个命令的文件路径。换言之，它会告诉你你执行某个具体命令的时候，在使用哪个文件。

### 根据文件的内容输出文件类型

``` bash
file 文件路径
```
如果一个文件缺失了扩展名，那么这个命令可能会非常有用。


### 利用 URL 语法在命令行下工作的文件传输工具
``` bash
curl www.baidu.com              #查看网站源码
curl -o 文件名 www.sina.com      #下载
```

### 运行脚本命令

``` bash
sh 脚本文件名
```
例: 所有需要的操作存成一个脚本，以后只要运行一次这个脚本就可以了。
1. 终端中运行nano /clean；
2. 把下列代码粘贴到 nano 中；  
rm -rf /System/Library/Extensions.kextcache  
rm -rf /System/Library/Extensions.mkext  
chown -R root:wheel /System/Library/Extensions  
chmod -R 755 /System/Library/Extensions  
diskutil repairpermissions /  
kextcache -k /System/Library/Extensions/  
3. Ctrl ＋O 存盘，Ctrl＋X 退出；  
4. 以后只要在终端中运行一次 sh /clean即可；  


### 常用解压和压缩命令

``` bash
# .tar（注：tar是打包，不是压缩）
tar xvf FileName.tar             #解压
tar cvf FileName.tar DirName     #打包
tar tvf FileName.tar             #查看包内容

# .zip
unzip FileName.zip               #解压
zip FileName.zip DirName         #打包
```


### say  
say 是一个文本转语音（TTS）的有趣工具，引擎和OS X使用的一样也是VoiceOver。可以用于在脚本中播放警告或提示。例如你可以设置Automator或Hazel脚本处理文件，并在任务完成时用  say 命令语音提示。 

``` bash
# 用 -f 选项朗读特定文本文件，-o 选项将朗读结果存为音频文件而不是播放
say -f mynovel.txt -o myaudiobook.aiff 
```

### 显示当前日历，或者指定日期的日历

``` bash
cal      #输出当前月日历
cal 2018 #输出2018年日历
```

### sudo
通过安全的方式使用特殊的权限运行程序（通常为系统的超级用户）
默认情况下，任何管理员账户都可以使用sudo来获取 root 权限，甚至当 root 账户在图形界面被禁用的情况下，sudo依然有效。这个命令是很多情况下我们不得不使用 Terminal 的原因，——同样也是给每个用户管理员身份的危险所在。不过，你可以调整sudo的配置文件，来限制它的使用。


## 彩蛋时刻

- telnet towel.blinkenlights.nl ：星球大战电影  

- 输入emacs，按空格键，再按“esc＋x”键，左下角会出现“M-x”，输入游戏代码，如tetris（俄罗斯方块）、gomoku（五子棋）就能玩玩游戏，open /usr/share/emacs/*/lisp/play 可查看游戏列表。

> ps: mac自带神的编辑器-Emacs,选择emacs就有来生的，因为一生刚够入门。


参考网址：  
[Linux命令大全](http://man.linuxde.net/)  
[每天一个linux命令](http://www.cnblogs.com/peida/tag/linux%E5%91%BD%E4%BB%A4/)