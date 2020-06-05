<template>
	<div>
		<el-breadcrumb separator-class="el-icon-arrow-right">
			<el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
			<el-breadcrumb-item>用户管理</el-breadcrumb-item>
			<el-breadcrumb-item :to="{ path: '/users' }">用户列表</el-breadcrumb-item>
		</el-breadcrumb>
		<el-card class="box-card">
			<el-row :gutter="20">
				<el-col :span="7">
					<el-input placeholder="请输入内容" class="input-with-select" v-model="queryInfo.query" clearable @clear="getUserList(1)">
						<el-button slot="append" icon="el-icon-search" @click="getUserList(1)"></el-button>
					</el-input>
				</el-col>
				<el-col :span="3">
					<el-button type="primary" @click="dialogVisible = true">添加用户</el-button>
				</el-col>
			</el-row>
			<!-- 数据表格 -->
			<el-table :data="userList" stripe border style="width: 100%">
				<el-table-column type="index" label="序列">
				</el-table-column>
				<el-table-column prop="username" label="姓名" width="180">
				</el-table-column>
				<el-table-column prop="email" label="邮箱" width="180">
				</el-table-column>
				<el-table-column prop="mobile" label="电话">
				</el-table-column>
				<el-table-column prop="role_name" label="角色" width="180">
				</el-table-column>
				<el-table-column label="状态" width="180">
					<!-- 作用域插槽 -->
					<template slot-scope="scope">
						<el-switch v-model="scope.row.mg_state" @change="userStateChanged(scope.row)">
						</el-switch>
					</template>
				</el-table-column>
				<el-table-column label="操作">
					<template slot-scope="scope">
						<el-tooltip class="item" effect="dark" content="编辑用户" placement="top" :enterable="false">
							<el-button size="mini" round type="primary" icon="el-icon-edit" @click="getEditUser(scope.row)"></el-button>
						</el-tooltip>
						<el-tooltip class="item" effect="dark" content="删除用户" placement="top" :enterable="false">
							<el-button size="mini" round type="danger" icon="el-icon-delete" @click="delUser(scope.row.id)"></el-button>
						</el-tooltip>
						<el-tooltip class="item" effect="dark" content="分配角色" placement="top" :enterable="false">
							<el-button size="mini" round type="warning" icon="el-icon-setting"></el-button>
						</el-tooltip>
					</template>
				</el-table-column>
				<!-- 分页 -->
			</el-table>
			<div class="block">
				<el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="queryInfo.pagenum" :page-sizes="pageSizes" :page-size="queryInfo.pagesize" layout="total, sizes, prev, pager, next, jumper" :total="total">
				</el-pagination>
			</div>
			<!-- 添加用户对话框 -->
			<el-dialog title="添加用户信息" :visible.sync="dialogVisible">
				<el-form :model="userForm" :rules="rules" ref="userForm" label-width="70px">
					<el-form-item prop="username" label="用户名">
						<el-input v-model="userForm.username"></el-input>
					</el-form-item>
					<el-form-item prop="password" label="密码">
						<el-input type="password" v-model="userForm.password"></el-input>
					</el-form-item>
					<el-form-item prop="email" label="邮箱">
						<el-input v-model="userForm.email"></el-input>
					</el-form-item>
					<el-form-item prop="mobile" label="手机号">
						<el-input v-model="userForm.mobile"></el-input>
					</el-form-item>
				</el-form>
				<span slot="footer" class="dialog-footer">
					<el-button @click="closeDialog">取 消</el-button>
					<el-button type="primary" @click="onSubmit">确 定</el-button>
				</span>
			</el-dialog>
			<!-- 修改用户对话框 -->
			<el-dialog title="修改用户信息" :visible.sync="editdialogVisible">
				<el-form :model="editUserForm" :rules="editUserFormRules" ref="editUserForm" label-width="70px">
					<el-form-item prop="username" label="用户名">
						<el-input v-model="editUserForm.username" disabled></el-input>
					</el-form-item>
					<el-form-item prop="email" label="邮箱">
						<el-input v-model="editUserForm.email"></el-input>
					</el-form-item>
					<el-form-item prop="mobile" label="手机号">
						<el-input v-model="editUserForm.mobile"></el-input>
					</el-form-item>
				</el-form>
				<span slot="footer" class="dialog-footer">
					<el-button @click="closeEditDialog">取 消</el-button>
					<el-button type="primary" @click="onEditSubmit">确 定</el-button>
				</span>
			</el-dialog>
		</el-card>
	</div>
</template>

