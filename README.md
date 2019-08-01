## 博客

地址：[https://github.com/dxhuii/blog/issues](https://github.com/dxhuii/blog/issues)

## hashids 服务使用

> /lib/extend/hashids/Hashids.php
```php
<?php

namespace app\home\controller;

use app\common\controller\Home;

class Test extends Home
{
    public function index()
    {
        $hashids = new \hashids\Hashids('my keys', 7);
        // 加密  
        $str = $hashids->encode(1);
        // 解密  
        $ids = $hashids->decode($str);

        print_r($str);
        print_r($ids);
    }
}
```
## 客户端

> https://github.com/niieani/hashids.js

## 验证码使用
```php
//生成图形验证码
public function verify()
{
    //随机码
    $key = $this->randstr(32, 'captcha');

    $config = [
        'length' => 4,
        'imageH' => 35,
        'imageW' => 120,
        'fontSize' => 16,
        'reset' => false,
        'expire' => 60,
        'useCurve' => false,
    ];
    $captcha = new \captcha\Captcha($config);

    //构造返回参数
    $data['imgkey'] = $key;
    $data['base64img'] = $captcha->entry($key);
    return json(array('code' => 0, 'msg' => '获取成功', 'data' => $data));
}

/**
* 验证图形验证码
*/
protected function checkcode($code, $id = '')
{
    $captcha = new \captcha\Captcha();
    return $captcha->check($code, $id);
}
```
## 验证验证码
```php
//验证码验证
$result = $this->checkcode($param['validate'], $param['key']);
if (!$result) {
    return json(['msg' => "验证码错误", 'rcode' => -2]);
}
```