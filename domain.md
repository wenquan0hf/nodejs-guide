# 域（Domain）

官方文档： [http://nodejs.org/api/domain.html](http://nodejs.org/api/domain.html)

NodeJS 提供了 domain 模块，可以简化异步代码的异常处理。在介绍该模块之前，我们需要首先理解“域”的概念。简单的讲，一个域就是一个 JS 运行环境，在一个运行环境中，如果一个异常没有被捕获，将作为一个全局异常被抛出。NodeJS 通过 process 对象提供了捕获全局异常的方法，示例代码如下

```
process.on('uncaughtException', function (err) {
    console.log('Error: %s', err.message);
});

setTimeout(function (fn) {
    fn();
});

-- Console ------------------------------
Error: undefined is not a function
```

虽然全局异常有个地方可以捕获了，但是对于大多数异常，我们希望尽早捕获，并根据结果决定代码的执行路径。我们用以下 HTTP 服务器代码作为例子：

```
function async(request, callback) {
    // Do something.
    asyncA(request, function (err, data) {
        if (err) {
            callback(err);
        } else {
            // Do something
            asyncB(request, function (err, data) {
                if (err) {
                    callback(err);
                } else {
                    // Do something
                    asyncC(request, function (err, data) {
                        if (err) {
                            callback(err);
                        } else {
                            // Do something
                            callback(null, data);
                        }
                    });
                }
            });
        }
    });
}

http.createServer(function (request, response) {
    async(request, function (err, data) {
        if (err) {
            response.writeHead(500);
            response.end();
        } else {
            response.writeHead(200);
            response.end(data);
        }
    });
});
```

以上代码将请求对象交给异步函数处理后，再根据处理结果返回响应。这里采用了使用回调函数传递异常的方案，因此 async 函数内部如果再多几个异步函数调用的话，代码就变成上边这副鬼样子了。为了让代码好看点，我们可以在每处理一个请求时，使用 domain 模块创建一个子域（JS 子运行环境）。在子域内运行的代码可以随意抛出异常，而这些异常可以通过子域对象的 error 事件统一捕获。于是以上代码可以做如下改造：

```
function async(request, callback) {
    // Do something.
    asyncA(request, function (data) {
        // Do something
        asyncB(request, function (data) {
            // Do something
            asyncC(request, function (data) {
                // Do something
                callback(data);
            });
        });
    });
}

http.createServer(function (request, response) {
    var d = domain.create();

    d.on('error', function () {
        response.writeHead(500);
        response.end();
    });

    d.run(function () {
        async(request, function (data) {
            response.writeHead(200);
            response.end(data);
        });
    });
});
```

可以看到，我们使用`.create`方法创建了一个子域对象，并通过`.run`方法进入需要在子域中运行的代码的入口点。而位于子域中的异步函数回调函数由于不再需要捕获异常，代码一下子瘦身很多。

## 陷阱

无论是通过 process 对象的 uncaughtException 事件捕获到全局异常，还是通过子域对象的 error 事件捕获到了子域异常，在 NodeJS 官方文档里都强烈建议处理完异常后立即重启程序，而不是让程序继续运行。按照官方文档的说法，发生异常后的程序处于一个不确定的运行状态，如果不立即退出的话，程序可能会发生严重内存泄漏，也可能表现得很奇怪。

但这里需要澄清一些事实。JS 本身的`throw..try..catch`异常处理机制并不会导致内存泄漏，也不会让程序的执行结果出乎意料，但 NodeJS 并不是存粹的 JS。NodeJS 里大量的 API 内部是用 C/C++ 实现的，因此 NodeJS 程序的运行过程中，代码执行路径穿梭于 JS 引擎内部和外部，而 JS 的异常抛出机制可能会打断正常的代码执行流程，导致 C/C++ 部分的代码表现异常，进而导致内存泄漏等问题。

因此，使用 uncaughtException 或 domain 捕获异常，代码执行路径里涉及到了 C/C++ 部分的代码时，如果不能确定是否会导致内存泄漏等问题，最好在处理完异常后重启程序比较妥当。而使用 try 语句捕获异常时一般捕获到的都是 JS 本身的异常，不用担心上诉问题。