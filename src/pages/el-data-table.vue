<template>
  <el-data-table ref="table" v-bind="$data">
    <el-table-column label="状态">
      <template slot-scope="scope">
        <span v-if="scope.row.status === 0" style="color: grey;">下架</span>
        <span v-else style="color: #82c45a;">上架</span>
      </template>
    </el-table-column>
    <el-table-column label="操作" width="230px;">
      <template slot-scope="scope">
        <el-button size="mini" type="primary">查看</el-button>
        <el-button size="mini" @click="handleEdit(scope.row)">编辑</el-button>
        <el-button v-if="scope.row.status === 1" size="mini">下架</el-button>
        <el-button v-else size="mini">上架</el-button>
      </template>
    </el-table-column>
    <el-dialog
      :append-to-body="true"
      :visible.sync="dialogFormVisible"
      title="修改"
    >
      <el-form :model="formObj">
        <el-form-item label="组件名字">
          <el-input v-model="formObj.name" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="handleSubmit">确 定</el-button>
      </div>
    </el-dialog>
  </el-data-table>
</template>

<script>
import Vue from 'vue'
import ElDataTable from '@femessage/el-data-table'
import ElFormRenderer from '@femessage/el-form-renderer'
import {
  Button,
  Dialog,
  Form,
  FormItem,
  Loading,
  Pagination,
  Table,
  TableColumn,
  Message,
  MessageBox,
} from 'element-ui'

Vue.use(Button)
Vue.use(Dialog)
Vue.use(Form)
Vue.use(FormItem)
Vue.use(Loading.directive)
Vue.use(Pagination)
Vue.use(Table)
Vue.use(TableColumn)
Vue.component('el-form-renderer', ElFormRenderer)
Vue.component('el-data-table', ElDataTable)

Vue.prototype.$confirm = MessageBox.confirm
Vue.prototype.$message = Message

export default {
  data() {
    return {
      url: '/components',
      columns: [
        {type: 'selection'},
        {prop: 'name', label: '组件名称'},
        {prop: 'category', label: '分类'},
        {prop: 'version', label: '版本'},
        {prop: 'lang', label: '开发语言'},
        {prop: 'lastUpdateDate', label: '最后更新时间'},
      ],
      hasOperation: false,
      dialogFormVisible: false,
      formObj: {
        name: '',
      },
      selectObj: {},
      form: [
        {
          type: 'input',
          id: 'name',
          label: '用户名',
          rules: [
            {
              required: true,
              message: '请输入用户名',
              trigger: 'blur',
              transform: v => v && v.trim(),
            },
          ],
          el: {placeholder: '请输入用户名'},
        },
      ],
      searchForm: [
        {
          type: 'input',
          id: 'name',
          label: '组件名称',
          collapsible: false,
          el: {placeholder: '请输入'},
        },
        {
          type: 'select',
          id: 'sort',
          label: '分类',
          el: {
            placeholder: '请选择',
          },
          options: [
            {
              value: '前端组件',
            },
            {
              value: 'test',
            },
            {
              value: '分布式工具',
            },
          ],
        },
        {
          type: 'select',
          id: 'status',
          label: '状态',
          el: {
            placeholder: '请选择',
          },
          options: [
            {
              label: '下架',
              value: 0,
            },
            {
              label: '上架',
              value: 1,
            },
          ],
        },
      ],
    }
  },
  methods: {
    handleEdit(obj) {
      this.selectObj = obj
      this.dialogFormVisible = true
      this.formObj.name = obj.name
    },
    handleSubmit() {
      const id = this.selectObj.id
      delete this.selectObj.id
      delete this.selectObj._show
      this.dialogFormVisible = false
      return this.$axios
        .put(`/components/${id}`, this.formObj)
        .then(res => {
          Object.assign(this.selectObj, res.data);
          this.$message.success('操作成功')
        })
        .catch(err => {})
    },
  },
}
</script>
