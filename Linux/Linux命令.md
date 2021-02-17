# linux-note

主要是纪录一些 linux 的指令📝

( 本篇文章会持续更新:smile: )

## cd

切换到家目录 `~`

```cmd
cd ~
```

切换到根目录 `/`

```cmd
cd /
```

回到上层目录

```cmd
cd ..
```

## man

线上说明手册 ( man page )

```cmd
man ls
```

![alt tag](https://i.imgur.com/3DDi208.png)

也可以使用

```cmd
ls --help
```

![alt tag](https://i.imgur.com/ZSZjuVC.png)

## pwd

查看目前的路径

```cmd
pwd
```

## ls

[Youtube Tutorial - Linux 指令教学 - ls](https://youtu.be/3Zy1AWuDUHE)

列出档案

```cmd
ls -l
```

`-l` 显示详细的资讯 ( 档案权限 )。

也等于直接输入 (L 的小写)

```cmd
ll
```

在 Linux 中，档案都拥有四种权限

* 可读取 ( r，Readable )，用数字 4 表示。

* 可写入 ( w，writable )，用数字 2 表示。

* 可执行 ( x，eXecute )，用数字 1 表示。

* 无权限 ( - )，用数字 0 表示。

为了更清楚，我把它整理成表格:yum:

|     字元     | 权限分数 |
|:------------:|:--------:|
|   r (read)   |     4    |
|   w (write)  |     2    |
|  x (execute) |     1    |
|    - 无权限  |     0    |

如下图所示，

![alt tag](https://i.imgur.com/AzfYBhf.png)

接著说明裡面每一栏的意思，

![alt tag](https://i.imgur.com/3TMcAtC.png)

* 第一栏 ( 图上编号 1 )，使用者权限。

由 10 个字元组成，

第一个字元代表档案型态 (`-` 为档案，`d` 为目录，`l` 为连结档案 )。

第二、三、四个字元 表示档案拥有者的存取权限。

第五、六、七个字元 表示档案拥有者所属群组成员的存取权限。

第八、九、十个字元 表示其他使用者的存取权限。

来看一个例子，drwxr-xr-x，

代表它是一个目录，

拥有者具备读、写、执行权限，

所属群组只拥有读、执行权限，

其他使用者只拥有读、执行权限。

为了更清楚，我把它整理成表格:yum:

|                |        拥有者        |      所属群组      |     其他使用者     |
|----------------|:--------------------:|:------------------:|:------------------:|
|        d       |          rwx         |         r-x        |         r-x        |
| 代表是一个目录 | 具备读、写、执行权限 | 只拥有读、执行权限 | 只拥有读、执行权限 |

它的权限分数是 755

|  身份  	| 权限 	|   分数   	|
|:------:	|:----:	|:--------:	|
|  owner 	|  rwx 	| 4+2+1 =7 	|
|  group 	|  r-x 	| 4+0+1 =5 	|
| others 	|  r-x 	| 4+0+1 =5 	|

* 第二栏 ( 图上编号 2 )，档案数量。

* 第三栏 ( 图上编号 3 )，拥有者。

* 第四栏 ( 图上编号 4 )，群组。

* 第五栏 ( 图上编号 5 )，档案大小。

* 第六栏 ( 图上编号 6 )，档案建立时间。

* 第七栏 ( 图上编号 7 )，档案名称。

ls 使用时间排序

```cmd
ls -t
```

列出特定档案 ( 列出为 .py 的档案 )

```cmd
ls *.py
```

`-h` 参数，使用 KB、MB、GB 单位显示档案或目录大小。

```cmd
ls -l -h
```

显示全部的档案 (包含隐藏档)

```cmd
ls -a
```

也可以使用

```cmd
ls -al
```

可以直接列出资料夹内的内容

```cmd
ls Downloads
```

像是在 home 底下, 直接列出 Downloads 内容

![alt tag](https://i.imgur.com/Dal7aSn.png)

sort

```cmd
ls -S
```

将输出结果写到档案裡

```cmd
ls -lS > file.txt
```

## su

切换不同的 user

```cmd
su <username>
```

## sudo

增加新的 user

```cmd
sudo useradd <username>
```

设定 user 的 password

```cmd
sudo passwd <username>
```

删除 user

```cmd
sudo userdel <username>
```

增加新的 group

```cmd
sudo groupadd <groupname>
```

删除 group

```cmd
sudo groupdel <groupname>
```

增加 user 到 group 中

```cmd
sudo usermod -g <groupname> <username>
```

查看所有 user

```cmd
sudo cat /etc/passwd
```

查看所有 group

```cmd
sudo cat /etc/group
```

不知道大家有没有这个困扰, 就是每次都要打上自己的密码很麻烦:expressionless:

这边提供一个方法给各位, 但还是要小心一点, 就是 `-S` 这个指令.

```text
The -S (stdin) option causes sudo to read the password from
the standard input instead of the terminal device.
```

简单说, 就是先打上你自己的密码, 这样就不用再打一次了, 以下举例

```cmd
echo YourPwd | sudo -S groupadd <groupname>
```

## chmod

[Youtube Tutorial - Linux 教学 - chmod](https://youtu.be/qwk4Pzgtf2I)

chmod 为 change mode 的缩写.

改变档案权限

```cmd
chmod XXX filename
```

举个例子，将权限设为 rw-rw-r--，

|  身份  	| 权限 	|   分数   	|
|:------:	|:----:	|:--------:	|
|  owner 	|  rw- 	| 4+2+0 =6 	|
|  group 	|  rw- 	| 4+2+0 =6 	|
| others 	|  r-- 	| 4+0+0 =4 	|

```cmd
chmod 664 README.md
```

常用修改权限的指令

```cmd
# 只有拥有者 owner 有读和写的权限
sudo chmod 600 ×××
```

```cmd
# 拥有者 owner 有读和写的权限，group，others 只有读的权限
sudo chmod 644 ×××
```

```cmd
# 拥有者 owner 有读和写以及执行的权限
sudo chmod 700 ×××
```

```cmd
# 拥有者 owner，group，others 都有读和写的权限
sudo chmod 666 ×××
```

```cmd
# 拥有者 owner，group，others 都有读和写以及执行的权限，基本上就是全开
sudo chmod -R 777 xxx
```

`-r` `-R` 代表 recursive 递迴 ( 目录底下所以档案包含子目录都会变更 )，

还有一种方法是使用 符号 来改变权限，

在介绍之前，先看下方的表格 :wink:

|       | u = user  |          |             |              |
|-------|-----------|----------|-------------|--------------|
|       | g = group | + (增加) | r = read    |              |
| chmod |           | - (移除) | w = write   | 档案或资料夹 |
|       | o = other | = (设定) | x = execute |              |
|       | a = all   |          |             |              |

举个例子，将 hello 权限设为 rw-rw-r--，

|  拥有者(u)  	| 所属群组(g) 	|   其他使用者(o)   	|
|:------:	|:----:	|:--------:	|
|  rw- 	|  rw- 	| r-- 	|

```cmd
chmod ug=rw,o=r hello
```

![alt tag](https://i.imgur.com/QgNuNel.png)

再举个例子，将 hello 权限设为 rwxr-xr–-，

```cmd
chmod u=rwx,g=rx,o=r hello
```

![alt tag](https://i.imgur.com/WlX8wPL.png)

接著假设我希望把 可执行的权限(x) 加上去 (全部人及群组都加上)

```cmd
chmod a+x hello
```

![alt tag](https://i.imgur.com/KLiwPXX.png)

移除所有人 可执行的权限(x)

```cmd
chmod a-x hello
```

你会发现大家的 可执行的权限(x) 都消失了

![alt tag](https://i.imgur.com/O8gh3Is.png)

相信经过这一连串的练习，大家肯定了解了，

如果不懂，多看几遍:satisfied:

## chown

修改档案或目录的拥有者与群组。

修改档案或目录的拥有者

```cmd
# 将 README.md ( 档案 ) 的拥有者改为 twtrubiks ( 使用者 )
chown twtrubiks README.md
```

修改档案或目录的群组

```cmd
# 将 README.md ( 档案 ) 的群组改为 twtrubiksgroup ( 群组 )
chown :twtrubiksgroup README.md
```

同时修改档案或目录的拥有者和群组

```cmd
# 将 README.md ( 档案 ) 的拥有者改为 twtrubiks ( 使用者 ) 以及
# 将 README.md ( 档案 ) 的群组改为 twtrubiksgroup ( 群组 )
chown twtrubiks:twtrubiksgroup README.md
```

## ln

[Youtube Tutorial - Linux 指令教学 - ln (Symbolic Link)](https://youtu.be/jdZsO2GAf2I)

有两种, 分别为 hard link 和 Symbolic link ( soft link ),

先介绍 hard link，注意，hard link not allowed for directory。

```cmd
ln /home/twtrubiks/Downloads/odoo-git/README.md
```

![alt tag](https://i.imgur.com/ioJXBRw.png)

hard link 特性为不管删除哪一个档案，档案都会被保留。除非你把最后一个档案也删除，

换个方式说，一个档案的 hard link 和本来的档案其实没有任何实质上的区别。

hard link 不允许资料夹，只允许档案。

symbolic link，也称 soft link，基本上它类似于 Windows 中的捷径:smile:

```cmd
ln -s /home/twtrubiks/Downloads/odoo-git/ dir-link
```

![alt tag](https://i.imgur.com/JGhlQZd.png)

当某个档案的的本体被删除后，它的 symbolic link 就无法读取到这个档案了，

一个档案的 symbolic link 和档案的本体是不同的两个东西。

symbolic link 允许档案和资料夹。

## zip unzip

zip 3.0 已经会保存档案的 permissions and ownership.

```cmd
sudo apt-get install zip unzip
```

zip

```cmd
zip -r <压缩后的档名> <压缩的档案>
zip -r file.zip file
```

unzip

```cmd
unzip <解压缩的档案> -d <解压缩的目标资料夹>
unzip file.zip -d zip_extract
```

如果希望直接解压缩到当前的目录，可以直接使用 `.`

```cmd
unzip file.zip -d .
```

## tar

tar **会**保存档案的 permissions and ownership.

压缩 `.tar` format

```cmd
tar cvf filename.tar source-folder
```

解压缩 `.tar` format

```cmd
tar xvf filename.tar
```

## unrar

```cmd
sudo apt-get install unrar
```

将 filename.rar 解压缩到目录底下

```cmd
unrar e filename.rar
```

列出 filename.rar 的资料

```cmd
unrar l filename.rar
```

测试 filename.rar 是否完整且正确

```cmd
unrar t filename.rar
```

## wget

下载工具

```cmd
sudo apt-get install wget
```

下载 URL 指令

```cmd
wget http://ftp.gnu.org/gnu/wget/wget-1.20.3.tar.gz
```

指定档名，请加上 `-O`

```cmd
wget -O wget.tar.gz http://ftp.gnu.org/gnu/wget/wget-1.20.3.tar.gz
```

## scp

全名为 Securely Copy,

这个方法适用于 Linux 和 Linux 之间互传档案，也适用于  Linux 和 Windows 之间互传档案，

假设，Linux ip 为 192.168.56.101，查看 ip 指令如下，

```cmd
ip addr show
```

![alt tag](https://i.imgur.com/AlAeRoD.png)

确认有安装 openssh-server

```cmd
sudo apt-get install openssh-server
```

使用 `ssh localhsot` 测试

![alt tag](https://i.imgur.com/nYo5NNn.png)

一切正常之后。

从 Windows 上传送档案给 Linux ( ip 为 192.168.56.101 )，

在 Windows 上的 cmd 执行以下指令，

```cmd
scp -rp 档案 linux的使用者@ip:目标路径
```

`-r` 代表 recursive.

`-p` 代表 保存原始档案的内容 (Preserves modification).

```cmd
scp -rp file twtrubiks@192.168.56.101:/home/twtrubiks
```

![alt tag](https://i.imgur.com/0nBrt00.png)

接下来，从 Linux 上拿档案回 Windows

```cmd
scp -P 22 linux的使用者@ip:目标路径 存放的目标位置
```

`-P` 代表明确指定连接的 port (remote host).

```cmd
scp -P 22 twtrubiks@192.168.56.101:/home/twtrubiks/linux_file.md .
```

`.` 代表当下目前路径 ( 也可以指定其他的路径 )。

![alt tag](https://i.imgur.com/aMnNlGI.png)

Linux 之间的传送也是相同的道理:smile:

## mv

[Youtube Tutorial - Linux 指令教学 - mv](https://youtu.be/VhyzaEaGnL8)

move ( rename ) files，**移动档案**或是**重新命名档案**。

修改 资料夹 or 档案 档名

```cmd
mv folder folder-new
mv README.md README_MV.md
```

移动档案

```cmd
mv README.md /examples
```

```cmd
mv file.md example/
```

其他的参数说明(参数可以多个一起使用)，

互动模式 , CLI 会询问你是否 overwriting files

```cmd
mv -i source_file path_to_destination/
```

只更新来源资料夹和目的地不同的档案

```cmd
mv -u source_file path_to_destination/
```

## rm

[Youtube Tutorial - Linux 指令教学 - rm](https://youtu.be/JqKjBZMXn_I)

删除档案

```cmd
rm file.md
```

删除资料夹

```cmd
rm -rf mydir
```

`-r` 代表使用 recursive 递迴删除。 ( 会将目录内所有档案删除 )

`-f` 代表强制删除 ( 不会跳出警告 )。

或是使用 rmdir 指令，

```cmd
rmdir mydir_name
```

不过要注意，被移除的资料夹裡面必须是空的，否则回无法移除。

删除特定的副档名，

```cmd
rm -f *.zip
```

也可以这样

```cmd
rm -f *demo.zip
```

## cp

[Youtube Tutorial - Linux 指令教学 - cp](https://youtu.be/ORl0YUGY728)

複製资料夹

```cmd
cp -r path_to_source/ path_to_destination/
```

`-r` `-R` 代表 recursive 递迴，

如果 path_to_destination 不存在，会自动建立 ;

如果存在，则直接使用。

只想複製资料夹底下的全部内容，

```cmd
cp -r dir_1/. dir_2
cp -r dir_1/. .
```

`.` 代表资料夹内的东西，也可以代表目前所在的地方。

有时候会希望複製时可以保存当时的权限，所以会加上 `-p`。

```cmd
cp -r --preserve=all path_to_source/ path_to_destination/
```

`-p` `--preserve` 代表一同複製当下的权限以及拥有者之类的。

其他的参数说明(参数可以多个一起使用)，

互动模式 , CLI 会询问你是否 overwriting files

```cmd
cp -i source_file path_to_destination/
```

不询问 , 直接 overwriting files

```cmd
cp -n source_file path_to_destination/
```

只更新来源资料夹和目的地不同的档案

```cmd
cp -u source_file path_to_destination/
```

印出资讯

```cmd
cp -v source_file path_to_destination/
```

## find

查询档案

找档案或资料夹

```cmd
sudo find / -name "dir-name"
sudo find / -name "file-name"
sudo find / -name "*.conf"
```

在当前目录下寻找档名为 README.md

```cmd
find . -name README.md
```

## source

source 指令通常用于刚修改的初始化文件, 让它立刻生效, 不必重开机(或登出再登入),

以下例子,

```cmd
source demo.sh
```

在当下的 shell 内去读取, 执行 demo.sh, 而 demo.sh **需要**有执行权限

(执行权限代表 `chmod +x demo.sh`)

source 指令也可以简写为 `.`

```cmd
. demo.sh
```

## sh or bash

使用 `sh` or `bash`执行时, **不需要**有执行权限.

(执行权限代表 `chmod +x demo.sh`)

```cmd
sh demo.sh
bash demo.sh
```

## ./

直接使用 `./` 执行, **需要**有执行权限.

(执行权限代表 `chmod +x demo.sh`)

当你执行

```cmd
./demo.sh

chmod +x demo.sh
./demo.sh
```

你会发现跳出类似讯息 `bash: ./demo.sh: Permission denied`,

修正方法如下,

```cmd
chmod +x demo.sh
./demo.sh
```

## where

寻找路径，

举例，寻找 python3 路径

```cmd
where python3
which python3
whereis python3
```

## tail

显示档案最后几行内容

```cmd
tail README.md
```

一次显示多个档案

```cmd
tail README_1.md README_2.md
```

指定显示档案最后 N 行内容

```cmd
tail -n 5 README.md
```

```cmd
tail README.md -n 5
```

持续显示更新内容，通常使用在 server 或看 log

```cmd
tail -f README.md
```

## file

检查档案类型

```cmd
file README.md
```

## cat

将档案内容显示在 terminal 上

```cmd
cat README.md
```

显示行数

```cmd
cat -n README.md
```

cat 也可以写入档案

```cmd
cat <<EOT >> hello_4.txt
line 1
line 2
line 3
EOT
```

## clear

clear the terminal screen ， 快捷键为 Ctrl+L

```cmd
clear
```

## grep

```cmd
# 格式
grep match_pattern file_name
```

```cmd
# 格式
grep "search name" README.md
```

也可以一次搜寻多个档案

```cmd
grep "name" README_1.md README_2.md
```

也可以使用 万用字元 `*`

```cmd
grep "print" *.py
```

搜寻当下目录资料夹内容

```cmd
grep -r "search name" .
```

case insensitive case (不区分大小写)

```cmd
grep -i "name" README_1.md
```

显示行数

```cmd
grep -n "name" README_1.md
```

## mkdir

建立资料夹

```cmd
mkdir -p dir1/dir2
```

`-p` `--parents`  代表自动建立上层目錄，如果目錄已存在则不会发生错误。

## kill

强制停止程式执行.

需要先查到程式的 PID, 使用方法如下,

```cmd
kill -9 PID
```

`-9` 立刻强制停止程式执行

## killall

killall 和 kill 的一个差别是可以使用程式名称,

不需要先找到程式的 PID,

例如想要强制停止 vlc

```cmd
killall vlc
```

## history

历史输入的指令

```cmd
history
```

```cmd
history | less
```

![alt tag](https://i.imgur.com/0YKqS3Y.png)

假设今天我不想打指令, 可以直接输入 `!`+ 数字, 会自动执行该指令.

```cmd
!1848
```

再显示一次最后输入的指令 (建议加上 sudo)

```cmd
!!
```

## echo

在 shell 中印出 shell 的值，

设定 EDITOR

```cmd
export EDITOR=vim
```

查看目前的 EDITOR，

```cmd
echo $EDITOR
```

查看目前的 shell，

```cmd
echo $SHELL
```

echo 也可以写入档案，

方法一

```cmd
echo "line 1" >> hello_1.txt
```

方法二 ( 写入多行 )

```cmd
echo "line 1
line 2" >> hello_2.txt
```

方法三 ( 写入多行 )

```cmd
{
    echo 'line1;'
    echo 'line2;'
} >> hello_3.txt
```

## du

[Youtube Tutorial - Linux 指令教学 - du(Disk Usage)](https://youtu.be/JZZoJnasnHE)

du 这个指令是 Disk Usage 的缩写,

在开始介绍 du 之前, 先来看一个例子,

使用 `ls -l -h` 观察 debian 资料夹

![alt tag](https://i.imgur.com/lXgxQop.png)

但是如果你进到资料夹裡面, 你会发现它明明有 17GB,

可是为什麽在资料夹外层看的时候却只有 4KB:question:

![alt tag](https://i.imgur.com/eOTKWJj.png)

原因是 `ls -l -h` 不会显示资料夹实际的大小, 只会显示所谓的 meta information,

所以, 如果你要看实际的大小, 比较好的方法是使用接下来要介绍的 `du` 指令:smile:

查看 du 指令说明

```cmd
du --help
```

![alt tag](https://i.imgur.com/IQLpqnC.png)

```cmd
-s, --summarize       display only a total for each argument
                      (Equivalent to -d 0)

-h, --human-readable  print sizes in human readable format (e.g., 1K 234M 2G)
    --inodes          list inode usage information instead of block usage

-c, --total           produce a grand total

-d, --max-depth=N     print the total for a directory (or file, with --all)
                      only if it is N or fewer levels below the command
                      line argument;  --max-depth=0 is the same as --summarize


-a, --all             write counts for all files, not just directories
    --apparent-size   print apparent sizes, rather than disk usage; although
                      the apparent size is usually smaller, it may be
                      larger due to holes in ('sparse') files, internal
                      fragmentation, indirect blocks, and the like
```

以下两个指令功能是相同的

```cmd
du -sh *
du --summarize --human-readable *
```

使用刚刚的那个例子, 在资料夹的外层就能看到实际的资料夹大小

![alt tag](https://i.imgur.com/hHjxXDx.png)

也可以搭配 `-d` 使用, 资料夹的层数, 看下面的例子你就会懂了

```cmd
du -d 2 -h
```

![alt tag](https://i.imgur.com/NdbqvSz.png)

## truncate

[Youtube Tutorial - Linux 指令教学 - truncate](https://youtu.be/w2pwD1AOhPI)

Shrink or extend the size of each FILE to the specified size.

truncate 指令可以将一个档案缩小或是增加大小.

开始介绍这个指令前, 先来看看适用的情境:smile:

有时候我们可能会希望把一个档案的大小归 0, 也就是将档案的内容全部删除,

但是要保留档案, 这时候就很适合使用这个指令:smirk:

那你可能会问我, 为什麽不直接删除档案再建立一个一模一样的就好:question:

原因很简单, 在 linux 的世界中, 档案是有权限的, 所以你还要去注意新建立

出来的档案, 权限是否和之前的一模一样( 否而可能会导致错误 ), 所以比较简单

的方法会是使用 truncate 这个指令, 它将只会清除内容 ( 档案大小为 0 ),

其馀的都保持原来的状态。

查看 truncate 指令说明,

```cmd
truncate --help
```

![alt tag](https://i.imgur.com/rOXR79N.png)

```cmd
Mandatory arguments to long options are mandatory for short options too.
-c, --no-create        do not create any files
-o, --io-blocks        treat SIZE as number of IO blocks instead of bytes
-r, --reference=RFILE  base size on RFILE
-s, --size=SIZE        set or adjust the file size by SIZE bytes
    --help     display this help and exit
    --version  output version information and exit

SIZE may also be prefixed by one of the following modifying characters:
'+' extend by, '-' reduce by, '<' at most, '>' at least,
'/' round down to multiple of, '%' round up to multiple of.
```

使用以下的范例来说明,

假设现在有一个 `demo.txt` 的档案 (如下)

![alt tag](https://i.imgur.com/nWoxmhn.png)

使用 truncate 将 `demo.txt` 放大到 1M,

```cmd
truncate -s 1M demo.txt
```

![alt tag](https://i.imgur.com/jeZ6Rkp.png)

注意 `du -ah` 是显示 apparent sizes (不是 disk usage ), 所以大小不会改变.

如果你去打开 `demo.txt`, 你会发现被塞了很多东西, 因为大小要变成 1M:smile:

![alt tag](https://i.imgur.com/mgQNkNn.png)

相反的, 现在使用 truncate 将 `demo.txt` 缩小到 0,

![alt tag](https://i.imgur.com/9czKNL5.png)

如果你去打开 `demo.txt`, 你会发现裡面的资料都消失了.

![alt tag](https://i.imgur.com/vmLwz96.png)

truncate 这个指令就非常适合去清除 log, 将 log 大小归 0, 其馀保持原样.

清空所有日志文件

```cmd
sudo truncate -s 0 /var/log/**/*.log
```

## 不用密码远端登入 Linux

### 方法一

先确认 Linux 上有 `.ssh` 资料夹，如果没有，

请使用以下指令建立 ( 以及权限 )，

```cmd
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

每次远端登入 Linux 都需要密码很麻烦，有没有可以透过其他的方式不要输入密码:question:

有，先在本机电脑使用 `ssh-keygen` 产生金钥

```cmd
ssh-keygen
```

接著你会有两把 key ,

id_rsa.pub：公开金钥（public key）: 要放在远端的 Linux 伺服器上。

id_rsa：私密金钥（private key）: 自己保护好，等同于你的 Linux 密码。

先把自己本机 `id_rsa.pub` 传到远端的 Linux server 上，

接著在 Linux 上执行以下指令

( 把公开金钥放到 `~/.ssh/authorized_keys` )

```cmd
cat id_rsa.pub >> ~/.ssh/authorized_keys
```

让我们来测试看看吧:smile:

`ssh twtrubiks@192.168.56.101`

![alt tag](https://i.imgur.com/97ndrP8.png)

不用输入密码就可以登入了:thumbsup:

### 方法二

也可以透过 `ssh-copy-id` 来完成,

```cmd
ssh-copy-id -i ~/.ssh/id_rsa.pub twtrubiks@192.168.56.101
```

![alt tag](https://i.imgur.com/eR5TIJ3.png)

其实不管是方法一还是方法二, 都只是把 key 加入 `/home/<user>/.ssh`

裡的 `authorized_keys` 而已:smile:

![alt tag](https://i.imgur.com/j4BRI1J.png)

## root 使用者登入远端 Linux

注意, 通常不会这样做:exclamation:

虽然这个方法可以比较危险，但我还是说明一下:joy:

先设定 root 密码，执行以下指令

```cmd
sudo passwd root
```
![alt tag](https://i.imgur.com/dsSrBJX.png)

再使用 `su -` 测试 root 密码

![alt tag](https://i.imgur.com/nmU9cgk.png)

测试完成后，再执行 `ssh root@192.168.56.101`

![alt tag](https://i.imgur.com/BF4JWnO.png)

你会发现一直出现 Permission denied, please try again.

这时候要到 Linux 上修改 root 的 ssh 设定，

```cmd
sudo vim /etc/ssh/sshd_config
```

找到 PermitRootLogin without-password

![alt tag](https://i.imgur.com/NO85xui.png)

修改成 PermitRootLogin yes

![alt tag](https://i.imgur.com/xpyfpwW.png)

最后记得一定要重新启动 sshd 让它生效 (或是重开机)

```cmd
systemctl restart sshd
```

成功使用 root 登入了:satisfied:

![alt tag](https://i.imgur.com/Au4wt32.png)

## 正确保护 server

比较安全的作法通常是关闭 `PermitRootLogin` 以及 `PasswordAuthentication`,

然后只启用 `PubkeyAuthentication` 的方式, 但这边要注意, 一定要把你的 key 放到

server 上, 否则如果设定完不小心退出, 就很麻烦:expressionless:

( 因为不能用密码登入, 又忘记将 key 放到 server 中 )

修改

```cmd
sudo vim /etc/ssh/sshd_config
```

禁止 root 登入, 将 `PermitRootLogin` 设为 `no`,

![alt tag](https://i.imgur.com/W6KBiXS.png)

禁止使用 password 登入, 将 `PasswordAuthentication` 设为 `no`,

![alt tag](https://i.imgur.com/L9WPRq5.png)

允许 `PubkeyAuthentication`, 设为 `yes`

![alt tag](https://i.imgur.com/iYyaAQ8.png)

补充, 还有一种是使用 PAM Authentication `UsePAM` ( AWS 就是使用这种方式 )

![alt tag](https://i.imgur.com/g3MdnKC.png)

如同说明, 如果希望只使用 PAM Authentication, 也可以把  `ChallengeResponseAuthentication` 设为 `no`.

最后记得重新启动 sshd 让它生效 (或是重开机)

```cmd
systemctl restart sshd
```

## 其他资讯

系统讯息

```cmd
uname -a
```

查看 cpu

```cmd
cat /proc/cpuinfo
```

查看全部的 ram 大小

```cmd
grep MemTotal /proc/meminfo
```

查看可用的 ram

```cmd
grep MemFree /proc/meminfo
```

也可以使用

```cmd
free -m
```

查看硬碟空间 ( disk free space, Human Readable )

```cmd
df -h
```

所有硬碟的讯息 ( List Drives and Mounts )

```cmd
lsblk
```

显示所有装置设备的资讯 ,UUID

```cmd
blkid
```

![alt tag](https://i.imgur.com/8a6V7fq.png)

目前硬碟 mount 状态 (开机自动挂载)

```cmd
cat /etc/fstab
```

![alt tag](https://i.imgur.com/79WxI5w.png)

查看所有 pci，

```cmd
lspci
```

查看所有 usb，

```cmd
lsusb
```

也可以搭配 grep 搜寻，只搜寻包含 VirtualBox 的内容，

```cmd
lsusb | grep VirtualBox
```

查看 ip，

```cmd
ip a
```

external ip Address ( 对外的 ip )，`ifconfig.me` 是一个网站，

```cmd
curl ifconfig.me
```

查看电脑目前资讯，CPU RAM 等等

```cmd
top
```

推荐 `htop` ( 资讯更清楚 ), 建议参考 [htop-tutorial](https://github.com/twtrubiks/linux-note/tree/master/htop-tutorial) - htop tutorial:thumbsup:

透过 xrandr 修改萤幕的亮度，

先查看萤幕的 name，

```cmd
xrandr | grep " connected" | cut -f1 -d " "
```

设定亮度 ( 0 - 1 )，

```cmd
xrandr --output DP-1 --brightness 0.7
```

透过 systemd-analyze 查看 Linux 启动时间

```cmd
systemd-analyze
```

查看细节

```cmd
systemd-analyze blame
```

###  如何进入 tty 介面

有时候开机时可能因为驱动没装, 导致卡在黑屏的画面,

这时候就可以进入 tty 介面安装驱动(需要的东西),

进入 tty 快捷建 `Ctrl+Alt+F2`

退出 tty 快捷建 `Ctrl+Alt+F7` 或 `Ctrl+Alt+(F2/F3/F4)`

### swap

如果你的本机 ram 够大, 可以考虑把它关掉,

(有些 distro 预设是打开的 )

关闭 swap

```cmd
sudo swapoff -a
```

打开 swap

```cmd
sudo swapon -a
```

## install packages

install

```cmd
sudo apt-get install xxx
```

如果只有 `.deb` 档案, 可以使用以下的方式

```cmd
sudo apt install ./xxx.deb
```

update list ( 更新 packages 的最新资讯及列表 )

```cmd
sudo apt-get update
```

upgrade ( 更新软体到最新的版本 )

```cmd
sudo apt-get upgrade
```

只更新特定的软体, 举例 vivaldi,

先更新再安装就是更新软体的意思,

```cmd
sudo apt update && sudo apt install vivaldi-stable
```

remove

```cmd
sudo apt-get --purge autoremove xxxx
```

清理不需要的 packages ( `.deb` )

```cmd
sudo apt autoclean
```

清除不需要的依赖

```cmd
sudo apt autoremove
```

## ppa add/remove

add

```cmd
sudo apt-add-repository ppa:xxxx
sudo apt update
```

remove

```cmd
sudo add-apt-repository -r ppa:xxxx
sudo apt update
```

## 无法进入 bios

```cmd
sudo vim /etc/default/grub
```

你应该会看到类似的画面

```text
GRUB_DEFAULT="0"
GRUB_TIMEOUT_STYLE="hidden"
GRUB_TIMEOUT=10   <<<<<<
GRUB_DISTRIBUTOR="`lsb_release -i -s 2> /dev/null || echo Debian`"
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.autosuspend=-1"
GRUB_CMDLINE_LINUX=""
```

将 GRUB_TIMEOUT 的时间改长一点, 因为有可能是太快了, 导致来不及按:disappointed_relieved:

也请记得要再执行以下的指令更新

```cmd
sudo update-grub
```

## remove snap

在 ubuntu 中, 预设会帮你安装 snap, 但有些人不太喜欢,

因为他是私人公司为维护的:smile:

以下附上移除 snap 指令,

```cmd
sudo rm -rf /var/cache/snapd/
sudo apt autoremove --purge snapd gnome-software-plugin-snap
rm -rf ~/snap
```

## remove ubuntu 不需要的软体

使用 ubuntu 18.04 当范例

```cmd
sudo apt purge deja-dup thunderbird rhythmbox ubuntu-web-launchers whoopsie
```

`deja-dup` ubuntu 内建备份软体.

`whoopsie` ubuntu 错误报告.

如果不小心删除到依赖 (把 settings 删除), 可使用以下指令将它装回来

```cmd
sudo apt-get install gnome-control-center
```

## Linux 档案系统(结构)

[Linux-File-System/Structure](https://github.com/twtrubiks/linux-note/tree/master/linux-file-system-structure)

## 更多文章

[zsh-tmux-tutorual](https://github.com/twtrubiks/linux-note/tree/master/zsh-tmux-tutorual) - 超好用 zsh 以及 tmux。

[zsh-powerlevel10k-tutorual](https://github.com/twtrubiks/linux-note/tree/master/zsh-powerlevel10k-tutorual) - zsh 搭配 Powerlevel10k, 超漂亮 terminal。

[vim-shortcuts](https://github.com/twtrubiks/linux-note/tree/master/vim-shortcuts) - 纪录 vim 快捷键

[imwheel-tutorual](https://github.com/twtrubiks/linux-note/tree/master/imwheel-tutorual) - 改善 linux 滑鼠滚动问题。

[shutter-tutorual](https://github.com/twtrubiks/linux-note/tree/master/shutter-tutorual) - Shutter is an excellent image capture tool.

[systemctl-tutorial](https://github.com/twtrubiks/linux-note/tree/master/systemctl-tutorial) - systemctl 命令是系统服务管理指令

[crontab-tutorual](https://github.com/twtrubiks/linux-note/tree/master/crontab-tutorual) - Linux Crontab

[mount-disks-at-system-startup-on-ubuntu](https://github.com/twtrubiks/linux-note/tree/master/mount-disks-at-system-startup-on-ubuntu)

[chinese-input-methods-on-ubuntu](https://github.com/twtrubiks/linux-note/tree/master/chinese-input-methods-on-ubuntu) - ubuntu 如何安装中文输入法

[hime-tutorial](https://github.com/twtrubiks/linux-note/tree/master/hime-tutorial) - Linux 中更像微软更好用的中文输入法 hime

[gnome-tweaks](https://github.com/twtrubiks/linux-note/tree/master/gnome-tweaks) - Ubuntu 安装 GNOME Tweak tool

[htop-tutorial](https://github.com/twtrubiks/linux-note/tree/master/htop-tutorial) - htop tutorial:thumbsup:

[neofetch-tutorial](https://github.com/twtrubiks/linux-note/tree/master/neofetch-tutorial) - command-line system information tool:thumbsup:

[copyq-tutorial](https://github.com/twtrubiks/linux-note/tree/master/copyq-tutorial) - 剪贴簿:thumbsup:

[krusader-tutorial](https://github.com/twtrubiks/linux-note/tree/master/krusader-tutorial) - file manager

[fail2ban-tutorial](https://github.com/twtrubiks/linux-note/tree/master/fail2ban-tutorial) - 让 server 更安全:smile:

[how-to-change-login-lock-background-ubuntu](https://github.com/twtrubiks/linux-note/tree/master/how-to-change-login-lock-background-ubuntu) - 修改Ubuntu 登入/锁屏时的背景

[grub-customizer-tutorial](https://github.com/twtrubiks/linux-note/tree/master/grub-customizer-tutorial) - 安装 grub-customizer

[enable-ubuntu-remote-tutorial](https://github.com/twtrubiks/linux-note/tree/master/enable-ubuntu-remote-tutorial) - 如何在 ubuntu 启用远端桌面

[linux-nfs-server](https://github.com/twtrubiks/linux-note/tree/master/linux-nfs-server) - 如何在 ubuntu 启用 NFS Server

## 状况排除

[fix_could_not_get_lock_dpkg_ubuntu](https://github.com/twtrubiks/linux-note/tree/master/fix_could_not_get_lock_dpkg_ubuntu) - 修正 `E: Could not get lock /var/lib/dpkg/lock` Error

[fix_network_manager_bug_ubuntu_18_04](https://github.com/twtrubiks/linux-note/tree/master/fix_network_manager_bug_ubuntu_18_04) - 修正 ubuntu 18.04 网路连线设定遗失问题.

## 其他

[Windows -> Linux 优缺点](https://github.com/twtrubiks/linux-note/tree/master/linux-is-better-than-windows) - Windows -> Linux 优缺点

[ubuntu-18-04-on-Lenovo-X1-Carbon-6](https://github.com/twtrubiks/linux-note/tree/master/ubuntu-18-04-on-Lenovo-X1-Carbon-6)

[透过 VirtualBox 安装 Ubuntu 19.10 （以及一些个人想法）](https://youtu.be/lI1EMwhW6lE)

[VirtualBox 6.1 Linux 安装 Guest Addition - 全屏教学](https://youtu.be/PMw6FPtbbaU)

[alternative-software](https://github.com/twtrubiks/linux-note/tree/master/alternative-software) - windows -> Linux 替代软体

[rclone-tutorial](https://github.com/twtrubiks/linux-note/tree/master/rclone-tutorial) - rclone 是一套很棒的文件同步管理工具

[stacer-tutorial](https://github.com/twtrubiks/linux-note/tree/master/stacer-tutorial) - Linux System Optimizer and Monitoring

[cmatrix-tutorial](https://github.com/twtrubiks/linux-note/tree/master/cmatrix-tutorial) - 超酷又超没用的 cmatrix

[sl-tutorial](https://github.com/twtrubiks/linux-note/tree/master/sl-tutorial) - sl 火车开起来

[linux-tlp-tutorial](https://github.com/twtrubiks/linux-note/tree/master/linux-tlp-tutorial) - Linux Advanced Power Management

[speedtest-cli-tutorial](https://github.com/twtrubiks/linux-note/tree/master/speedtest-cli-tutorial) - Linux speedtest-cli tutorial

[gnirehtet-tutorial-tutorial](https://github.com/twtrubiks/linux-note/tree/master/gnirehtet-tutorial) - 透过电脑让手机上网

[scrcpy-tutorial-tutorial](https://github.com/twtrubiks/linux-note/tree/master/scrcpy-tutorial) - 使用电脑控制手机

[linux-tlp-tutorial](https://github.com/twtrubiks/linux-note/tree/master/linux-tlp-tutorial) - Linux Advanced Power Management

[variety-tutorual](https://github.com/twtrubiks/linux-note/tree/master/variety-tutorual) - variety 自动更换桌面

[arandr-tutorual](https://github.com/twtrubiks/linux-note/tree/master/arandr-tutorual) - arandr 设定多萤幕工具

[linux-virtualbox-ssh-tutorial](https://github.com/twtrubiks/linux-note/tree/master/linux-virtualbox-ssh-tutorial) - 在 Linux 中设定 VirtualBox 把玩 ssh

[linux-virtualbox-problem](https://github.com/twtrubiks/linux-note/tree/master/linux-virtualbox-problem) - 在 Linux 中设定 VirtualBox - 常见问题

[Linux 桌面环境 Desktop Environment](https://github.com/twtrubiks/linux-note/tree/master/linux-de)

[认识 Linux 发行版 distribution](https://github.com/twtrubiks/linux-note/tree/master/linux-distro)

[KDE setting](https://github.com/twtrubiks/linux-note/tree/master/kde-settings)

## Reference

* [Linux Command 命令列指令与基本操作入门教学](https://blog.techbridge.cc/2017/12/23/linux-commnd-line-tutorial/)

## Donation

文章都是我自己研究内化后原创，如果有帮助到您，也想鼓励我的话，欢迎请我喝一杯咖啡:laughing:

绿界科技ECPAY ( 不需注册会员 )

![alt tag](https://payment.ecpay.com.tw/Upload/QRCode/201906/QRCode_672351b8-5ab3-42dd-9c7c-c24c3e6a10a0.png)

[赞助者付款](http://bit.ly/2F7Jrha)

欧付宝 ( 需注册会员 )

![alt tag](https://i.imgur.com/LRct9xa.png)

[赞助者付款](https://payment.opay.tw/Broadcaster/Donate/9E47FDEF85ABE383A0F5FC6A218606F8)

## 赞助名单

[赞助名单](https://github.com/twtrubiks/Thank-you-for-donate)

## License

MIT license
