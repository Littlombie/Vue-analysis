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
![vue 的生命周期](https://github.com/Littlombie/Vue-analysis/blob/master/02-lifeCycle/lifecycle.png?raw=true)

* 图中 数据观测： 
    
在new Vue() 时 开始观测数据

- init event & lifeCycle:  表示new Vue 后事件和生命周期的init
- 当created完成之后，它会去判断instance（实例）里面是否含有“el”option（选项），如果没有的话，它会调用vm.$mount(el)这个方法，然后执行下一步；如果有的话，直接执行下一步。紧接着会判断是否含有“template”这个选项，如果有的话，它会把template解析成一个render function ，这是一个template编译的过程，结果是解析成了render函数：
```
render (h) {
  return h('div', {}, this.text)
}
```
render函数里面的传参h就是Vue里面的createElement方法，return返回一个createElement方法，其中要传3个参数，第一个参数就是创建的div标签；第二个参数传了一个对象，对象里面可以是我们组件上面的props，或者是事件之类的东西；第三个参数就是div标签里面的内容，这里我们指向了data里面的text。  
使用render函数的结果和我们之前使用template解析出来的结果是一样的。render函数是发生在beforeMount和mounted之间的，这也从侧面说明了，在beforeMount的时候，$el还只是我们在HTML里面写的节点，然后到mounted的时候，它就把渲染出来的内容挂载到了DOM节点上。这中间的过程其实是执行了render function的内容。  
在使用.vue文件开发的过程当中，我们在里面写了template模板，在经过了vue-loader的处理之后，就变成了render function，最终放到了vue-loader解析过的文件里面。这样做有什么好处呢？原因是由于在解析template变成render function的过程，是一个非常耗时的过程，vue-loader帮我们处理了这些内容之后，当我们在页面上执行vue代码的时候，效率会变得更高。  
beforeMount在有了render function的时候才会执行，当执行完render function之后，就会调用mounted这个钩子，在mounted挂载完毕之后，这个实例就算是走完流程了。
后续的钩子函数执行的过程都是需要外部的触发才会执行。比如说有数据的变化，会调用beforeUpdate，然后经过Virtual DOM，最后updated更新完毕。当组件被销毁的时候，它会调用beforeDestory，以及destoryed。  

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

- beforeCreate: 
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
  - 最佳实践：在此也可以用于销毁定时器，

注：
- created阶段的ajax请求与mounted请求的区别：前者页面视图未出现，如果请求信息过多，页面会长时间处于白屏状态，此时如果做DOM请求时会报错,页面还没有渲染出来
- mounted 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用 vm.$nextTick 

- beforeCreate，created，beforeMount，mounted 是自动被调用  
- beforeUpdate, updated,beforeDestroy,destroyed 是手动调用  

- 子组件的created 优先于父组件的created 执行， 父组件的beforeCreate 优先于子组件的beforeCreate
- 子组件的mounted优先于父组件的mounted， 父组件的beforeMount 优先于子组件的 beforeMount

最后还有 两个钩子函数没显示  
activated & deactivated  
activated 和 deactivated 钩子函数是专门为 keep-alive 组件定制的钩子

实例demo：
<!-- 
![](https://files.jb51.net/file_images/article/201709/2017927151335093.png?2017827151357) -->
