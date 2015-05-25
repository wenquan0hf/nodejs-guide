# NPM

NPM 是随同 NodeJS 一起安装的包管理工具，能解决 NodeJS 代码部署上的很多问题，常见的使用场景有以下几种：

允许用户从 NPM 服务器下载别人编写的三方包到本地使用。

允许用户从 NPM 服务器下载并安装别人编写的命令行程序到本地使用。

允许用户将自己编写的包或命令行程序上传到 NPM 服务器供别人使用。

可以看到，NPM 建立了一个 NodeJS 生态圈，NodeJS 开发者和用户可以在里边互通有无。以下分别介绍这三种场景下怎样使用 NPM。

## 下载三方包

需要使用三方包时，首先得知道有哪些包可用。虽然 npmjs.org 提供了个搜索框可以根据包名来搜索，但如果连想使用的三方包的名字都不确定的话，就请百度一下吧。知道了包名后，比如上边例子中的 argv，就可以在工程目录下打开终端，使用以下命令来下载三方包。

```
$ npm install argv
...
argv@0.0.2 node_modules\argv
```

下载好之后，argv 包就放在了工程目录下的 node_modules 目录中，因此在代码中只需要通过 require('argv')的方式就好，无需指定三方包路径。

以上命令默认下载最新版三方包，如果想要下载指定版本的话，可以在包名后边加上`@<version>`，例如通过以下命令可下载 0.0.1 版的 argv。

```
$ npm install argv@0.0.1
...
argv@0.0.1 node_modules\argv
```

如果使用到的三方包比较多，在终端下一个包一条命令地安装未免太人肉了。因此 NPM 对 package.json 的字段做了扩展，允许在其中申明三方包依赖。因此，上边例子中的 package.json 可以改写如下：

```
{
    "name": "node-echo",
    "main": "./lib/echo.js",
    "dependencies": {
        "argv": "0.0.2"
    }
}
```

这样处理后，在工程目录下就可以使用 npm install 命令批量安装三方包了。更重要的是，当以后 node-echo 也上传到了 NPM 服务器，别人下载这个包时，NPM 会根据包中申明的三方包依赖自动下载进一步依赖的三方包。例如，使用 npm install node-echo 命令时，NPM 会自动创建以下目录结构。

```
- project/
    - node_modules/
        - node-echo/
            - node_modules/
                + argv/
            ...
    ...
```

如此一来，用户只需关心自己直接使用的三方包，不需要自己去解决所有包的依赖关系。

## 安装命令行程序

从 NPM 服务上下载安装一个命令行程序的方法与三方包类似。例如上例中的 node-echo 提供了命令行使用方式，只要 node-echo 自己配置好了相关的 package.json 字段，对于用户而言，只需要使用以下命令安装程序。

```
$ npm install node-echo -g
```

参数中的 -g 表示全局安装，因此 node-echo 会默认安装到以下位置，并且 NPM 会自动创建好 Linux 系统下需要的软链文件或 Windows 系统下需要的`.cmd`文件。

```
- /usr/local/               # Linux系统下
    - lib/node_modules/
        + node-echo/
        ...
    - bin/
        node-echo
        ...
    ...

- %APPDATA%\npm\            # Windows系统下
    - node_modules\
        + node-echo\
        ...
    node-echo.cmd
    ...
```

## 发布代码

第一次使用 NPM 发布代码前需要注册一个账号。终端下运行 npm adduser，之后按照提示做即可。账号搞定后，接着我们需要编辑 package.json 文件，加入 NPM 必需的字段。接着上边 node-echo 的例子，package.json 里必要的字段如下。

```
{
    "name": "node-echo",           # 包名，在NPM服务器上须要保持唯一
    "version": "1.0.0",            # 当前版本号
    "dependencies": {              # 三方包依赖，需要指定包名和版本号
        "argv": "0.0.2"
      },
    "main": "./lib/echo.js",       # 入口模块位置
    "bin" : {
        "node-echo": "./bin/node-echo"      # 命令行程序名和主模块位置
    }
}
```

之后，我们就可以在 package.json 所在目录下运行 npm publish 发布代码了。

## 版本号

使用 NPM 下载和发布代码时都会接触到版本号。NPM 使用语义版本号来管理代码，这里简单介绍一下。

语义版本号分为 X.Y.Z 三位，分别代表主版本号、次版本号和补丁版本号。当代码变更时，版本号按以下原则更新。

```
+ 如果只是修复bug，需要更新Z位。

+ 如果是新增了功能，但是向下兼容，需要更新Y位。

+ 如果有大变动，向下不兼容，需要更新X位。
```

版本号有了这个保证后，在申明三方包依赖时，除了可依赖于一个固定版本号外，还可依赖于某个范围的版本号。例如"argv": "0.0.x"表示依赖于 0.0.x 系列的最新版 argv。NPM 支持的所有版本号范围指定方式可以查看官方文档。

## 灵机一点

除了本章介绍的部分外，NPM 还提供了很多功能，package.json 里也有很多其它有用的字段。除了可以在[npmjs.org/doc/](npmjs.org/doc/) 查看官方文档外，这里再介绍一些 NPM 常用命令。

- NPM 提供了很多命令，例如 install 和 publish，使用 npm help 可查看所有命令。

- 使用 npm help <command>可查看某条命令的详细帮助，例如 npm help install。

- 在 package.json 所在目录下使用`npm install . -g`可先在本地安装当前命令行程序，可用于发布前的本地测试。

- 使用`npm update <package>`可以把当前目录下 node_modules 子目录里边的对应模块更新至最新版本。

- 使用`npm update <package> -g`可以把全局安装的对应命令行程序更新至最新版。

- 使用`npm cache clear`可以清空 NPM 本地缓存，用于对付使用相同版本号发布新版本代码的人。

- 使用`npm unpublish <package>@<version>`可以撤销发布自己发布过的某个版本代码。