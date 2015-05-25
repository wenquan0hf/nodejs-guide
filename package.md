# 包

我们已经知道了 JS 模块的基本单位是单个 JS 文件，但复杂些的模块往往由多个子模块组成。为了便于管理和使用，我们可以把由多个子模块组成的大模块称做包，并把所有子模块放在同一个目录里。

在组成一个包的所有子模块中，需要有一个入口模块，入口模块的导出对象被作为包的导出对象。例如有以下目录结构。

```
- /home/user/lib/
    - cat/
        head.js
        body.js
        main.js
```

其中 cat 目录定义了一个包，其中包含了 3 个子模块。main.js 作为入口模块，其内容如下：

```
var head = require('./head');
var body = require('./body');

exports.create = function (name) {
    return {
        name: name,
        head: head.create(),
        body: body.create()
    };
};
```

在其它模块里使用包的时候，需要加载包的入口模块。接着上例，使用 require('/home/user/lib/cat/main')能达到目的，但是入口模块名称出现在路径里看上去不是个好主意。因此我们需要做点额外的工作，让包使用起来更像是单个模块。

## index.js

当模块的文件名是 index.js，加载模块时可以使用模块所在目录的路径代替模块文件路径，因此接着上例，以下两条语句等价。

```
var cat = require('/home/user/lib/cat');
var cat = require('/home/user/lib/cat/index');
```

这样处理后，就只需要把包目录路径传递给 require 函数，感觉上整个目录被当作单个模块使用，更有整体感。

## package.json

如果想自定义入口模块的文件名和存放位置，就需要在包目录下包含一个 package.json 文件，并在其中指定入口模块的路径。上例中的 cat 模块可以重构如下。

```
- /home/user/lib/
    - cat/
        + doc/
        - lib/
            head.js
            body.js
            main.js
        + tests/
        package.json
其中package.json内容如下。

{
    "name": "cat",
    "main": "./lib/main.js"
}
```

如此一来，就同样可以使用 require('/home/user/lib/cat')的方式加载模块。NodeJS 会根据包目录下的 package.json 找到入口模块所在位置。