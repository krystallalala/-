### 一、以下PHP常量表示什么意思？
#### PHP魔术常量
##### 1. `__FILE__`: 文件的完整路径和文件名。
如果用在包含文件中，则返回包含文件名。
```php
// 举例：
echo '该文件位于 " '  . __FILE__ . ' " ';
// 该文件位于 “ E:\wamp\www\test\index.php ”
```
自 PHP 4.0.2 起，`__FILE__ `总是包含一个绝对路径（如果是符号连接，则是解析后的绝对路径），而在此之前的版本有时会包含一个相对路径。
> 这是PHP提供的预定义常量之一。
> 这是一个魔术常量。

其他魔术常量：

##### 2. `__LINE__`: 文件中的当前行号。
##### 3. `__DIR__`: 文件所在的目录。 
如果用在包含文件中，则返回包含文件名。
这等价于 `dirname(__FILE__)`，除非是根目录，否则目录中名不包括末尾的斜杠。（PHP 5.3.0中新增）
```php
// 举例：
echo '该文件位于 " '  . __DIR__ . ' " ';
// 该文件位于 “ E:\wamp\www\test ”
```
###### 4. `__FUNCTION__`: 函数名（PHP 4.3.0 新加）。
自 PHP 5 起本常量返回该函数被定义时的名字（区分大小写）。在 PHP 4 中该值总是小写字母的。
```php
// 举例：
function test() {
    echo '函数名为: '. __FUNCTION__;
}
// 函数名为: test
```
##### 5. `__CLASS__`: 类的名称（PHP 4.3.0 新加）。
自 PHP 5 起本常量返回该类被定义时的名字（区分大小写）。
自 PHP 5.4 起 `__CLASS__` 对 trait 也起作用。当用在 trait 方法中时，`__CLASS__`是调用 trait 方法的类的名字。
```php
class test {
    function _print() {
        echo '类名为：'  . __CLASS__ . "<br>";
        echo  '函数名为：' . __FUNCTION__ ;
    }
}
$t = new test();
$t->_print();

// 类名为：test
// 函数名为：_print
```
##### 6. `__TRAIT__`: Trait 的名字（PHP 5.4.0 新加）。
自 PHP 5.4.0 起，PHP 实现了**代码复用**的一个方法，称为 traits。
Trait 名包括其被声明的作用区域（例如 Foo\Bar）。
优先顺序如下：
当前类的方法    （1）
trait方法       （2）
基类中的方法    （3）
```php
// 从基类继承的成员被插入的 SayWorld Trait 中的 MyHelloWorld 方法所覆盖。
// 其行为 MyHelloWorld 类中定义的方法一致。
// 优先顺序是当前类中的方法会覆盖 trait 方法，而 trait 方法又覆盖了基类中的方法。

class Base {
    public function sayHello() {
        echo 'Hello ';
    }
}
 
trait SayWorld {
    public function sayHello() {
        parent::sayHello();
        echo 'World!';
    }
}
 
class MyHelloWorld extends Base {
    use SayWorld; // trait方法起效
}
 
$o = new MyHelloWorld();
$o->sayHello();

// Hello World!

```

##### 7. `__METHOD__`: 类的方法名（PHP 5.0.0 新加）。
返回该方法被定义时的名字（区分大小写）。
```php
function test() {
    echo  '函数名为：' . __METHOD__ ;
}
test();

// test
```
##### 8. `__NAMESPACE__`: 当前命名空间的名称（区分大小写）。
此常量是在编译时定义的（PHP 5.3.0 新增）。
```php
namespace MyProject;
 
echo '命名空间为："', __NAMESPACE__, '"';
// 输出 "MyProject"
```

--

#### PHP超全局变量
所谓`超全局变量`，即在脚本全部作用域中都可以使用。是PHP预定义常量。

##### 1. `$_SERVER`: 服务器和执行环境信息。
包含了诸如头信息（header）、路径（path）、以及脚本位置（script locations）等信息的数组。
这个数组中的项目由Web服务器创建。不能保证每个服务器都提供全部信息项。
> PHP_SELF: 当前执行脚本的文件名，与document root有关。
```php
// 在地址为 http://example.com/foo/bar.php 的脚本中使用
$_SERVER['PHP_SELF'] 
//->  /foo/bar.php (document root 以外的部分)
```
> REMOTE_ADDR: 浏览当前页面的用户的IP地址。（客户端IP地址）

> REMOTE_HOST: 客户端主机名

> REMOTE_PORT: 客户端连接到Web服务器所使用的端口号。

> REMOTE_USER: 经验证的客户端用户
---

