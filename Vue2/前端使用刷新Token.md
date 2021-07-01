---
title: 前端使用刷新Token
date: '2021-05-26'
categories:
 - Vue
tags:
 - Vue2
---

> 在做后台系统登入的时候，保存Token进行登入是很正常的操作，但如果要加上刷新Token，那我们前端改怎么实现这个逻辑呢

如果你还不知道什么是Token和刷新Token，请参考此文章，[还分不清 Cookie、Session、Token、JWT？](https://zhuanlan.zhihu.com/p/164696755)

首先我们先用一张图片看下项目的业务逻辑，并用一张图片具体演示

1. 调用login接口，向后端传递账户密码，获得Tokne和refreshToken
2. 保存Token和refreshToken到cookie中（也有保存到 local storage中，我这里保存到cookie是因为需要在相同主域名中要传递属性，使用cookie的domin属性）
3. 通过请求头传递Token，来请求userInfo来获取用户信息及权限
4. 当token过期返回4002，并向后端传递refreshToken
5. 当传递refreshToken时请求任意接口都可获得token和refreshToken（或者让后端单独写获取接口）
6. 保存token和refreshToken，然后响应拦截器中重新请求上次未完成的请求，并返回数据

![](.\img\refreshToken\思维脑图.png)

Talk is cheap , show me code

```js
// request.js

const handleData = async ({ config, data, status, statusText }) => {

  switch (code) {
    case 2000:
      return data
    case 4002:
      //--------------------------更新前--------------------------
      // 根据网上教程，当402时获取Token，然后根据Token再次请求上次未请求成功的数据，并返回
      //let newToken = await store.dispatch('user/needRefreshToken', true)
      //config.headers['Authorization'] = `Bearer ${newToken}`
      //return await axios.request(config)
      //--------------------------更新后--------------------------
      // 因为项目中设置了拦截器，获取刷新token后我们还想继续使用拦截器，然后直接通过实例并传入config返回即可
      // （这里config是一个对象，包含请求信息，baseUrl等，通过config的信息axios会进行解析）
      await store.dispatch('user/needRefreshToken', true)
      return instance(config)
    default:
      break
  }
   return Promise.reject(err)
}

const instance = axios.create({
  baseURL: process.env.NODE_ENV === 'development' ? devAPI : proAPI,
  timeout: requestTimeout,
  headers: {
    'Content-Type': contentType,
  },
})

/**
 * @description axios请求拦截器
 */
instance.interceptors.request.use(
  (config) => {
    const token = store.getters['user/token']
    const refreshToken = store.getters['user/refreshToken']
    const isNeedRefreshToken = store.getters['user/isNeedRefreshToken']

    if (token && !isNeedRefreshToken) {
      config.headers['Authorization'] = `Bearer ${token}`
    } else {
      config.headers['Authorization'] = `Refresh ${refreshToken}`
    }
	// ...其他业务逻辑代码
    return config
  },
  (error) => {
    return Promise.reject(error)
  }
)

/**
 * @description axios响应拦截器
 */
instance.interceptors.response.use(
  (response) => handleData(response),
  (error) => {}
)
```

```js
// user.js
const actions = {
  async needRefreshToken({ commit }, isNeed) {
    commit('needRefreshToken', isNeed)
    // 重新获得刷新token和token
    const { data } = await getUserInfo()
    const token = data[tokenName]
    const refreshToken = data[refreshTokenName]

    if (token && refreshToken) {
      commit('setToken', token)	//保存token
      commit('setRefreshToken', refreshToken)	//保存refreshToken
      commit('needRefreshToken', false)	//关闭开关，使请求头refreshToken更改为正常token
    }
    return token
  },
}
const mutations = {
  needRefreshToken(state, isNeed) {
    state.isNeedRefreshToken = isNeed
  },
}
```

所有的逻辑代码都已经列出（意思就是这样你还不会我也带不动）

事实证明画思维脑图真的帮助很大，凌乱的思维一下理清了，完结撒花🎉

