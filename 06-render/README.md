# render
	
* 研究方向
	- render机制

* 研究方法
	- 条件渲染、列表渲染、update、模板、服务器渲染、渲染性能，如何编译，ast是什么
******

[渲染函数](https://cn.vuejs.org/v2/guide/render-function.html)

web页面渲染分四种方式  
* 后端模板渲染
* 客户端渲染
* node中间层
* 服务端渲染（ssr）

### 什么是render函数

Render函数是Vue2.x版本新增的一个函数；使用虚拟dom来渲染节点提升性能，因为它是基于JavaScript计算。通过使用createElement(h)来创建dom节点。createElement是render的核心方法。其Vue编译的时候会把template里面的节点解析成虚拟dom；

VUE推荐在绝大多数情况下使用template来创建我们的HTML。然而在一些场景中，我们真的需要JavaScript的完全编程的能力，这就是render函数，它比template更接近编译器。

>在之前的Vue1.X版本中没有Virtual DOM,Vue2.0之后添加了此功能，而Virtual DOM 最后是通过`render`函数来生成模板页面

vue  在new Vue()最后的渲染只认render 函数 所有的东西 html,template 都会编译成render函数  
demo：  
```
render (h) {
  return h('div', {}, this.text)
}
```
`render`函数里面的传参h就是Vue里面的`createElement`方法，`return`返回一个`createElement`方法，其中要传3个参数，第一个参数就是创建的div标签；第二个参数传了一个对象，对象里面可以是我们组件上面的props，或者是事件之类的东西；第三个参数就是div标签里面的内容，这里我们指向了data里面的text。  

使用render函数的结果和我们之前使用`template`解析出来的结果是一样的。`render`函数是发生在`beforeMount`和`mounted`之间的，这也从侧面说明了，在`beforeMount`的时候，`$el`还只是我们在HTML里面写的节点，然后到`mounted`的时候，它就把渲染出来的内容挂载到了`DOM`节点上。这中间的过程其实是执行了`render function`的内容。    

在使用`.vue`文件开发的过程当中，我们在里面写了`template`模板，在经过了`vue-loader`的处理之后，就变成了`render function`，最终放到了`vue-loader`解析过的文件里面。这样做有什么好处呢？原因是由于在解析`template`变成`render function`的过程，是一个非常耗时的过程，`vue-loader`帮我们处理了这些内容之后，当我们在页面上执行`vue`代码的时候，效率会变得更高。  

之前的实例可以改为render 写法
``` javascript
var vm = new Vue({
  el: '#app',
  render (createElement) {
    return createElement('div', {
      attrs: {
        id: 'app'
      }
    }, this.message)
  },
  data () {
    return message: 'Hello Vue!'
  }
}) 
```
上边代码最后显示出来 跟之前 那种是一样的效果 

### 源码分析

编译相关的代码都在 compiler文件中

core/instance / render.js 中

observe - 响应式
_ 在js中默认为是定义的私有属性 ，建议不要多次访问 

(视频2-5， 2-6)
platform/util/index.js  判断是否是render 还是template

new watcher() 渲染 watcher (observer/watcher.js ) 

#

Vue 的 _render 方法是实例的一个私有方法，它用来把实例渲染成一个虚拟 Node。它的定义在 src/core/instance/render.js 文件中：
```javascript
//再次定义一个render私有方法  返回一个vnode，通过vm.$options拿到render函数
Vue.prototype._render = function (): VNode {
  const vm: Component = this
  const { render, _parentVnode } = vm.$options 

  // 复位_render标志在插槽上用于重复的插槽检查
  if (process.env.NODE_ENV !== 'production') {
    for (const key in vm.$slots) {
      // $flow-disable-line
      vm.$slots[key]._rendered = false
    }
  }

  if (_parentVnode) {
    vm.$scopedSlots = _parentVnode.data.scopedSlots || emptyObject
  }

  // 设置父vnode。这允许呈现函数访问占位符节点上的数据。
  vm.$vnode = _parentVnode
  // render self
  let vnode
  try {
    //利用call的方法 参数一当前上下文，vm._renderProxy再生产环境下 就是vm，也就是this 本身，开发环境是一个proxy 对象
    vnode = render.call(vm._renderProxy, vm.$createElement)
  } catch (e) {
    handleError(e, vm, `render`)
    // 返回错误呈现结果，
    // 或先前的vnode，以防止呈现错误导致空白组件
    /* istanbul ignore else */
    if (process.env.NODE_ENV !== 'production') {
      if (vm.$options.renderError) {
        try {
          vnode = vm.$options.renderError.call(vm._renderProxy, vm.$createElement, e)
        } catch (e) {
          handleError(e, vm, `renderError`)
          vnode = vm._vnode
        }
      } else {
        vnode = vm._vnode
      }
    } else {
      vnode = vm._vnode
    }
	}
	
	// 上边 会返回一个 vnode ， $options 这个函数可以自己写 ，也可以通过编译生成
  // 如果呈现函数出错，返回空vnode
  if (!(vnode instanceof VNode)) {
    if (process.env.NODE_ENV !== 'production' && Array.isArray(vnode)) {
      warn(
        'Multiple root nodes returned from render function. Render function ' +
        'should return a single root node.',
        vm
      )
    }
    vnode = createEmptyVNode()
  }
  // set parent
  vnode.parent = _parentVnode
  return vnode
}
```
这段代码最关键的是 `render` 方法的调用，我们在平时的开发工作中手写 `render` 方法的场景比较少，而写的比较多的是 `template` 模板，在之前的 mounted 方法的实现中，会把 `template` 编译成 `render` 方法，但这个编译过程是非常复杂的，我们不打算在这里展开讲，之后会专门花一个章节来分析 `Vue` 的编译过程。  

在 Vue 的官方文档中介绍了 `render` 函数的第一个参数是 `createElement`，那么结合之前的例子：

``` javascript
<div id="app">
  {{ message }}
</div>
```
相当于我们编写如下 `render` 函数：
``` javascript
render: function (createElement) {
  return createElement('div', {
     attrs: {
        id: 'app'
      },
  }, this.message)
}
```
再回到 `_render` 函数中的 `render `方法的调用：

```javascript
vnode = render.call(vm._renderProxy, vm.$createElement)
```
可以看到，`render` 函数中的 `createElement` 方法就是 `vm.$createElement` 方法：
```javascript
export function initRender (vm: Component) {
  // ...
  //将createElement fn绑定到这个实例，这样我们就可以在其中获得适当的呈现上下文。
  // args顺序:标签、数据、子元素、normalizationType、alwaysNormalize内部版本由模板编译的呈现函数使用
  vm._c = (a, b, c, d) => createElement(vm, a, b, c, d, false)
  //规范化通常应用于公共版本，用于用户编写的呈现函数。

  //手写render函数 创建的方法
  vm.$createElement = (a, b, c, d) => createElement(vm, a, b, c, d, true)
}
```
实际上，`vm.$createElement` 方法定义是在执行 `initRender` 方法的时候，可以看到除了 `vm.$createElement` 方法，还有一个 `vm._c` 方法，它是被模板编译成的 `render` 函数使用，而 `vm.$createElement` 是用户手写 `render` 方法使用的， 这俩个方法支持的参数相同，并且内部都调用了 `createElement` 方法。

# 总结


1. render方法的实质就是生成template模板； 
2. 通过调用一个方法来生成，而这个方法是通过render方法的参数传递给它的； 
3. 这个方法有三个参数，分别提供标签名，标签相关属性，标签内部的html内容 
4. 通过这三个参数，可以生成一个完整的木模板  

备注：
render方法可以使用JSX语法，但需要Babel plugin插件；
render方法里的第三个参数可以使用函数来生成多个组件（特别是如果他们相同的话），只要生成结果是一个数组，且数组元素都是VNode即可；  

注意： 
render函数室友限制的，Vue.js 2.X支持，但是1.X无法使用。

`vm._render `最终是通过执行 `createElement` 方法并返回的是 `vnode`，它是一个虚拟 `Node`。Vue 2.0 相比 Vue 1.0 最大的升级就是利用了 `Virtual DOM`。因此在分析 `createElement` 的实现前，我们先了解一下 `Virtual DOM` 的概念。

### [参考博客]
[vue Render函数进阶](https://blog.csdn.net/wngzhem/article/details/54291024)  
[理解Vue中的Render渲染函数](https://www.cnblogs.com/tugenhua0707/p/7528621.html)  
[(一) Vue基础个人总结，条件渲染，列表渲染，组件等](https://blog.csdn.net/weixin_42229553/article/details/80444919  )  
[如何理解Vue的render函数的具体用法](https://www.jb51.net/article/122424.htm )   
[Vue2.x中的Render函数](https://www.cnblogs.com/leungUwah/p/6921873.html )   
[用jsx写vue组件](http://www.alloyteam.com/2017/07/12918/)
