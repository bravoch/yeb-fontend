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
      <el-button size="small" type="primary" icon="el-icon-plus" style="margin-left: 8px" @click="addJobLevel">新增
      </el-button>
    </div>

    <div style="margin-top: 10px">
      <el-table
          stripe
          border
          size="small"
          :data="jls"
          @selection-change="handleSelectionChange"
          style="width: 70%">

        <el-table-column
            type="selection"
            width="55">
        </el-table-column>

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
            <el-button size="small" @click="showEdit(scope.row)">编辑</el-button>
            <el-button type="danger" size="small" @click="deleteHandle(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <div>
      <el-button type="danger" size="small" :disabled="this.multipleSelection.length === 0" @click="deleteBatch"
                 style="margin-top: 10px">批量删除
      </el-button>
    </div>

    <div>
      <el-dialog
          title="编辑职称"
          :visible.sync="dialogVisible"
          width="50%">
        <table>
          <div>
            <tr>
              <td><span>职称名称</span></td>
              <td>
                <el-input size="small" v-model="updateJl.name" placeholder="请输出职称名称"
                          style="margin-left: 8px;width: 300px"></el-input>
              </td>
            </tr>
          </div>

          <div style="margin-top: 8px">
            <tr>
              <td><span>职称等级</span></td>
              <td>
                <el-select size="small" style="margin-left: 8px" v-model="updateJl.titleLevel" placeholder="请选择职称等级">
                  <el-option
                      v-for="item in titleLevels"
                      :key="item"
                      :label="item"
                      :value="item">
                  </el-option>
                </el-select>
              </td>
            </tr>
          </div>

          <div style="margin-top: 8px">
            <tr>
              <td><span>是否启用</span></td>
              <td>
                <el-switch style="margin-left: 8px"
                           v-model="updateJl.enabled"
                           active-color="#13ce66"
                           inactive-color="#ff4949"
                           active-text="已启用"
                           inactive-text="未启用">
                </el-switch>
              </td>
            </tr>
          </div>

        </table>

        <span slot="footer" class="dialog-footer">
      <el-button size="small" @click="dialogVisible = false">取 消</el-button>
      <el-button size="small" type="primary" @click="doUpdate">确 定</el-button>
    </span>
      </el-dialog>
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
      ],
      dialogVisible: false,
      updateJl: {
        name: '',
        titleLevel: '',
        enabled: '',
      },
      multipleSelection: [],
    }
  },
  mounted() {
    this.initJobLevel()
  },
  methods: {
    deleteBatch() {
      this.$confirm('此操作将永久删除' + this.multipleSelection.length + '条数据, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let ids = '?'
        this.multipleSelection.forEach(item => {
          ids += 'ids=' + item.id + '&'
        })
        this.deleteRequest('/system/basic/joblevel/' + ids).then(resp => {
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
    handleSelectionChange(val) {
      this.multipleSelection = val
    },
    doUpdate() {
      this.putRequest('/system/basic/joblevel/',
          {
            id: this.updateJl.id,
            name: this.updateJl.name,
            titleLevel: this.updateJl.titleLevel,
            enabled: this.updateJl.enabled
          })
          .then(resp => {
            if (resp) {
              this.initJobLevel()
              this.dialogVisible = false
            }
          })
    },
    showEdit(data) {
      Object.assign(this.updateJl, data)
      this.dialogVisible = true
    },
    deleteHandle(data) {
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
    addJobLevel() {
      if (this.jl.name && this.jl.titleLevel) {
        this.postRequest('/system/basic/joblevel/', this.jl).then(resp => {
          if (resp) {
            this.initJobLevel()
          }
        })
      } else {
        this.$message.error('字段不能为空')
      }
    },
    initJobLevel() {
      this.getRequest('/system/basic/joblevel/').then(resp => {
        if (resp) {
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