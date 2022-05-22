<template>
  <div>
    <div style="display: flex;justify-content: center;margin-top: 15px">
      <el-input v-model="keyword" prefix-icon="el-icon-search" placeholder="通过用户名搜索用户"
                style="width: 400px;margin-right: 10px"></el-input>
      <el-button type="primary" icon="el-icon-search" @click="doSearch">搜索</el-button>
    </div>
    <div class="admin-container">
      <el-card class="box-card" v-for="(admin,index) in admins" :key="index">
        <div slot="header" class="clearfix">
          <span>{{ admin.name }}</span>
          <el-button style="float: right; padding: 3px 0;color: red" type="text" icon="el-icon-delete" @click="dodelete(admin)"></el-button>
        </div>
        <div>
          <div style="width: 100%; display: flex; justify-content: center">
            <img :src="admin.userFace" :alt="admin.name" :title="admin.name" class="userface-img">
          </div>
          <div class="userinfo-container">
            <div>用户名：{{ admin.name }}</div>
            <div>手机号码：{{ admin.phone }}</div>
            <div>电话号码：{{ admin.telephone }}</div>
            <div>地址：{{ admin.address }}</div>
            <div>用户状态：
              <el-switch
                  v-model="admin.enabled"
                  active-color="#13ce66"
                  inactive-color="#ff4949"
                  @change="dochange(admin)"
                  active-text="启用"
                  inactive-text="禁用">
              </el-switch>
            </div>
            <div>
              用户角色：
              <el-tag style="margin-right: 4px" type="success" v-for="(role,index) in admin.roles" :key="index">
                {{ role.nameZh }}
              </el-tag>
              <el-button type="text" icon="el-icon-more"></el-button>
            </div>
            <div>备注：{{ admin.remark }}</div>
          </div>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script>
export default {
  name: "SysAdmin",
  data() {
    return {
      admins: [],
      keyword: ''
    }
  },
  mounted() {
    this.initAdmins()
  },
  methods: {
    dochange(admin){
      this.putRequest('/system/admin/',admin).then(resp => {
        if (resp){
          this.initAdmins()
        }
      })
    },
    dodelete(admin){
      this.$confirm('此操作将永久删除['+admin.name+']操作员, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/system/admin/'+admin.id).then(resp => {
          if (resp){
            this.initAdmins()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    doSearch() {
      this.initAdmins()
    },
    initAdmins() {
      this.getRequest('/system/admin/?keyword=' + this.keyword).then(resp => {
        if (resp) {
          this.admins = resp
        }
      })
    }
  }
}
</script>

<style>
.box-card {
  width: 360px;
  margin-bottom: 20px;
}

.admin-container {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  margin-top: 10px;
}

.userface-img {
  width: 72px;
  height: 72px;
  border-radius: 72px;
}

.userinfo-container {
  font-size: 12px;
  color: #409eff;
}

</style>