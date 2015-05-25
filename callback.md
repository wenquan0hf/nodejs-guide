# 回调

NodeJS 最大的卖点——事件机制和异步 IO，对开发者并不是透明的。开发者需要按异步方式编写代码才用得上这个卖点，而这一点也遭到了一些 NodeJS 反对者的抨击。但不管怎样，异步编程确实是 NodeJS 最大的特点，没有掌握异步编程就不能说是真正学会了 NodeJS。本章将介绍与异步编程相关的各种知识。

在代码中，异步编程的直接体现就是回调。异步编程依托于回调来实现，但不能说使用了回调后程序就异步化了。我们首先可以看看以下代码。

```
function heavyCompute(n, callback) {
    var count = 0,
        i, j;

    for (i = n; i > 0; --i) {
        for (j = n; j > 0; --j) {
            count += 1;
        }
    }

    callback(count);
}

heavyCompute(10000, function (count) {
    console.log(count);
});

console.log('hello');

-- Console ------------------------------
100000000
hello
```

可以看到，以上代码中的回调函数仍然先于后续代码执行。JS 本身是单线程运行的，不可能在一段代码还未结束运行时去运行别的代码，因此也就不存在异步执行的概念。

但是，如果某个函数做的事情是创建一个别的线程或进程，并与JS主线程并行地做一些事情，并在事情做完后通知 JS 主线程，那情况又不一样了。我们接着看看以下代码。

```
setTimeout(function () {
    console.log('world');
}, 1000);

console.log('hello');

-- Console ------------------------------
hello
world
```

这次可以看到，回调函数后于后续代码执行了。如同上边所说，JS 本身是单线程的，无法异步执行，因此我们可以认为 setTimeout 这类 JS 规范之外的由运行环境提供的特殊函数做的事情是创建一个平行线程后立即返回，让 JS 主进程可以接着执行后续代码，并在收到平行进程的通知后再执行回调函数。除了 setTimeout、setInterval 这些常见的，这类函数还包括 NodeJS 提供的诸如 fs.readFile 之类的异步 API。

另外，我们仍然回到 JS 是单线程运行的这个事实上，这决定了 JS 在执行完一段代码之前无法执行包括回调函数在内的别的代码。也就是说，即使平行线程完成工作了，通知 JS 主线程执行回调函数了，回调函数也要等到 JS 主线程空闲时才能开始执行。以下就是这么一个例子。

```
function heavyCompute(n) {
    var count = 0,
        i, j;

    for (i = n; i > 0; --i) {
        for (j = n; j > 0; --j) {
            count += 1;
        }
    }
}

var t = new Date();

setTimeout(function () {
    console.log(new Date() - t);
}, 1000);

heavyCompute(50000);

-- Console ------------------------------
8520
```

可以看到，本来应该在1秒后被调用的回调函数因为 JS 主线程忙于运行其它代码，实际执行时间被大幅延迟。