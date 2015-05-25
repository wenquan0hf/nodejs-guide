# 小结

本章将之前零散介绍的知识点串了起来，完整地演示了一个使用 NodeJS 开发程序的例子，至此我们的课程就全部结束了。以下是对新诞生的 NodeJSer 的一些建议。

要熟悉官方 API 文档。并不是说要熟悉到能记住每个 API 的名称和用法，而是要熟悉 NodeJS 提供了哪些功能，一旦需要时知道查询API文档的哪块地方。

要先设计再实现。在开发一个程序前首先要有一个全局的设计，不一定要很周全，但要足够能写出一些代码。

要实现后再设计。在写了一些代码，有了一些具体的东西后，一定会发现一些之前忽略掉的细节。这时再反过来改进之前的设计，为第二轮迭代做准备。

要充分利用三方包。NodeJS 有一个庞大的生态圈，在写代码之前先看看有没有现成的三方包能节省不少时间。

不要迷信三方包。任何事情做过头了就不好了，三方包也是一样。三方包是一个黑盒，每多使用一个三方包，就为程序增加了一份潜在风险。并且三方包很难恰好只提供程序需要的功能，每多使用一个三方包，就让程序更加臃肿一些。因此在决定使用某个三方包之前，最好三思而后行。