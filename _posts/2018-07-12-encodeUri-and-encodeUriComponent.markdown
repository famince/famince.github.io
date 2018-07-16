一、统一资源识别符
Web上可用的每种资源 - HTML文档、图像、视频片段、程序等 - 均由URI进行定位

URI ：Uniform Resource Identifier， RFC 1630

有两种形式：
URL：Uniform Resource Locator，统一资源定位符；代表资源的住址,提供查找该资源的方法;RFC1738
URN：Uniform Resource Name，统一资源名称; 用于命名,定义某事物的名称,身份;不依赖于位置，并且有可能减少失效连接的个数;

* 用于标识唯一书目的ISBN系统是一个典型的URN使用范例。例如，ISBN 0486275574(urn:isbn:0-486-27557-4)无二义性地标识出莎士比亚的戏剧《罗密欧与朱丽叶》的某一特定版本。为获得该资源并阅读该书，人们需要它的位置，也就是一个URL地址。在类Unix操作系统中，一个典型的URL地址可能是一个文件目录，例如file:///home/username/RomeoAndJuliet.pdf。该URL标识出存储于本地硬盘中的电子书文件。因此，URL和URN有着互补的作用。


URI语法：
URI = scheme:[//authority]path[?query][#fragment]，例子：s
https://cn.bing.com/search?q=url


其中：fragment: 锚点，代表网页中的一个位置。#右面的字符，就是该位置的标识符。#是用来指导浏览器动作的，对服务器端完全无用，所以不会传到服务器。
为网页位置指定标识符，有两个方法。一是使用锚点，比如<a name="print"></a>，二是使用id属性，比如<div id="print">。
https://tools.ietf.org/html/rfc3986#section-3.1


* 这部分网上很多资料，自己熟悉好了


二、百分号编码

在浏览器中输入 https://zh.m.wikipedia.org/wiki/统一资源定位符
https://zh.m.wikipedia.org/wiki/%E7%BB%9F%E4%B8%80%E8%B5%84%E6%BA%90%E5%AE%9A%E4%BD%8D%E7%AC%A6


浏览器 对中文进行了编码成 “% + 英文或数字的组合”

为什么URL需要进行编码，看URL定义：URL中只能使用 ASCII可打印字符（32-126）。

上面的 % + 英文或数字的组合 叫做 百分号编码(Percent-encoding in a URI)

出现在 URI 里面的字符，要么是保留字符，要么是非保留字符（或者是%编码后的字符) rfc3986 

保留字符: 通常包含特殊含义，比如斜杠'/'用来分隔URL的不同部分。
gen-delims  = ":" / "/" / "?" / "#" / "[" / "]" / "@"                           # 共7个
sub-delims  = "!" / "$" / "&" / "'" / "(" / ")" / "*" / "+" / "," / ";" / "="   # 共11个
reserved    = gen-delims / sub-delims                                           # 共18个

非保留字符:  没有特殊含义
unreserved  = ALPHA / DIGIT / "-" / "." / "_" / "~"                             # 共66个（26*2 + 10 + 4）

以下为ruby测试代码，验证剩余可打印字符：

gen_delims = [":", "/", "?", "#", "[", "]", "@"]
sub_delims = ["!", "$", "&", "'", "(", ")", "*", "+", ",", ";", "="]
unreserved = [('A'..'Z').to_a, ('a'..'z').to_a, (0..9).to_a, "-", ".", "_", "~"].flatten
printable_ch = (32..126).map{|e| e.chr}
printable_ch - gen_delims - sub_delims - unreserved
[" ", "\"", "%", "<", ">", "\\", "^", "`!`27`!{", "|", "}"]

除去保留字符，未保留字符，ASCII 中可打印字符 剩下11个是会被 %编码的，他们还有一个名字：
不安全字符(Unsafe Characters,15个) RFC1738，还包括保留字符中的3个 "#", "[", "]", 以及不保留字符中的1个 "~"
[" ", "\"", "%", "<", ">", "\\", "^", "`!`7`!{", "|", "}", "#", "[", "]", "~"]


结论：
2.1.URL其它字符(中文等)会转换为UTF-8字节序列, 然后对其字节值使用%编码;
2.2.保留字符,未保留字符 正常情况下不会被编码
2.3.保留字符不用做特殊作用时，如作为参数也是会被%编码的(部分字符待验证);


* 至于扩展ASCII码支持情况，没仔细研究，有需要后面再看




三、现在回到 JS 中 encodeURI 和 encodeURIComponent 方法：

3.1 encodeURI()  and encodeURIComponent

encodeURI() : 处理整个url
encodeURIComponent： 处理url某个部分，如查询部分


The encodeURI() function is used to encode a URI.
This function encodes special characters, except: , / ? : @ & = + $ # (Use encodeURIComponent() to encode these characters).


这里只说了编码特殊字符，那是那些呢，是上面剩下的 11个通用字符，以及2个不安全保留字符[]
[" ", "\"", "%", "<", ">", "\\", "^", "`", "{", "|", "}", "[", "]"]



3.2 终端测试
在firefox 终端测试所有保留字符的编码情况:
uri = "my test.asp?name=abc:/?#[]@!$&'()*+,;=abc";
encodeURI(uri);
my%20test.asp?name=abc:/?#%5B%5D@!$&'()*+,;=abc

只转义了两个 [], 其他都没有转义

encodeURIComponent(uri);
my%20test.asp%3Fname%3Dabc%3A%2F%3F%23%5B%5D%40!%24%26'()*%2B%2C%3B%3Dabc

可以看到不转义的有5个: !'()*, 其他13个都转义了


3.3 浏览器测试
直接在firefox上检索保留字符，结果是:
https://cn.bing.com/search?q=abc%3A%2F%3F%23[]%40!%24%26'()*%2B%2C%3B%3Dabc

[]!'()* 是没有被%编码的，奇怪。

在chrome, ie,360上测试:
https://cn.bing.com/search?q=abc%3A%2F%3F%23%5B%5D%40%21%24%26%27%28%29*%2B%2C%3B%3Dabc
https://www.baidu.com/s?wd=abc%3A%2F%3F%23%5B%5D%40!%24%26%27()*%2B%2C%3B%3Dabc


可见不仅仅不同浏览器，编码不一致，连不同搜索引擎都不太一致,或许浏览器版本也有关联，好神奇，留作大神帮忙解惑.


后记：附件名带保留字符引起的解析错误

参考：
https://en.wikipedia.org/wiki/Uniform_Resource_Identifier
https://en.wikipedia.org/wiki/URL
https://zh.m.wikipedia.org/wiki/%E7%BB%9F%E4%B8%80%E8%B5%84%E6%BA%90%E5%AE%9A%E4%BD%8D%E7%AC%A6
https://www.tinktodo.com/encodeurl-encodeuricomponent-2/
https://perishablepress.com/stop-using-unsafe-characters-in-urls/
https://tech.upyun.com/article/272/1.html

