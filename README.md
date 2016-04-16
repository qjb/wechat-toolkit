更改说明
========
wechat-toolkit是一个挺不错的nodejs的接口库，只是我们在使用的时候发现有一些功能不太方便，于是就fork了一下，主要更改的功能包括：

* 添加jsapi的signature功能
* 添加了对scancode_push和scancode_waitmsg的支持


谢谢原作者提供的这么好的模块！
https://github.com/kyfxbl/wechat-toolkit



下面内容来自原来的项目：

wechat-toolkit
==============
微信公众平台开发SDK，用于node平台，需要配合express使用

# Features

1、微信服务器消息解析中间件。解析微信服务器发来的请求。解析后，会在req上增加weixin对象，从中可以直接取到微信服务器发来的参数，比如req.weixin.fan_open_id

2、打开开发者模式。公众号启用开发者模式，需要和微信服务器做一次验证

3、验证消息来源，判断消息是否来自微信服务器

4、被动回复粉丝消息

5、获取access_token

6、创建、查询、删除自定义菜单

7、发送客服消息（粉丝主动给公众号发起互动之后，公众号可以在48小时内回复无限次消息）

8、上传、下载临时多媒体素材（在微信服务器保留3天）

9、粉丝群组操作。包括创建群组、查询群组、修改群组、查询粉丝所属群组、移动粉丝到群组

10、获取粉丝信息。包括获取粉丝列表，以及获取粉丝详细信息

11、群发消息。包括上传图文消息素材，根据群组群发，根据open_id群发，撤销消息

12、修改粉丝备注名

13、网页Oauth2。用户授权后，跳转到开发者页面后，URL中会携带code字段，根据此code字段，可以获取到用户的open_id，以及用户详细信息

14、生成带场景值二维码。包括生成永久二维码，生成临时二维码，获取二维码URL，下载二维码图片

15、长链接转短链接服务

16、模拟微信向开发者推送消息

17、发送模板消息

18、获取jsapi_ticket

19、根据订单ID获取订单详情

20、发送现金红包

21、上传、下载永久多媒体素材（在微信服务器长期保存）

# Install
npm install wechat-toolkit --save

# API Example

## 解析微信消息

```
var wx = require("wechat-toolkit");

var app = express();
app.use(wx.xml_parser());
```

```
req.weixin.my_origin_id
req.weixin.fan_open_id
req.weixin.message_time
req.weixin.message_type
req.weixin.message_id
req.weixin.content
req.weixin.event
req.weixin.event_key
```

## 开启开发者模式

```
var app = express();
app.get("/weixin/weixinInterface", wx.enable_dev_mode(token));// token在公众平台管理后台设置
```

## 验证消息来源
```
var flag = wx.validate(req, token);// true or false
```

## 被动回复粉丝消息
```
wx.replyTextMessage(req, res, "感谢您的关注");
```

```
var item1 = {
            title: "标题1",
            desc: "描述1",
            picUrl: "http://xxx/1.png",
            url: "http://www.baidu.com"
        };

        var item2 = {
            title: "标题2",
            desc: "描述2",
            picUrl: "http://xxx/2.png",
            url: "http://www.baidu.com"
        };

        var contents = [item1, item2];

        wx.replyNewsMessage(req, res, contents);
```

## 获取access_token
```
wx.getAccessToken("app_id", "app_secret", function(err, access_token){

    if(err){
        console.log(err);
        return;
    }

    console.log(access_token);
});
```

## 创建自定义菜单
```
var obj = {
    "button" : [
        {
            "name" : "我要购买",
            "type" : "click",
            "key" : "BUY"
        },
        {
            "name" : "商务",
            "type" : "click",
            "key" : "BUSINESS"
        },
        {
            "name" : "关于",
            "sub_button" : [
                {
                    "name" : "官网",
                    "type" : "view",
                    "url" : "http://www.baidu.com/"
                },
                {
                    "name" : "团队介绍",
                    "type" : "click",
                    "key" : "ABOUT"
                }
            ]
        }
    ]
};

menus.createMenu(access_token, obj, function(err, error_code, error_message){

    if(err){
        console.log(err);
        return;
    }

    console.log(error_code);
    console.log(error_message)
});
```

## 发送客服消息
```
cs.replyTextMessage(access_token, fan_open_id, "刚才收到您的回复", function(err, code, msg){

    console.log(code);
    console.log(msg);
});
```

