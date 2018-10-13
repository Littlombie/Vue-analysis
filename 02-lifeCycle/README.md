# Vue的生命周期方法
* 研究方向
	- hook机制

* 研究方法
	- 各个hook在哪个阶段触发，为啥会在这个阶段触发？触发这个钩子可以改变什么或者得到什么？

******
### 钩子函数（Hook）

钩子是Windows消息处理机制的一个平台,应用程序可以在上面设置子程以监视指定窗口的某种消息；
钩子机制允许应用程序截获处理window消息或特定事件，
钩子函数可以理解为js运行进程中在其特定段时的回调函数

## 什么是生命周期？

生命周期：Vue 实例从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、卸载等一系列过程，我们称这是 Vue 的生命周期，各个阶段有相对应的事件钩子
![vue 的生命周期](https://github.com/Littlombie/Vue-analysis/blob/master/02-lifeCycle/LifeCycle1.png?raw=true)

* 图中 数据观测：
    
在new Vue() 时 开始观测数据

- `init event & lifeCycle`:  表示`new Vue` 后事件和生命周期的`init`
- 当`created`完成之后，它会去判断`instance`（实例）里面是否含有`“el”option`（选项），如果没有的话，它会调用`vm.$mount(el)`这个方法，然后执行下一步；如果有的话，直接执行下一步。紧接着会判断是否含有`“template”`这个选项，如果有的话，它会把`template`解析成一个`render function` ，这是一个`template`编译的过程，结果是解析成了`render`函数：
```
render (h) {
  return h('div', {}, this.text)
}
```
`render`函数里面的传参h就是Vue里面的`createElement`方法，`return`返回一个`createElement`方法，其中要传3个参数，第一个参数就是创建的div标签；第二个参数传了一个对象，对象里面可以是我们组件上面的props，或者是事件之类的东西；第三个参数就是div标签里面的内容，这里我们指向了data里面的text。  

使用render函数的结果和我们之前使用`template`解析出来的结果是一样的。`render`函数是发生在`beforeMount`和`mounted`之间的，这也从侧面说明了，在`beforeMount`的时候，`$el`还只是我们在HTML里面写的节点，然后到`mounted`的时候，它就把渲染出来的内容挂载到了`DOM`节点上。这中间的过程其实是执行了`render function`的内容。    

在使用`.vue`文件开发的过程当中，我们在里面写了`template`模板，在经过了`vue-loader`的处理之后，就变成了`render function`，最终放到了`vue-loader`解析过的文件里面。这样做有什么好处呢？原因是由于在解析`template`变成`render function`的过程，是一个非常耗时的过程，`vue-loader`帮我们处理了这些内容之后，当我们在页面上执行`vue`代码的时候，效率会变得更高。  

`beforeMount`在有了`render function`的时候才会执行，当执行完`render function`之后，就会调用`mounted`这个钩子，在`mounted`挂载完毕之后，这个实例就算是走完流程了。
后续的钩子函数执行的过程都是需要外部的触发才会执行。比如说有数据的变化，会调用`beforeUpdate`，然后经过`Virtual DOM`，最后`updated`更新完毕。当组件被销毁的时候，它会调用`beforeDestory`，以及`destoryed`。  

### 生命周期的钩子 (lifecyclehook)

| 生命周期中的钩子函数 | 组建状态 | 最佳实践 |
|-----|-----|-----|
| beforeCreate | 实例初始化之后,this指向创建的实例，此时还不能访问到`data`,`computed`,`watch`,`methods`上的数据和方法 | 常用于初始化非响应式变量|
| created | 实例创建完成，可以访问到 `data`,`computed`,`watch`,`methods`上的数据和方法，未挂载到dom 上，不能访问`#el` 属性， `$ref`属性d额内容为空数组 | 常用语简单的`ajax请求`,页面的初始化 |
| beforeMount | 在挂载开始前被调用，`beforeMount`之前，会找到对应的`template`，并编译成`render`函数 | - |
| mounted | 实例挂载到DOM上，此时可以通过`DOM API` 获取到`DOM` 节点，`$ref` 属性可以访问 | 常用于获取vnode信息和操作，`ajax`请求 |
|beforeUpdate|响应式数据更新时调用，发生在虚拟DOM打补丁之前| 适合在更新之前访问现有的DOM，比如手动移除已添加的事件监听器 |
|updated | 虚拟DOM重新渲染和打补丁之后调用，组件DOM已经更新，可执行依赖于DOM的操作 | 避免在这个钩子函数中操作数据，可能陷入死循环 |
| beforeDestroy | 实例销毁之前调用。这一步，实力任然完全可用，`this`仍能获取到实例 | 常用于销毁定时器，解绑迁居时间，销毁插件对象等操作 |
| destroyed |实力销毁后调用，调用后，Vue实例只是的所有东西都会解绑，所有的事件监听器会被移除，所有的姿势里也会被销毁  | 在此也可以用于销毁定时器 |
||||

<!-- - beforeCreate: 
  - 组件状态： 实例初始化之后,this指向创建的实例，此时还不能访问到`data`,`computed`,`watch`,`methods`上的数据和方法
  - 最佳实践：常用于初始化非响应式变量
- created:
  - 组件状态：实例创建完成，可以访问到 `data`,`computed`,`watch`,`methods`上的数据和方法，未挂载到dom 上，不能访问`#el` 属性， `$ref`属性d额内容为空数组
  - 最佳实践：常用语简单的`ajax请求`,页面的初始化  
- beforeMount: 
  - 组件状态：在挂载开始前被调用，`beforeMount`之前，会找到对应的`template`，并编译成`render`函数
- mounted:
  - 组件状态：实例挂载到DOM上，此时可以通过`DOM API` 获取到`DOM` 节点，`$ref` 属性可以访问
  - 最佳实践：常用于获取vnode信息和操作，`ajax`请求
- beforeUpdate:
  - 组件状态：响应式数据更新时调用，发生在虚拟DOM打补丁之前
  - 最佳实践：适合在更新之前访问现有的DOM，比如手动移除已添加的事件监听器
- updated:
  - 组件状态：虚拟DOM重新渲染和打补丁之后调用，组件DOM已经更新，可执行依赖于DOM的操作
  - 最佳实践：避免在这个钩子函数中操作数据，可能陷入死循环
- beforeDestroy:
   - 组件状态：实例销毁之前调用。这一步，实力任然完全可用，`this`仍能获取到实例
   - 最佳实践：常用于销毁定时器，解绑迁居时间，销毁插件对象等操作
- destroyed:
  - 组件状态：实力销毁后调用，调用后，Vue实例只是的所有东西都会解绑，所有的事件监听器会被移除，所有的姿势里也会被销毁  
  - 最佳实践：在此也可以用于销毁定时器， -->

注：
- created阶段的`ajax`请求与`mounted`请求的区别：前者页面视图未出现，如果请求信息过多，页面会长时间处于白屏状态，此时如果做`DOM`请求时会报错,页面还没有渲染出来
- mounted 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用 [`vm.$nextTick `](https://blog.csdn.net/shuidinaozhongyan/article/details/72630573)
(https://blog.csdn.net/sinat_17775997/article/details/71638144)

- `beforeCreate`，`created`，`beforeMount`，`mounted` 是自动被调用  
- `beforeUpdate`, `updated`,`beforeDestroy`,`destroyed` 是手动调用  

- 子组件的`created` 优先于父组件的`created` 执行， 父组件的`beforeCreate` 优先于子组件的`beforeCreate`
- 子组件的`mounted`优先于父组件的`mounted`， 父组件的`beforeMount` 优先于子组件的 `beforeMount`
- 子组件的`destroyed`优先于父组件的`beforeDestroy`， 父组件的`beforeDestroy` 优先于子组件的 `beforeDestroy`

#### 最后还有 两个钩子函数没显示  
activated & deactivated 
使用`<keep-alive>`会将数据保留在内存中，如果要在每次进入页面的时候获取最新的数据，需要在activated阶段获取数据，承担原来created钩子中获取数据的任务

- activated： 在组件被激活时调用，在组件第一次渲染时也会被调用，之后每次keep-alive激活时被调用，该钩子在服务器端渲染期间不被调用。
- deactivated： keep-alive组件停用时调用。该钩子在服务端渲染期间不被调用。

  组件内的离开当前路由钩子beforeRouteLeave =>  路由前置守卫 beforeEach =>
    全局后置钩子afterEach => deactivated 离开缓存组件 => activated 进入缓存组件(如果你进入的也是缓存路由)
    // 如果离开的组件没有缓存的话 beforeDestroy会替换deactivated 
    // 如果进入的路由也没有缓存的话  全局后置钩子afterEach=>销毁 destroyed=> beforeCreate等


### 路由导航守卫

[Vue的钩子函数 [路由导航守卫、keep-alive、生命周期钩子]](https://juejin.im/post/5b41bdef6fb9a04fe63765f1)

实例demo：
<!-- 
![](https://files.jb51.net/file_images/article/201709/2017927151335093.png?2017827151357) -->
