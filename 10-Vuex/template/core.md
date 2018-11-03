# 核心概念

## state
`state` 是用来存储状态，也就是变量；
### store.js 中的写入
```  javascript
state: {
  name: 'weish',
  age: 22
},
```
### 在Vue中获取Vuex的状态
从 store 实例中读取状态最简单的方法就是在计算属性中返回某个状态：
``` javascript
const tpl = {
  template: `<div>His name is: {{name}},and age is{{age}}</div>`,
  computed: {
    name() { 
      //store 为注册的全局
      return store.state.name
    },
    age () {
      return store.state.age
    }
  }
}
```


## getters
`getters` 表示派生状态。也就是`set`、`get`中的`get`，有两个可选参数：`state`、`getters`分别可以获取`state`中的变量和其他的`getters`。外部调用方式：`store.getters.personInfo()`。就和`vue`的`computed`差不多
``` javascript
 getters: {
    getInfo(state) {
      return `My name is ${state.name}, I am ${state.age}`;
    }
  }
```

## mutations

## actions

## modules


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
