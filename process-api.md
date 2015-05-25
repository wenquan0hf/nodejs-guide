# API 走马观花

我们先大致看看 NodeJS 提供了哪些和进程管理有关的 API。这里并不逐一介绍每个 API 的使用方法，官方文档已经做得很好了。

## Process

官方文档： [http://nodejs.org/api/process.html](http://nodejs.org/api/process.html)

任何一个进程都有启动进程时使用的命令行参数，有标准输入标准输出，有运行权限，有运行环境和运行状态。在 NodeJS 中，可以通过 process 对象感知和控制 NodeJS 自身进程的方方面面。另外需要注意的是，process 不是内置模块，而是一个全局对象，因此在任何地方都可以直接使用。

## Child Process

官方文档： [http://nodejs.org/api/child_process.html](http://nodejs.org/api/child_process.html)

使用 child_process 模块可以创建和控制子进程。该模块提供的 API 中最核心的是`.spawn`，其余 API 都是针对特定使用场景对它的进一步封装，算是一种语法糖。

## Cluster

官方文档： [http://nodejs.org/api/cluster.html](http://nodejs.org/api/cluster.html])

cluster 模块是对 child_process 模块的进一步封装，专用于解决单进程 NodeJS Web 服务器无法充分利用多核 CPU 的问题。使用该模块可以简化多进程服务器程序的开发，让每个核上运行一个工作进程，并统一通过主进程监听端口和分发请求。

