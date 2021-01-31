# vuex相关要点

### 一、作用

相当于大管家，管理各组件的共享数据

### 二、原理

这个状态自管理应用包含以下几个部分：

- **state**，驱动应用的数据源；
- **view**，以声明方式将 **state** 映射到视图；
- **actions**，响应在 **view** 上的用户输入导致的状态变化。

<img src="https://vuex.vuejs.org/flow.png" alt="img" style="zoom: 33%;" />

<img src="https://vuex.vuejs.org/vuex.png" alt="vuex" style="zoom: 80%;" />

### 三、核心概念

- `state`：状态，通常就是数据。存储在 Vuex 中的数据和 Vue 实例中的 `data` 遵循相同的规则

- `mutations`:可以理解为更改数据的操作。更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数。要在组件中调用这个回调函数，需要使用`store.commit('increment')`这种方式。

  - 可以向 `store.commit` 传入额外的参数，即 mutation 的 **载荷（payload）**：

    ```javascript
    // ...
    mutations: {
      increment (state, n) {
        state.count += n
      }
    }
    ```

    ```javascript
    store.commit('increment', 10)
    ```

  - mutation还有一种提交风格，直接使用包含 `type` 属性的对象：

    ```js
    store.commit({
      type: 'increment',
      amount: 10
    })
    ```

    当使用对象风格的提交方式，整个对象都作为载荷传给 mutation 函数，因此 handler 保持不变：

    ```js
    mutations: {
      increment (state, payload) {
        state.count += payload.amount
      }
    }
    ```

    

- `getters`:Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。Getter 接受 state 作为其第一个参数

- `actions`:处理异步操作。参见上图，过程为：vue组件里Dispatch 先Actions里面的操作，Actions里边的操作再发送异步请求，并commit一下mutation里边的操作。mutation里边再更改state里边的数据。这样Devtools才能跟踪数据的变化。

  如果组件想知道操作的反馈，可用两种办法。第一种，payload里边携带一个函数，在actions里边发送commit之后执行这个回调函数。第二种办法，actions里边直接返回一个Promise对象，然后在组件里边then操作。

  例如：

  ```js
  actions: {
      // aChangeStudents(context,payload) {
      //   setTimeout(() => {
      //     context.commit('changeStudents')
      //     console.log(payload.message)
      //     payloud.callbackFunc()
      //   }, 1000);
      // }
      aChangeStudents(context,payload) {
        return new Promise((resolve,reject) =>{
          setTimeout(() => {
            context.commit('changeStudents')
            console.log(payload.message)
            resolve('已经更改完毕')
          }, 1000)
        })
      }
    },
  ```

  ```js
  btnClick(){
          const payload = {
            message:'我是携带的信息',
            callbackFunc:() => console.log('回调函数信息：已经更改成功！')
          }
          // this.$store.dispatch('aChangeStudents',payload)
          this.$store.dispatch('aChangeStudents',payload).then(res => console.log(res))
        }
  ```

  



