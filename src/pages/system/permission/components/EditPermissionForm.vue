<template>
  <div>
    <a-form-model ref="Form" :model="form" :rules="formRules" layout="vertical">
      <a-row :gutter="32">
        <a-col :md="12">
          <a-form-model-item label="权限名称" prop="name">
            <a-input v-model="form.name" placeholder="请填写权限名称" />
          </a-form-model-item>
        </a-col>
        <a-col :md="12" :sm="24">
          <a-form-model-item label="上级权限">
            <a-tree-select
              v-model="form.parentId"
              style="width: 100%"
              :dropdown-style="{ maxHeight: '400px', overflow: 'auto' }"
              :tree-data="permissionTreeList"
              placeholder="请选择上级权限"
              :replace-fields="{
                title: 'name',
                key: 'id',
                value: 'id'
              }"
              allowClear
              showSearch
            >
            </a-tree-select>
          </a-form-model-item>
        </a-col>
      </a-row>
      <a-row :gutter="32">
        <a-col :md="12" :sm="24">
          <a-form-model-item>
            <span slot="label"
              >图标&nbsp;
              <a-tooltip title="图标库 ICON 名称">
                <a-icon type="question-circle-o" />
              </a-tooltip>
            </span>
            <a-input v-model="form.icon" placeholder="请填写图标名称" />
          </a-form-model-item>
        </a-col>
        <a-col :md="12">
          <a-form-model-item label="排序">
            <a-input-number style="width: 100%" v-model="form.sort" :min="0" :max="1000" />
          </a-form-model-item>
        </a-col>
      </a-row>
      <a-row :gutter="32">
        <a-col :md="12" :sm="24">
          <a-form-model-item prop="resourceType">
            <span slot="label"
              >类型&nbsp;
              <a-tooltip title="目录：包含一个或多个权限，权限：具体对应某一个页面，按钮：页面元素">
                <a-icon type="question-circle-o" />
              </a-tooltip>
            </span>
            <a-radio-group v-model="form.resourceType">
              <a-radio-button value="directory"> 目录 </a-radio-button>
              <a-radio-button value="menu"> 菜单 </a-radio-button>
              <a-radio-button value="button"> 按钮 </a-radio-button>
            </a-radio-group>
          </a-form-model-item>
        </a-col>
      </a-row>
      <a-row :gutter="32">
        <a-col :md="12" :sm="24">
          <a-form-model-item prop="permissionCode">
            <span slot="label"
              >权限标识&nbsp;
              <a-tooltip title="权限唯一标识">
                <a-icon type="question-circle-o" />
              </a-tooltip>
            </span>
            <a-input v-model="form.permissionCode" placeholder="请填写权限标识" />
          </a-form-model-item>
        </a-col>
        <a-col
          v-if="form.resourceType === 'directory' || form.resourceType === 'menu'"
          :md="12"
          :sm="24"
        >
          <a-form-model-item label="路径">
            <a-input v-model="form.path" placeholder="请填写前端路径" />
          </a-form-model-item>
        </a-col>
      </a-row>
    </a-form-model>
    <div
      :style="{
        position: 'absolute',
        right: 0,
        bottom: 0,
        width: '100%',
        borderTop: '1px solid #e9e9e9',
        padding: '10px 16px',
        background: '#fff',
        textAlign: 'right',
        zIndex: 1
      }"
    >
      <a-button :style="{ marginRight: '8px' }" @click="handleCloseModal"> 取消 </a-button>
      <a-button type="primary" @click="handleModalSubmit"> 提交 </a-button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'EditPermission',
  props: {
    // 弹窗表单
    form: {
      type: Object,
      default: () => ({
        name: '',
        parentId: null,
        icon: '',
        sort: null,
        resourceType: '',
        permissionCode: '',
        path: ''
      })
    }
  },
  data() {
    return {
      permissionTreeList: [
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
      formRules: {
        name: [{ required: true, message: '请填写权限名称', trigger: 'blur' }],
        resourceType: [{ required: true, message: '请选择类型', trigger: 'change' }],
        permissionCode: [{ required: true, message: '请填写权限标识' }]
      }
    }
  },
  methods: {
    handleCloseModal() {
      this.$emit('cancel');
    },
    handleModalSubmit() {
      this.$emit('submit');
    }
  }
}
</script>

<style scoped lang="less"></style>