<script>
export default {
  data () {
    // 自定义邮箱规则
    var checkEmail = (rule, value, callback) => {
      const regEmail = /^\w+@\w+(\.\w+)+$/
      if (regEmail.test(value)) {
        // 合法邮箱
        return callback()
      }
      callback(new Error('请输入合法邮箱'))
    }
    // 自定义手机号规则
    var checkMobile = (rule, value, callback) => {
      const regMobile = /^1[34578]\d{9}$/
      if (regMobile.test(value)) {
        return callback()
      }
      // 返回一个错误提示
      callback(new Error('请输入合法的手机号码'))
    }
    return {
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 2
      },
      userList: [],
      total: 0,
      pageSizes: [2, 15, 20, 25],
      dialogVisible: false,
      editdialogVisible: false,
      userForm: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      editUserForm: {
        username: '',
        email: '',
        mobile: ''
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          {
            min: 2,
            max: 10,
            message: '用户名的长度在2～10个字',
            trigger: 'blur'
          }
        ],
        password: [
          { required: true, message: '请输入用户密码', trigger: 'blur' },
          {
            min: 6,
            max: 18,
            message: '用户密码的长度在6～18个字',
            trigger: 'blur'
          }
        ],
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { validator: checkEmail, trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: '请输入手机号码', trigger: 'blur' },
          { validator: checkMobile, trigger: 'blur' }
        ]
      },
      // 编辑用户表单验证
      editUserFormRules: {
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { validator: checkEmail, trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: '请输入手机号码', trigger: 'blur' },
          { validator: checkMobile, trigger: 'blur' }
        ]
      }
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    async getUserList (pagenum) {
      this.queryInfo.pagenum = pagenum || this.queryInfo.pagenum
      const { data: res } = await this.$http.get('users', { params: this.queryInfo })
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.userList = res.data.users
      this.total = res.data.total
    },
    handleSizeChange (pagesize) {
      this.queryInfo.pagesize = pagesize
      this.getUserList()
    },
    handleCurrentChange (pagenum) {
      this.queryInfo.pagenum = pagenum
      this.getUserList()
    },
    async userStateChanged (userinfo) {
      const { data: res } = await this.$http.put(`users/${userinfo.id}/state/${userinfo.mg_state}`)
      if (res.meta.status !== 200) {
        userinfo.mg_state = !userinfo.mg_state
        return this.$message.error(res.meta.msg)
      }
      this.$message.success(res.meta.msg)
    },
    async getEditUser (userinfo) {
      const { data: res } = await this.$http.get(`users/${userinfo.id}`)
      if (res.meta.status !== 200) {
        return this.$message.error(res.meta.msg)
      }
      this.editUserForm = res.data
      this.editdialogVisible = true
    },
    closeEditDialog () {
      console.log(this.$refs.editUserForm)
      this.editdialogVisible = false
      this.$refs.editUserForm.resetFields()
    },
    onEditSubmit () {
      this.$refs.editUserForm.validate(async (valid) => {
        if (!valid) return false
        const { data: res } = await this.$http.put(
          `users/${this.editUserForm.id}`, {
            email: this.editUserForm.email,
            mobile: this.editUserForm.mobile
          })
        console.log(res)
        if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
        this.$message.success(res.meta.msg)
        this.editdialogVisible = false
        this.getUserList()
      })
    },
    //  删除
    async delUser (id) {
      const confirmResult = await this.$confirm(
        '此操作将永久删除该用户, 是否继续?',
        '提示',
        {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }
      ).catch(err => err)
      // 点击确定 返回值为：confirm
      // 点击取消 返回值为： cancel
      if (confirmResult !== 'confirm') {
        return this.$message.info('已取消删除')
      }
      const { data: res } = await this.$http.delete(`users/${id}`)
      if (res.meta.status !== 200) {
        return this.$message.error(res.meta.msg)
      }
      this.$message.success(res.meta.msg)
      this.getUserList(1)
    },
    closeDialog () {
      this.dialogVisible = false
      console.log(this.$refs.userForm)
      this.$refs.userForm.resetFields()
    },
    onSubmit () {
      this.$refs.userForm.validate(async (valid) => {
        console.log(valid)
        if (!valid) return false
        const { data: res } = await this.$http.post('users', this.userForm)
        console.log(res)
        if (res.meta.status !== 201) return this.$message.error(res.meta.msg)
        this.$message.success(res.meta.msg)
        this.dialogVisible = false
        this.getUserList()
      })
    },
    handleClose (e) {
      console.log(e)
    }
  }
}
</script>

<style>
</style>
