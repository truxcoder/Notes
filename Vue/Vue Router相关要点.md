# Vue Router相关要点

### 一、生效步骤

1. 通过Vue.use来安装插件

   `Vue.use(VueRouter)`

2. 创建VueRouter对象

   `const routes = [ ]`

3. 配置路由和组件之间的映射关系

   `const router = new VueRouter({ routes })`

4. 将router对象传入vue实例

   `export default router`

### 二、路由映射和呈现

- router-link
  - to
  - tag
  - replace
  - active-class
- router-view
- 通过代码跳转
  - 主要用到vue自动在组件中注册的$router属性。通过`this.$router.push('/home')`和`this.$router.replace('/home')`方法跳转

### 三、动态路由

- 配置过程

  - 在路由配置页的路径里加上参数变量`path: '/user/:userID'`

  - 此时在组件里就可以用这个变量了。这个变量存在`this.$route.params.userID`，this就是当前组件。$route是指当前活跃的路由。要跟$router区分开，后者是指整个路由对象router（前文的一.3）。可以用计算属性得到这个值。

  - 在router-link里直接在路径后面拼接参数。可以用v-bind动态绑定。

    `<router-link :to="'/user/'+userID">User</router-link>`

### 四、路由懒加载

- 随着项目文件的增大，编译成一个js显的太大了，需要把各个组件分离成单独的js
- 在导入组件时改用：`const User = () => import('../components/User.vue')`

### 五、路由嵌套

- 在相应的父路由里配置children属性。该属性是一个数组，每个元素是一个子路由。
- 在router-link里要写完整路径/home/news。
- route-view要写在相应的组件里

### 六、传递参数

- 第一种方式，参考前文***三、动态路由***的配置
- 第二种方式，在`<router-link>`标签里，`to`属性传递一个对象：`:to="{path:'/about',query:{name:'truxcoder',age:37,job:'police'}}"`，这样，接收时，可用`<h2>{{$route.query}}</h2>`的方式获取`query`对象。

### 七、导航守卫

- 作用：监听路由跳转过程

- 可以使用 `router.beforeEach` 注册一个全局前置守卫

  ```js
  const router = new VueRouter({ ... })
  
  router.beforeEach((to, from, next) => {
    // ...
  })
  ```

  `beforeEach`接收一个函数作为参数。该函数又有三个参数。

  - **`to: Route`**: 即将要进入的目标 。是一个路由对象，包含matched属性，该属性是个包含子路由元素的数组
  - **`from: Route`**: 当前导航正要离开的路由
  - **`next: Function`**: 一定要调用该方法来，否则不进行跳转。内部已经实现，只需要调用一下。

- 案例：每次跳转都更改页面的title。

  - 方法一：在每个组件里都用`created`生命周期函数来更改。
  - 方法二：在定义路由里，每个路由定义一个`meta`属性，是一个对象，里面再定义title。然后再导航守卫里设置`document.title=to.matched[0].meta.title`

### 八、keep-alive

- **Props**：

  - `include` - 字符串或正则表达式。只有名称匹配的组件会被缓存。

    这里的名称是指组件的name属性值。

  - `exclude` - 字符串或正则表达式。任何名称匹配的组件都不会被缓存。

  - `max` - 数字。最多可以缓存多少组件实例。

- **用法**：

  `<keep-alive>` 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和 `<transition>` 相似，`<keep-alive>` 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在组件的父组件链中。

  当组件在 `<keep-alive>` 内被切换，它的 `activated` 和 `deactivated` 这两个生命周期钩子函数将会被对应执行。

  

