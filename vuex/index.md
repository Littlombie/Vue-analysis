# Vuex

* 研究方向
	- state prop机制

* 研究方法
	- vuex主要解决什么问题，如果不用vuex如何解决，
  - vuex如何管理state prop; state prop分别来处理什么内容？
******

>如果你在使用Vue.js，肯定有很多组件之间相互传递参数的经历。当遇到父子组件需要通信的时候，我们通常会采用 props + emit 这种方式。但当通信双方不是父子组件甚至压根不存在相关联系，或者一个状态需要共享给多个组件时，就会非常麻烦，数据也会相当难维护。其实除了传递参数的方式，我们还可以结合业务需要考虑是否用Vuex去解决。

## Vuex是什么？
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

状态管理模式、集中式存储管理初次了解的话感觉有点难以理解，我们通过官方文档中提供的这张图加深下理解。

![vue-State](https://github.com/Littlombie/Vue-analysis/blob/6bcf8295feb4829d3b02520b912bbdbb0349d61f/10-Vuex/images/state.png?raw=true)

## 参考
[Vuex](https://vuex.vuejs.org/zh/)   [prop](https://cn.vuejs.org/v2/guide/components.html#通过-Prop-向子组件传递数据)