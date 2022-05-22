<template>
  <div>
    <div style="width: 500px">
      <el-input
          placeholder="输入部门名称进行搜索"
          v-model="filterText">
      </el-input>

      <el-tree
          :data="department"
          :props="defaultProps"
          :expand-on-click-node="false"
          :filter-node-method="filterNode"
          ref="tree">

       <span class="custom-tree-node" slot-scope="{ node, data }"
             style="display: flex;justify-content: space-between;width: 100%">
        <span>{{ data.name }}</span>
        <span>
          <el-button
              class="depBtn"
              type="primary"
              size="mini"
              @click="() => showAddDep(data)">
            添加部门
          </el-button>
          <el-button
              class="depBtn"
              type="danger"
              size="mini"
              @click="() => deleteDep(data)">
            删除部门
          </el-button>
        </span>
      </span>
      </el-tree>
    </div>
    <el-dialog
        title="添加部门"
        :visible.sync="dialogVisible"
        width="50%">
      <div>
        <table>
          <tr>
            <td><span>上级部门</span></td>
            <div style="margin-bottom: 6px">
              <td>
                <el-tag type="primary" style="margin-left: 8px">{{ pname }}</el-tag>
              </td>
            </div>
          </tr>

          <tr>
            <td><span>部门名称</span></td>
            <td>
              <el-input v-model="dep.name" placeholder="请输入部门名称" size="small" style="margin-left: 8px"></el-input>
            </td>
          </tr>
        </table>
      </div>
      <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="doAppDep">确 定</el-button>
  </span>
    </el-dialog>
  </div>
</template>

<script>
import el from "element-ui/src/locale/lang/el";

export default {
  name: "DepMana",
  data() {
    return {
      filterText: '',
      department: [],
      defaultProps: {
        label: 'name',
        children: 'children'
      },
      dialogVisible: false,
      dep: {
        name: '',
        parentId: -1
      },
      pname: ''
    }
  },
  watch: {
    filterText(val) {
      this.$refs.tree.filter(val)
    }
  },
  mounted() {
    this.initDep()
  },
  methods: {
    removeDepFromDeps(parentDep,deps,did){
      for (let i = 0;i < deps.length;i++){
        let dep = deps[i]
        if (dep.id === did){
          deps.splice(i,1)
          if (deps.length === 0){
            parentDep.isParent = false
          }
          return;
        }else {
          this.removeDepFromDeps(dep,dep.children,did)
        }
      }
    },
    initDeps() {
      this.dep = {
        name: '',
        parentId: -1
      }
      this.pname = ''
    },
    initAdd2Add(deps, dep) {
      for (let i = 0; i < deps.length; i++) {
        let d = deps[i]
        if (d.id === dep.parentId) {
          d.children = d.children.concat(dep)
          if (d.children.length > 0){
            d.isParent = true
          }
          return;
        } else {
          this.initAdd2Add(d.children, dep)
        }
      }
    },
    doAppDep() {
      this.postRequest('/system/basic/department/', this.dep).then(resp => {
        if (resp) {
          this.initAdd2Add(this.department, resp.obj)
          this.dialogVisible = false
          this.initDeps()
        }
      })
    },
    showAddDep(data) {
      this.dep.parentId = data.id
      this.pname = data.name
      this.dialogVisible = true
    },
    deleteDep(data) {
      if (data.isParent){
       this.$message.error('父部门删除失败')
      }else {
        this.$confirm('此操作将永久删除['+data.name+']该部门, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.deleteRequest('/system/basic/department/'+data.id).then(resp => {
            if (resp){
              this.removeDepFromDeps(null,this.department,data.id)
            }
          })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      }
    },
    initDep() {
      this.getRequest('/system/basic/department/').then(resp => {
        if (resp) {
          this.department = resp
        }
      })
    },
    filterNode(value, data) {
      if (!value) return true;
      return data.name.indexOf(value) !== -1;
    }
  }
}
</script>

<style scoped>

.depBtn {
  padding: 2px;
}

</style>