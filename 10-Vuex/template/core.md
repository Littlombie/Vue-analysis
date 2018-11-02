# 核心概念

## state
`state` 是用来存储状态，也就是变量；
```  javascript
state: {
  name: 'weish',
  age: 22
},
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
