<template>
  <div>
    <el-container>
      <el-header class="homeHeader">
        <div class="title">云E办</div>
        <el-dropdown class="userInfo" @command="handleCommand">
          <span class="el-dropdown-link">
            <span class="userName">{{ user.name }}</span><i><img :src="user.userFace"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="userinfo">个人中心</el-dropdown-item>
            <el-dropdown-item command="setting">设置</el-dropdown-item>
            <el-dropdown-item command="logout">注销登录</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </el-header>
      <el-container>
        <el-aside style="width: 200px;overflow: hidden">
          <el-menu router unique-opened>
            <el-submenu :index="index+''" v-for="(item,index) in this.routes" :key='index' v-if="!item.hidden">
              <template slot="title">
                <i :class="item.iconCls" style="color: #1accff ;margin-right: 5px"></i>
                <spa>{{ item.name }}</spa>
              </template>
              <el-menu-item-group>
                <el-menu-item :index="children.path" v-for="(children,index) in item.children" :key="index">
                  {{ children.name }}
                </el-menu-item>
              </el-menu-item-group>
            </el-submenu>
          </el-menu>
        </el-aside>
        <el-main >
          <el-breadcrumb separator-class="el-icon-arrow-right" v-if="this.$router.currentRoute.path!== '/home'">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>{{ this.$router.currentRoute.name }}</el-breadcrumb-item>
          </el-breadcrumb>
          <div class="homeWelcome" v-if="this.$router.currentRoute.path === '/home'">
            欢迎来到云E办系统
          </div>
          <router-view class="homeRouteView"/>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
export default {
  name: "Home",
  methods: {
    handleCommand(command) {
      if (command === 'logout') {

        this.$confirm('此操作将注销登录, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          // 注销登录
          this.getRequest('/logout');
          // 清空用户信息
          window.sessionStorage.removeItem('tokenStr')
          window.sessionStorage.removeItem('user')
          // 清空菜单
          this.$store.commit('initRoutes', [])
          // 跳转到登录页
          this.$router.replace('/')
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消注销登录'
          });
        });

      }
    }
  },
  computed: {
    routes() {
      return this.$store.state.routes
    }
  },
  data() {
    return {
      user: JSON.parse(window.sessionStorage.getItem('user'))
    }
  }
}
</script>

<style scoped>

.homeHeader {
  background-color: #409eff;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin: auto 0px;
  box-sizing: border-box;
}

.homeHeader .title {
  font-size: 30px;
  font-family: 华文楷体;
  color: white;
}

.homeHeader .userInfo {
  cursor: pointer;
}

.el-dropdown-link img {
  width: 48px;
  height: 48px;
  border-radius: 24px;
  margin-left: 5px;
}

.userName {
  font-size: 18px;
  color: white;
}

.homeWelcome{
  text-align: center;
  font-size: 30px;
  font-family: 华文楷体;
  color: #409eff;
  padding-top: 50px;
}

.homeRouteView{
  margin-top: 10px;
}


</style>