<template>
  <div class="menu-page" :style="`min-height: ${pageMinHeight}px`">
    <div class="table-search">
      <a-form>
        <a-row>
          <a-col :md="8" :sm="24">
            <a-form-item
              label="权限名称"
              :labelCol="{ span: 5 }"
              :wrapperCol="{ span: 18, offset: 1 }"
            >
              <a-input placeholder="请输入" />
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item
              label="隐藏状态"
              :labelCol="{ span: 5 }"
              :wrapperCol="{ span: 18, offset: 1 }"
            >
              <a-select placeholder="请选择">
                <a-select-option value="1">显示</a-select-option>
                <a-select-option value="2">隐藏</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="" :labelCol="{ span: 0 }" :wrapperCol="{ span: 23, offset: 1 }">
              <span class="search-action">
                <a-button class="action-btn" type="primary">查询</a-button>
                <a-button class="action-btn">重置</a-button>
              </span>
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <div class="table-wrapper">
      <advance-table
        :columns="tableColumns"
        :data-source="tableData"
        title="权限列表"
        :loading="tableLoading"
        rowKey="id"
        @refresh="onRefresh"
        :defaultExpandAllRows="true"
      >
        <div slot="action-extra">
          <a-button
            @click="handleCreatePermission"
            style="margin: 0 8px 0 0"
            icon="plus"
            type="primary"
            >新建</a-button
          >
        </div>
        <span slot="status" slot-scope="{ text }">
          <a-tag v-if="text === 1" color="green">显示</a-tag>
          <a-tag v-if="text === 2" color="red">禁用</a-tag>
        </span>
        <span slot="resourceType" slot-scope="{ text }">
          <a-tag v-if="text === 'directory'" color="purple">目录</a-tag>
          <a-tag v-if="text === 'menu'" color="orange">权限</a-tag>
          <a-tag v-if="text === 'action'" color="blue">动作</a-tag>
          <a-tag v-if="text === 'button'" color="cyan">按钮</a-tag>
        </span>
        <div slot="action" slot-scope="{ record }">
          <a class="table-btn" href="#" @click="handleEditSubPermission(record)">子权限</a>
          <a class="table-btn" href="#" @click="handleEditPermission(record)">编辑</a>
          <a class="table-btn" href="#">删除</a>
        </div>
      </advance-table>
    </div>

    <!-- <edit-permission :visible="isModalShow"></edit-permission> -->
    <a-drawer
      title="编辑"
      :visible="isModalShow"
      wrapClassName="custom-ant-drawer"
      :bodyStyle="{ paddingBottom: '80px' }"
      @close="handleCloseModal"
    >
      <edit-permission-form :form="modalForm"></edit-permission-form>
    </a-drawer>
  </div>
</template>

<script>
import AdvanceTable from '@/components/table/advance/AdvanceTable'
import { mapState } from 'vuex'
import EditPermissionForm from './components/EditPermissionForm'

export default {
  name: 'Menu',
  i18n: require('./i18n'),
  components: {
    AdvanceTable,
    EditPermissionForm
  },
  data() {
    return {
      // 搜索表单
      searchForm: {

      },

      tableColumns: [
        {
          title: '权限名称',
          dataIndex: 'name',
          key: 'name'
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
          scopedSlots: { customRender: 'status' }
        },
        {
          title: '类型',
          dataIndex: 'resourceType',
          key: 'resourceType',
          scopedSlots: { customRender: 'resourceType' }
        },
        {
          title: '创建时间',
          dataIndex: 'createdAt'
        },
        {
          title: '操作',
          key: 'action',
          scopedSlots: { customRender: 'action' }
        }
      ],
      tableLoading: false,
      tableData: [
        {
          id: 1,
          name: '权限1',
          permissionCode: 'menu-1',
          sort: 10,
          status: 1,
          resourceType: 'directory',
          children: [
            {
              id: 2,
              name: '权限1-1',
              permissionCode: 'menu-1-1',
              sort: 10,
              status: 2,
              resourceType: 'menu'
            }
          ]
        }
      ],

      // 抽屉展示
      isModalShow: false,
      modalForm: {
        name: '',
        parentId: null,
        icon: '',
        sort: null,
        resourceType: '',
        permissionCode: '',
        path: ''
      },
    }
  },
  computed: {
    ...mapState('setting', ['pageMinHeight']),
    desc() {
      return null
    }
  },
  methods: {
    getTableData() {
      this.loading = true
    },
    onRefresh() {
      this.getTableData()
    },
    // 新建权限
    handleCreatePermission() {
      this.handleOpenModal()
      this.initModalForm({
        name: '',
        parentId: null,
        icon: '',
        sort: null,
        resourceType: '',
        permissionCode: '',
        path: ''
      })
    },
    /**
     * 编辑权限
     * @param row 表格行元素
     */
    handleEditPermission(row) {
      this.initModalForm({
        ...row
      })
      this.handleOpenModal()
    },
    /**
     * 编辑子菜单
     * @param row 表格行元素
     */
    handleEditSubPermission(row) {
      this.initModalForm({
        name: '',
        parentId: row.id,
        icon: '',
        sort: null,
        resourceType: '',
        permissionCode: '',
        path: ''
      })
      this.handleOpenModal()
    },
    // 初始化 modalForm
    initModalForm(formData) {
      this.modalForm = formData
    },
    // 打开弹窗
    handleOpenModal() {
      this.isModalShow = true
    },
    // 关闭弹窗
    handleCloseModal() {
      this.isModalShow = false
    },
    // 弹窗提交
    handleModalSubmit() {
      // TODO Submit
      this.handleCloseModal();
    }
  }
}
</script>

<style scoped lang="less">
@import 'index';
</style>
