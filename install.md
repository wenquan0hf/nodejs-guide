# 安装

## 安装程序

NodeJS 提供了一些安装程序，都可以在 nodejs.org 这里下载并安装。

Windows 系统下，选择和系统版本匹配的 .msi 后缀的安装文件。Mac OS X 系统下，选择 .pkg 后缀的安装文件。

## 编译安装

Linux 系统下没有现成的安装程序可用，虽然一些发行版可以使用 apt-get 之类的方式安装，但不一定能安装到最新版。因此 Linux 系统下一般使用以下方式编译方式安装 NodeJS。

1.确保系统下 g++ 版本在 4.6 以上，python 版本在 2.6 以上。

2.从 nodejs.org 下载 tar.gz 后缀的 NodeJS 最新版源代码包并解压到某个位置。

3.进入解压到的目录，使用以下命令编译和安装。

```
$ ./configure

$ make

$ sudo make install
```