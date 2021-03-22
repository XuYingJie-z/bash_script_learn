# bash脚本中if判断语句

```bash
#!/bin/sh
SYSTEM=`uname -s`    #获取操作系统类型，我本地是linux
if [ $SYSTEM = "Linux" ] ; then     #如果是linux的话打印linux字符串
echo "Linux"
elif [ $SYSTEM = "FreeBSD" ] ; then  
echo "FreeBSD"
elif [ $SYSTEM = "Solaris" ] ; then
echo "Solaris"
else
echo "What?"
fi     #ifend
```

1. 字符串判断

str1 = str2　　　　　　当两个串有相同内容、长度时为真<br>
str1 != str2　　　　　 当串str1和str2不等时为真 <br>
-n str1　　　　　　　 当串的长度大于0时为真(串非空) <br>
-z str1　　　　　　　 当串的长度为0时为真(空串) <br>
str1　　　　　　　　   当串str1为非空时为真 <br>

2. 数字的判断

int1 -eq int2　　　　两数相等为真 <br>
int1 -ne int2　　　　两数不等为真 <br>
int1 -gt int2　　　　int1大于int2为真 <br>
int1 -ge int2　　　　int1大于等于int2为真 <br>
int1 -lt int2　　　　int1小于int2为真 <br>
int1 -le int2　　　　int1小于等于int2为真 <br>

3. 文件的判断

` if [ ! -f "/usr/bin/svnserve" ] `  注意签好的空格，此处语句为判断文件是否存在

-r file　　　　　用户可读为真 <br>
-w file　　　　　用户可写为真 <br>
-x file　　　　　用户可执行为真  <br>
-f file　　　　　文件为正规文件为真  <br>
-d file　　　　　文件为目录为真  <br>
-c file　　　　　文件为字符特殊文件为真  <br>
-b file　　　　　文件为块特殊文件为真 <br>
-s file　　　　　文件大小非0时为真  <br>
-t file　　　　　当文件描述符(默认为1)指定的设备为终端时为真 <br>

4. 多个条件并列,及分支语句,&&为并，||为或

```bash
#!/bin/bash
read -p "请输入考试成绩：" insert 
if [ $insert -ge 85 ] && [ $insert -le 100 ]  <!--85~100分，优秀-->
  then
    echo "恭喜您考试成绩为优秀！！！"
elif [ $insert -ge 70 ] && [ $insert -le 84 ] <!--70~84分，合格-->
  then
    echo "恭喜您考试成绩为合格！！！"
else     <!--其他分数，不合格-->
    echo "很遗憾您考试成绩可以收拾收拾回家种苞米了！！！"
fi      <!--if语句结束-->

```


