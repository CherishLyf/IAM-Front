<template>
  <div class="menu-page" :style="`min-height: ${pageMinHeight}px`">
    <advance-table
      :columns="columns"
      :data-source="dataSource"
      title="菜单列表"
      :loading="tableLoading"
      rowKey="id"
      @search="onSearch"
      @refresh="onRefresh"
      :format-conditions="true"
      @reset="onReset"
      :defaultExpandAllRows="true"
      :pagination="{
        current: page,
        pageSize: pageSize,
        total: total,
        showSizeChanger: true,
        showLessItems: true,
        showQuickJumper: true,
        showTotal: (total, range) => `第 ${range[0]}-${range[1]} 条，总计 ${total} 条`,
        onChange: onPageChange,
        onShowSizeChange: onSizeChange
      }"
    >
    <div slot="action-extra">
      <a-button style="margin: 0 8px 0 0;" icon="plus" type="primary">新建</a-button>
    </div>
    <span slot="status" slot-scope="{ text }">
      <a-tag v-if="text === 1" color="green">
        显示
      </a-tag>
      <a-tag v-if="text === 2" color="red">
        禁用
      </a-tag>
    </span>
    <div slot="action">
      <a class='table-btn' href="#">子菜单</a>
      <a class='table-btn' href="#">编辑</a>
      <a class='table-btn' href="#">删除</a>
    </div>
    </advance-table>
  </div>
</template>

<script>
import AdvanceTable from '@/components/table/advance/AdvanceTable'
import { mapState } from 'vuex'

export default {
  name: 'Menu',
  i18n: require('./i18n'),
  components: {
    AdvanceTable,
  },
  data() {
    return {
      expandedRowKeys: [],
      columns: [
        {
          title: '菜单名称',
          dataIndex: 'name',
          key: 'name',
        },
        {
          title: '权限标识',
          dataIndex: 'permissionCode'
        },
        {
          title: '排序',
          dataIndex: 'sort'
        },
        {
          title: '状态',
          key: 'status',
          dataIndex: 'status',
          scopedSlots: { customRender: 'status' },
        },
        {
          title: '类型',
          dataIndex: 'type'
        },
        {
          title: '创建时间',
          dataIndex: 'createdAt'
        },
        {
          title: '操作',
          key: 'action',
          scopedSlots: { customRender: 'action' },
        },
      ],
      tableLoading: false,
      dataSource: [
        {
          id: 1,
          name: '菜单1',
          permissionCode: 'menu-1',
          sort: 10,
          status: 1,
          children: [
            {
              id: 2,
              name: '菜单1-1',
              permissionCode: 'menu-1-1',
              sort: 10,
              status: 2,
            }
          ],
        },
      ],
      page: 1,
      pageSize: 10,
      total: 0
    }
  },
  computed: {
    ...mapState('setting', ['pageMinHeight']),
    desc() {
      return null
    }
  },
  methods: {
    getGoodList() {
      this.loading = true
    },
    getColumns() {},
    onSearch(conditions, searchOptions) {
      console.log(searchOptions)
      this.page = 1
      // this.conditions = conditions
      this.getGoodList()
    },
    onSizeChange(current, size) {
      this.page = 1
      this.pageSize = size
      this.getGoodList()
    },
    onRefresh(conditions) {
      this.conditions = conditions
      this.getGoodList()
    },
    onReset(conditions) {
      this.conditions = conditions
      this.getGoodList()
    },
    onPageChange(page, pageSize) {
      this.page = page
      this.pageSize = pageSize
      this.getGoodList()
    }
  }
}
</script>

<style scoped lang="less">
@import 'index';
</style>
