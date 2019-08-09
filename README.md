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
## JWT 验证 
> https://199508.com/post/2034
- /lib/user/controller/Api.php
```php

    // 引用 
    $auth = $user->createAccessToken($userinfo);
    $info['user']['user_id'] = $userinfo['user_id'];
    $info['user']['user_name'] = $userinfo['user_name'];
    $info['user']['user_nickname'] = !$userinfo['user_nickname'] ? $userinfo['user_nickname'] : $userinfo['user_name'];
    $info['user']['user_email'] = $userinfo['user_email'];
    $info['user']['user_isemail'] = $userinfo['user_isemail'];
    $info['user']['user_last_login_time'] = $userinfo['user_last_login_time'];
    $info['auth'] = $auth;
    $info['token'] = $this->createJwt($info['user']);


    public function createJwt($user)
    {
        $key = md5('plain'); //jwt的签发密钥，验证token的时候需要用到
        $time = time(); //签发时间
        $expire = $time + 14400; //过期时间
        $token = array(
            "data" => $user,
            "iss" => "https://www.99496.com", //签发组织
            "aud" => "https://www.99496.com", //签发作者
            "iat" => $time,
            "nbf" => $time,
            "exp" => $expire
        );
        $jwt = JWT::encode($token, $key);
        return $jwt;
    }
```
## 验证 /lib/home/controller/User.php
```php
    public function _initialize()
    {
        parent::_initialize();
        $this->checkToken();
    }

    public function checkToken()
    {
        $header = Request::instance()->header();
        if (empty($header['authorization'])){
            echo json_encode([
                'status' => 1002,
                'msg' => 'Token不存在,拒绝访问'
            ]);
            exit;
        }else{
            $checkJwtToken = $this->verifyJwt($header['authorization']);
            if ($checkJwtToken['status'] == 1001) {
                return true;
            }
        }
    }

    //校验jwt权限API
    protected function verifyJwt($jwt)
    {
        $key = md5('plain');
        // JWT::$leeway = 3;
        try {
            $jwtAuth = json_encode(JWT::decode($jwt, $key, array('HS256')));
            $authInfo = json_decode($jwtAuth, true);
            $msg = [];
            if (!empty($authInfo['data']['user_id'])) {
                $msg = [
                    'status' => 1001,
                    'msg' => 'Token验证通过'
                ];
            } else {
                $msg = [
                    'status' => 1002,
                    'msg' => 'Token验证不通过,用户不存在'
                ];
            }
            return $msg;
        } catch (Exception $e) {
            return $e;
        }
    }
```
## 使用
- 接口调用的时候，接口传入 header: { authorization: 'xxxxx' }