```
var articles = [
    {
        title: "Happy Day",
        description: "Is Really A Happy Day",
        url: "http://www.yilos.com",
        picurl: "http://121.40.75.73/resource/right_more.png"
    },
    {
        title: "test2",
        description: "test2",
        url: "http://www.yilos.com",
        picurl: "http://121.40.75.73/resource/right_more.png"
    }
];

cs.replyNewsMessge(access_token,fan_open_id, articles, function(err, code, msg){

    console.log(code);
    console.log(msg);
});
```

## 上传、下载临时多媒体素材
```
media.uploadMedia(access_token, "image", "/users/apple/test/8.jpg", function(err, type, media_id, created_at){

    if(err){
        console.log(err);
        return;
    }

    console.log(type);
    console.log(media_id);
    console.log(created_at);
});

var media_id = "NAZPpT5BpHwnHoz5BYWJoOiC2nWtbXt7Inu6FUGtmTVCuYWKQJzVU0P38tMmQZ70";

media.downloadMedia(access_token, media_id, function(err, result){

    if(err){
        console.log(err);
    }else{
        console.log(result);
    }

});
```

## 粉丝群组操作
```
group.addGroup(access_token, "kyfxbl", function(err, group_id){

    if(err){
        console.log(err);
        return;
    }

    console.log(group_id);
});

group.queryGroup(access_token, function(err, groups){

    if(err){
        console.log(err);
        return;
    }

    console.log(groups);
});

group.fanInGroup(access_token, open_id, function(err, group_id){

    if(err){
        console.log(err);
        return;
    }

    console.log(group_id);
});

group.modifyGroup(access_token, "113", "hehe", function(err){

    if(err){
        console.log(err);
        return;
    }

    console.log("modify group name success");
});

group.moveFan(access_token, open_id, "115", function(err){

    if(err){
        console.log(err);
        return;
    }

    console.log("move user success");
});
```

## 获取粉丝信息
```
fan.getFanInfo(access_token, open_id, function(err, info){

    if(err){
        console.log(err);
        return;
    }

    console.log(info);
});

fan.getFans(access_token, null, function(err, result){

    if(err){
        console.log(err);
        return;
    }

    console.log(result);
});
```

## 群发消息

如果是群发图文消息，需要先调用uploadNews方法，上传图文消息，再根据返回结果中的media_id来群发
```
var contents = [];
contents.push(
    {
        "thumb_media_id":"ztVzolmokH_20L0d0cLE36HW03mqBUa2FR38xPkCahqhmkPKfpAHmrqtdpr-wKHq",
        "author":"kyfxbl",
        "title":"Happy Day",
        "content_source_url":"www.yilos.com",
        "content":"here is content",
        "digest":"digest",
        "show_cover_pic":"1"
    },
    {
        "thumb_media_id":"ztVzolmokH_20L0d0cLE36HW03mqBUa2FR38xPkCahqhmkPKfpAHmrqtdpr-wKHq",
        "author":"kyfxbl",
        "title":"Happy Day 3",
        "content_source_url":"www.yilos.com",
        "content":"here is content 3",
        "digest":"digest 3",
        "show_cover_pic":"0"
    }
);

api.uploadNews(access_token, contents, function(err, media_id, created_at){

    if(err){
        console.log(err);
        return;
    }

    console.log(media_id);
    console.log(created_at);
});

api.broadcastNewsByGroup(access_token, group_id, news_id, function(err, result){

    if(err){
        console.log(err);
        return;
    }

    console.log(result);
});

api.broadcastNewsByOpenId(access_token, open_id, news_id, function(err, result){

    if(err){
        console.log(err);
        return;
    }

    console.log(result);
});

api.withdrawBroadcast(access_token, "2349457266", function(err, result){

    if(err){
        console.log(err);
        return;
    }

    console.log(result);
});
```

## 修改粉丝备注名
```
api.modifyNickname(access_token, open_id, "dongge", function(err, response){

    if(err){
        console.log(err);
        return;
    }

    console.log(response);
});
```

## 网页Oauth2

整体的流程：

1, 当用户授权之后，跳转到开发者自己的页面，URL中会带有code字段，根据这个code，调用exchangeAccessToken函数，将会得到open_id和access_token

