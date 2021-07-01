---
title: Element-beautify
date: '2021-04-24'
categories:
 - 后台模板
---

### package.json的配置文件说明

* `build:report`    //打包并预览包大小
* `lint`    //自动修复
* `inspect`    //检查webpack配置
* `templateb`    //生成模板
* `clear`    //清空node_modules 并使用淘宝镜像重新安装
* `update`    //使用淘宝镜像，更新项目的所有依赖



`gitHooks`和`lint-staged`：提交代码的时候进行eslint检查，如不符合规范则不允许提交

注意：运行`clear`命令需要先装`rimraf`包，是快速删除node_modules包的工具

### 项目打包

运行 `npm run deploy`

在命令中可以看到运行的是`deploy.sh`的文件

```sh
#!/usr/bin/env bash
set -e
npm run build		# 打包不带mock的命令
# npm run build:test # 打包带mock的命令
cd dist
touch .nojekyll  # 推送空的.nojekyll文件
git init
git add -A
git commit -m 'deploy'
git push -f "https://${access_token}@gitee.com/chu1204505056/admin-pro.git" master:gh-pages  # 推送到码云的仓库下的gh-pages分支。gh-pages是主页的意思
start "https://gitee.com/chu1204505056/admin-pro/pages"  # 码云需要打开网页，手动更新一次部署
cd -
exec /bin/bash
```



### vue.config.js说明

`productionGzipExtensions`中配置的是开其Gzip压缩的格式，生产模式下只压缩`'html', 'js', 'css', 'svg'`即可

普及，**什么是Gzip压缩：**通过把文件压缩后传递给浏览器，浏览器进行解压缩，把服务端的压力传递给每个客户端

一直以为只有服务器能开启Gizp，原来前端也能做到



### mock

mock分为`js版mock`和`nodejs版mock`

区别：

* `mock-js`是静态版的mock，不会在network中查看到请求
* `mock-nodejs`是本地服务器版的mock，可以在network中查看到请求链接



### 各个版本区别

| 分支名                                                       | 是否精简 commit | 是否精简功能 | 是否支持 i18n | 更新时间 |                            维护人                            |
| ------------------------------------------------------------ | :-------------: | :----------: | :-----------: | :------: | :----------------------------------------------------------: |
| [master](https://github.com/vue-admin-beautiful/admin-pro/)  |        ❌        |      ❌       |       ✔       |   即时   | <a href="https://github.com/chuzhixin" target="_blank"><img style="border-radius:999px" src="https://avatars3.githubusercontent.com/u/26647258?s=50&u=753921fb23f418996dffd6196e89729fcb2329ed&v=4"/></a> |
| [release/main](https://github.com/vue-admin-beautiful/admin-pro/tree/release/main) |        ✔        |      ❌       |       ✔       | 10-30 天 | <a href="https://github.com/fwfmiao" target="_blank"><img style="border-radius:999px" src="https://avatars3.githubusercontent.com/u/29328241?s=50&u=bb0977b405ccf1a101ce4e18e4fb8d958854ca60&v=4"/></a> |
| [release/template](https://github.com/vue-admin-beautiful/admin-pro/tree/release/template) |        ✔        |      ✔       |       ✔       | 10-30 天 | <a href="https://github.com/fwfmiao" target="_blank"><img style="border-radius:999px" src="https://avatars3.githubusercontent.com/u/29328241?s=50&u=bb0977b405ccf1a101ce4e18e4fb8d958854ca60&v=4"/></a> |
| [release/seed](https://github.com/vue-admin-beautiful/admin-pro/tree/release/seed) |        ✔        |      ✔       |       ❌       | 10-30 天 | <a href="https://github.com/fwfmiao" target="_blank"><img style="border-radius:999px" src="https://avatars3.githubusercontent.com/u/29328241?s=50&u=bb0977b405ccf1a101ce4e18e4fb8d958854ca60&v=4"/></a> |



### 背景虚化毛玻璃特效取消

全局搜索`vab.scss`文件，注释掉 **遮罩** 效果中的`backdrop-filter`属性



### 使用Vab-icon设置本地svg大小

要把`width`和`height`大小与`font-size`设置一致，才能调整大小
