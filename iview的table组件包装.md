``` javascript
<template>
  <div>
    <Table :columns="tableColumns" :data="dataList" :loading="loading">
      <!--iview 3.2.0版本字段支持slot配置，如此可以嵌套多层实现Table封装-->
      <template slot-scope="{row}" slot="action">
        <slot name="operation" :row="row" >
        </slot>
      </template>
    </Table>
    <template v-if="!disablePage">
      <Page v-if="total" :total="total" :show-total="true" :page-size="pageSize" :current="page" @on-change="changePage" prev-text="上一页" next-text="下一页"/>
    </template>
  </div>
</template>

<script>
export default {
  data () {
    return {
      total: 0,
      tableColumns: this.columns,
      pageSize: this.pageSize || 10,
      page: 1,
      dataList: [],
      loading: false
    }
  },
  props: {
    // 表格的每列字段
    columns: {
      required: true,
      type: Array
    },
    params: {
      type: Object,
      default: () => {}
    },
    getList: {
      type: Function,
      required: true
    },
    disablePage: {
      type: Boolean,
      default: false
    }
  },
  watch: {
    params: {
      handler (newVal, oldVal) {
        this.syncGetList()
      },
      // obj对象需要深入监听
      deep: true,
      // 先声明params,然后立即去执行里面的handler方法
      immediate: true
    }
  },
  methods: {
    async syncGetList (page = 1) {
      this.loading = true
      this.dataList = []
      const {pageSize} = this
      // 更新Page组件
      this.page = page
      // 获取的列表格式为{list, pager:{total}}，如果不是分页列表，需要在获取时做数据结构的处理
      const {list, pager} = await this.getList(page, pageSize, this.params)
      this.total = pager && pager.total
      this.dataList = list
      this.loading = false
    },
    async changePage (page) {
      await this.syncGetList(page)
      this.$emit('on-page-change', page)
    },
    // 组件对外接口，用于刷新页面，并且可以设置初始页数
    async refresh (page) {
      await this.syncGetList(page)
    }
  }
}
</script>
```

通过watch监听查询参数的变化，并实时修改列表数据。

`page`（页数）通过参数从外部传入，以适应初始化时页数不为1的情况。同时，非分页列表不传入`total`（总条数），隐藏分页组件。

提供`refresh`接口用于外部调用进行刷新，通常在修改行状态（如活动上下架）之后执行。

为更改页数等操作提供回调，保证内部操作的透明。

选传入函数而不是`data`，不用维护两份数据，想要什么通过作用域插槽来获取，如果我改变了页数，不用再通过`emit`来与父组件通信修改`data`
