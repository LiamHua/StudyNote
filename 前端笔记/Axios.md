## 一、拦截器

```js
import axios from 'axios'
// 导入配置文件
import Config from '@/settings'

const service = axios.create({
  baseURL: process.env.NODE_ENV === 'production' ? process.env.VUE_APP_BASE_API : '/',
  timeout: Config.timeout
})

service.interceptors.request.use(
  config	 => {
    // 这里处理每个请求在发送之前需要做的事情
    // 让每个请求头携带token
    if (window.sessionStorage.token) {
      config.headers.Authorization = window.sessionStorage.token
    }
    config.headers['Content-Type'] = 'application/json'
    return config
  },
  error => {
    // 处理请求失败的情况，暂不清楚具体是什么情况导致请求失败
    return Promise.reject(error)
  }
)

service.interceptors.response.use(
  response => {
    // 这里处理响应成功的情况，即返回状态码是‘2XX’之类的
    // 处理请求成功服务器返回的状态码
    return response
  },
  error => {
    // 这里处理响应失败的情况，即返回状态码不是‘2XX“之类的
    Vue.prototype.$Message.error('网络错误')
    return Promise.reject(error)
  }
)

export default service
```

