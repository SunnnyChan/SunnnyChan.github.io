# 日志级别、错误处理方式 对定位问题的影响
> By SunnyChan (sunnnychan@gmail.com)
> 2015-10-30

```md
今天有小伙伴遇到了一个很困惑的问题：
```
```php
list($var1, $var2, $var3) = explode('_', $str);
```
```md
上面这段代码在测试环境报PHP falal，日志是这样的：
[30-Oct-2015 11:38:29] php fatal error happen! [errno:8] [errstr:Undefined offset:  2]

很明显，意思是说，list在赋值的时候，没有找到数组索引为2的元素。
```
```md
导致这个问题的原因是，explode产生的数组元素个数不足3个，
看到这里，我总觉得list没有那么脆弱，元素不够，总会有默认的处理方式，不至于报PHP fatal。
```
```md
写了一段测试代码：
```
```php
list($var1, $var2, $var3) = array(1, 2);
```
```php
执行完，php-error没有任何的日志，实际的赋值效果和我预期的一样，$var3值为null。
var_dump($var1, $var2, $var3);

打印：
int(1)
int(2)
NULL
```
```md
> 同样的代码，不一样的处理方式，那肯定是和 执行环境有关系了。
```
```md
调整下php.ini 日志级别：
error_reporting  =  E_ALL | E_STRICT

(建议：开发环境下，设置以上的错误日志级别，关注所有级别的错误。)
```
```md
再执行，php_errror.log 打印：
[30-Oct-2015 14:05:31] PHP Notice:  Undefined offset:  2 ......
```
```php
额，list 在赋值的时候，仅能用于数字索引的数组，并假定数字索引从 0 开始。

list($var1, $var2, $var3) = array(1, 2 => 2）;
var_dump($var1, $var2, $var3);

打印：

int(1)
NULL
int(2)
```
```md
看完上面的示例，你应该能明白 list 的赋值机制了， 当找不到对应的数组元素时，会打印一条Notice error log。
```
```md
即使这样，应该也不会影响程序的执行，因为PHP 只有在打印 FATAL 级别日志时，才会中断脚本的运行。
这时候，我们可能会想到 函数 set_error_handler()，它用于自定义error 日志处理方式。

由于 PHP 弱类型的语法，致使很容易写出不够健壮的 PHP 代码，  
所以在我们的开发环境里，修改了错误日志的处理方式，
即使是 NOTICE 级别的error log，也变更为打印 FATAL 级别的日志，同时中断脚本的执行。

> 建议大家都这么做，即使是 NOTICE 级别的日志，也要做处理，以提升代码的健壮性。
```
```md
> 另外要格外提醒一点，开发环境、调试环境、生产环境 要保持一致  
例如：PHP的版本号，日志级别的设置，等等。否则在查问题的时候，容易造成困惑。
```
