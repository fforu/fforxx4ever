# 类与对象：

```php
<?php
class person {
/* 成员变量 */
var $name;
var $age; /* 成员函数 */
function setName($name){
        $this->name = $name;
}

function setAge($age){
        $this->age = $age;
}
function getName(){
        echo $this->name . PHP_EOL;
}
function getAge(){
        echo $this->age . PHP_EOL;
}
}

$a = new person();
$a->setName("lili");
$a->setAge(111);
$a->getName();
$a->getAge();

?>
```

## 1.构造函数：
//对象被创建时调用
```php
function __construct( $name, $age ) {
    $this->name = $name;
    $this->age = $age;
}
```

## 2.析构函数：
//对象被摧毁后调用
```php
function __destruct() {
    print "销毁 " . $this->name . "\n";
}
```

## 3.继承：
//继承父类后，具备父类的大部分方法与变量
```php
class Child extends Person{

}
```

## 4. 方法重写：
// 重写父类的方法
```php
class child extends person{
    function getName()
    {
        print $this->name . "是个傻逼";
    }
}
```

## 5. 访问控制：
public（公有）：公有的类成员可以在任何地方被访问。
protected（受保护）：受保护的类成员则可以被其自身以及其子类和父类访问。
private（私有）：私有的类成员则只能被其定义所在的类访问。

```php
<?php
class Person1
{
    // 声明一个公有的构造函数
    public function __construct()
    {

    }

    // 声明一个公有的方法
    public function func1()
    {
        echo '这里是公有方法' . PHP_EOL;
    }

    // 声明一个受保护的方法
    protected function func2()
    {
        echo '这里是收保护的方法' . PHP_EOL;
    }

    // 声明一个私有的方法
    private function func3()
    {
        echo '这里时私有方法' . PHP_EOL;
    }

    // 此方法为公有
    function test()
    {
        $this->func1(); // 正常执行
        $this->func2(); // 正常执行 protected受保护的方法,可以被自身调用以及子类调用
        $this->func3();// 正常执行 private私有方法,可以被自身其他方法调用，子类不行
    }
}
$class = new Person1();
$class->func1(); // 正常执行
# $class->func2(); // 被protected修饰的方法 只能在类方法中被调用#
//$class->func3(); // 被private修饰的方法 只能在类方法中被调用
$class->test(); // 公有，受保护，私有都可以执行
class Superman extends Person1
{
// 此方法为公有
function test2()
{
$this->func1();
$this->func2(); // 正常执行 protected受保护的方法,可以被自身调用以及子类调用
//$this->func3(); // 不能正常执行 private私有方法,可以被自身其他方法调用，子类不行
}
}

$myclass2 = new Superman();
$myclass2->func1();
$myclass2->test(); // 这行能被正常执行
$myclass2->test2(); // 公有的和受保护的都可执行，但私有的不行
```

## 6.接口
接口就只是声明有这个方法，方便程序员查看，不能写方法功能

```php
// 声明一个'interfaceDemo'接口
interface interfaceDemo
{
    public function echo_flag($flag);
//不能添加已经被实现的方法
}
// 实现接口 ,关键词implements
class Demo implements interfaceDemo
{
    public function echo_flag($flag)
    {
        echo $flag;
    }
}

$a = new Demo();
$a->echo_flag(234);
```

## 7. 常量
```php
class MyClass
{
const constant = '常量值';

function showConstant() {
    echo self::constant . PHP_EOL;
 }
}
```

## 8.抽象类
抽象类要求子类必须有抽象类定义的方法
```php
abstract class AbstractClass
{
    abstract protected function echo_flag($flag2);
    public function echo_flag2($flag2){
        echo $flag2;
    }
}
class Demo extends AbstractClass
{
    public function echo_flag($flag){
        echo $flag;
    }
}

$a = new Demo();
$a->echo_flag(123);
```
此抽象类定义了echo_flag，所以Demo子类必须包含echo_flag，不然会报错

## 9. Static
静态方法
静态类可以直接调用[CLASS::FUNCTION()],而不需要创建一个新的对象
```php
<?php
class Foo {
    public static $my_static = 'foo';

    public function staticValue() {
        return self::$my_static;
    }

    public static function  echo_flag($a){
        echo $a;
    }
}
print Foo::$my_static . PHP_EOL;
$foo = new Foo();
print $foo->staticValue() . PHP_EOL;
$foo->echo_flag(123);
print Foo::echo_flag(123);
```

