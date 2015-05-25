# 命令行程序

使用 NodeJS 编写的东西，要么是一个包，要么是一个命令行程序，而前者最终也会用于开发后者。因此我们在部署代码时需要一些技巧，让用户觉得自己是在使用一个命令行程序。

例如我们用 NodeJS 写了个程序，可以把命令行参数原样打印出来。该程序很简单，在主模块内实现了所有功能。并且写好后，我们把该程序部署在 /home/user/bin/node-echo.js 这个位置。为了在任何目录下都能运行该程序，我们需要使用以下终端命令。

```
$ node /home/user/bin/node-echo.js Hello World
Hello World
```

这种使用方式看起来不怎么像是一个命令行程序，下边的才是我们期望的方式。

```
$ node-echo Hello World
```

## Linux


在 Linux 系统下，我们可以把 JS 文件当作 shell 脚本来运行，从而达到上述目的，具体步骤如下：

在 shell 脚本中，可以通过`#!`注释来指定当前脚本使用的解析器。所以我们首先在 node-echo.js 文件顶部增加以下一行注释，表明当前脚本使用 NodeJS 解析。

```
 #! /usr/bin/env node
```

NodeJS 会忽略掉位于 JS 模块首行的`#!`注释，不必担心这行注释是非法语句。

然后，我们使用以下命令赋予 node-echo.js 文件执行权限。

```
 $ chmod +x /home/user/bin/node-echo.js
```

最后，我们在 PATH 环境变量中指定的某个目录下，例如在 /usr/local/bin 下边创建一个软链文件，文件名与我们希望使用的终端命令同名，命令如下：

```
 $ sudo ln -s /home/user/bin/node-echo.js /usr/local/bin/node-echo
```

这样处理后，我们就可以在任何目录下使用 node-echo 命令了。

## Windows

在 Windows 系统下的做法完全不同，我们得靠`.cmd`文件来解决问题。假设 node-echo.js 存放在 C:\Users\user\bin 目录，并且该目录已经添加到 PATH 环境变量里了。接下来需要在该目录下新建一个名为 node-echo.cmd 的文件，文件内容如下：

```
@node "C:\User\user\bin\node-echo.js" %*
```

这样处理后，我们就可以在任何目录下使用 node-echo 命令了。