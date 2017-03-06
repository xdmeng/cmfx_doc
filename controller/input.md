# 输入变量

请参考：http://www.kancloud.cn/manual/thinkphp/1721



在Web开发过程中，我们经常需要获取系统变量或者用户提交的数据，这些变量数据错综复杂，而且一不小心就容易引起安全隐患，但是如果利用好ThinkPHP提供的变量获取功能，就可以轻松的获取和驾驭变量了。

获取变量

虽然你仍然可以在开发过程中使用传统方式获取各种系统变量，例如：

$id    =  $_GET['id']; // 获取get变量
$name  =  $_POST['name'];  // 获取post变量
$value =  $_SESSION['var']; // 获取session变量
$name  =  $_COOKIE['name']; // 获取cookie变量
$file  =  $_SERVER['PHP_SELF']; // 获取server变量

但是我们不建议直接使用传统方式获取，因为没有统一的安全处理机制，后期如果调整的话，改起来会比较麻烦。所以，更好的方式是在框架中统一使用I函数进行变量获取和过滤。

I方法是ThinkPHP用于更加方便和安全的获取系统输入变量，可以用于任何地方，用法格式如下：

I('变量类型.变量名/修饰符',['默认值'],['过滤方法或正则'],['额外数据源'])
变量类型是指请求方式或者输入类型，包括：

变量类型	含义
get	获取GET参数
post	获取POST参数
param	自动判断请求类型获取GET、POST或者PUT参数
request	获取REQUEST 参数
put	获取PUT 参数
session	获取 $_SESSION 参数
cookie	获取 $_COOKIE 参数
server	获取 $_SERVER 参数
globals	获取 $GLOBALS参数
path	获取 PATHINFO模式的URL参数
data	获取 其他类型的参数，需要配合额外数据源参数
注意：变量类型不区分大小写，变量名则严格区分大小写。 
默认值和过滤方法均属于可选参数。
我们以GET变量类型为例，说明下I方法的使用：

echo I('get.id'); // 相当于 $_GET['id']
echo I('get.name'); // 相当于 $_GET['name']
支持默认值：

echo I('get.id',0); // 如果不存在$_GET['id'] 则返回0
echo I('get.name',''); // 如果不存在$_GET['name'] 则返回空字符串
采用方法过滤：

// 采用htmlspecialchars方法对$_GET['name'] 进行过滤，如果不存在则返回空字符串
echo I('get.name','','htmlspecialchars'); 
支持直接获取整个变量类型，例如：

// 获取整个$_GET 数组
I('get.'); 
用同样的方式，我们可以获取post或者其他输入类型的变量，例如：

I('post.name','','htmlspecialchars'); // 采用htmlspecialchars方法对$_POST['name'] 进行过滤，如果不存在则返回空字符串
I('session.user_id',0); // 获取$_SESSION['user_id'] 如果不存在则默认为0
I('cookie.'); // 获取整个 $_COOKIE 数组
I('server.REQUEST_METHOD'); // 获取 $_SERVER['REQUEST_METHOD'] 
param变量类型是框架特有的支持自动判断当前请求类型的变量获取方式，例如：

echo I('param.id');
如果当前请求类型是GET，那么等效于 $_GET['id']，如果当前请求类型是POST或者PUT，那么相当于获取 $_POST['id'] 或者 PUT参数id。

由于param类型是I函数默认获取的变量类型，因此事实上param变量类型的写法可以简化为：

I('id'); // 等同于 I('param.id')
I('name'); // 等同于 I('param.name')
path类型变量可以用于获取URL参数（必须是PATHINFO模式参数有效，无论是GET还是POST方式都有效），例如： 当前访问URL地址是 http://serverName/index.php/New/2013/06/01

那么我们可以通过

echo I('path.1'); // 输出2013
echo I('path.2'); // 输出06
echo I('path.3'); // 输出01
data类型变量可以用于获取不支持的变量类型的读取，例如：

I('data.file1','','',$_FILES);
变量过滤

如果你没有在调用I函数的时候指定过滤方法的话，系统会采用默认的过滤机制（由DEFAULT_FILTER配置），事实上，该参数的默认设置是：

// 系统默认的变量过滤机制
'DEFAULT_FILTER'        => 'htmlspecialchars'
也就说，I方法的所有获取变量如果没有设置过滤方法的话都会进行htmlspecialchars过滤，那么：

// 等同于 htmlspecialchars($_GET['name'])
I('get.name'); 
同样，该参数也可以设置支持多个过滤，例如：

'DEFAULT_FILTER'        => 'strip_tags,htmlspecialchars'
设置后，我们在使用：

// 等同于 htmlspecialchars(strip_tags($_GET['name']))
I('get.name'); 
如果我们在使用I方法的时候 指定了过滤方法，那么就会忽略DEFAULT_FILTER的设置，例如：

// 等同于 strip_tags($_GET['name'])
echo I('get.name','','strip_tags'); 
I方法的第三个参数如果传入函数名，则表示调用该函数对变量进行过滤并返回（在变量是数组的情况下自动使用array_map进行过滤处理），否则会调用PHP内置的filter_var方法进行过滤处理，例如：

I('post.email','',FILTER_VALIDATE_EMAIL);
表示 会对$_POST['email'] 进行 格式验证，如果不符合要求的话，返回空字符串。 （关于更多的验证格式，可以参考 官方手册的filter_var用法。） 或者可以用下面的字符标识方式：

I('post.email','','email');
可以支持的过滤名称必须是filter_list方法中的有效值（不同的服务器环境可能有所不同），可能支持的包括：

int
boolean
float
validate_regexp
validate_url
validate_email
validate_ip
string
stripped
encoded
special_chars
unsafe_raw
email
url
number_int
number_float
magic_quotes
callback
还可以支持进行正则匹配过滤，例如：

// 采用正则表达式进行变量过滤
I('get.name','','/^[A-Za-z]+$/');
I('get.id',0,'/^\d+$/');
如果正则匹配不通过的话，则返回默认值。

在有些特殊的情况下，我们不希望进行任何过滤，即使DEFAULT_FILTER已经有所设置，可以使用：

// 下面两种方式都不采用任何过滤方法
I('get.name','','');
I('get.id','',false);
一旦过滤参数设置为空字符串或者false，即表示不再进行任何的过滤。

变量修饰符

最新版本的I函数支持对变量使用修饰符功能，可以更方便的通过类型过滤变量。

用法如下：

I('变量类型.变量名/修饰符')
例如：

I('get.id/d'); // 强制变量转换为整型
I('post.name/s'); // 强制转换变量为字符串类型
I('post.ids/a'); // 强制变量转换为数组类型
可以使用的修饰符包括：

修饰符	作用
s	强制转换为字符串类型
d	强制转换为整型类型
b	强制转换为布尔类型
a	强制转换为数组类型
f	强制转换为浮点类型
