# 核心概念

首先得引入`vuex`，然后通过
`vue.use(Vuex)`来
注册插件
``` javascript
const store = new Vuex.Store({
  //vuex的配置
})
```
``` javascript
new Vue ({
  el:'#app',
  store, // store:store
})
```
一个`vue Store` 里边包括`state（状态）`、`getters（派生状态）`、`mutations（提交修改）`、`actions（提交修改数据）`、`module（模块化）`。

## State
`state` 是用来存储状态，也就是变量；
### store.js 中的写入
```  javascript
state: {
  count: 22,
  list:[1,5,6,4,3,56,20]
},
```
### 在Vue中获取Vuex的状态
从 store 实例中读取状态最简单的方法就是在计算属性中返回某个状态：
``` javascript
const tpl = {
  template: `<div>Your number of likes is: {{count}}</div>`,
  computed: {
    count() { 
      //store 为注册的全局
      return this.$store.state.count
    }
  }
}
```
state 只是读取数据，他不会修改数据
## Getters
`getters` 表示派生状态。也就是`set`、`get`中的`get`，有两个可选参数：`state`、`getters`分别可以获取`state`中的变量和其他的`getters`。外部调用方式：`store.getters.personInfo()`。就和`vue`的`computed`差不多
``` javascript
 getters: {
    getInfo(state) {
      return `our number of likes is: ${state.count}`;
    },
    filterdList (state, getters) {
      // return this.$store.list.filter (item => item > 10);
      return state.list.filter (item => item > 10);
    }
  }
```
getters中可以通过 参数`getters`来相互依赖引用其他的`getters（getters.getInfo）`

## Mutations
mutations 是对`state`中的数据修改  
类似于js 的观察这模式，页面提交修改，然后这边做改变处理
``` javascript
mutations: {
  increment(state,n = 1) {
    state.count += n;
  },
  decresement(state) {
    state.count -- ;
  }
}
```
mutations中尽量不要操作异步数据，操作的话 数据不会立即改变，我们一般情况下都是在actions中操作异步数据
## Actions
<!-- actions 也是用来提交修改state的，但是它是显式的提交修改mutations，进而可以异步的实现异步 -->
Action 类似于 `mutation`，不同在于：

Action 提交的是 `mutation`，而不是直接变更状态。
Action 可以包含任意异步操作。

操作
``` javascript
actions: {
  increment (context) { //context表示上线问
    context.commit('increment');
  }
}
```
触发`actions` 的方法是在`template`中我们通过`diapatch()`来提交
``` html
 <button @click="$store.dispatch('increment')">add</button>
```

我们通过actions来执行异步操作
``` javascript
actions: {
  increment (context) {
    return new Promise((resolve,reject) => {
      setTimeout(() => {
        context.commit('increment');
        resolve();
      },2000);
    });
  }
}
```
组件中这样实现
``` javascript
  methods: {
    handleActionAdd () {
      this.$state.dispatch('increment').then( () => {
        console.log(111);
      })
    }
  }
```
会发现 在两秒后才会状态才会发生改变
## Modules
如果项目比较大的时候，项目全部放到一个`store.js` 或者 `main.js` 中 ，感觉比较乱，不太友好，
所以我们需要按模块分开， 
每个模块拥有自己的 `state`、`mutation`、`action`、`getter`、甚至是嵌套子模块——从上至下进行同样方式的分割：
``` javascript
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

## 项目结构

Vuex 并不限制你的代码结构。但是，它规定了一些需要遵守的规则：  
* 应用层级的状态应该集中到单个 store 对象中。
* 提交 mutation 是更改状态的唯一方法，并且这个过程是同步的。
* 异步逻辑都应该封装到 action 里面。

对于大型应用我们会希望把 Vuex 相关代码分割到模块中。下面是项目结构示例：
```
├── index.html
├── main.js
├── api
│   └── ... # 抽取出API请求
├── components
│   ├── App.vue
│   └── ...
└── store
    ├── index.js          # 我们组装模块并导出 store 的地方
    ├── actions.js        # 根级别的 action
    ├── mutations.js      # 根级别的 mutation
    └── modules
        ├── cart.js       # 购物车模块
        └── products.js   # 产品模块
```
