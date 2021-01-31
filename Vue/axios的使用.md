# axios的使用

### 一、安装

npm install axios

### 二、基本使用

- 全局配置

  ```js
  axios.defaults.baseURL = 'http://www.httpbin.org'
  
  axios({
    url:'/get'
  }).then(res =>console.log(res))
  ```

- 并发

  ```js
  axios.all([
    axios(req1),axios(req2)
  ]).then(results =>console.log(results))
  ```

- 创建实例

  ```js
  var instance = axios.create()
  instance.defaults.baseURL = 'http://www.httpbin.org'
  instance({
    url:'/ip'
  }).then(res =>console.log(res))
  ```

### 三、请求方法封装

- 封装的目的

  目的是为了使项目对第三方库（axios）的依赖减少。随时可更换其他库。

- 回调请求结果的几种方法

  ```js
  //想回调结果，有几种办法：
  //1.直接让调用者传入三个参数，配置、成功回调函数、失败回调函数
  export function request(config,success,failure) {
      const instance =axios.create({
          baseURL:'http://www.httpbin.org',
          timeout:5000
      })
      instance(config).then(res => success(res)).catch(err => failure(err))
  }
  //2.让调用者传一个参数，为对象，包括基本配置和成功失败回调函数
  export function request(config) {
      const instance =axios.create({
          baseURL:'http://www.httpbin.org',
          timeout:5000
      })
      instance(config.baseConfig).then(res => config.success(res)).catch(err => config.failure(err))
  }
  //3.直接返回一个Promise对象。
  export function request(config) {
      return new Promise((resolve,reject)=>{
          const instance =axios.create({
              baseURL:'http://www.httpbin.org',
              timeout:5000
          })
          instance(config).then(res => resolve(res)).catch(err => reject(err))
      })
  }
  
  //4.事实上，instance(config)本来就返回一个Promise实例。可以直接return。上述代码可简化为
  export function request(config) {
      const instance =axios.create({
          baseURL:'http://www.httpbin.org',
          timeout:5000
      })
      return instance(config)
  }
  ```

  ```js
  //使用封装的请求方法
  function success(res){
    console.log('成功------',res)
  }
  function failure(err){
    console.log('失败------',err)
  }
  //方法一：
  request(req1,success,failure)
  //方法二：
  const config = {
    baseConfig:req1,
    success,failure
  }
  request(config)
  //方法三
  request(req1).then(res =>console.log(res)).catch(err =>console.log(err))
  ```

  

