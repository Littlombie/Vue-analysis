# render
	
* 研究方向
	- render机制

* 研究方法
	- 条件渲染、列表渲染、update、模板、服务器渲染、渲染性能，如何编译，ast是什么
******

web页面渲染分四种方式  
* 后端模板渲染： 
  * 指使用PHP等后端语言来生成页面，通常情况下，需要后端配合，混合项目开发。以前项目都这样搞，缺点大于优点
* 客户端渲染：
  * 指使用 JS 来渲染页面大部分内容，后端资源都是通过ajax请求数据来渲染。代表是现在流行的 SPA 单页面应用；
* node中间层：
  * 前后端分离，但优于前端直接请求接口从而产生的一系列问题。 比如可以用PHP写后端简单的接口，Node.js封装PHP接口，前端axios请求封装后的接口，将需要的数据返回到对应的view层页面，既解决了跨域问题（Node.js作为服务端，服务端没有跨域一说），同时又不需要配后端环境，只需要一个PHP接口 [详细说明](https://segmentfault.com/a/1190000012950302)
* 服务端渲染（ssr）：
  *  主要指的是ssr，在准确点说就是「同构渲染」指前后端共用 JS，首次渲染时使用 Node.js 来直出 HTML。一般来说同构渲染是介于前后端中的共有部分。


### 什么是render函数 ([渲染函数](https://cn.vuejs.org/v2/guide/render-function.html))

Render函数是Vue2.x版本新增的一个函数；使用虚拟dom来渲染节点提升性能，因为它是基于JavaScript计算。通过使用createElement(h)来创建dom节点。createElement是render的核心方法。其Vue编译的时候会把template里面的节点解析成虚拟dom；

vue推荐在绝大多数情况下使用template来创建我们的HTML。然而在一些场景中，我们真的需要JavaScript的完全编程的能力，这就是render函数，它比template更接近编译器。

./template/demo1

>在之前的Vue1.X版本中没有Virtual DOM,Vue2.0之后添加了此功能，而Virtual DOM 最后是通过`render`函数来生成模板页面  
vue  在new Vue()最后的渲染只认render 函数 所有的东西 html,template 都会编译成render函数  


### createElement 参数
demo：  
```
render (h) {
  return h('div', {}, this.text)
}
```
`render`函数里面的传参h就是Vue里面的`createElement`方法，`return`返回一个`createElement`方法，（官方文档：返回的其实不是一个实际的 DOM 元素。它更准确的名字可能是 createNodeDescription，因为它所包含的信息会告诉 Vue 页面上需要渲染什么样的节点，及其子节点。我们把这样的节点描述为“虚拟节点 (Virtual Node)”，也常简写它为“VNode”。“虚拟 DOM”是我们对由 Vue 组件树建立起来的整个 VNode 树的称呼。）

其中可以传三个参数：
* 第一个参数 {String | Object | Function} 表示可以传一个 HTML 标签字符串，组件选项对象，或者解析上述任何一种的一个 async 异步函数。必需参数；
* 第二个参数 {Object} 一个包含模板相关属性的数据对象，对象里面可以是我们组件上面的props，或者是事件之类的东西，你可以在 template 中使用这些特性。可选参数；  
  data的对象：
  ``` javascript
    {
    // 和`v-bind:class`一样的 API
    // 接收一个字符串、对象或字符串和对象组成的数组
    'class': {
      foo: true,
      bar: false
    },
    // 和`v-bind:style`一样的 API
    // 接收一个字符串、对象或对象组成的数组
    style: {
      color: 'red',
      fontSize: '14px'
    },
    // 普通的 HTML 特性
    attrs: {
      id: 'foo'
    },
    // 组件 props
    props: {
      myProp: 'bar'
    },
    // DOM 属性
    domProps: {
      innerHTML: 'baz'
    },
    // 事件监听器基于 `on`
    // 所以不再支持如 `v-on:keyup.enter` 修饰器
    // 需要手动匹配 keyCode。
    on: {
      click: this.clickHandler
    },
    // 仅用于组件，用于监听原生事件，而不是组件内部使用
    // `vm.$emit` 触发的事件。
    nativeOn: {
      click: this.nativeClickHandler
    },
    // 自定义指令。注意，你无法对 `binding` 中的 `oldValue`
    // 赋值，因为 Vue 已经自动为你进行了同步。
    directives: [
      {
        name: 'my-custom-directive',
        value: '2',
        expression: '1 + 1',
        arg: 'foo',
        modifiers: {
          bar: true
        }
      }
    ],
    // 作用域插槽格式
    // { name: props => VNode | Array<VNode> }
    scopedSlots: {
      default: props => createElement('span', props.text)
    },
    // 如果组件是其他组件的子组件，需为插槽指定名称
    slot: 'name-of-slot',
    // 其他特殊顶层属性
    key: 'myKey',
    ref: 'myRef',
    // 如果你在渲染函数中向多个元素都应用了相同的 ref 名，
    // 那么 `$refs.myRef` 会变成一个数组。
    refInFor: true
  }
  ```
* 第三个参数 {String | Array} 子虚拟节点 (VNodes)，由 `createElement()` 构建而成， 也可以使用字符串来生成“文本虚拟节点”。可选参数。
 如：
  ```javascript
    [
      '先写一些文字',
      createElement('h1', '一则头条'),
      createElement(MyComponent, {
        props: {
          someProp: 'foobar'
        }
      })
    ]
  ```
使用render函数的结果和我们之前使用`template`解析出来的结果是一样的。`render`函数是发生在`beforeMount`和`mounted`之间的，这也从侧面说明了，在`beforeMount`的时候，`$el`还只是我们在HTML里面写的节点，然后到`mounted`的时候，它就把渲染出来的内容挂载到了`DOM`节点上。这中间的过程其实是执行了`render function`的内容。    

在使用`.vue`文件开发的过程当中，我们在里面写了`template`模板，在经过了`vue-loader`的处理之后，就变成了`render function`，最终放到了`vue-loader`解析过的文件里面。这样做有什么好处呢？原因是由于在解析`template`变成`render function`的过程，是一个非常耗时的过程，`vue-loader`帮我们处理了这些内容之后，当我们在页面上执行`vue`代码的时候，效率会变得更高。  

VNodes必须唯一
组件树中的所有 VNodes 必须是唯一的。这意味着，下面的 render function 是无效的： 
``` javascript
render: function (createElement) {
  var myParagraphVNode = createElement('p', 'hi')
  return createElement('div', [
    // 错误-重复的 VNodes
    myParagraphVNode, myParagraphVNode
  ])
}
```
如果你真的需要重复很多次的元素/组件，你可以使用工厂函数来实现。例如，下面这个例子 render 函数完美有效地渲染了 20 个相同的段落：
``` javascript
  render: function (createElement) {
  return createElement('div',
    Array.apply(null, { length: 20 }).map(function () {
      return createElement('p', 'hi')
    })
  )
}
```
### 使用JavaScript 代替模板功能
只要在原生的 JavaScript 中可以轻松完成的操作，Vue 的 render 函数就不会提供专有的替代方法。比如，在 template 中使用的 v-if 和 v-for：
``` html
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>No items found.</p>
```
这些都会在 render 函数中被 JavaScript 的 if/else 和 map 重写：
``` javascript
props: ['items'],
render: function (createElement) {
  if (this.items.length) {
    return createElement('ul', this.items.map(function (item) {
      return createElement('li', item.name)
    }))
  } else {
    return createElement('p', 'No items found.')
  }
}
```
v-model  
render 函数中没有与 v-model 的直接对应 - 你必须自己实现相应的逻辑：
``` javascript
props: ['value'],
render: function (createElement) {
  var self = this
  return createElement('input', {
    domProps: {
      value: self.value
    },
    on: {
      input: function (event) {
        self.$emit('input', event.target.value)
      }
    }
  })
}
```
这就是深入底层的代价，但与 v-model 相比，这可以让你更好地控制交互细节。

#### 事件&案件修饰符
对于 .passive、.capture 和 .once事件修饰符, Vue 提供了相应的前缀可以用于 on：
Modifier(s) |	Prefix
|-|-|
.passive |	&
.capture|	!
.once|	~
.capture.once or .once.capture	| ~!

例如:
``` javascript
  on: {
    '!click': this.doThisInCapturingMode,
    '~keyup': this.doThisOnce,
    '~!mouseover': this.doThisOnceInCapturingMode
  }
```
对于其他的修饰符，前缀不是很重要，因为你可以在事件处理函数中使用事件方法：
Modifier(s) |	Equivalent in Handler
|-|-|
.stop |	event.stopPropagation()
.prevent|	event.preventDefault()
.self |	if (event.target !== event.currentTarget) return
Keys: .enter, .13	 | if (event.keyCode !== 13) return (change 13 to another key code for other key modifiers)
Modifiers Keys: .ctrl, .alt, .shift, .meta |	if (!event.ctrlKey) return (change ctrlKey to altKey, shiftKey, or metaKey, respectively)
这里是一个使用所有修饰符的例子：
``` javascript
  on: {
    keyup: function (event) {
      // 如果触发事件的元素不是事件绑定的元素
      // 则返回
      if (event.target !== event.currentTarget) return
      // 如果按下去的不是 enter 键或者
      // 没有同时按下 shift 键
      // 则返回
      if (!event.shiftKey || event.keyCode !== 13) return
      // 阻止 事件冒泡
      event.stopPropagation()
      // 阻止该元素默认的 keyup 事件
      event.preventDefault()
      // ...
    }
  }
```
#### 插槽
你可以通过 this.$slots 访问静态插槽的内容，得到的是一个 VNodes 数组：
``` javascript
render: function (createElement) {
  // `<div><slot></slot></div>`
  return createElement('div', this.$slots.default)
}
```
也可以通过 this.$scopedSlots 访问作用域插槽，得到的是一个返回 VNodes 的函
``` javascript
props: ['message'],
render: function (createElement) {
  // `<div><slot :text="message"></slot></div>`
  return createElement('div', [
    this.$scopedSlots.default({
      text: this.message
    })
  ])
}
```
如果要用渲染函数向子组件中传递作用域插槽，可以利用 `VNode` 数据对象中的 `scopedSlots` 域：
``` javascript
render: function (createElement) {
  return createElement('div', [
    createElement('child', {
      // 在数据对象中传递 `scopedSlots`
      // 格式：{ name: props => VNode | Array<VNode> }
      scopedSlots: {
        default: function (props) {
          return createElement('span', props.text)
        }
      }
    })
  ])
}
```
### [AST](http://www.php.cn/js-tutorial-407382.html) 

AST是指抽象语法树（abstract syntax tree），或者语法树（syntax tree），是源代码的抽象语法结构的树状表现形式。Vue在mount过程中，template会被编译成AST语法树。
然后，经过generate（将AST语法树转化成render function字符串的过程）得到render函数，返回VNode。

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
//再此定义一个render私有方法  返回一个vnode，通过vm.$options拿到render函数
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

### 总结
#
1. render方法的实质就是生成template模板； 
2. 通过调用一个方法来生成，而这个方法是通过render方法的参数传递给它的； 
3. 这个方法有三个参数，分别提供标签名，标签相关属性，标签内部的html内容 
4. 通过这三个参数，可以生成一个完整的模板  

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