### 二、与客户端相关（HTTP协议）

1. 如何获取客户端的IP地址？
```php 
$_SERVER['REMOTE_ADDR']
```

2. 写出使用header函数跳转页面的语句
```php 
header('Location: index.php');
```

### 三、PHP逻辑处理相关

1. $str是一段HTML文本，使用正则表达式去除其中的所有js脚本。
```php
$pattern = '/\.+<\/script>/';

preg_replace($pattern, '', $str);
```

#### 正则表达式语法规则
正则表达式的构成元素中一般包括：普通字符、元字符、限定字符、定位点、非打印字符和指定替换项等。
##### 1. 普通字符
普通字符包括没有显式指定为元字符的所有可打印和不可打印字符，包括所有大小写字母、数字、标点符号和一些字符。
最简单的正则表达式是用于搜索字符串相比较的单个普通字符。例如，单字符正则表达式`/A/`会始终匹配字母 `A`。
也可以将多个单字符组合起来形成较长的表达式，例如，正则表达式`/the/`会匹配搜索字符串中的 `the`、`there`、`other` 和 `over the lazy dog` 等。无须使用任何串联运算符，只需连续输入字符即可。

##### 2. 元字符
元字符可分为单字符元字符和多字符元字符。例如，元字符\d，它与数字字符相匹配。
|元字符|行为|示例|
|  -  | - | - |
| *|0次或多次 {0,} |zo* 与 z 和 zoo匹配|
|+ |1次或多次 {1,} |zo+ 与 zo 和 zoo 匹配，但与z不匹配|
|? |0次或1次 {0,1} |zo? 与 z 和 zo 匹配， 但与zoo不匹配|
|^ |匹配搜索字符串开始的位置|^\d{3}与搜索字符串开始处的3个字符匹配 <br> [^abc] 与除a/b/c 外的任何字符匹配|
| .|匹配除换行符\n外的任何单个字符|a.c 与 abc a1c 和 a-c匹配|
|[]|标记括号表达式的开始和结尾|[1-4] 与 1、2、3 或 4 匹配|
|{}|标记限定符表达式的开始和结尾| a{2,3}与 aa 和 aaa 匹配|
|()|标记子表达式的开始和结尾，可以保存子表达式，以备将来之用|A(\d) 与“A0”至“A9”匹配。保存该数字以备将来之用|
| \| |指示两个或多个项之间进行选择|z\|food 与“z”或“food”匹配  <br> (z\|f)ood 与 “zood”或“food”匹配|
|/|表示 JavaScript 中的文本正则表达式模式的开始和结尾。<br>在第二个 “/”后添加单字符标志可以指定搜索行为|/abc/gi 是与 “abc”匹配的 JavaScript 文本正则表达式。<br>g（全局）标志指定查找模式的所有匹配项，i（忽略大小写）标志使搜索不区分大小写|
|\ |	将下一字符标记为特殊字符、文本、反向引用或八进制转义符|	\n 与换行符匹配。\( 与 “(”匹配。\\\ 与 “\”匹配|

2. 写出将一个数组里的空置去掉的语句
```php
array_filter()
```

3. 写出获取当前时间戳的函数，以及打印前一天的时间的方法
```php
// 当前时间戳
time()

// 前一天时间（Y-m-d H:m:s）
date('Y-m-d H:m:s', strtotime("-1 day"))
```

#### PHP时间函数

##### date(): -可把时间戳格式化为可读性更好的日期和时间。
> 时间戳是一个字符序列，表示一定的事件发生的日期/时间。
```php
date ( string $format [, int $timestamp ] ): string
```
##### time(): -返回当前时间的Unix时间戳
```php
time(): int

```
##### strtotime(): -将任何字符串的日期时间描述解析为 Unix 时间戳
```php
strtotime ( string $time [, int $now = time() ] ): int

// 示例
strtotime("+1 week 2 days 4 hours 2 seconds")
```

4. $str = "1,2,3,4,5,6"，使用什么函数可以把字符串str转化为包含各个数字的数组？
```php
$arr = explode(',', $str);
```

5. serialize() /unserialize()函数的作用
> 很适合用于redis 存储 object

6. 一个文件的路径为 /wwwroot/include/page.class.php， 写出获得该文件扩展名的方法
```php
$arr = pathinfo("/wwwroot/include/page.class.php");
$str = substr($arr['basename'], strrpos($arr['basename'], '.'));
```
##### pathinfo(): -以数组的形式返回关于文件路径的信息。
返回的数组元素如下：
[dirname]: 目录路径
[basename]: 文件名
[extension]: 文件后缀名
[filename]: 不包含后缀的文件名
```php
pathinfo(path,options)
```

##### substr(): - 返回字符串的一部分
```php
substr(string,start,length)
```

##### strrpos(): -查找字符串在另一字符串中最后一次出现的位置（区分大小写）。

---

### PHP 函数

#### HTTP 函数
##### 1. header(): 向客户端发送原始的HTTP报头。
```php
header(string $string, bool $replace = true, int $response_code = ?): void
// string 头字符串
// 有两种特别的头。
// 第一种以“HTTP/”开头的 (case is not significant)，将会被用来计算出将要发送的HTTP状态码。 例如在 Apache 服务器上用 PHP 脚本来处理不存在文件的请求（使用 ErrorDocument 指令）， 就会希望脚本响应了正确的状态码。
header("HTTP/1.1 404 Not Found");
// 第二种特殊情况是“Location:”的头信息。它不仅把报文发送给浏览器，而且还将返回给浏览器一个 REDIRECT（302）的状态码，除非状态码已经事先被设置为了201或者3xx。
header("Location: http://www.example.com/"); /* Redirect browser */

// replace
// 可选参数 replace 表明是否用后面的头替换前面相同类型的头。 
// 默认情况下会替换。如果传入 false，就可以强制使相同的头信息并存。

// response_code
// 强制指定 HTTP 响应的值。注意，这个参数只有在报文字符串（header）不为空的情况下才有效。

// 如果你想提醒用户去保存你发送的数据，例如保存一个生成的PDF文件。
// 使用» Content-Disposition的报文信息来提供一个推荐的文件名，
// 并且强制浏览器显示一个文件下载的对话框。

// 输出 PDF 文件
header('Content-type: application/pdf');

// 名称为 downloaded.pdf
header('Content-Disposition: attachment; filename="downloaded.pdf"');

// 该 PDF 来源于 original.pdf
readfile('original.pdf');
```
> 注意：
header()必须在任何实际输出之前调用，不管是普通的HTML标签，还是文件或PHP输出的空行、空格。

##### 2. headers_list() — 返回已发送的 HTTP 响应头（或准备发送的）
```php
// 会返回准备发送给浏览器/客户端的 HTTP 头列表。
headers_list(): array

// PHP 7.3.0 起有效的签名：
setcookie(string $name, string $value = "", array $options = []): bool
// setcookie() 定义了 Cookie，会和剩下的 HTTP 头一起发送给客户端。 
// 和其他 HTTP 头一样，必须在脚本产生任意输出之前发送 Cookie（由于协议的限制）。 
// 请在产生任何输出之前（包括 <html> 和 <head> 或者空格）调用本函数。

一旦设置 Cookie 后，下次打开页面时可以使用 $_COOKIE 读取。 
Cookie 值同样也存在于 $_REQUEST。

// 示例

/* setcookie() 会自己添加一个响应头 */
setcookie('foo', 'bar');

/* 添加自定义的响应头
 大多数客户端会主动忽略 */
header("X-Sample-Test: foo");

/* 响应中指定内容为明文 text */
header('Content-type: text/plain');

/* 所以会发送什么头呢？ */
var_dump(headers_list());

// 返回
array(4) {
  [0]=>
  string(23) "X-Powered-By: PHP/5.1.3"
  [1]=>
  string(19) "Set-Cookie: foo=bar"
  [2]=>
  string(18) "X-Sample-Test: foo"
  [3]=>
  string(24) "Content-type: text/plain"
}
```

##### 3. headers_sent() — 检测 HTTP 头是否已经发送

```php
// 检测 HTTP 头是否已经发送。
headers_sent(string &$file = ?, int &$line = ?): bool
// HTTP 头已经发送时，就无法通过 header() 添加更多头字段。 使用此函数起码可以防止 HTTP 头出错。另一个解决方案是用 输出缓冲。
--
// 参数
// file
// 若设置了可选参数 file and line， headers_sent() 会把 PHP 文件名放在 file 变量里， 输出开始的行号放在 line 变量里。

// line
// 输出开始的行号。
--
// 使用 file 和 line 参数选项的例子

// 注意 $filename 和 $linenum 用于下文中使用
// 所以不要提前为它们赋值
if (!headers_sent($filename, $linenum)) {
    header('Location: http://www.example.com/');
    exit;

// 很有可能在这里触发错误
} else {

    echo "Headers already sent in $filename on line $linenum\n" .
          "Cannot redirect, for now please click this <a " .
          "href=\"http://www.example.com\">link</a> instead\n";
    exit;
}


```

