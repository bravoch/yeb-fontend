<template>
  <div>
    <div>
      <el-input size="small"
                placeholder="请输入职称"
                v-model="jl.name"
                suffix-icon="el-icon-plus"
                style="width: 300px;margin-right: 8px"></el-input>

      <el-select size="small" v-model="jl.titleLevel" placeholder="职称等级" clearable>
        <el-option
            v-for="item in titleLevels"
            :key="item"
            :label="item"
            :value="item">
        </el-option>
      </el-select>
      <el-button size="small" type="primary" icon="el-icon-plus" style="margin-left: 8px" @click="addJobLevel">新增</el-button>
    </div>

    <div style="margin-top: 10px">
      <el-table
          stripe
          border
          size="small"
          :data="jls"
          style="width: 70%">
        <el-table-column
            prop="id"
            label="编号"
            width="55">
        </el-table-column>
        <el-table-column
            prop="name"
            label="职称名称"
            width="150">
        </el-table-column>
        <el-table-column
            prop="titleLevel"
            label="职称等级"
            width="150">
        </el-table-column>
        <el-table-column
            prop="createDate"
            label="创建时间"
            width="150">
        </el-table-column>
        <el-table-column
            prop="enabled"
            label="是否启用"
            width="150">
          <template slot-scope="scope">
            <el-tag size="small" type="success" v-if="scope.row.enabled">已启用</el-tag>
            <el-tag size="small" type="danger" v-else>未启用</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            label="操作">
          <template slot-scope="scope">
            <el-button size="small">编辑</el-button>
            <el-button type="danger" size="small" @click="deleteHandle(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
import el from "element-ui/src/locale/lang/el";

export default {
  name: "JobMana",
  data() {
    return {
      jl: {
        name: '',
        titleLevel: ''
      },
      jls: [],
      titleLevels: [
        '正高级', '副高级', '中级', '初级', '员级'
      ]
    }
  },
  mounted() {
    this.initJobLevel()
  },
  methods: {
    deleteHandle(data){
      this.$confirm('此操作将永久删除[' + data.name + ']职称, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/system/basic/joblevel/' + data.id).then(resp => {
          if (resp) {
            this.initJobLevel()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    addJobLevel(){
      if (this.jl.name && this.jl.titleLevel){
        this.postRequest('/system/basic/joblevel/',this.jl).then(resp => {
          if (resp){
            this.initJobLevel()
          }
        })
      }else {
        this.$message.error('字段不能为空')
      }
    },
    initJobLevel(){
      this.getRequest('/system/basic/joblevel/').then(resp => {
        if (resp){
          this.jls = resp
          this.jl.name = ''
          this.jl.titleLevel = ''
        }
      })
    }
  }
}
</script>

<style scoped>

</style>