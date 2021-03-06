# 简介
简单且快速Dingtalk SDK for PHP

## 安装
```sh
composer require liupei\dingtalk 
```

## 如何使用
下面介绍几种常规姿势

### 配置
```php
include 'vendor/autoload.php';

// 最低配置
liupei\dingtalk\Client::config([
    'type' => 'corp', // corp:企业内部开发
    'app_key' => 'xxxxxxx', // 钉钉微应用对应的app_key
    'app_secret' => 'xxxxxxxxxxxx', // 钉钉微应用对应的app_secret
    'corp_id' => 'XXXXXXXXXX', // 企业id
    'agent_id' => 'XXXXXXXXXXXXX', // agent_id
]);

// 标准配置（带缓存配置）
//为什么要带缓存配置？每次请求接口都从钉钉拿access_token这样不好吧，配置缓存后后续请求可以从缓存拿……好了不能透露太多
liupei\dingtalk\Client::config([
    'type' => 'corp', // corp:企业内部开发
    'app_key' => 'xxxxxxxxxxxxx', // 钉钉微应用对应的app_key
    'app_secret' => 'xxxxxxxxxxxxxxxxxx', // 钉钉微应用对应的app_secret
    'corp_id' => 'XXXXXXXXXXXXXXX', // 企业id
    'agent_id' => 'XXXXXXXXXXX', // agent_id
    'cache' => [       // 支持redis等方案
        'host'   => '127.0.0.1',
        'port'   => 6379,
        'select' => 0,
        'password' => '',
        'prefix' => 'dingtalk_'
    ]
]);

```


### 注册hook事件
```php
// 发送请求前事件
liupei\dingtalk\Client::event('before_request', function ($data) {
   var_dump($data);
});
//发送请求后事件
liupei\dingtalk\Client::event('after_request', function ($data) {
    var_dump($data);
});
```

### 调用接口
```php
// 模拟get请求
$client = liupei\dingtalk\Client::newClient();
$response = $client
    ->withAccessToken(true) // true: 自动获取access_token并且自动缓存（需配置缓存），否则需传入access_token
    ->path('/user/get') // 请求钉钉接口路径
    ->queryParam(['userid' => '0447185746671825']) // 设置get参数
    ->request();
var_dump($response->isSuccess());
var_dump($response->getData());
var_dump($response->getErrMsg());

// 模拟post请求
$client = liupei\dingtalk\Client::newClient();
$response = $client
    ->withAccessToken(true) // true: 自动获取access_token并且自动缓存（需配置缓存），否则需传入access_token
    ->path('/user/get') // 请求钉钉接口路径
    ->queryParam(['userid' => '0447185746671825']) // 设置get参数
    ->postParam(['feild' => 'xxxxxx'])
    ->request(); // 会自动识别get,post
var_dump($response->isSuccess());
var_dump($response->getData());
var_dump($response->getErrMsg());
```

### 钉钉机器人调用
```php
include 'vendor/autoload.php';

// 默认方式实例化
$robot = new liupei\dingtalk\Robot('access_token');
// 使用签名方式实例化
$robot = new liupei\dingtalk\Robot('access_token','secret');

// 向机器人群发送消息，消息格式参考链接：https://ding-doc.dingtalk.com/doc#/serverapi3/iydd5h
$response = $robot->send([
    "msgtype" => "text",
    "text" => [
        "content" => "测试内容"
    ]
]);
var_dump($response->isSuccess());
var_dump($response->getErrMsg());
```

### JSAPI鉴权，生成前端需要的数据
```php
$client = liupei\dingtalk\Client::newClient();
$config = $client->getSign()->getCorpSign('http://www.baidu.com');
var_dump($config);
```

### 获取自定义空间
```php
$client = liupei\dingtalk\Client::newClient();
$spaceId = $client->getFile()->getCustomSpace('wensi');
var_dump($spaceId);
```

### 授权自定义空间
```php
$client = liupei\dingtalk\Client::newClient();
$success = $client->getFile()->grantCustomSpace('userid','/', 'add', null, 'wensi');
var_dump($success);
```