## 10. Final
如果父类中的方法被声明为 final，则子类无法覆盖该方法。如果一个类被声明为 final，则不能被继承。
```php
class BaseClass {
    public function test() {
        echo "BaseClass::test() called" . PHP_EOL;
    } 
    final public function moreTesting() {
        echo "BaseClass::moreTesting() called" . PHP_EOL;
    }
}

class ChildClass extends BaseClass {
    public function moreTesting() {
        echo "ChildClass::moreTesting() called" . PHP_EOL;
    }
} // 报错信息 Fatal error: Cannot override final method BaseClass::moreTesting()
```

## 11. 调用父类构造方法

```php
class BaseClass {
    function __construct() {
        print "BaseClass 类中构造方法" . PHP_EOL;
    }
}
class SubClass extends BaseClass {
    function __construct() {
        parent::__construct(); // 子类构造方法不能自动调用父类的构造方法
        print "SubClass 类中构造方法" . PHP_EOL;
    }
}
class OtherSubClass extends BaseClass {
 // 继承 BaseClass 的构造方法
}

 // 调用 BaseClass 构造方法
$obj = new BaseClass();

// 调用 BaseClass、SubClass 构造方法
$obj = new SubClass();

// 调用 BaseClass 构造方法
$obj = new OtherSubClass();
```

## 12. 魔术方法

```php
__construct()
实例化类时自动调用

__destruct()
类对象使用结束时自动调用

__set()
在给未定义的属性赋值时自动调用

__get()
调用未定义的属性时自动调用

__isset()
使用 isset() 或 empty() 函数时自动调用

__unset()
使用 unset() 时自动调用

__sleep()
使用 serialize 序列化时自动调用

__wakeup()
使用 unserialize 反序列化时自动调用

__call()
调用一个不存在的方法时自动调用

__callStatic()
调用一个不存在的静态方法时自动调用

__toString()
把对象转换成字符串时自动调用

__invoke()
当尝试把对象当方法调用时自动调用

__set_state()
当使用 var_export() 函数时自动调用，接受一个数组参数

__clone()
当使用 clone 复制一个对象时自动调用

__debugInfo()
使用 var_dump() 打印对象信息时自动调用

__autoload()
尝试加载未定义的类
```

## 13. 魔术常量


1.__LINE__
```php
<?php
    echo '这是第 " ' . __LINE__ . ' " 行';
?>
```

2.__FILE__
```php
<?php
    echo '该文件位于 " ' . __FILE__ . ' " ';
?>
```

3. __DIR__
```php
<?php
    echo '该文件位于 " ' . __DIR__ . ' " ';
?>
```

4. __FUNCTION__
```php
<?php
function test() {
    echo '函数名为：' . __FUNCTION__ ;
}
```

5. __CLASS__
```php
<?php
class test {
    function _print() {
    echo '类名为：' . __CLASS__ . "<br>";
    echo '函数名为：' . __FUNCTION__ ;
}
}
```

6.__TRAIT__
返回当前 trait 的名称。Traits 是一种代码复用机制
```php
<?php
class Base {
public function sayHello() {
    echo 'Hello ';
}
}
trait SayWorld {
public function sayHello() {
    parent::sayHello();
    echo 'World!';
}
}
class MyHelloWorld extends Base {
    use SayWorld;
}
    $o = new MyHelloWorld();
    $o->sayHello();
?>
```
7. __METHOD__
```php
<?php
function test() {
    echo '函数名为：' . __METHOD__ ;
}
test();
```
8. __NAMESPACE__
```php
<?php
namespace MyProject;
    echo '命名空间为："', __NAMESPACE__, '"'; // 输出 "MyProject"
?>
```


## 命名空间
1. 定义命名空间
   
```php
<?php
/////////////////////////////////////
namespace Space1; 
const flag = 1;
function func() {
    echo '这里是Space1';
}
/////////////////////////////////////
namespace Space2;
const flag = 2;
function func() {
    echo '这里是Space2';
}
// 默认会使用前面的命名空间对应的函数
func();
// 选择命名空间调用函数
\Space1\func();
\Space2\func();
?>
```

