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