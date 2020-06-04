<template>
	<div class="login_container">
		<div class="login_box">
			<!-- logo -->
			<div class="header_login">
				<img src="../assets/logo.png" alt="">
			</div>
			<!-- 表单 -->
			<div class="login_form">
				<!-- <el-form :model="loginForm" label-width="80px">
					<el-form-item label="账号" prop="">
						<el-input v-model="loginForm.username"></el-input>
					</el-form-item>
					<el-form-item label="密码" prop="">
						<el-input v-model="loginForm.password"></el-input>
					</el-form-item>
					<el-form-item label="" prop="">
						<el-button type="primary" @click="onSubmit">登录</el-button>
						<el-button>重置</el-button>
					</el-form-item>
				</el-form> -->
				<el-form :model="loginForm" :rules="rules" ref="loginForm">
					<el-form-item prop="username">
						<el-input v-model="loginForm.username" prefix-icon="el-icon-user-solid"></el-input>
					</el-form-item>
					<el-form-item prop="password">
						<el-input type="password" v-model="loginForm.password" prefix-icon="icon iconfont icon-password"></el-input>
					</el-form-item>
					<el-form-item prop="">
						<el-button type="primary" @click="onSubmit">登录</el-button>
						<el-button @click="resetFilds">重置</el-button>
					</el-form-item>
				</el-form>
			</div>
		</div>
	</div>
</template>

<script>
export default {
  name: 'login',
  data () {
    return {
      loginForm: {
        username: 'admin',
        password: '123456'
      },
      // 验证规则
      rules: {
        username: [
          { required: true, message: '请输入活动名称', trigger: 'blur' },
          { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入活动名称', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    onSubmit () {
      this.$refs.loginForm.validate(async (valid) => {
        if (!valid) return false
        const { data: res } = await this.$http.post('login', this.loginForm)
        console.log(res)

        if (res.meta.status !== 200) { return this.$message.error(res.meta.msg) }
        this.$message.success(res.meta.msg)
        window.sessionStorage.setItem('token', res.data.token)
        this.$router.push('/home')
      })
    },
    resetFilds () {
      console.log(this.$refs.loginForm)
      this.$refs.loginForm.resetFields()
    }
  },
  mounted () {

  }
}
</script>

<style scoped lang="less">
.login_container {
	background-color: #2b4b6b;
	height: 100%;
	.login_box {
		width: 450px;
		height: 360px;
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		border-radius: 5px;
		background-color: #fff;
		font-size: 20px;
		.header_login {
			position: absolute;
			width: 150px;
			height: 150px;
			border: 1px solid #eee;
			left: 50%;
			transform: translate(-50%, -50%);
			border-radius: 50%;
			padding: 10px;
			box-shadow: 0 0 10px #ddd;
			background-color: #fff;
			img {
				width: 100%;
				height: 100%;
				border-radius: 50%;
				background-color: #eee;
			}
		}
		.login_form {
			position: absolute;
			bottom: 50px;
			width: 100%;
			padding: 0 20px;
			box-sizing: border-box;
			text-align: center;
		}
	}
}
</style>
