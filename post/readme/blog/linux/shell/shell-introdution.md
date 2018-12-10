# Shell - Introdution
> By SunnyChan (sunnnychan@gmail.com)

```md
该篇是我分享 Shell 相关知识的PPT。先发布下文档，后续对章节做一些详细说明。
```

### Why Shell？
* **任务自动化**  
```md
  系统管理、日志分析、数据统计、负载监控、自动部署…
```

* **Unix 基本哲学**  
```md
  一次只做一件事，并且把它做好  
  一切皆件
```

### Shell Command  

1.  alias
2.  Shell 函数
3.	内建命令（C 函数）
```sh
 	cd exit test trap unmask shift  …
```
4. 外部命令 和 脚本 
```md
	PATH  
```

>  如果命名名称一致，谁先被执行？

### Shell File-redirection  

1. cat < path.t >/dev/null 2>&1 输出是什么？为什么？  
2.   File Descriptor  
```sh
      STDIN STDOUT STDERR
```
3. <、>、>>  
```sh
	> filename   
	cat  < file  和 cat file 的区别 ?  
	cat file | echo 输出是什么？为什么？  
```
4. &  
5. exec  

### Shell Process and Sub-process  

1. 为什么 cd 是内建命令？  
```sh
     ( cd /home/user;  ls; ) 和 { cd /home/user ;  ls ; } 的区别？ 
``` 
2. 为什么用户退出之后，任务不会继续执行？  
```md
	nohup、disown 
```   
3. 进程挂起 和 &  
4. 管道机制  
```md
	echo "1 + 1"  |  bc  
	管道应用的原则（漏斗）  
```
5.  wait  
6.  source  和 exec  

### Shell Variables  

1.环境变量  
```md
（PATH、LD_LIBRARY_PATH、HOME、PWD …）
```  

2.内建变量  
```md
（1）位置参数变量 $0 - $9  
（2）$*、$@、$#、$?、$!、$$  
（3） IFS(Internal Field Seprator) ，内部域分隔符  
	 <space><tab><newline>
```  

3.自定义变量
```sh  
（1）var=“shell” ${var}  
（2）local and export  
（3）unset  
```

### Shell Array
1.索引数组  
```sh
arr=(“1” “2” “3”)  
```

2.关联数组  
```sh
declare -A arr  
```

3.数组操作  
```sh
（1） ${arr[0]} 、arr[0]=100、unset arr  
（2） length : ${#arr[@]}  
（3） split : ${a[@]:0:3}  
（4） get_keys : ${!arr[@]} 
``` 

### Shell Bash Expansion

1. Brace Expansion  
```sh
   tail –f  log/{access_log,error_log}
   mv file{1,2,3,4}.txt /dir
```
2. Command Substitution
```sh
   $(cmd)    or   `cmd`
```
3. Arithmetic Expansion
```sh
   $((expression)) 
```
4. Patameter Expansion
```sh
    ${var/pattern/string}     
    ${parameter:-word}
```
5. Pathname Expansion
```sh
   *、?、[]  
``

### Shell Conditional  

1. 0 和 非0  
```sh
	exit  status   (0-127)  
	return   
	$?  
```

2. [  ] test   命令  
```sh
	[ ${var} –lt 1 ] [ ${var} –le 1 ] [ ${var} –ge1 ] [ ${var} –gt 1 ]  
	[ -e ${file} ] [ -f ${file} ] [ -d ${file} ] [ -h ${file} ] [ -s ${file} ]  
	[ -z "${var}" ] [ -n "${var}" ]  
	[  "${var1}" == "${var2}" ] [  "${var1}" == "${var2}" ]  
```

3.  -a  -r  
```sh
       [ ${var} –lt 1 -a  ${var} –le 1 ]  
```

4. !  &&  ||  
```sh
	[ ${var} –lt 1 ] && [ ${var} –le 1 ]  
	cd /home/user && mkdir  sunny  
