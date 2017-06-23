---
title: shell中的运算
date: 2017-06-19 10:01:43
tags:
    - WORK
categories: SHELL
---

### 1. 常见的shell算术运算符
- \+ \- x / % ** \+\+ \-\-  加 减 乘 除 取 余 幂运算 增加 减少
- ! && ||  非 逻辑与 逻辑或
- 大于 小于 等于 大于等于 小于 等于（==）不等于（!==）赋值（=）
- \>\> \<\< 向右偏移，向左偏移

### 2. Shell 中常见的算术运算**命令**
- (())   用于整数运算的常用运算符，效率较高
- let    用于整数的运算，类似于“(())”
- expr   用于整数运算，还有很多其他额外功能（判断是否为整数）
- bc     Linux下自带的一个计算器程序（整数和小数计算）
- $[]    用于整数运算
- awk    awk既可以用于整数运算，也可用于小数运算
- declare 定义变量的属性，-i 参数就是定义整型变量

### 3. 使用举例
1. ((1+1))  可以使用成"echo ((1+1))" 或者 ((8>7&&5==5)) 或者 i=$((i+1))
提示： 在(()) 中使用变量可以将$符号省略
利用(())制作一个简单的计算器
```
#/bin/bash
if [ $# != 2 ]
then
echo "please input two args,Age: /bin/sh $0 10 5"
exit 1
fi
echo "$1+$2=$(($1+$2))"
echo "$1-$2=$(($1-$2))"
echo "$1*$2=$(($1*$2))"
echo "$1/$2=$(($1/$2))"
echo "$1**$2=$(($1**$2))"
echo "$1%$2=$(($1%$2))"
```
2. let运算命令用法
格式： let 赋值表达式  例如: let i=i+8 && echo $1
这个用法等同于((i+8)), (())这种方式效率更高
例子: 使用let，对web服务页面进行监控
在这里我们使用`wget` 命令可以**检测某网页是否能正常访问**
```
#!/bin/bash
checkurl(){
timeout=5
fails=0
success=0
while true #死循环，一直检测
do
wget --timeout=$timeout --tries=1 www.baidu.com -q -O /dev/null
echo $fails $success
if [ $? -ne 0 ]   #返回值不为0 就表示失败
then
        let fails=fails+1     #可用((fails=fails+1))代替
echo $fails 
else
        let success=success+1  #返回为0，代表访问成功
echo $success
fi
if [ $success -ge 1 ]
then
echo 'sys is open!'
exit 0
fi
if [ $fails -ge 2 ]
then
echo "sys is down!"
#mail -s "sys is down" root@localhost  #如果访问失败次数大于等于2，就发邮件告警
exit 2
fi
done
}
ehceckurl #执行函数
```
3. expr 命令用法
格式： expr(evaluate(求值) expressions(表达式)) 它既可用于整数，也可小数
```
 expr 2 + 2  #数字作用至少有一个空格，使用乘号，需要使用\转义 
 4
 i=`expr $i + 6`
```
使用expr判断变量是否是一个整数
```
#!/bin/bash
read -p "input a number:" a
expr $a + 1 >/dev/null 2>&1
[ $? -eq 0 ] && echo "$a is a int" || echo "$a is a chars"

 ```
提示：也可以使用expr match 功能进行整数判断，可执行 man expr 获得帮助
4. 利用expr 判断扩展名是否符合要求
```
#!/bin/sh
if expr "$1" : ".*\.pub" &>/dev/null
then
echo "your are useing $1"
else
echo "your use *.pbu file"
fi
```
5. 利用expr 计算字符串的长度
```
[admin@web_server ~]$ char='pengke'
[admin@web_server ~]$ expr length '$char'
5
```
6. 利用bc命令来计算，它是Linux下面的计算器
   按下bc后，就能进入运算界面。或者：
   `echo 3+5 | bc`
7. 利用sed 生成连续的数字序列
```
sed -s "+" 10      # -s 是自定义分隔符
1+2+3+4+5+6+7+8+9+10
echo  {1..10} | tr " " "+"   #生产空格分隔的数列，用tr将空格替换成+
1+2+3+4+5+6+7+8+9+10
```
8. 利用awk实现运算
echo "7.7 3.8" | awk '{print ($1-$2)}'

### 4. 总结
大多运算命令都支持整数，支持小数的有：expr 和awk 
expr 和 awk 都是很重要的命令


