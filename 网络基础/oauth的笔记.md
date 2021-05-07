## 完成QQ第三方登入

### 接入流程

![](.\img\oauth\接入流程.png)

### 注册QQAPI

#### 审核资料

[QQ互联网注册成为开发者文档](https://wiki.connect.qq.com/成为开发者)

打开[QQ互联](https://connect.qq.com/)，点击**应用管理**，点击头像进行资料审核，一般会在一周左右审核完毕，然后进行**创建应用**

#### 创建应用

> 一般在一周内审核完成，但有一个好处就是应用创建完成之后可以立马使用

根据自己的应用选择**网页应用**or**移动应用**

然后按照步骤填写即可

**域名：填写时必须是真实的域名，并且经过备案的，互联网可用的**

**回调域：当登入成功后QQ把源码返回给回调域名**

**提供方：写人名或者公司名称**

**备案号：当站备案的号码**



### 新建node项目

```
cnpm i express-generator -g  ## 全局安装express脚手架

express qqoauth  ## 创建qqoauth项目

cd qqoauth	

cnpm i

SET DEBUG=qqoauth:* & npm start	 ## run 项目  ，这里有个坑，要用CMD命令行去运行
```

当提示 `  qqoauth:server Listening on port 3000 +0ms` 时，则运行成功，打开`localhost:3000`即可



### 开发步骤

> 在申请完成qq开发者，并且创建好应用，获取到appid和appkey后，就可以进行正式的开发了

一、放置登入按钮

[QQ图标链接及步骤](https://wiki.connect.qq.com/%e6%94%be%e7%bd%aeqq%e7%99%bb%e5%bd%95%e6%8c%89%e9%92%ae_oauth2-0)

二、编写代码

```
cnpm i ejs -S   # 安装ejs
```

```js
// app.js
// view engine setup  设置视图引擎为html
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'html');
app.engine('html',require('ejs').__express)
```

在重写代码后还要重启，可以使用nodemon

```
nodemon ./bin/www  # 热更新代码，自动重启
```

三、获取授权码，即Authorization Code

> 通过登入获取授权码，再通过授权码获取Token

[步骤详解](https://wiki.connect.qq.com/%e5%bc%80%e5%8f%91%e6%94%bb%e7%95%a5_server-side)

[获取授权码及Token攻略](https://wiki.connect.qq.com/%e4%bd%bf%e7%94%a8authorization_code%e8%8e%b7%e5%8f%96access_token)

```js
// config.js
module.exports = {
  AppId: '101499864',    // 注册的应用ID，即使是应用名字也可以
  appKey: "4129ba38845f4784484eb00eb877068f",  //注册的应用密钥
  redirect_uri: 'http://front.zhufengpeixun.cn/user/callback',  // 把code 传回的域名

  authorizeUrl: 'https://graph.qq.com/oauth2.0/authorize?',	// 获取授权码
  fetchTokenUrl: 'https://graph.qq.com/oauth2.0/token?',	// 获取Token
  fetchOpenIdUrl: 'https://graph.qq.com/oauth2.0/me?',		// 获取OpenID
  fetchUserInfoUrl:'https://graph.qq.com/user/get_user_info?'	// 获取用户信息
}
```



```js
// user.js
const logger = require('debug')('qqoauth:user');
const queryString = require('querystring')

const {AppId, redirect_uri, authorizeUrl} = require('../config')

const state = new Date().getTime()  // 保证回调域名传回的唯一性，方式csrf攻击
router.get('/login', function (req, res, next) {
  
  let options = {
    response_type: 'code', //授权类型
    client_id: AppId,  // 客户端ID，就是appID
    redirect_uri,
    state,
    scope: 'get_user_info,list_album',
  }
  let queryOption = queryString.stringify(options)		// 字符串化url
  let totalAuthorizeUrl = authorizeUrl + queryOption
  logger('totalAuthorizeUrl', totalAuthorizeUrl)
  res.render('login', {title: '登入', totalAuthorizeUrl})
});

// 回调Url
//http://front.zhufengpeixun.cn/user/callback?code=9A5F06AF&state=test
router.get('/callback', function (req, res) {
  let {code, state} = req.query;
  res.send(code);	// 得到授权码
});
```

四、通过授权码=>Token=>OpenID=>userInfo

```js
const request = require('request')
const queryString = require('querystring')
const logger = require('debug')('qqoauth:oauth');

const {AppId, appKey, redirect_uri, fetchTokenUrl, fetchOpenIdUrl, fetchUserInfoUrl} = require('../config')

(async function () {
  let tokenOptions = {
    grant_type: 'authorization_code',
    client_id: AppId,
    client_secret: appKey,
    code: 'D3A4AF6235605C342125E8C18B13C1FB',
    redirect_uri
  }
  let fullFetchTokenUrl = fetchTokenUrl + queryString.stringify(tokenOptions)
  let tokenResponse = await request(fullFetchTokenUrl)
  let {access_token, expires_in, refresh_token} = queryString.parse(tokenResponse)
  // 得到Token后获取OpenID
  let OpenIdOptions = {
    access_token,
    fmt: 'json'
  }
  let totalOpenIdUrl = fetchOpenIdUrl + queryString.stringify(OpenIdOptions)
  let OpenIdResponse = await request(totalOpenIdUrl)
  let {openid} = JSON.parse(OpenIdResponse.slice(OpenIdResponse.indexOf('{'), OpenIdResponse.indexOf('}') + 1))
  // 通过openId获取到用户信息
  let userInfoOptions = {
    access_token,
    oauth_consumer_key: AppId,
    openid
  }
  let totalUserInfoUrl = fetchUserInfoUrl + queryString.stringify(userInfoOptions)
  let userInfoResponse = await request(totalUserInfoUrl)
  return JSON.parse(userInfoResponse)
})().then(res => console.log(res), err => console.log(err))
```



## 建立自己的oauth系统

### 创建客户端和服务端

```
express oauth-client  ## 创建客户端
cd oauth-client
cnpm i
SET PORT=4000  												 ## 注意：需要在CMD命令行中执行
SET DEBUG=oauth-client:* & npm start   ## 注意：需要在CMD命令行中执行

express oauth-server  ## 创建服务端
cd oauth-server
cnpm i
SET PORT=5000
SET DEBUG=oauth-server:* & npm start  
```

并更换所有的视图模板引擎为html （客户端和服务端都更换）

```js
// app.js
// view engine setup  设置视图引擎为html
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'html');
app.engine('html',require('ejs').__express)
```

安装bootstrap3  （客户端）

```
cnpm i bootstrap@3  
# 并把node_modules 中的dist文件复制到public中进行使用
```

