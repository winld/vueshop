<template>
	<el-container class="container">
		<el-header>
			<div class="logo-wrap">
				<img src="../assets/logo.png" alt="" />
				<p class="header-title">电商系统</p>
			</div>
			<el-button @click="logout">退出</el-button>
		</el-header>
		<el-container>
			<!-- 左侧栏 -->
			<el-aside :width="isCollapse?'64px':'200px'">
				<div class="toggle-button" @click="toggleCollapse">|||</div>
				<el-menu :default-active="curentPath" background-color="#333744" text-color="#fff" active-text-color="#409EFF" :unique-opened="true"
            :collapse="isCollapse" :collapse-transition="false"
            router>
					<el-submenu :index="'/'+item.path" v-for="item in menuList" :key="item.id">
						<template slot="title">
							<i :class="icons[item.id]"></i>
							<span>{{item.authName}}</span>
						</template>
						<el-menu-item :index="'/'+subItem.path" v-for="subItem in item.children" :key="subItem.id" @click="saveNavState('/'+subItem.path)">
							<template slot="title">
								<i class="el-icon-menu"></i>
								<span index="1-4-1">{{subItem.authName}}</span>
							</template>
						</el-menu-item>
					</el-submenu>
				</el-menu>
			</el-aside>
			<el-main>
				<router-view></router-view>
			</el-main>
		</el-container>
	</el-container>
</template>

<script>
export default {
  data () {
    return {
      menuList: [],
      icons: {
        125: 'iconfont icon-account-fill',
        103: 'iconfont icon-quanxian ',
        101: 'iconfont icon-shangpin',
        102: 'iconfont icon-dingdan',
        145: 'iconfont icon-zhandianshujutongji'
      },
      isCollapse: false,
      curentPath: ''
    }
  },
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    async getMenuList () {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menuList = res.data
    },
    // 点击切换菜单折叠
    toggleCollapse () {
      this.isCollapse = !this.isCollapse
    },
    //  保存链接激活状态
    saveNavState (curentPath) {
      window.sessionStorage.setItem('curentPath', curentPath)
      this.curentPath = curentPath
    }
  },
  created () {
    this.getMenuList()
    this.curentPath = window.sessionStorage.getItem('curentPath')
  }

}
</script>

<style lang="less" scope>
.container {
	height: 100%;
	.el-header {
		display: flex;
		background-color: #373d41;
		justify-content: space-between;
		align-items: center;
		padding: 0 20px 0 0;
		.logo-wrap {
			height: 100%;
			display: flex;
			img {
				height: 100%;
			}
			.header-title {
				color: white;
				font-size: 20px;
			}
		}
	}
	.el-aside {
		background-color: #333744;
		.toggle-button {
			background-color: #4a5064;
			font-size: 10px;
			color: white;
			text-align: center;
			cursor: pointer;
			line-height: 24px;
			letter-spacing: 0.3em;
		}
		.el-menu {
			border-right: none;
		}
	}
	.el-main {
		background-color: #f7f7f7;
	}
	.iconfont {
		padding-right: 5px;
	}
}
</style>
