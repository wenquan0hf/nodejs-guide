# 进程介绍

NodeJS 可以感知和控制自身进程的运行环境和状态，也可以创建子进程并与其协同工作，这使得 NodeJS 可以把多个程序组合在一起共同完成某项工作，并在其中充当胶水和调度器的作用。本章除了介绍与之相关的 NodeJS 内置模块外，还会重点介绍典型的使用场景。

我们已经知道了 NodeJS 自带的 fs 模块比较基础，把一个目录里的所有文件和子目录都拷贝到另一个目录里需要写不少代码。另外我们也知道，终端下的cp命令比较好用，一条`cp -r source/* target`命令就能搞定目录拷贝。那我们首先看看如何使用 NodeJS 调用终端命令来简化目录拷贝，示例代码如下：

```
var child_process = require('child_process');
var util = require('util');

function copy(source, target, callback) {
    child_process.exec(
        util.format('cp -r %s/* %s', source, target), callback);
}

copy('a', 'b', function (err) {
    // ...
});
```

从以上代码中可以看到，子进程是异步运行的，通过回调函数返回执行结果。