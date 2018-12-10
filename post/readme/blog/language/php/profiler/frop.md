# forp 实践
> By SunnyChan (sunnnychan@gmail.com)
> 2015-10-30

## What is frop ?
```md
forp 是一个轻量级的PHP扩展，用于提供PHP相关的分析数据。

可以提供以下分析数据：
1. 每个函数执行时间和分配的内存；
2. CPU使用率；
3. 函数调用发生的文件名和代码行；
4. 与 Google 事件跟踪 格式一致的输出；
5. 函数说明（可以用#符号引用函数参数）；
6. 函数所属的分组；
7. 函数的别名，（如果使匿名函数，会十分有用）。
```
## 部署
```sh
获取 源代码文件：
git clone https://github.com/aterrien/forp-PHP-profiler

进入安装目录：
cd forp-PHP-profiler/ext/forp
```
```md
如果需要使用指定版本的 php 环境来安装forp，就需要指定 phpize、php-config脚本的路径，否则默认由PATH环境变量来指定。
```
* 1.执行 phpize 脚本，为PHP扩展准备构建环境
```sh
/home/sunny/x86_64/php5/bin/phpize  -c /home/sunny/x86_64/php5/etc/php.ini
```
* 2.配置编译选项
```sh
./configure  --with-php-config=/home/sunny/x86_64/php5/bin/php-config 
```
```md
php-config 脚本用于获取安装的PHP配置信息
```
```md
执行完上面的命令行后，会生成Makefile文件，检查下Makefile文件， 一些变量的 目录设置可能会不正确。  
这与PHP版本编译时的环境有关系。

php-config -h 检查下PHP版本的路径信息是否一致。
否则手动修改（也可以修改php-config脚本）：
```
```sh
Shell> sed s#/home/xxx/php-5.5.27#/home/sunny/x86_64/php5#g -i Makefile
```

* 3.编译
```sh
Shell> make
```

* 4.安装
```sh
Shell>make install
/usr/bin/make64 MAC=64 install
Installing shared extensions:     /home/sunny/x86_64/php5/lib/php/extensions
```

* 注意点：
```md
（1）PHP 5.3以上版本才支持 forp。
（2）如果你的PHP版本是从其他地方 Copy 获取的，可能实际的环境和 PHP 配置文件、脚本的配置的地址不一致。  
    可以手动对 php.ini、phpize、php-config 文件做下修改。
```
```md
以上安装过程，也可以用于在源码编译安装其他 PHP 扩展时做参考。
```
## 应用
```md
以源代码中的实例做一个简单的解释：
```
```php
<?php
// first thing to do, enable forp profiler
forp_start();

// here, our PHP code we want to profile
function foo()
{
    echo "Hello world !\n";
};

foo();

// stop forp buffering
forp_end();

// get the stack as an array
$profileStack = forp_dump();

print_r($profileStack);
```

* 执行后输出：
```php
Hello world !
Array
(
    [utime] => 0
    [stime] => 0
    [stack] => Array
        (
            [0] => Array
                (
                    [file] => /home/users/chenguang02/sunny/programe/php/forp.php
                    [function] => {main}
                    [usec] => 23
                    [pusec] => 10
                    [bytes] => 32
                    [level] => 0
                )

            [1] => Array
                (
                    [file] => /home/users/chenguang02/sunny/programe/php/forp.php
                    [function] => foo
                    [lineno] => 11
                    [usec] => 15
                    [pusec] => 0
                    [bytes] => 32
                    [level] => 1
                    [parent] => 0
                )

        )

)
```

* 全局输出：
```md
utime：用户函数调用CPU耗时
stime：系统调用CPU耗时
```

* 调用栈输出：
```md
file：函数调用发生的文件路径
function：函数名
usec：函数调用时长（不包含分析的开销）
pusec：内部分析时长（不执行函数）
bytes：函数消耗的内存空间
level：函数调用的深度（从forp_start调用开始计算）
还有可能出现：
class：类名
groups：函数所属分组
caption：函数说明
parent：父函数栈（调用当前函数的函数）索引
```
