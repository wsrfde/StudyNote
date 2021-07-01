---
title: oauth的笔记
date: '2021-05-26'
categories:
 - 网络基础
---

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



## 完成微信第三方登入（前端篇）

> 微信的官方文档中已经写的很清楚，这里注明踩过的几个坑



### 一、申请网站应用

注意，申请网站URL的时候**不要填写协议(http或https)，不要填写协议，不要填写协议（重要的事情说三遍！）**

只填写域名即可（二级或一级域名都行），如（www.aaa.com）或者（second.aaa.com）

### 二、申请code

注册好应用后，按照[官方文档](https://developers.weixin.qq.com/doc/oplatform/Website_App/WeChat_Login/Wechat_Login.html)的方式填写参数

前端添加一个微信图片，然后在图片上添加跳转到新的空白页面。

空白页面中代码如下：showMeCode

```html
<el-tooltip
  content="微信登录"
  effect="light"
  :hide-after="3000"
  placement="right"
>
  <vab-icon
    icon="wechat"
    :is-custom-svg="true"
    @click="openNewPage"
  />
</el-tooltip>

<script>
 methods：{
   openNewPage() {
      this.isClosePage = false		// 初始化开关
      const originHref = window.location.origin
      window.open(originHref + '/wechat/login')	//跳转微信登入页
      window.addEventListener('storage', this.refreshPage)	//使用storage监听事件，可以监听多页面的storage保存状态
      this.$on('hook:destroyed', () => {	// hook方法，组件销毁时移除监听
        window.removeEventListener('storage', this.refreshPage)
      })
    },
    refreshPage() {
      const token = getToken()
      const refreshToken = getRefreshToken()
      if (Boolean(token) && Boolean(refreshToken) && !this.isClosePage) {
        this.isClosePage = true		// 设置开关，因为storage更新时会更新多次
        location.reload()
      }
    }
 }
</script>
```

```html
// WechatLogin.vue

<template><div></div></template>
<script>
  import { getWechatLoginToken } from '@/api/wechat'
  import { setToken, setRefreshToken } from '@/utils/token'
  import qs from 'qs'
  import { getQueryVariable } from '@/utils/getQueryVariable'

  export default {
    name: 'WechatLogin',
    mounted() {
      // 回调页面，获取code和state
      let callbackCode = getQueryVariable('code')
      let callbackState = getQueryVariable('state')
      let wxState = localStorage.getItem('wx_state')
      if (callbackState && wxState === callbackState) {
        this._getWechatLoginToken(callbackCode)
      } else {
        //没有回调则跳转到微信登入
        this.pageJump()
      }
    },
    methods: {
      pageJump() {
        const getCodeUrl = 'https://open.weixin.qq.com/connect/qrconnect?'
        const appid = 'wxaaaaaaaaaaaaaa'	//填入你申请的appid
        //填写你申请好的回调url，注意申请的时候不要带http://或https:// ,上面有写示例
        const redirect_uri = 'https://auth.aaa.com/wechat/login'
        const state = new Date().getTime().toString()	// 防止csrf攻击
        localStorage.setItem('wx_state', state)	// 设置state，再回调的时候来进行对比两次是否一致
        const option = {
          appid,
          redirect_uri,
          response_type: 'code',
          scope: 'snsapi_login',
          state,
        }
        //qs.stringify 将对象序列化成URL的形式，以&进行拼接
        window.location.href =
          getCodeUrl + qs.stringify(option) + '#wechat_redirect'	
      },
      _getWechatLoginToken(code) {
        getWechatLoginToken(code).then((res) => {
          localStorage.setItem('wx_state', '')
          if (res.code === 2000) {
            let data = res.data
            let { token, refresh_token } = data
            setToken(token)	// 里面就是储存方法，setLocalStorage
            setRefreshToken(refresh_token)
            window.location.href = 'about:blank'
            window.close()	// 关闭窗口
          } else {
            this.$baseNotify('登入异常，请尝试重新登入', '提示', 'error')
          }
        })
      },
    },
  }
</script>
```

可以看到，通过回调页面，向微信跳转，跳转验证并登入成功后，微信会返回给我们code。

之后我们发送给后端，后端返回给我们Token之后，通过localStorage保存。

而登入页面监测到localStorage后刷新页面，并关闭回调页面，则登入完成    （前端基本操作的已完成）
