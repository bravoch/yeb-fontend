<template>
  <div>
    <div class="permissCss">
      <el-input size="small" placeholder="请输入角色英文名" v-model="role.name">
        <template slot="prepend">ROLE_</template>
      </el-input>

      <el-input size="small" placeholder="请输入角色中文名" v-model="role.nameZh" @keydown.enter.native="addRoles">
      </el-input>

      <el-button size="small" type="primary" icon="el-icon-plus" @click="addRoles">添加角色</el-button>
    </div>

    <div class="permissCss2">
      <el-collapse v-model="activeName" accordion @change="change">
        <el-collapse-item :title="item.nameZh" :name="item.id" v-for="(item,index) in roles" :key="index">

          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>可访问资源</span>
              <el-button style="float: right; padding: 3px 0;color: #ff0000" type="text" @click="deleteRole(item)" icon="el-icon-delete"></el-button>
            </div>

            <div>
              <el-tree
                  show-checkbox
                  ref="tree"
                  node-key="id"
                  :key="index"
                  :default-checked-keys="allSelectedMenus"
                  :data="allMenus"
                  :props="defaultProps">
              </el-tree>

              <div style="display: flex;justify-content: flex-end">
                <el-button size="mini" @click="cancelUpdate">取消修改</el-button>
                <el-button size="mini" type="primary" @click="doUpdate(item.id,index)">确认修改</el-button>
              </div>
            </div>

          </el-card>

        </el-collapse-item>
      </el-collapse>
    </div>
  </div>
</template>

<script>
export default {
  name: "PermissMana",
  data() {
    return {
      role: {
        name: '',
        nameZh: ''
      },
      roles: [],
      allMenus: [],
      defaultProps: {
        label: 'name',
        children: 'children'
      },
      allSelectedMenus: [],
      activeName: -1
    }
  },
  mounted() {
    this.initPermiss()
  },
  methods: {
    deleteRole(item){
      this.$confirm('此操作将永久删除' + item.nameZh + '角色, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/system/basic/premiss/role/'+item.id).then(resp => {
          if (resp){
            this.initPermiss()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    addRoles(){
      if (this.role.name && this.role.nameZh){
        this.postRequest('/system/basic/premiss/role',this.role).then(resp => {
          if (resp){
            this.initPermiss()
            this.role.name = ''
            this.role.nameZh = ''
          }
        })
      }else {
        this.$message.error("所有字段不能为空")
      }
    },
    cancelUpdate(){
      this.activeName = -1
    },
    doUpdate(rid,index){
      let tree = this.$refs.tree[index]
      let selectedKyes = tree.getCheckedKeys(true)
      let url = '/system/basic/premiss/roleMenu/?rid=' + rid
      selectedKyes.forEach(key => {
        url += '&mids=' + key
      })
      this.getRequest(url).then(resp => {
        if (resp){
          this.initAllMenus()
          this.initSelectedMenus(rid)
          this.activeName = -1
        }
      })
    },
    change(rid){
      if (rid){
        this.initAllMenus()
        this.initSelectedMenus(rid)
      }
    },
    initSelectedMenus(rid){
      this.getRequest('/system/basic/premiss/mid/'+rid).then(resp => {
        if (resp){
          this.allSelectedMenus = resp
        }
      })
    },
    initAllMenus(){
      this.getRequest('/system/basic/premiss/menus').then(resp => {
        if (resp){
          this.allMenus = resp
        }
      })
    },
    initPermiss(){
      this.getRequest('/system/basic/premiss/').then(resp => {
        if (resp){
          this.roles = resp
        }
      })
    }
  }
}
</script>

<style>
.permissCss {
  display: flex;
  width: 700px;
  justify-content: flex-start;

}

.permissCss .el-input {
  margin-right: 8px;
  width: 300px;

}

.permissCss2{
  width: 700px;
  margin-top: 10px;
}

</style>