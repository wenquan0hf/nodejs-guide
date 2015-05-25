# 运行
打开终端，键入 node 进入命令交互模式，可以输入一条代码语句后立即执行并显示结果，例如：

```
$ node
> console.log('Hello World!');
Hello World!
```

如果要运行一大段代码的话，可以先写一个JS文件再运行。例如有以下 hello.js。

```
function hello() {
    console.log('Hello World!');
}
hello();
```

写好后在终端下键入 node hello.js 运行，结果如下：

```
$ node hello.js
Hello World!
```

## 权限问题

在 Linux 系统下，使用 NodeJS 监听 80 或 443 端口提供 HTTP(S)服务时需要 root 权限，有两种方式可以做到。

一种方式是使用 sudo 命令运行 NodeJS。例如通过以下命令运行的 server.js 中有权限使用 80 和 443 端口。一般推荐这种方式，可以保证仅为有需要的 JS 脚本提供 root 权限。

```
$ sudo node server.js
```

另一种方式是使用 chmod +s 命令让 NodeJS 总是以 root 权限运行，具体做法如下。因为这种方式让任何JS脚本都有了 root 权限，不太安全，因此在需要很考虑安全的系统下不推荐使用。

```
$ sudo chown root /usr/local/bin/node
$ sudo chmod +s /usr/local/bin/node
```
