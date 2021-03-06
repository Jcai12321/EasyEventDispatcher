# 简介
这是一个php的简单事件分发器, 其中 Listener 支持如下类型:
- \Closure
- callable
	> `is_callable()` 返回true
- Class implements ListenerInterface
- string(类名)
- string(类型@方法)

# EventDispatcher 接口
```php
interface EventDispatcherInterface
{
    /**
     * @param string         $event string 事件名称
     * @param \Closure|mixed $listener
     *
     * @return mixed
     */
    public function listen($event, $listener);

    /**
     * @param EventInterface|string $event
     * @param array                 $payload
     * @param bool                  $halt
     *
     * @return mixed
     */
    public function dispatch($event, $payload = [], $halt = false);
}
```

# 示例
## 简单示例1
```php
require "vendor/autoload.php";

$dispatcher = new \EasyEvent\EventDispatcher();

$dispatcher->listen("test", function ($msg, $event) {
    return "event \"$event\" with msg \"$msg\" handled.";
});

$dispatcher->listen("test", function ($msg, $event) {
    return "listener2";
});

// 返回字符串 'event "test" with msg "hello" handled.'
var_dump($dispatcher->dispatch("test", "hello", true));

// 返回数组 ['event "test" with msg "hello" handled.', 'listener2']
var_dump($dispatcher->dispatch("test", "hello"));
```

## 简单示例2
```php
require "vendor/autoload.php";

class TestObj
{
    public function test($arr)
    {
        return implode(', ', $arr);
    }

    public static function staticTest($arr)
    {
        return implode(', ', $arr);
    }
}

$dispatcher = new \EasyEvent\EventDispatcher();
$dispatcher->listen("event", [new TestObj(), "test"]);
$dispatcher->listen("event", "TestObj@test");
$dispatcher->listen("event", ["TestObj", "test"]);

var_dump($dispatcher->dispatch("event", [['a', 'b', 'c']]));
```