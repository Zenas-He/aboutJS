#### 设计思路
把Toast组件直接$mount，并且添加到body上，作为一个和页面无关的组件使用。Toast组件里根据业务不同包含多种弹窗，对外暴露添加方法（），并且传入配置用以渲染正确的弹窗。
设计参考iview的Notice组件。
#### 业务上的最小单元
如果写个div，给个fixed，并且传入top、left，再通过插槽自定义渲染内容，固然有极高的自由度，但是对使用者来说是不够友好的，所有的东西包括展示内容的样式都要自己去写。  

而如果每种弹窗都自己写一个组件，如果业务多起来了，使用起来会比较繁琐，更会对不了解项目对人造成困扰，对老手来说，需要花额外的时间来了解项目里的组件封装程度，对新手来说，可能会因为没有明显可用的组件而花时间重新写一个组件。  
所以这里把所有的弹窗组件都写在Toast组件里，根据页面布局，差别小的弹窗归为一个组件。

``` javascript
// toast.vue
<template>
  <div :styles="wrappedStyles">
    <share
      v-if="shareShow"
      :name="shareInfo.name"
      :styles="shareInfo.styles"
      :content="shareInfo.content">
    </share>
    <download-tip ...></download-tip>
  </div>
</template>

<script>
import Share from './share.vue'
import DownloadTip from './download-tip.vue

export default {
  name: 'Toast',
  data () {
    return {
      shareInfo: {},
      shareShow: false,
      downloadTipInfo: {},
      downloadTipShow: false
    }
  },
  props: {
    styles: {
      type: Object,
      default: () => {}
    }
  },
  computed: {
    wrappedStyles () {
      let styles = Object.assign({}, this.styles)
      styles['z-index'] = 1000
      return styles
    }
  },
  components: {Share, DownloadTip},
  methods: {
    addShare (info) {
      let _info = Object.assign({
        name: 'toast_share_' + new Date().getTime()
      }, info)

      this.shareShow = true
      // 因为是整个对象的赋值，不用担心给配置添加的属性无法响应
      this.shareInfo = _info
    },
    addDownloadTip (info) {
      // ...
    },
    close (kind) {
      if (kind === 'share') {
        this.shareShow = false
        // 释放空间
        this.shareInfo = {}
      } else if (kind === 'download-tip') {
        // ...
      }
    },
    closeAll () {
      this.shareShow = false
      this.downloadTipShow = false
    }
  }
}
</script>
```
wrappedStyles通过computed对传入的样式配置进行进一步修改，比如在弹窗里需要考虑到的index，以及多个弹窗的index的问题

``` javascript
// share.vue
<template>
  <transition name="fade">
    <div :style="styles">
      <img src="./close.png" @click="closeHandler"/>
      <div v-if="renderFunc">
        <render-cell :render="renderFunc"></render-cell>
      </div>
      <div v-else v-html="content"></div>
    </div>
  </transition>
</template>

<script>
import renderCell from './render'

export default {
  props: {
    ...
    // 由于toast是直接挂载在body上的，并没有和页面产生任何联系，所以关闭的回调通过接口传入
    onClose: {
      type: Function,
      default: function () {}
    },
    render: {
      type: Function
    }
  },
  computed: {
    renderFunc () {
      return this.render
    }
  },
  components: {renderCell},
  methods: {
    closeHandler () {
      this.$parent.close('share')
    }
  }
}
</script>

// render.vue
export default {
  name: 'renderCell',
  functional: true,
  props: {
    render: Function
  },
  render: (h, ctx) => {
    return ctx.props.render(h)
  }
}
```
除了直接调用`this.$parent.close`，也可以通过`emit`、`eventbus`、`dispatch`、`findComponents`的方式进行组件间通信，从本质上来说后两者还是对$parent的使用。

通过函数组件把自定义内容通过render函数的形式传递到组件中进行渲染。如果没有传render函数，允许使用者传入静态标签或者文字进行渲染。


#### PS
- 弹窗组件作为一个单例，既可以直接在引入时执行，也可以在调用时执行。
- 单例模式可以用`proxy`和`Reflect.construct(target, args)`来实现。
