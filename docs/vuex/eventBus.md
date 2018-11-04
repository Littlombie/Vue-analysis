# eventBus

`eventBus`的作用就是解决一些数据量不大，但是需要组件 之间传值。这时候用`Vuex`的话就显得比较大材小用。

## 使用方法
在根目录创建一个`event-bus.js`文件夹：

``` javascript
const install = function (Vue) {
  const Bus = new Vue({
    methods: {
      emit (event, ...args) {
        this.$emit(event, ...args)
      },
      on (event, callback) {
        this.$on(event, callback)
      },
      off (event, callback) {
        this.$off(event, callback)
      }
    },
    data () {
      return {}
    }
  })
  Vue.prototype.$bus = Bus
}
export default install
```
上边其实就是创建了一个`Vue`实例，然后创建三个方法，其中`event`是表示发送改变参数，`on`表示接收参数，`off`表示停止参数接收，内部还可以配置`data`，看个人项目的实际需求。  

接着在`main.js`中引入`bus`文件，注册 `bus`。 

``` javascript
import Bus from './vue-bus'
Vue.use(Bus)
```
然后就可以在组件中使用bus了  
A组件发送
``` html
<template>
  <div>
    <button @click="handleAdd">随机增加</button>
  </div>
</template>
<script>
  export default ({
    methods: {
      handleAdd () {
        const num = Math.floor(Math.random() * 100 + 1)
        this.$bus.emit('add', num)
      }
    }
  })
</script>
```
B组件接收  
``` html
<template>
  <div>
    <i class="info-cout">{{num}} </i></router-link>
  </div>
</template>
<script>
  export default ({
    data () {
      return {
        num: 0
      }
    },
    created () {
      this.$bus.on('add', (val) => {
        this.num = val
      })
    }
  })
</script>
```
这样就实现了数据的组件间传值。