2. 子命名空间
子命名空间是通过分层次的命名空间来组织代码。它们允许你将相关的代码分组在一起，从而创建更有条理的代码结构。子命名空间可以看作是主命名空间中的子集或子目录。
```php
<?php
namespace Space1;
function func() {
    echo '这里是Space1';
}

namespace Space1\Son1;
function func() {
    echo '这里是Son1';
}

namespace Space1\Son2;
function func() {
    echo '这里是Son2';
}

func();
\Space1\Son1\func();
\Space1\func();
?>

```

3. 使用命名空间
```php
<?php
namespace Space1;
// 包含一个文件，可以调用这个文件中的类、变量、方法等。
// 但由于 PHP 无法直接区分哪些代码是哪个文件中的，
// 因此必须设置一个命名空间，
// 否则不同文件中的类、变量、方法等就会混淆。
include 'demo.php';

function func() {
    echo '这里是Space1';
}

namespace Space1\Son1;
// 声明分层次的单个命名空间
function func() {
    echo '这里是Son1';
}

namespace Space1\Son2;
// 声明分层次的单个命名空间
function func() {
    echo '这里是Son2';
}

namespace Space1\Son2\grandson;
// 声明分层次的单个命名空间
function func() {
    echo '这里是grandson';
}

func(); // 由于当前命名空间是 Space1\Son2\grandson，调用的 func() 是 Space1\Son2\grandson 命名空间中的函数，输出 "这里是grandson"。
\Space1\Son2\func(); // 调用 Space1\Son2 命名空间中的 func() 函数，输出 "这里是Son2"。
\Space1\Son1\func(); // 调用 Space1\Son1 命名空间中的 func() 函数，输出 "这里是Son1"。
\Space1\func(); // 调用 Space1 命名空间中的 func() 函数，输出 "这里是Space1"。
?>
```

4. 动态语言特征
运行时定义：在动态语言中，命名空间可以在运行时动态地定义和修改。这意味着您可以根据需要创建、修改或删除命名空间，而不需要在编译时确定。
命名空间嵌套：动态语言的命名空间支持嵌套结构，允许创建多层次的命名空间。这使得代码的组织更加灵活，可以按照逻辑层次或模块化的方式组织代码。
动态导入和使用：动态语言的命名空间支持动态导入和使用其他命名空间中的代码。这意味着您可以在运行时根据需要引入其他命名空间的定义，以便使用其中的类、函数、常量等。
避免命名冲突：动态语言的命名空间提供了一种避免命名冲突的机制。通过将代码放置在适当的命名空间中，可以确保不同模块或库中的相同名称不会冲突，从而增加了代码的可维护性和可扩展性。
test3.php
```php
<?php
namespace Space2;

function func() {
    echo '这里是Space2';
}

namespace Space2\son;

const DEMO = '这是一个常量';

function func() {
    echo '这里是Space2的son';
}

class classDemo {
    public function __construct() {
        echo 'classDemo被创建';
    }
}
```

test2.php
```php
namespace Space1;

// 包含一个文件，可以调用这个文件中的类、变量、方法...
// 但由于 PHP 无法直接区分哪些代码属于哪个文件，
// 因此必须设置一个命名空间，
// 否则不同文件中的类、变量、方法... 就会混淆
include 'test3.php';

$a = '\Space2\son\DEMO';
echo constant($a);

$b = '\Space2\son\func';
$b();

$c = '\Space2\son\classDemo';
$d = new $c;
```
输出：
这是一个常量 这里是Space2的son classDemo被创建

5. __NAMESPACE__
```php
namespace Space;
// 输出当前命名空间的名字 "Spacet"
echo '"', __NAMESPACE__, '"';
```

6. 别名/导入
```php
namespace Space1;

// 包含一个文件，可以调用这个文件中的类、变量、方法...
// 但由于 PHP 无法直接区分哪些代码属于哪个文件，
// 因此必须设置一个命名空间，
// 否则不同文件中的类、变量、方法... 就会混淆
include 'test3.php';

$a = '\Space2\son\DEMO';
echo constant($a);

$b = '\Space2\son\func';
$b();

use Space2\son\classDemo as Demo;
$a = new Demo();
```