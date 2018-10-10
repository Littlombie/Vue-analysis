# Vue的生命周期方法
* 研究方向
	- hook机制

* 研究方法
	- 各个hook在哪个阶段触发，为啥会在这个阶段触发？触发这个钩子可以改变什么或者得到什么？

******
## 什么是生命周期？

生命周期：Vue 实例从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、卸载等一系列过程，我们称这是 Vue 的生命周期，各个阶段有相对应的事件钩子
![vue 的生命周期](https://github.com/Littlombie/Vue-analysis/blob/master/02-lifeCycle/lifecycle.png?raw=true)

* 图中 数据观测： 
    
在new Vue() 时 开始观测数据

- init event & lifeCycle:  表示new Vue 后事件和生命周期的init
- 从has "el" options 到下边表示 把模板编译成render 函数 的过程 
编译完成后 我们要把vnode挂载（嵌入）到dom上，挂载以后执行mounted

### 钩子函数（Hook）

钩子是Windows消息处理机制的一个平台,应用程序可以在上面设置子程以监视指定窗口的某种消息；
钩子机制允许应用程序截获处理window消息或特定事件，
钩子函数可以理解为js运行进程中在其特定段时的回调函数

### 生命周期的钩子 (lifecyclehook)

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
  - 最佳实践：再次也可以用于销毁定时器，

注：
- created阶段的ajax请求与mounted请求的区别：前者页面视图未出现，如果请求信息过多，页面会长时间处于白屏状态，此时如果做DOM请求时会报错,页面还没有渲染出来
- mounted 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用 vm.$nextTick 

- beforeCreate，created，beforeMount，mounted 是自动被调用  
- beforeUpdate, updated,beforeDestroy,destroyed 是手动调用  

- 子组件的created 优先于父组件的created 执行， 父组件的beforeCreate 优先于子组件的beforeCreate
- 子组件的mounted优先于父组件的mounted， 父组件的beforeMount 优先于子组件的 beforeMount

最后还有 两个钩子函数没显示  
activated & deactivated  
activated 和 deactivated 钩子函数是专门为 keep-alive 组件定制的钩子，我们会在介绍 keep-alive 组件的时候详细介绍，这里先留个悬念

实例demo：

源码分析

![](https://files.jb51.net/file_images/article/201709/2017927151335093.png?2017827151357)