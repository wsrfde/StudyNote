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



