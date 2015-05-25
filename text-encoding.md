# 文本编码

使用 NodeJS 编写前端工具时，操作得最多的是文本文件，因此也就涉及到了文件编码的处理问题。我们常用的文本编码有 UTF8 和 GBK 两种，并且 UTF8 文件还可能带有 BOM。在读取不同编码的文本文件时，需要将文件内容转换为 JS 使用的 UTF8 编码字符串后才能正常处理。

## BOM 的移除

BOM 用于标记一个文本文件使用 Unicode 编码，其本身是一个 Unicode 字符（"\uFEFF"），位于文本文件头部。在不同的 Unicode 编码下，BOM 字符对应的二进制字节如下：

```
    Bytes      Encoding
----------------------------
    FE FF       UTF16BE
    FF FE       UTF16LE
    EF BB BF    UTF8
```

因此，我们可以根据文本文件头几个字节等于啥来判断文件是否包含 BOM，以及使用哪种 Unicode 编码。但是，BOM 字符虽然起到了标记文件编码的作用，其本身却不属于文件内容的一部分，如果读取文本文件时不去掉 BOM，在某些使用场景下就会有问题。例如我们把几个 JS 文件合并成一个文件后，如果文件中间含有 BOM 字符，就会导致浏览器 JS 语法错误。因此，使用 NodeJS 读取文本文件时，一般需要去掉 BOM。例如，以下代码实现了识别和去除 UTF8 BOM 的功能。

```
function readText(pathname) {
    var bin = fs.readFileSync(pathname);

    if (bin[0] === 0xEF && bin[1] === 0xBB && bin[2] === 0xBF) {
        bin = bin.slice(3);
    }

    return bin.toString('utf-8');
}
```

## GBK 转 UTF8

NodeJS 支持在读取文本文件时，或者在 Buffer 转换为字符串时指定文本编码，但遗憾的是，GBK 编码不在NodeJS自身支持范围内。因此，一般我们借助 iconv-lite 这个三方包来转换编码。使用 NPM 下载该包后，我们可以按下边方式编写一个读取 GBK 文本文件的函数。

```
var iconv = require('iconv-lite');

function readGBKText(pathname) {
    var bin = fs.readFileSync(pathname);

    return iconv.decode(bin, 'gbk');
}
```

## 单字节编码

有时候，我们无法预知需要读取的文件采用哪种编码，因此也就无法指定正确的编码。比如我们要处理的某些 CSS 文件中，有的用 GBK 编码，有的用 UTF8 编码。虽然可以一定程度可以根据文件的字节内容猜测出文本编码，但这里要介绍的是有些局限，但是要简单得多的一种技术。

首先我们知道，如果一个文本文件只包含英文字符，比如 Hello World，那无论用 GBK 编码或是 UTF8 编码读取这个文件都是没问题的。这是因为在这些编码下，ASCII0~128 范围内字符都使用相同的单字节编码。

反过来讲，即使一个文本文件中有中文等字符，如果我们需要处理的字符仅在 ASCII0~128 范围内，比如除了注释和字符串以外的JS代码，我们就可以统一使用单字节编码来读取文件，不用关心文件的实际编码是 GBK 还是 UTF8。以下示例说明了这种方法。

```
1. GBK编码源文件内容：

    var foo = '中文';

2. 对应字节：

    76 61 72 20 66 6F 6F 20 3D 20 27 D6 D0 CE C4 27 3B

3. 使用单字节编码读取后得到的内容：

    var foo = '{乱码}{乱码}{乱码}{乱码}';

4. 替换内容：

    var bar = '{乱码}{乱码}{乱码}{乱码}';

5. 使用单字节编码保存后对应字节：

    76 61 72 20 62 61 72 20 3D 20 27 D6 D0 CE C4 27 3B

6. 使用 GBK 编码读取后得到内容：

    var bar = '中文';
```


这里的诀窍在于，不管大于 0xEF 的单个字节在单字节编码下被解析成什么乱码字符，使用同样的单字节编码保存这些乱码字符时，背后对应的字节保持不变。

NodeJS 中自带了一种 binary 编码可以用来实现这个方法，因此在下例中，我们使用这种编码来演示上例对应的代码该怎么写。

```
function replace(pathname) {
    var str = fs.readFileSync(pathname, 'binary');
    str = str.replace('foo', 'bar');
    fs.writeFileSync(pathname, str, 'binary');
}
```