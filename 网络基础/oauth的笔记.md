## 完成QQ第三方登入

### 接入流程

![](.\img\oauth\接入流程.png)

### 注册QQAPI

#### 审核资料

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

当提示 `  qqoauth:server Listening on port 3000 +0ms` 时，则运行成功，打开`localhost:3000`