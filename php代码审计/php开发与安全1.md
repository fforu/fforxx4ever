## 基于前端的登录

```php
<!DOCTYPE html>
<html>
<head>
    <title>简单登录功能</title>
    <meta charset="UTF-8">
</head>
<body>
<h1>登录</h1>
<form id="loginForm" onsubmit="return login()">
    <label for="username">用户名:</label>
    <input type="text" id="username" required>
    <br>
    <label for="password">密码:</label>
    <input type="password" id="password" required>
    <br>
    <input type="submit" value="登录">
</form>
<p id="message"></p>

<script>
    function login() {
        var username = document.getElementById("username").value;
        var password = document.getElementById("password").value;

        // 这里应该发送登录请求到服务器进行身份验证
        // 在这个例子中，我们只是简单判断用户名和密码是否符合预定义值

        // 假设正确的用户名和密码是：
        var validUsername = "admin";
        var validPassword = "admin";

        if (username === validUsername && password === validPassword) {
            document.getElementById("message").innerText = "登录成功！";
        } else {
            document.getElementById("message").innerText = "登录失败，请检查用户名和密码！";
        }

        // 阻止表单的默认提交行为
        return false;
    }
</script>
</body>
</html>
```
此时审查前端源代码就可以得到密码信息，因为都是前端代码

## 后端sql数据连接登录，存在sql注入
```php
<?php
$servername = "localhost";
$username = "root";
$password = "123456";
$dbname = "demo1";

// 建立数据库连接
$conn = mysqli_connect($servername, $username, $password, $dbname);
if (!$conn) {
    die("连接失败：" . mysqli_connect_error());
}

// 获取表单数据
$username = $_POST["username"];
$password = $_POST["password"];

// 对用户名进行安全处理，防止SQL注入
//$username = mysqli_real_escape_string($conn, $username);
$username = stripslashes($username);
// 查询用户信息
$sql = "SELECT username FROM users WHERE password = '$password '";
$result = mysqli_query($conn, $sql);
echo $sql;

if (!$result) {
    die("查询错误：" . mysqli_error($conn));
}

$row = mysqli_fetch_assoc($result);
if ($row) {
    $usernamecheck = $row["username"];
    // 验证密码
    if ($usernamecheck == $username ) {
        // 登录成功
        echo "登录成功！欢迎，" . htmlspecialchars($username);
    } else {
        // 登录失败
        echo "无效的用户名或密码，请重试。";
    }
} else {
    echo "用户不存在，请重试。";
}

// 关闭数据库连接
mysqli_close($conn);
?>
```
此处查询语句明显存在sql注入
username=admin&password=1'+or+1%3d1%23
直接进

## 基于数据库查询的登录功能（链式调用）

```php
<?php
class DatabaseQuery
{
    private $connection;
    private $sql;

    public function __construct($connection)
    {
        $this->connection = $connection;
    }

    public function select($columns)
    {
        $this->sql = 'SELECT ' . $columns;
        return $this;
    }

    public function from($table)
    {
        $this->sql .= " FROM $table";
        return $this;
    }

    public function where($column)
    {
        $this->sql .= " WHERE $column";
        return $this;
    }

    public function eq($value)
    {
        $this->sql .= " = '$value'";
        return $this;
    }

    public function andWhere($column)
    {
        $this->sql .= " AND $column";
        return $this;
    }

    public function execute()
    {
        // 准备查询语句
        $stmt = mysqli_prepare($this->connection, $this->sql);
        if (!$stmt) {
            die("查询错误：" . mysqli_error($this->connection));
        }

        // 绑定参数值
        if (!empty($this->bindParams)) {
            $types = str_repeat('s', count($this->bindParams)); // 指定参数类型为字符串
            mysqli_stmt_bind_param($stmt, $types, ...$this->bindParams);
        }

        // 执行查询
        mysqli_stmt_execute($stmt);

        // 获取结果
        $result = mysqli_stmt_get_result($stmt);

        // 返回结果
        return $result;
    }
}

// 示例使用
$servername = "localhost";
$username_db = "root";
$password_db = "root";
$dbname = "demo";

$conn = mysqli_connect($servername, $username_db, $password_db, $dbname);
if (!$conn) {
    die("连接失败：" . mysqli_connect_error());
}

$username = $_POST["username"];
$password = $_POST["password"];

$dbQuery = new DatabaseQuery($conn);
$result = $dbQuery->select('username')
    ->from('users')
    ->where('password')->eq($password)
    ->execute();

if (!$result) {
    die("查询错误：" . mysqli_error($conn));
}

while ($row = mysqli_fetch_assoc($result)) {
    echo "Username: " . $row['username'] . "<br>";
}

mysqli_close($conn);
?>
```
php的特性在于可以直接通过->调用函数，在sql注入是可以采用->链接各个查询部分
区别在于链式调用将sql查询语句的每个部分都写了方法，在使用sql查询时直接调用方法即可，相比于直接查询更加方便、安全
对比
```php
$result = $dbQuery->select('username')
    ->from('users')
    ->where('password')->eq($password)
    ->execute();


$sql = "SELECT username FROM users WHERE username = '$password'";
$result = mysqli_query($conn, $sql);
```

## 预编译处理sql注入

```php
if ($_SERVER["REQUEST_METHOD"] === "POST") {
    // 从表单中获取用户名和密码
    $username = $_POST["username"];
    $password = $_POST["password"];

    // 连接到MySQL数据库
    $conn = new mysqli("localhost", "root", "root", "demo");
    if ($conn->connect_error) {
        die("连接失败: " . $conn->connect_error);
    }

    // 准备SQL查询语句
    $stmt = $conn->prepare("SELECT password FROM users WHERE username = ?");
    // 将参数插入空位
    $stmt->bind_param("s", $username);
    // 执行SQL语句
    $stmt->execute();
    //存入数据
    $stmt->store_result();
    // 将结果进行绑定
    $stmt->bind_result($hashedPassword);
    // 获取结果
    $stmt->fetch();

    
    // 验证密码
    if ($stmt->num_rows > 0 && ($password == $hashedPassword)) {
        // 登录成功
        echo "登录成功！欢迎，" . htmlspecialchars($username);
        // 在此处可以执行其他操作或重定向到另一个页面
    } else {
        // 登录失败
        echo "无效的用户名或密码，请重试。";
        // 在此处可以重定向回登录页面或显示错误消息
    }

    // 关闭语句和连接
    $stmt->close();
    $conn->close();
}
```