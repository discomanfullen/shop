<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb>
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片 -->
    <el-card>
      <!-- 搜索框 -->
      <el-row :gutter="20">
        <el-col :span="6">
          <el-input placeholder="请输入内容" v-model="queryInfo.query">
            <el-button slot="append" icon="el-icon-search" @click="search"></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="showDialog">立即搜索</el-button>
        </el-col>
      </el-row>
      <!-- 表格 -->
      <el-table
        :data="userList"
        style="width: 100%">
        <el-table-column
          type="index"
          :index="1"
          label="#"
          align="center"
          width="30">
        </el-table-column>
        <el-table-column
          prop="username"
          label="姓名"
          align="center"
          width="80">
        </el-table-column>
        <el-table-column
          prop="email"
          label="邮箱"
          align="center"
          width="160">
        </el-table-column>
        <el-table-column
          prop="mobile"
          label="电话"
          align="center">
        </el-table-column>
        <el-table-column
          prop="role_name"
          label="角色"
          align="center">
        </el-table-column>
        <el-table-column
          prop="mg_state"
          label="状态"
          align="center">
          <template slot-scope="scope">
            <el-switch
              v-model="scope.row.mg_state"
              active-color="#13ce66"
              inactive-color="#ff4949"
              @change="changeState(scope.row)">
            </el-switch>
          </template>
        </el-table-column>
        <el-table-column
          prop="address"
          label="操作"
          align="center"
          width="200">
          <template slot-scope="scope">
            <el-tooltip effect="dark" content="修改角色" placement="top" :enterable="false" :hide-after="2000">
              <el-button size="mini" type="primary" icon="el-icon-edit" @click="editUser(scope)"></el-button>
            </el-tooltip>
            <el-tooltip effect="dark" content="删除角色" placement="top" :enterable="false" :hide-after="2000">
              <el-button size="mini" type="danger" icon="el-icon-delete" @click="deleteUser(scope)"></el-button>
            </el-tooltip>
            <el-tooltip effect="dark" content="分配角色" placement="top" :enterable="false" :hide-after="2000">
              <el-button size="mini" type="warning" icon="el-icon-setting"></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页器 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[2, 3, 5, 7]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="totalpage">
      </el-pagination>
    </el-card>
    <!-- 添加用户模态框 -->
    <el-dialog title="添加用户" :visible.sync="dialogFormVisible" width="30%">
      <el-form ref="formRef" :rules="rules" :model="form">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="form.username" disable></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="form.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="form.mobile"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="submit">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 修改用户模态框 -->
    <el-dialog title="修改用户" :visible.sync="eidtDialogFormVisible" width="30%">
      <el-form ref="editRef" :rules="rules" :model="editForm" @close="editClose">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="editForm.username" disabled></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="editForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="editForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button @click="editClose">取 消</el-button>
        <el-button type="primary" @click="edit">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 删除用户模态框 -->
    <el-dialog title="提示" :visible.sync="deleteDialogFormVisible" width="30%">
      <span>确认删除该用户吗？</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="deleteDialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="confirmDelete">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data () {
    var checkEmail = (rule, value, callback) => {
      rule = /^\w+@\w+(\.\w+)+$/
      if (!rule.test(value)) return callback(new Error('邮箱格式错误'))
      callback()
    }
    function checkMobile (rule, value, callback) {
      rule = /^1[34578]\d{9}$/
      if (rule.test(value)) return callback()
      callback(new Error('手机号码格式错误'))
    }
    return {
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 2
      },
      form: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      editForm: {
        id: '',
        username: '',
        email: '',
        mobile: ''
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 2, max: 8, message: '用户名长度在2~8', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 3, max: 8, message: '密码长度在3~8', trigger: 'blur' }
        ],
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { validator: checkEmail, trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: '请输入手机', trigger: 'blur' },
          { validator: checkMobile, trigger: 'blur' }
        ]
      },
      userList: [],
      totalpage: 0,
      dialogFormVisible: false,
      eidtDialogFormVisible: false,
      deleteDialogFormVisible: false
    }
  },
  methods: {
    // 获取用户数据
    async getUserList () {
      const { data: { data: { total, users }, meta: { status } } } = await this.$axios.get('/users', { params: this.queryInfo })
      if (!status === 200) return false
      else {
        this.userList = users
        this.totalpage = total
        // console.log(users)
      }
    },
    // 页码大小改变
    handleSizeChange (val) {
      this.queryInfo.pagesize = val
      // console.log(val)
      this.getUserList()
    },
    // 当前页改变
    handleCurrentChange (val) {
      this.queryInfo.pagenum = val
      this.getUserList()
    },
    // 查询
    search () {
      this.getUserList()
    },
    // 开关事件
    async changeState (user) {
      const { data: { meta: { msg, status } } } = await this.$axios.put(`/users/${user.id}/state/${user.mg_state}`)
      if (!status === 200) {
        this.userList.mg_state = !this.userList.mg_state
        return this.$message.error(msg)
      } else {
        this.$message.success(msg)
      }
    },
    submit () {
      this.dialogFormVisible = true
      this.$refs.formRef.validate(async valide => {
        // console.log(valide)
        if (!valide) return false
        const { data: { meta: { status, msg } } } = await this.$axios.post('users', this.form)
        // console.log(status, msg)
        if (!status) return this.$message.error(msg)
        this.$message.success(msg)
        this.dialogFormVisible = false
        this.$refs.formRef.resetFields()
      })
    },
    // 模态框显示与隐藏
    showDialog () {
      this.dialogFormVisible = true
    },
    // 修改用户
    editUser (scope) {
      this.eidtDialogFormVisible = true
      this.editForm = JSON.parse(JSON.stringify(scope.row))
    },
    // 提交修改用户
    edit () {
      this.$refs.editRef.validate(async valide => {
        if (!valide) return
        // const result = await this.$axios.put('users/' + this.editForm.id, this.editForm)
        // console.log(result)
        const { data: { meta: { msg, status } } } = await this.$axios.put('users/' + this.editForm.id, this.editForm)
        if (!status === 200) return this.$message.error(msg)
        this.$message.success(msg)
        this.getUserList()
        this.eidtDialogFormVisible = false
      })
    },
    // 修改用户关闭
    editClose () {
      this.$refs.editRef.resetFields()
      this.eidtDialogFormVisible = false
    },
    // 打开删除用户
    deleteUser (scope) {
      this.deleteDialogFormVisible = true
      window.sessionStorage.setItem('deleteUser', scope.row.id)
    },
    // 删除用户
    async confirmDelete () {
      const id = window.sessionStorage.getItem('deleteUser')
      // console.log(id)
      const { data: { meta: { msg, status } } } = await this.$axios.delete(`users/${id}`)
      if (!status === 200) return this.$message.error(msg)
      this.$message.success(msg)
      this.getUserList()
      this.deleteDialogFormVisible = false
    }
  },
  created () {
    this.getUserList()
  }
}
</script>

<style lang="less" scoped>
</style>
