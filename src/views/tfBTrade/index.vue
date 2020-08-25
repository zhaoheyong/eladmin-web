<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">执行状态</label>
        <el-input v-model="query.status" clearable placeholder="执行状态" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="执行月">
            <el-input v-model="form.execMonth" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="执行天">
            <el-input v-model="form.execDay" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="执行状态">
            <el-input v-model="form.status" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="执行结果">
            <el-input v-model="form.execDesc" :rows="3" type="textarea" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
        <el-button :loading="crud.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
    </div>
  </el-dialog>
  <!--表格渲染-->
  <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
  <el-table-column type="selection" width="55" />
  <el-table-column prop="id" label="id" />
  <el-table-column prop="execMonth" label="执行月" />
  <el-table-column prop="execDay" label="执行天" />
  <el-table-column prop="status" label="执行状态" />
  <el-table-column prop="execDesc" label="执行结果" />
  <el-table-column v-permission="['admin','tfBTrade:edit','tfBTrade:del']" label="操作" width="150px" align="center">
    <template slot-scope="scope">
      <udOperation
        :data="scope.row"
        :permission="permission"
      />
    </template>
  </el-table-column>
</el-table>
  <!--分页组件-->
<pagination />
  </div>
  </div>
  </template>

<script>
import crudTfBTrade from '@/api/tfBTrade/tfBTrade'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { execMonth: null, execDay: null, status: null, execDesc: null, id: null }
export default {
  name: 'TfBTrade',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: 'Trade', url: 'api/tfBTrade', sort: 'id,desc', crudMethod: { ...crudTfBTrade }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'tfBTrade:add'],
        edit: ['admin', 'tfBTrade:edit'],
        del: ['admin', 'tfBTrade:del']
      },
      rules: {
        id: [
          { required: true, message: '不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'status', display_name: '执行状态' }
      ]
    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