2, 如果此前选择的scope是snsapi_userinfo，那么可以继续调用getUserInfo，得到用户的详细信息

```
api.exchangeAccessToken(app_id, app_secret, code, function(err, result){

    if(err){
        console.log(err);
        return;
    }

    console.log(result);
});

api.refreshAccessToken(app_id, refresh_token, function(err, result){

    if(err){
        console.log(err);
        return;
    }

    console.log(result);
});

api.getUserInfo(token, fan_open_id, function(err, result){

    if(err){
        console.log(err);
        return;
    }

    console.log(result);
});

api.validateAccessToken(refresh_token, fan_open_id, function(err, flag){

    console.log(flag);
});
```

## 生成带场景值二维码
```
api.generateTempQR(access_token, 600, 123456, function(err, result){

    if(err){
        console.log(err);
        return;
    }

    console.log(result);
});

api.generateEternalQR(access_token, 23, function(err, result){

    if(err){
        console.log(err);
        return;
    }

    console.log(result);
});
```

## 长链接转短链接
```
api.shortenURL(access_token, "http://www.yilos.com", function(err, url){

    if(err){
        console.log(err);
        return;
    }

    console.log(url);
});
```

## 模拟微信推送消息
```
api.simulateEvent(url, token, fan_open_id, event_key);
```

## 发送模板消息
```
var data = {

    first: {
        value: "您好，您已成功消费。",
        color: "#0A0A0A"
    },
"keyword1":{
        value: "守护色",
        color: "#CCCCCC"
    },
"keyword2":{
        value: "1次",
        color: "#CCCCCC"
    },
"keyword3":{
        value: "福田店",
        color: "#CCCCCC"
    },
"keyword4":{
        value: "3次",
        color: "#CCCCCC"
    },
"remark":{
        value: "欢迎下次光顾",
        color: "#173177"
    }
};

var obj = {
    access_token: "access_token",
    fan_open_id: "fan_open_id",
    template_id: "template_id",
    url: "http://www.yilos.com",
    top_color: "#000000",
    data: data
};

api.sendTemplateMessage(obj, function(err, code, message){

    if(err){
        console.log(err);
        return;
    }

console.log(code);
console.log(message);
});
```

## 获取jsapi_ticket
```
wx.getJsApiTicket("access_token", function(err, jsapi_ticket){

    if(err){
        console.log(err);
        return;
    }

    console.log(jsapi_ticket);
});
```

## 根据订单ID获取订单详情
```
wx.getOrderDetail("access_token", "order_id", function(err, order_detail){

    if(err){
        console.log(err);
        return;
    }

    console.log(order_detail);
});
```

## 发送现金红包
```
var params = {
    mch_id: "xxxx",// 商户账号
    wxappid: "xxxx",// 公众号app_id
    nick_name: "发红包的活动主办者名称",
    send_name: "发红包的商户名称",
    re_openid: "xxxx",// 目标用户的open_id
    total_amount: 100,// 单位分，最小是100
    wishing: "测试发送红包",// 祝福语
    act_name: "送红包活动",// 活动名称
    remark: "快来领啊",// 备注
    logo_imgurl: "http://xxx.com/abc.png"// logo url
};

// 第一个参数是params object
// 第二个参数是商户API密钥
// 第三个参数是p12证书的路径
// 回调函数code，0表示发送红包成功，1表示发送红包失败
// 回调函数result，已经解析成json对象
payment.cash_redpack(params, "abc", "apiclient_cert.p12", function(err, code, result){

    if(err){
        console.log(err);
        return;
    }

    if(code === 0){
        console.log("调用成功");
    }else{
        console.log("调用失败");
    }

    console.log(result);
});
```

# Roadmap
目前还有以下特性未实现，会逐步加入，也欢迎PR

1. 其它消息类型发送，如video，voice等
2. 2014年9月19日推出的新的自定义菜单，及相对应的推送事件，如弹出系统拍照发图等
3. 设备接口

# FAQ
Q：使用的限制？

A：本SDK基于express，为了方便依赖了express的中间件机制，以及req, res对象，所以需要配合express使用。如果你的web框架不是选型express，那么只能看一下源码，了解一下微信原生接口的参数格式了

Q：有进行过测试吗？

A：所有的接口都经过单元测试。但是由于单元测试代码要跑通，必然包含公众号的access_token等敏感信息，所以单元测试的代码不能上传
