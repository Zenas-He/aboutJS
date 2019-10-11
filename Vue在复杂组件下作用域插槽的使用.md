在`Vue`里面，作用域插槽的应用范围广泛，比如表格操作、视频/新闻卡片点击交互、评论列表交互等。
正常情况下通过如下方式实现
``` javascript
// parent.vue
<child>
  <div slot="action" slot-scope="obj" @click="edit(obj)">
  </div>
</child>

// child.vue
<div v-for="">
  <slot name="action" :obj="obj" >
  </slot>
</div>
```
如果组件只有一层的话，作用域插槽的使用比较顺滑，但是当组件复杂起来之后，基础`UI`组件（如`Row`、`Card`），功能组件（如`Table`），业务组件一层层组装之后，如果再想通过这种方式使用作用域插槽，就只有通过作用域插槽嵌套使用的方式来进行了。如下：
``` javascript
// grandparent.vue
<parent>
  <div slot="action" slot-scope="{obj}" @click="edit(obj)">
  </div>
</parent>

// parent.vue
<child v-for="">
  <template slot="search" slot-scope="{obj}">
    <slot name="action"> :obj="obj"></slot>
  </template>
</child>

//child.vue
<div>
  <slot name="search" :obj="obj" :key="key"></slot>
</div>
```
虽然这样写也可以，但是当组件层级多了之后每加一层，都要修改所有组件，维护工作量巨大，所以是不推荐。

面对这种情况，避免修改中间组件，有两种方法推荐使用，一种是使用传入`render`函数，并传给函数组件进行渲染，这种方式在其他文章里有提到过，这里要说的是通过`$scopeSlots`来实现相同的目的。
和传`render`函数相比，`$scopeSlots`在最外层写的还是作用域插槽，和`render`函数相比更加友好。

``` javascript
// grandparent.vue
<parent>
  <div slot="action" slot-scope="{obj}" @click="edit(obj)">
  </div>
</parent>

// child.vue
<div>
  <scope-slot name="search" :obj="obj" :key="key"></scope-slot>
</div>

// scope-slot.js
export default {
  functional: true,
  inject: ['tableRoot'],
  props: {
    slotName: {
      type: String,
      required: true
    },
    obj: Object
  },
  render: (h, ctx) => {
    return h('div', [ctx.injections.tableRoot.$scopedSlots[ctx.props.slotName]({
      ...ctx.props.obj
    })])
  }
}
```
`Vue2.x`里面把作用域插槽编译成函数的形式，如
``` javascript
function(ref) {
  var obj = ref.obj
  return _c(
    "div",
    {},
    [
      _c(
        "div",
        {
          attrs: {},
          on: {
            click: function($event) {}
          }
        },
        [_vm._v("操作")]
      )
    ],
    1
  )
}
```
调用之后生成`VNode`对象供函数式组件渲染。需要注意的是作用域插槽返回的`VNode`需要用中括号包起来，让`Vue`将其识别为`children`，如果没有的话，将会被识别为`data`，导致最后渲染出来的是一个空`div`标签。
