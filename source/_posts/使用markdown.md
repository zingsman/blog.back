---
title: 使用markdown
date: 2017-06-08 11:34:45
tags:
  - WORK
categories: HELP
---
# Markdown简明语法手册
### 1. 斜体和粗体
使用\* 和 \*\* 表示斜体和粗体，用\*或\*\*包起来
例子：
这是 *斜体*，这是**粗体**
### 2. 分级标题
使用 # 代表一级标题， ## 代表二级标题， ### 代表三级标题，一次类推。
例子：
```
# 这是一个一级标题
## 这是一个二级标题
### 这是一个三级标题
```
注意： Markdown一共支持六级标题。
### 3. 外连接
使用 \[描述](链接地址) 为文字添加外链接。
图片为：\!\[](){ImgCap}{/ImgCap}
链接为：\[]()
例子：
这是给大家推荐的淘宝内部券网站: [嗨购王](www.higouwang.com) 的链接。

### 4. 无序列表
使用 * , - ,表示无须列表
例子：
- 无序列表项 一
- 无序列表项 二
- 无序列表项 三

### 5. 有序列表
使用数字和点表示有序列表，符号要和文字之间加上一个字符的空格。
例子：
1. 有序列表项 一
2. 有序列表项 二
3. 有序列表项 三 

### 6. 分割线
分割线的语法只需要三个 * 号。例如
你愿意和我一起学习吗？
***
我们去小树林玩耍可好～

### 7. 文字引用
使用 > 表示文字引用。注意符合后面的一个空格。应用完有一条**空行**。
示例：
> 野火烧不尽，春风吹又生。

### 8. 行内代码块
使用 \`代码\` 表示行内代码块。
示例：
让我们聊聊 `html` 你能教给我吗？
注意：使用 `tab` 可缩进
### 9. 插入图像
使用 \!\[描述](图片链接地址) 插入图像。
图片为：\!\[](){ImgCap}{/ImgCap}
链接为：\[]()
例子：
![美女](http://pke-data.oss-cn-shenzhen.aliyuncs.com/image/avatar.png)
### 11. 加强的代码块
使用 \`\`\` 是基本高亮
使用 \`\`\`python 是针对语言高亮
例如：
```shell
#/bin/bash
ip=$(cat aa)
for a in ip
do
        ping -c 1 -w 3 $ip$a &> /dev/null
        if [ $? == 0 ];then
        echo "$ip is OK!"
        else
        echo -e "\e[1;31m $ip is NO! \e[0m"
        fi
done
```
