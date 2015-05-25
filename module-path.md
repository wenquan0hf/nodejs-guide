# 模块路径解析规则

有经验的 C 程序员在编写一个新程序时首先从 make 文件写起。同样的，使用 NodeJS 编写程序前，为了有个良好的开端，首先需要准备好代码的目录结构和部署方式，就如同修房子要先搭脚手架。本章将介绍与之相关的各种知识。

## 模块路径解析规则

我们已经知道，require函数支持斜杠（/）或盘符（C:）开头的绝对路径，也支持./开头的相对路径。但这两种路径在模块之间建立了强耦合关系，一旦某个模块文件的存放位置需要变更，使用该模块的其它模块的代码也需要跟着调整，变得牵一发动全身。因此，require函数支持第三种形式的路径，写法类似于foo/bar，并依次按照以下规则解析路径，直到找到模块位置。

### 内置模块

如果传递给 require 函数的是 NodeJS 内置模块名称，不做路径解析，直接返回内部模块的导出对象，例如
require('fs')。

### node_modules 目录

NodeJS 定义了一个特殊的 node_modules 目录用于存放模块。例如某个模块的绝对路径是 /home/user/hello.js，在该模块中使用 require('foo/bar') 方式加载模块时，则 NodeJS 依次尝试使用以下路径。

```
 /home/user/node_modules/foo/bar
 /home/node_modules/foo/bar
 /node_modules/foo/bar
```

### NODE_PATH 环境变量

与 PATH 环境变量类似，NodeJS 允许通过 NODE_PATH 环境变量来指定额外的模块搜索路径。NODE_PATH 环境变量中包含一到多个目录路径，路径之间在 Linux 下使用:分隔，在 Windows 下使用;分隔。例如定义了以下 NODE_PATH 环境变量：

```
 NODE_PATH=/home/user/lib:/home/lib
```

当使用 require('foo/bar')的方式加载模块时，则 NodeJS 依次尝试以下路径。

```
 /home/user/lib/foo/bar
 /home/lib/foo/bar
```