```

5. [[  ]]  关键字  
```sh
      [[  “${var1}” =~ “${pattern}” ]]     #正则匹配  
      [[ hello == hell? ]]    #通配符  
      [[“${var1}” == “${var2}” && “${var3}" == "${var4}" ]]  
```
### Shell Control Structrue  

1. if  
```sh
if...then...else...fi  
if ...then...elif....fi  
```

2. case  
```sh
case "$var" in  
[yY][eE][sS] ) echo "Your input is YES" ;;  
[nN][oO] ) echo "Your input is no" ;;  
* ) echo "Input Error!" ;;  
esac  
```

3. for  
```sh
   for((i=1;i<=10;i++))
 
   for i in {1..10}
   for i in $(seq 10)
   for i in `ls`
   for i in ${arr[@]}
   for i in $*

   for i in /proc/sys/net/ipv4/conf/*/accept_redirects
   for i in fil1 file2 file3
   for i in *.txt
   for i in $(ls *.txt)

   for i in " sys usr data"
   for I in `cat ${file}`
```
4. while  
```sh
while read line
do
    echo "${line}" >> ${out_file}
done  <  ${file} 
```

```sh
exec  3<${file}
while read -u3 line
do
    echo "${line}" >> ${out_file}
done
exec 3<&-
```

```sh
 cat ${file} | while read line
 do
    echo "${line}" >> ${out_file}
 done
```

5. break  N  

6. continue N 

7. true  false  

### Shell Function  
1.定义
```sh
function add() { 
	local a="$1"
	local b="$2"
	echo $(echo "$a + $b " | bc)
    return 0               
}
```
2. 调用  
```md
    先定义 后调用
```  

3. 传参  
```md
    $1  $2  …  

    $# $@ $*  
     add  1  2  
     add $@  和  add $*  
```
     
4. 返回值  
```sh
 ret=add  1  2
 ret=$(add  1  2)
```

### Shell sed  
```sh
sed -e '/pattern/ s/holle/world/g'  -e '1,3 d'  file
```

1.  定址  
```md
（1）line1，line2
（2）line1，/pattern1/
（3）/pattern1/，line1
（4）/pattern1/，/pattern2/
```

2. 模式空间 和 -i 选项  

3. 子命令  

4. 命令行  
```sh
sed -n '$ p'  file
sed 10q
sed q
sed  ' $!d ' 或 sed -n '$p' 
sed -n '/regexp/p'
sed '/^$/d;G '
sed 's/$/;/'
sed 's/^[ \t]*//;s/[ \t]*$// '
```

### Shell awk  
```sh
awk ' BEGIN { actions } 
         pattern1 { actions } 
	    ...
         patternN { actions } 
         END { actions } 
'    inputfile  
```

**内置变量**
```md
$0
$1 … $N
NR
FNR
NF
FS OFS RS ORS OFMT
FILENAME
ARGV  ARGC
RSTART RLENGTH
SUBSEP     “\34”    多维数组
```

**语法**  
类C语言语法  

**函数**

**命令行**
```sh
awk -F : 'NR>1 {print p} {p=$2}' file
awk 'NR==FNR {a[$1] = $0} NR>FNR {print $1" "a[$2]}' file1 file2
ps -u chenguang02 | grep php-cgi |awk  '{print "kill -9  " $2 }' | sh
```

### Shell Command 
```md 
lsof
type
sort / uniq
readlink -f
strings
file
du
dos2unix / unix2dos
iconv
tee
rsync
md5sum
bg/fg/jobs
nslookup
ldd
wc
```

### Shell 执行过程  
```md
01．  Shell首先从命令行中找出特殊字符（元字符），在将元字符翻译成间隔符号，元字符将命令行划分成小块tokens。
         Shell中的元字符如下所示： 
         SPACE , TAB , NEWLINE , & , ; , ( , ) ,< , > , | 
02．  程序块tokens被处理，检查看他们是否是shell中所引用到的关键字。 
03．  当程序块tokens被确定以后，shell根据aliases文件中的列表来检查命令的第一个单词。
          如果这个单词出现在alias表中，执行替换操作并且处理过程回到第一步重新分割程序块tokens。
04．  Shell对~符号进行替换。 
05．  Shell对所有前面带有$符号的变量进行替换。 
06．  Shell将命令行中的内嵌命令表达式替换成命令，他们一般都采用$(command)标记法。 
07．  Shell计算采用$((expression))标记的算术表达式。 
08．  Shell将命令字符串重新划分为新的块tokens。
         这次划分的依据是栏位分割符号，称为IFS。缺省的IFS变量包含有：SPACE , TAB 和换行符号。 
09．  Shell执行通配符* ? [ ]的替换。 
10．  Shell把所有从处理的结果中用到的注释删除，並且按照下面的顺序实行命令的检查： 
         内建的命令 、shell函数（由用户自己定义的） 可执行的脚本文件（需要寻找文件和PATH路径） 
11．  在执行前的最后一步是初始化所有的输入输出重定向。 
12．  最后，执行命令。
```
### Shell Debug  
```md
开启  sh -x
设置  export PS4='+{$LINENO:${FUNCNAME[0]}}
打印  log
```
### Shell Change Your Work  
***重复的事情都交给机器去做！***


## ***注***
> 如果对Shell感兴趣，可以关注：
[shell-utilities](https://github.com/SunnnyChan/shell-utilities)
















