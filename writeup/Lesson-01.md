# Lesson-01
```
明注-,-Bool盲注-,-时间盲注-,-报错注入
```

---
打开主页进入第一关 : 

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-4fb127751bf85e39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

提示传入id这个参数

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-b4dda3aaff925d7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

发现这个页面会根据传入的ID查询到对应的用户
这里我们可以通过查看数据库进行验证 : 

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-8bc323ff0d75e3c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这样就可以推断出PHP执行的SQL语句的功能就是从数据库中查询出id为我们传入的id的那个用户 , 并且把得到的数据中的用户名和密码显示出来
我们猜测SQL语句可能是这样 : 
```SQL
SELECT username,password FROM `users` WHERE id = $_GET['id'];
```
但是我们猜测的这个SQL语句并不一定正确
因为SQL语句在执行的时候 , 对语法有一定的要求 , 但是并不是特别严格 , 例如 : 
```
SELECT username,password FROM `users` WHERE id = '$_GET['id']';
SELECT username,password FROM `users` WHERE id = "$_GET['id']";
```
这两种都是可以查询到数据的
因此我们如果要攻击这个网站的话 , 就要想办法猜测出这个页面的SQL语句是怎么写的
要猜测属于上面的哪一种情况 , 这样我们才可以将我们自己精心构造的SQL语句注入到正常的参数里面
让我们的SQL语句的到执行 , 达到我们的目的(读出本来我们不能读取的内容 , 或者对数据库进行增/删/改/查的操作 , 或者利用数据库软件读取或者写入服务器上的文件 ... )
我们可以先在本地的MySQL中对SQL语句进行测试 : 
```
SELECT username,password FROM `users` WHERE id = 1;
```


![图片.png](http://upload-images.jianshu.io/upload_images/2355077-494046fbda32332a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-55f6c5d096cc27ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-0070253fa69d57b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么如果当我们输入的参数id并不是按照程序员想的是一个整数呢 ?
如果我们输入一个字母/一个符号会怎么样 ?

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-2a5a8fc68211f6d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-9b173663d2401fbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个时候 , 当我们输入的参数是 `1'`
发现这个页面报错了 , 报错的内容是 : 
```
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''1'' LIMIT 0,1' at line 1
```
根据这个报错 , 我们可以看到它显示出来了一部分SQL语句 : 
```SQL
'1'' LIMIT 0,1
```
而这其中的`1’`
就是我们刚才输入的参数
那么我们就可以继续对刚才的猜测进行完善
可以看到我们输入的参数`1'`是被两个单引号包裹起来的
而且在后面还对查询的结果集进行了切片 , 这样查询的结果就只有一条
```
SELECT username,password FROM `users` WHERE id = '$_GET['id']' LIMIT 0,1;
```
那么刚才我们将畸形的参数传入以后 , 拼接得到的SQL语句就应该是 : 
```
SELECT username,password FROM `users` WHERE id = '1'' LIMIT 0,1;
```
我们放到MySQL里面执行一下

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-28cc1b6274cbf972.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
发现当我们输入的单引号不匹配的时候 , MySQL的客户端会一直等待用户闭合这个单引号
但是当使用PHP去操作MySQL的时候 , 因为用户是通过PHP对数据库进行操作 , 所以不可能像我们刚才那样存在一个交互的界面 , 因此就这个单引号就不可能被闭合 , 因此这个时候就会报错 , 也就是刚才我们得到的这个错误
这个时候 , 如果我们继续修改一下查询的id这个参数
这里可以尝试使用MySQL定义的注释关键字
> -- 
> #
> /**/

需要说明一下 , 第一个是两个`-`连接符后面紧跟着一个空格
前两个注释符是单行注释 , 第三个是多行注释
如果我们在id这个参数后面添加了注释的时候 : 
SQL语句变成这样 : 
```
SELECT username,password FROM `users` WHERE id = '1'-- ' LIMIT 0,1;
SELECT username,password FROM `users` WHERE id = '1'#' LIMIT 0,1;
```

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-4eaf2fbd2e2a6cbb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-4994be37fec858c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
都是可以正常得到查询的结果
说明后面的SQL语句已经被我们注释掉了
这样的话 , 如果我们刚才猜测的后台的SQL语句是正确的
如果我们传递参数为 : 
```
http://127.0.0.1/Less-1/?id=1%27--+
http://127.0.0.1/Less-1/?id=1%27%23
```
注意这里我们没有使用到原本的字符`'`和`#`
而是将它们URL编码以后再进行参数的传递 , 这里是因为服务器再接收到参数以后会对参数进行一次URL解码
这样的话解码之后刚好就可以拼凑成正常的SQL语句
还有一个需要注意的地方就是 : 
1. 为什么是`--+`而不是`-- `
> 这里字符`-`和字符`+`在URL中都是有固定的含义的 , 比如说`+`就在URL编码中就代表空格 , 而URL编码中`-`不用编码
2. 为什么`--+`没有被URL编码
> 由于这里我们是用`+`代替了` ` , 因此不需要进行编码 , 我们也可以不用`+` 而使用空格的URL编码 , 那么编码得到的URL就应该是 : 
```
http://127.0.0.1/Less-1/?id=1%27--%20
```
3. `#` 又为什么必须得编码 , 不编码可以吗 ? 
> 不可以 , 因为`#` 在URL中是有固定的含义的 , 表示页面中的锚点 , 如果不进行编码浏览器就会将其当成页面的锚点 , 而这里我们是需要将其作为数据传输给服务器的 , 因此需要进行URL编码
4. 为什么不用多行注释来注释后面的SQL语句
> 因为多行注释的格式是 : 
```
/*注释内容*/
```
在注释内容的前后都需要有标记
而这里我们只能控制SQL语句的一个位置 , 也就是输入id的地方
所以这样我们并不能构成一个正确的多行注释 , 因此不可以 , 大家也可以自己尝试一下 , 会直接报错(语法错误)

好了 , 现在我们尝试访问一下 : 
```
http://127.0.0.1/Less-1/?id=1%27--+
http://127.0.0.1/Less-1/?id=1%27--%20
http://127.0.0.1/Less-1/?id=1%27%23
```

![图片.png](http://upload-images.jianshu.io/upload_images/2355077-ebe0e9f84350bc00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

就可以正常查询出数据 , 而且是所有匹配的数据(因为注释掉了`LIMIT 0,1`)
到这里 , 我们就可以肯定这个网站的这个PHP文件的id这个参数 , 是存在注入点的
接下来 , 我们就要利用这个注入点将其数据库中的数据一步一步查询出来

编辑中...
-----------

http://127.0.0.1/Less-1/?id=0%27union%20select%201,2,3--+
http://127.0.0.1/Less-1/?id=0%27union%20select%201,group_concat(schema_name),2%20from%20information_schema.schemata--+
