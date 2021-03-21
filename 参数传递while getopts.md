https://cloud.tencent.com/developer/article/1529851
转载
getpots是Shell命令行参数解析工具，旨在从Shell Script的命令行当中解析参数。getopts被Shell程序用来分析位置参数，option包含需要被识别的选项字符，如果这里的字符后面跟着一个冒号，表明该字符选项需要一个参数，其参数需要以空格分隔。冒号和问号不能被用作选项字符。getopts每次被调用时，它会将下一个选项字符放置到变量中，OPTARG则可以拿到参数值；如果option前面加冒号，则代表忽略错误；

命令格式： 

`getopts optstring name [arg...]`

示例说明：
1）在shell脚本中，对于简单的参数，常常会使用$1，$2，...,$n来处理即可，具体如下：

```bash
[root@bobo tmp]# cat test.sh                      
#!/bin/bash

SYSCODE=$1
APP_NAME=$2
MODE_NAME=$3

echo "${SYSCODE}下的${APP_NAME}分布在${MODE_NAME}里面"

[root@bobo tmp]# sh test.sh caiwu reops kebank_uut
caiwu下的reops分布在kebank_uut里面
```
caiwu下的reops分布在kebank_uut里面

上面的例子中参数少还可以，但是如果脚本中使用的参数非常多的情况下，那使用上面这种方式就非常不合适，这样就无法清楚地记得每个位置对应的是什么参数！这个时候我们就可以使用bash内置的getopts工具了，用于解析shell脚本中的参数！下面就来看几个例子：
getopt:
```bash
[root@bobo tmp]# cat test.sh
#!/bin/bash

func() {
    echo "Usage:"
    echo "test.sh [-j S_DIR] [-m D_DIR]"
    echo "Description:"
    echo "S_DIR,the path of source."
    echo "D_DIR,the path of destination."
    exit -1
}

upload="false"

while getopts 'h:j:m:u' OPT; do
    case $OPT in
        j) S_DIR="$OPTARG";;
        m) D_DIR="$OPTARG";;
        u) upload="true";;
        h) func;;
        ?) func;;
    esac
done

echo $S_DIR
echo $D_DIR
echo $upload
```

 执行脚本
 
```bash
[root@bobo tmp]# sh test.sh -j /data/usw/web -m /opt/data/web 
/data/usw/web
/opt/data/web
false

[root@bobo tmp]# sh test.sh -j /data/usw/web -m /opt/data/web -u
/data/usw/web
/opt/data/web
true

[root@bobo tmp]# sh test.sh -j /data/usw/web 
/data/usw/web

false

[root@bobo tmp]# sh test.sh -m /opt/data/web                  

/opt/data/web
false

[root@bobo tmp]# sh test.sh -h
test.sh: option requires an argument -- h
Usage:
test.sh [-j S_DIR] [-m D_DIR]
Description:
S_DIR,the path of source.
D_DIR,the path of destination.

[root@bobo tmp]# sh test.sh j


false

[root@bobo tmp]# sh test.sh j m


false
```
getopts后面跟的字符串就是参数列表，每个字母代表一个选项，如果字母后面跟一个：，则就表示这个选项还会有一个值，比如上面例子中对应的-j /data/usw/web 和-m /opt/data/web 。而getopts字符串中没有跟随:的字母就是开关型选项，不需要指定值，等同于true/false,只要带上了这个参数就是true。

getopts识别出各个选项之后，就可以配合case进行操作。操作中，有两个"常量"，一个是OPTARG，用来获取当前选项的值；另外一个就是OPTIND，表示当前选项在参数列表中的位移。case的最后一项是?，用来识别非法的选项，进行相应的操作，我们的脚本中输出了帮助信息。

3）getopts示例二：当选项参数识别完成以后，就能识别剩余的参数了，我们可以使用shift进行位移，抹去选项参数。

```bash
[root@bobo tmp]# cat test.sh
#!/bin/bash
 
func() {
    echo "func:"
    echo "test.sh [-j S_DIR] [-m D_DIR]"
    echo "Description:"
    echo "S_DIR, the path of source."
    echo "D_DIR, the path of destination."
    exit -1
}
 
upload="false"
 
echo $OPTIND
 
while getopts 'j:m:u' OPT; do
    case $OPT in
        j) S_DIR="$OPTARG";;
        m) D_DIR="$OPTARG";;
        u) upload="true";;
        ?) func;;
    esac
done
 
echo $OPTIND
shift $(($OPTIND - 1))
echo $1
```
执行脚本：
```bash
[root@bobo tmp]# sh test.sh -j /data/usw/web beijing
1              #执行的是第一个"echo $OPTIND"
3              #执行的是第二个"echo $OPTIND"
beijing        #此时$1是"beijing"

[root@bobo tmp]# sh test.sh -m /opt/data/web beijing                 
1              #执行的是第一个"echo $OPTIND"
3              #执行的是第二个"echo $OPTIND"
beijing

[root@bobo tmp]# sh test.sh -j /data/usw/web -m /opt/data/web beijing
1              #执行的是第一个"echo $OPTIND"
5              #执行的是第二个"echo $OPTIND"
beijing

                  参数位置： 1        2       3       4        5     6
[root@bobo tmp]# sh test.sh -j /data/usw/web -m /opt/data/web -u beijing
6
beijing
```

在上面的脚本中，我们位移的长度等于case循环结束后的OPTIND - 1，OPTIND的初始值为1。当选项参数处理结束后，其指向剩余参数的第一个。getopts在处理参数时，处理带值的选项参数，OPTIND加2；处理开关型变量时，OPTIND则加1。

如上执行的脚本：1）第一个脚本执行，-j的参数位置为1，由于-j后面带有参数，即处理带值选项参数，所以其OPTIND为1+2=3；2）第二个脚本执行，-m参数位置为1，由于其后带有参数，所以其OPTIND也为1+2=3；3）第三个脚本执行，-m的参数位置 (观察最后一个参数的位置) 为3，由于其后面带有参数，所以其OPTIND为3+2=5；4）第四个脚本执行，-u参数位置为5，由于其后面不带参数，即为处理开关型变量，所以其OPTIND为5+1=6。
