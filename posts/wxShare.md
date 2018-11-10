+++
title = "微信分享"
date = "2018年07月14日 09:53:22"
tags = ["微信开发"]
categories = ["微信开发"]
+++
### 微信分享步骤
- 引入jｓ
- 设置白名单
- 引入sdk
- 实现分享功能
<!--more-->
### 绑定域名＆　引入js
https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115　参考文档步骤１，步骤２

### 设置ip白名单
在 `基本配置`->`ip白名单`将服务器Ip配置好即可
###  生成分享配置
将下面代码加入项目

``` php
<?php
class WxShare
{
    private $appId;
    private $appSecret;


    public function __construct($appId = '自己的appid', $appSecret = '自己的appSecret')
    {
        $this->appId = $appId;
        $this->appSecret = $appSecret;
    }


    public function getSignPackage()
    {
//接收到前端的转义url转义回来
        $url = $_POST;
        $durl = $url['url'];
        $durl = urldecode($durl);

        $jsapiTicket = $this->getJsApiTicket();
        var_dump($jsapiTicket);
        $timestamp = time();
        $nonceStr = $this->createNonceStr();
        // 这里参数的顺序要按照 key 值 ASCII 码升序排序
        $string = "jsapi_ticket=$jsapiTicket&noncestr=$nonceStr&timestamp=$timestamp&url=$durl";


        $signature = sha1($string);


        $signPackage = [
            "appId" => $this->appId,
            "nonceStr" => $nonceStr,
            "timestamp" => $timestamp,
            "url" => $url,
            "signature" => $signature,
            "rawString" => $string
        ];
//        var_dump($signPackage);die;
        return $signPackage;
    }




    public function curlGet($url) {
        $curl = curl_init(); // 启动一个CURL会话
        curl_setopt($curl, CURLOPT_URL, $url);
        curl_setopt($curl, CURLOPT_HEADER, 0);
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false); // 跳过证书检查
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, true);  // 从证书中检查SSL加密算法是否存在
        $tmpInfo = curl_exec($curl);     //返回api的json对象
        //关闭URL请求
        curl_close($curl);
        return $tmpInfo;    //返回json对象
    }

    public function curlPost($url, $data) {
        $curl = curl_init(); // 启动一个CURL会话
        curl_setopt($curl, CURLOPT_URL, $url); // 要访问的地址
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, 0); // 对认证证书来源的检查
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, 1); // 从证书中检查SSL加密算法是否存在
        curl_setopt($curl, CURLOPT_USERAGENT, $_SERVER['HTTP_USER_AGENT']); // 模拟用户使用的浏览器
        curl_setopt($curl, CURLOPT_FOLLOWLOCATION, 1); // 使用自动跳转
        curl_setopt($curl, CURLOPT_AUTOREFERER, 1); // 自动设置Referer
        curl_setopt($curl, CURLOPT_POST, 1); // 发送一个常规的Post请求
        curl_setopt($curl, CURLOPT_POSTFIELDS, $data); // Post提交的数据包
        curl_setopt($curl, CURLOPT_TIMEOUT, 30); // 设置超时限制防止死循环
        curl_setopt($curl, CURLOPT_HEADER, 0); // 显示返回的Header区域内容
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1); // 获取的信息以文件流的形式返回
        $tmpInfo = curl_exec($curl); // 执行操作
        if (curl_errno($curl)) {
            echo 'Errno'.curl_error($curl);//捕抓异常
        }
        curl_close($curl); // 关闭CURL会话
        return $tmpInfo; // 返回数据，json格式
    }

    private function createNonceStr($length = 16)
    {
        $chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
        $str = "";
        for ($i = 0; $i < $length; $i++) {
            $str .= substr($chars, mt_rand(0, strlen($chars) - 1), 1);
        }
        return $str;
    }


    private function getJsApiTicket()
    {
        // jsapi_ticket 应该全局存储与更新，以下代码以写入到文件中做示例
        $data = json_decode(file_get_contents("jssdk/jsapi_ticket.json"));
        if ( empty($data) ||  $data->expire_time < time()) {
            $accessToken = $this->getAccessToken();
            //定义传递的参数数组
            $params['type'] = 'jsapi';
            $params['access_token'] = $accessToken;
            $url = "https://api.weixin.qq.com/cgi-bin/ticket/getticket?access_token=" . $params['access_token'] . "&type=" . $params['type'] . "";
            $res = json_decode($this->curlGet($url));
            $ticket = isset($res->ticket) ? $res->ticket : NULL;
            if ($ticket) {
                $res->expire_time = time() + 7000;
                $res->jsapi_ticket = $ticket;
                $fp = fopen("jssdk/jsapi_ticket.json", "w");
                fwrite($fp, json_encode($res));
                fclose($fp);
            }
        } else {
            $ticket = $data->jsapi_ticket;
        }
        return $ticket;
    }


    private function getAccessToken()
    {
        // access_token 应该全局存储与更新，以下代码以写入到文件中做示例
        $data = json_decode(file_get_contents("jssdk/access_token.json"));
        if ( empty($data) || $data->expire_time < time()) {
            //定义传递的参数数组
            $params['grant_type'] = 'client_credential';
            $params['appid'] = $this->appId;
            $params['secret'] = $this->appSecret;
            $url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=" . $params['grant_type'] . "&appid=" . $params['appid'] . "&secret=" . $params['secret'] . "";
            $res = json_decode($this->curlPost($url, $params));
            $access_token = isset($res->access_token) ? $res->access_token : NULL;
            if ($access_token) {
                $res->expire_time = time() + 7000;
                $res->access_token = $access_token;
                $fp = fopen("jssdk/access_token.json", "w");
                fwrite($fp, json_encode($res));
                fclose($fp);
            }
        } else {
            $access_token = $data->access_token;
        }
        return $access_token;
    }
}
```

