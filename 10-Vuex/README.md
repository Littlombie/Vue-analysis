# Vuex

* 研究方向
	- state prop机制

* 研究方法
	- vuex主要解决什么问题，如果不用vuex如何解决，
  - vuex如何管理state prop; state prop分别来处理什么内容？
******

如果你在使用Vue.js，肯定有很多组件之间相互传递参数的经历。当遇到父子组件需要通信的时候，我们通常会采用 props + emit 这种方式。但当通信双方不是父子组件甚至压根不存在相关联系，或者一个状态需要共享给多个组件时，就会非常麻烦，数据也会相当难维护。其实除了传递参数的方式，我们还可以结合业务需要考虑是否用Vuex去解决。

## vuex 认识
Vuex 是一个专为 Vue.js 应用程序开发的`状态管理模式`。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 Vue 的官方调试工具 [devtools extension](https://github.com/vuejs/vue-devtools)，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能。

## 什么是“状态管理模式”？
我们从一个简单的技术demo来看 

``` javascript
new Vue({
  //state
  data () {
    return {
      count: 0
    }
  },
  //view
  template: `
  <div>{{count}}</div>
  `,
  //actions
  methods: {
    increment () {
      this.count++
    }
  }
})
```
这个状态自管理应用包含以下几个部分：
* state，驱动应用的数据源；
* view，以声明方式将 state 映射到视图；
* actions，响应在 view 上的用户输入导致的状态变化。

以下是一个表示“单向数据流”理念的极简示意：


![单向数据流](/Vue-analysis/assets/vue-zhuangtai.png)


但是，当我们的应用遇到多个组件共享状态时，单向数据流的简洁性很容易被破坏：

* 多个视图依赖于同一状态。
* 来自不同视图的行为需要变更同一状态。

对于问题一，传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。  
对于问题二，我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。  

因此，我们可以把组件的共享状态抽取出来，以一个`全局单例模式`管理;在这种模式下,我们组件树构成一个巨大的`“视图”`，不管我们在处于输的那一层，或者任何组件都可以获取到状态,或者触发行为。

另外，通过定义和隔离状态管理中的各种概念并强制遵守一定的规则，我们的代码将会变得更结构化且易维护。

这就是 Vuex 背后的基本思想，借鉴了 [Flux](https://facebook.github.io/flux/docs/overview.html)、[Redux](http://redux.js.org/)、和 [The Elm Architecture](https://guide.elm-lang.org/architecture/)。与其他模式不同的是，Vuex 是专门为 Vue.js 设计的状态管理库，以利用 Vue.js 的细粒度数据响应机制来进行高效的状态更新。


![vuex](/Vue-analysis/assets/vuex1.png)


简而言之：Vuex 相当于某种意义上设置了读写权限的全局变量，将数据保存保存到该“全局变量”下，并通过一定的方法去读写数据。


# 什么情况下我们应该使用vuex？
虽然 Vuex 可以帮助我们管理共享状态，但也附带了更多的概念和框架。这需要对短期和长期效益进行权衡。

如果您不打算开发大型单页应用，使用 Vuex 可能是繁琐冗余的。确实是如此——如果您的应用够简单，您最好不要使用 Vuex。一个简单的 [store](https://cn.vuejs.org/v2/guide/state-management.html#%E7%AE%80%E5%8D%95%E7%8A%B6%E6%80%81%E7%AE%A1%E7%90%86%E8%B5%B7%E6%AD%A5%E4%BD%BF%E7%94%A8) 模式就足够您所需了。但是，如果您需要构建一个中大型单页应用，您很可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。



# 参考

[Vuex](https://vuex.vuejs.org/zh/)
[prop](https://cn.vuejs.org/v2/guide/components.html#通过-Prop-向子组件传递数据)