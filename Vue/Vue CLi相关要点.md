# Vue Cli相关要点

### 一、安装

- 安装依赖Node和Webpack

- 如果需要Cli2的初始化工作，可按照官方说明：Vue CLI >= 3 和旧版使用了相同的 `vue` 命令，所以 Vue CLI 2 (`vue-cli`) 被覆盖了。如果你仍然需要使用旧版本的 `vue init` 功能，你可以全局安装一个桥接工具：

  ```sh
  npm install -g @vue/cli-init
  # `vue init` 的运行效果将会跟 `vue-cli@2.x` 相同
  vue init webpack my-project
  ```

### 二、使用

#### 2.1 runtime-compiler和runtime-only的区别

- runtime-compiler渲染步骤

  - template -> ast -> render -> vdom -> UI

- runtime-only渲染步骤

  - render - > vdom -> UI

- 相当于省去了把模板文件解析成ast文件的过程，这个过程实际上是由vue-template-compiler完成的

- 在runtime-only模式中，是调用render函数替代template加上components的过程。render函数实际上在创造Dom元素。

  ```js
  render:function(createElement){
      //1,普通用法：createElement("标签",{标签的属性},['标签的内容'])
      //return createElement('h2',{class:'box'},['Hello world'])
      //2,传入组件对象
      return createElement(App)
  }
  ```

- 所以runtime-only的性能更高，需要的代码更少

