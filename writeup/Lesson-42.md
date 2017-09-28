# Lesson-42
```

```

用户登陆界面 , 注册一个用户然后登陆后 , 进入主页 , 发现有修改密码的功能 : 
猜测SQL语句为 : 
```
SELECT id,password FROM users WHERE username='admin';
UPDATE users SET password = 'new_password' WHERE id='';
```
如果是这样的话 , 可以做文章的就是第二句 : 控制 `new_password` 这句
// 或者控制第一句 , 让选出来的ID被我们控制 , 比如说利用union select , 这个是突然想到的 , 不知道是否可行
// 第一句中的`username`不知道是不是可以被我们控制
分析到这里 , 用Burp抓包看看
// 还有突然想到 , 这里由于会把用户的信息打印出来 , 说不定登陆的时候也可能存在注入点 , 说不定可以越权访问
