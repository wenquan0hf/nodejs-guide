# 灵机一点

使用 NodeJS 操作网络，特别是操作 HTTP 请求和响应时会遇到一些惊喜，这里对一些常见问题做解答。

问： 为什么通过 headers 对象访问到的 HTTP 请求头或响应头字段不是驼峰的？

答： 从规范上讲，HTTP 请求头和响应头字段都应该是驼峰的。但现实是残酷的，不是每个 HTTP 服务端或客户端程序都严格遵循规范，所以 NodeJS 在处理从别的客户端或服务端收到的头字段时，都统一地转换为了小写字母格式，以便开发者能使用统一的方式来访问头字段，例如 headers['content-length']。

问： 为什么 http 模块创建的 HTTP 服务器返回的响应是 chunked 传输方式的？

答： 因为默认情况下，使用`.writeHead`方法写入响应头后，允许使用`.write`方法写入任意长度的响应体数据，并使用`.end`方法结束一个响应。由于响应体数据长度不确定，因此 NodeJS 自动在响应头里添加了 Transfer-Encoding: chunked 字段，并采用 chunked 传输方式。但是当响应体数据长度确定时，可使用`.writeHead`方法在响应头里加上 Content-Length 字段，这样做之后 NodeJS 就不会自动添加 Transfer-Encoding 字段和使用 chunked 传输方式。

问： 为什么使用 http 模块发起 HTTP 客户端请求时，有时候会发生 socket hang up 错误？

答： 发起客户端 HTTP 请求前需要先创建一个客户端。http 模块提供了一个全局客户端 http.globalAgent，可以让我们使用`.request`或`.get`方法时不用手动创建客户端。但是全局客户端默认只允许 5 个并发 Socket 连接，当某一个时刻 HTTP 客户端请求创建过多，超过这个数字时，就会发生 socket hang up 错误。解决方法也很简单，通过 http.globalAgent.maxSockets 属性把这个数字改大些即可。另外，https 模块遇到这个问题时也一样通过 https.globalAgent.maxSockets 属性来处理。