调用上述代码中的`getSignPackage`方法生成配置信息

### 前台获取配置信息
将下列代码加入需要分享页面的js中
```javascript
$(function(){
        jQuery.post("获取配置的url地址", {"url": encodeURIComponent(window.location.href.split('#')[0]),"t": new Date().getTime()}, function (result) {
            var result = JSON.parse(result)
            var shareUrl = result.url;
            wx.config({
                debug: false, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
                appId: result.appId, // 必填，公众号的唯一标识
                timestamp: result.timestamp, // 必填，生成签名的时间戳
                nonceStr: result.nonceStr, // 必填，生成签名的随机串
                signature: result.signature,// 必填，签名，见附录1
                jsApiList: ['onMenuShareAppMessage','onMenuShareTimeline','onMenuShareQQ','onMenuShareWeibo','onMenuShareQZone'], // 必填，需要使用的JS接口列表，所有JS接口列表见附录2
                success:function(){
                    console.log("success");

                }
            })
        });

        wx.ready(function(){
            wx.error(function(res){
                // config信息验证失败会执行error函数，如签名过期导致验证失败，具体错误信息可以打开config的debug模式查看，也可以在返回的res参数中查看，对于SPA可以在这里更新签名。
                console.log(res);
            });


            var s_title = '分享标题';
            var s_desc = '分享描述';
            var img_url = '分享图片地址';

            wx.onMenuShareAppMessage({
                title: s_title, // 分享标题
                desc: s_desc, // 分享描述
                link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
                imgUrl: img_url, // 分享图标
                type: 'link', // 分享类型,music、video或link，不填默认为link
                dataUrl: '', // 如果type是music或video，则要提供数据链接，默认为空
                success: function () {
                    // 用户点击了分享后执行的回调函数
                }
            });

            wx.onMenuShareTimeline({
                title: s_title, // 分享标题
                link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
                imgUrl: img_url, // 分享图标
                success: function () {
                    // 用户点击了分享后执行的回调函数
                }
            });



        });



    })
```

### 结束
代码已经放上只需要将自己的配置信息一一对应，即可实现微信分享功能