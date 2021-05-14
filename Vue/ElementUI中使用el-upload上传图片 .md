[^未正式编写博客]: 



### ElementUI中使用el-upload上传图片 

> 1



![](.\img\Element\el-upload.png)



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
changeFile(file) {
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
    data,
  })
}
```

