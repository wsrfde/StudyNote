### ElementUI中使用el-upload上传图片

> 介绍：这次使用的使用二进制的方法去上传图片



![](.\img\Element\el-upload.png)

在后端写接口的时候，使用img上传格式为binary，百度后才知道是二进制的格式上传

在上传的时候踩了很多坑，但怎么上传都不成功

**踩坑记录：**

坑1：如果使用`http-request`的方法上传，则`action`必须留个空格，不然去掉会报错

坑2：要在`before-upload`中校验格式和大小，`on-change`中校验type会失败

坑3：要么使用form表单的方式提交，要么自己`new FormData`，把文件添加进表单（代码中是后者）

坑4：最后上传怎么也不没有成功，打印添加好文件的formData也是空（实际跟这个没关系，文件已经添加进去），最后发现要在发送请求中添加请求头，上传的`content-type`要为图片中`multipart/form-data`的格式才会成功上传

```html
<el-avatar fit="fit" shape="circle" :size="110" :src="avatar" />
<el-upload
           ref="upload"
           accept="image/jpeg,image/gif,image/png"
           action=""
           :before-upload="beforeUpload"
           class="upload-demo"
           :file-list="fileList"
           :http-request="_updateAvatar"
           :on-change="changeFile"
           :show-file-list="false"
           :with-credentials="true"
           >
    <el-tooltip
                class="item"
                content="只能上传jpg/png/gif文件，且不超过2MB"
                effect="dark"
                placement="right"
                >
        <el-button plain size="mini" type="primary">
            更换头像
        </el-button>
    </el-tooltip>
</el-upload>
```

```js
beforeUpload(file) {
    const isIMAGE = file.type === 'image/jpeg' || 'image/gif' || 'image/png'
    const isLt2M = file.size / 1024 / 1024 < 2

    if (!isIMAGE) {
        this.$message.error('上传头像图片只能是 JPG/PNG/GIF 格式!')
    }
    if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!')
    }
    return isIMAGE && isLt2M
},
changeFile(file) {	//由于只上传一个图片，所以上传一次会替换上次的file
   this.fileList.splice(0, 1, file)
},
_updateAvatar(file) {
    const formData = new FormData()
    formData.append('img', file.file)
    updateAvatar(formData).then((res) => {
        console.log(res)
    })
},
```

```js
// api
import request from '@/utils/request'

export function updateAvatar(data) {
  return request({
    url: '/user/avatar',
    method: 'patch',
    headers: {
      'content-type': 'multipart/form-data;',
    },
    data,	// 我们后端设置字段为img，有的公司则设置file。自己在formData中append进去
  })
}
```

终于出坑🕳了 😭

