# 小结

本章介绍了使用 NodeJS 操作网络时需要的 API 以及一些坑回避技巧，总结起来有以下几点：

- http 和 https 模块支持服务端模式和客户端模式两种使用方式。

- request 和 response 对象除了用于读写头数据外，都可以当作数据流来操作。

- url.parse 方法加上 request.url 属性是处理 HTTP 请求时的固定搭配。

- 使用 zlib 模块可以减少使用 HTTP 协议时的数据传输量。

- 通过 net 模块的 Socket 服务器与客户端可对 HTTP 协议做底层操作。

