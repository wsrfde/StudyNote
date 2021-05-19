## axios网络请求

> 淘汰ajax，jquery ajax，选择axios  

axios名称的由来   个人理解  
axios: ajax i/o system

### *号的意思是重要

### 请求方式

* axios(config)		//默认请求方式
* axios.request(config)
* axios.get(url,{config})
* axios.delete(url,{config})
* axios.head(url,{config})
* axios.post(url,data,{config})
* axios.put(url,data,{config})
* axios.patch(url,data,{config})

### *  axios的使用

1. 安装axios
		`npm install axios --save`
	
2. 这里使用第一种请求方式axios(config)	

    ```js
    axios({
      url:'',
      method:'GET'
    }).then(res =>{
      console.log(res)
    })
    ```

3. config配置属性  

    ```js
      url:'http://被请求的接口',
      method:'get或post',
      params:{适用于GET...的请求的参数拼接},
      data:{针对POST/PUT/PATCH/DELETE的请求时参数获取}
    ```

4. axios支持promise，成功后回调then，失败回调catch



### axios并发请求

* 使用方法和promist.all一样。

* 在axios.all([])放入并发请求的axios，都请求成功后在then中回调

* axios.all([])返回的是一个数组，axios还提供了axios.spread，
  它可以将数组展开使用，res1,res2,,

  ```js
  //这个httpbin.org网站可以做很多网络请求模拟，
  //没有接口可以尝试用这个
  axios.all([
    axios({
      url:'接口1',
    }),
    axios({
      url:'接口2',
      params:{
        type:'pop',
        page:1
      }
    })
  ]).then(axios.spread((res1,res2) => {
    console.log(res1);
    console.log(res2);
  }))
  ```
  
  

如果想两个接口内容相同，我们只需获取一个接口的话，请参考<a href='#a'>axios封装第二步</a>，封装两个接口，
在catch中使用接口2并传入参数即可。


### 全局配置

把一些固定的参数抽取出来进行全局配置

	axios.defaults.timeout = 1000
	axios.defaults.baseURL = '基础接口';
	//后面设置url的时候只需写路径即可，如/home/data

### 常见的配置选项

* 请求地址
	* url: '/user',
* 请求类型
	* method: 'get',
* 请根路径
	* baseURL: 'http://www.mt.com/api',
* 请求前的数据处理
	* transformRequest:[function(data){}],
* 请求后的数据处理
	* transformResponse: [function(data){}],
* 自定义的请求头
	* headers:{'x-Requested-With':'XMLHttpRequest'},
* URL查询对象
	* params:{ id: 12 },
* 查询对象序列化函数
	* paramsSerializer: function(params){ }
* request body
	* data: { key: 'aa'},
* 超时设置
	* timeout: 1000,
* 跨域是否带Token
	* withCredentials: false,
* 自定义请求处理
	* adapter: function(resolve, reject, config){},
* 身份验证信息
	* auth: { uname: '', pwd: '12'},
* 响应的数据格式 json / blob /document /arraybuffer / text / stream
	* responseType: 'json',

### *  axios实例

在开发中有可能我们用的不是一个接口，这个时候使用全局配置就无法获取多个接口了  

我们可以创建一个axios实例，一个实例对应一个接口和相应配置，提高开发效率。

1. 创建实例中基础配置
		
	```js
	const instance1  = axios.create({
	  baseURL:'基础接口',
	  timeout:5000
	})
	```
	
2. 使用实例和配置路径等
		
	```js
	instance1({
	  url:'路径1',
	  params:{}
	}).then(rel =>{
	  console.log(rel)
	})
	```

**当有多个接口时怎么办？**

* 给多个接口配置多个实例即可

* 或者在实例中使用`baseURL`，它会自动覆盖掉创建实例时写的参数

  ```js
  instance1({
    baseURL:'http://api-xxx.com'
    url:'路径2',
    params:{}
  }).then(rel =>{
    console.log(rel)
  })
  ```

  

### *  axios封装

在网络开发中，我们使用第三方插件有可能出现第三方插件重大bug或不再维护的情况  

当出问题时多个页面修改第三方插件是一件很麻烦的事，所以我们可以封装一个文件作为代理  

其他页面从代理中传入config并获取请求，当第三方插件失效只需修改代理文件即可，无需全部修改  

#### 一、 回调式封装

1. 封装代理文件，以函数形式导出。如多个接口可配置多个函数导出

2. 函数内传入三个参数，config，success，failure。这里success和failure是函数。

3. 在函数中配置axios实例，在实例then和catch中分别回调函数success和failure

4. 在需要请求页面导入代理，调用并传入(config,success函数，failure函数)

   ```js
   export function request1(config,success,failure) {
     const instance = axios.create({
       baseURL:'http://123.207.32.32:8000/api/h8',
       timeout:5000
     })
     instance(config).then(res =>{
       success(res)
     }).catch(err =>{
       failure(err);
     })
   }   
   ```

<a name='a'></a>

#### 二、 promise式封装

1. 导出封装函数，函数中传入config参数。如多个接口可配置多个函数导出
2. 函数中返回一个promise，在promise中嵌套axios实例
3. 实例回调result中再使用promise的回调，把回调的结果返回出去
4. 在请求页面导入，因已返回结果，所以直接传入axios配置，然后正常then/catch即可。

```javascript
import axios from 'axios'

export function request(config) {
  return new Promise((resolve, reject) => {
    const instance = axios.create({
      baseURL:'http://123.207.32.32:8000/api/hy',
      timeout:5000
    })

    instance(config).then( res => {
      resolve(res)
    }).catch(err =>{
      reject(err)
    })

  })
}
```

#### 三、 最终封装-由promise式改编（大道至简）

在axios实例中已经内置了promise，所以我们才能回调then/catch，  

为什么我们还要再使用promise再回调一次呢，我们的目的是返回结果， 

直接把axios实例返回出去不就好了嘛  


```javascript
import axios from 'axios'

export function request(config) {
    const instance = axios.create({
      baseURL:'http://123.207.32.32:8000/api/hy',
      timeout:5000
    })
    return instance(config)
}
```

啰嗦一句。这里不是export default导出。导入的话记得加import{} 花括号
<br>

### *  axios拦截器

**全局拦截**：  
拦截器前面使用axios  

**局部拦截**：  
拦截器前面使用的是实例的名称

**拦截四种方式：**  
* request成功拦截  

* request失败拦截  
		
	```js
	instance.interceptors.request.use(config =>{
	  console.log(config);
	  return config
	},error => {
	  console.log(error);
	})
	```
	
* response成功拦截  

* response失败拦截  
		
	```js
	instance.interceptors.response.use(result =>{
	  console.log(result);
	  return result.data
	},error => {
	  console.log(error);
	})
	```

**拦截器需求列举**
1. 拦截一些config中不符合服务器要求的信息
2. 发送请求时，都希望在界面中显示一个请求图标，如图片加载转圈
3. 网络登入请求（token），没有特殊信息不允许登入

**注意事项：**  
1. 拦截器中use传入的是两个函数，result回调和error回调
2. request拦截成功后记得把数据再返回出去
3. response拦截成功后只需把data返回出去即可，其他东西是axios这个框架添加的
4. result拦截主要是对数据进行过滤，error拦截主要是根据错误跳转不同错误提示页面 