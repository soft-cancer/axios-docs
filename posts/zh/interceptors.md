---
title: '拦截器'
prev_title: '默认配置'
prev_link: '/zh/docs/config_defaults'
next_title: '错误处理'
next_link: '/zh/docs/handling_errors'
---

在请求或响应被 then 或 catch 处理前拦截它们。

```js
// 创建一个新的axios实例  request
const request = axios.create({
    //设置请求超时的时间 5000毫秒 
    Timeout: 5000
})
// 添加请求拦截器
request.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    // 在本地存储中取出 token  用于身份验证
    let token = localStorage.getItem('token');
    if (token) {
        // 配置请求头的授权 添加token  一种规范
        // 处在于可以让请求方和服务方都快速而准确地识别Token的传递方式，使得身份验证更加规范化和通用化  前后一致 （）
        config.headers.Authorization = 'Bearer ' + token;
    }
    return config;
}, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
});

// 添加响应拦截器
request.interceptors.response.use(function (response) {
    // 2xx 范围内的状态码都会触发该函数。
    // 对响应数据做点什么
    return response;
}, function (error) {
    // 超出 2xx 范围的状态码都会触发该函数。
    // 对响应错误做点什么 -- 提示 根据错误的不用  401   403  404  500 
    if (error.response.status == '401') {
        alert('请求需要用户验证 检查token');
        location.href = '/public/phone/login.html';
    }
    if (error.response.status == '403') {
        alert('服务器拒绝请求')
        location.href = '/public/phone/login.html';
    }
    if (error.response.status == '404') {
        alert('找不到资源 检查路径')
        location.href = '/public/phone/login.html';
    }
    if (error.response.status == '500') {
        alert('服务器错误')
        location.href = '/public/phone/login.html';
    }
    return Promise.reject(error);
});
```

如果你稍后需要移除拦截器，可以这样：

```js
const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```

可以给自定义的 axios 实例添加拦截器。 

```js
const instance = axios.create();
instance.interceptors.request.use(function () {/*...*/});
```
