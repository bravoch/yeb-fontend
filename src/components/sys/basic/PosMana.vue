<template>
  <div>
    <div>
      <el-input size="small"
                suffix-icon="el-icon-plus"
                v-model="pos.name"
                class="addPosInput"
                placeholder="添加职位"
                @keydown.enter.native="addPosition">
      </el-input>
      <el-button icon="el-icon-plus" type="primary" size="small" @click="addPosition">添加</el-button>
    </div>

    <div class="tablePos">
      <el-table
          stripe
          border
          :data="posData"
          @selection-change="handleSelectionChange"
          style="width: 70%">
        <el-table-column
            type="selection"
            width="55">
        </el-table-column>
        <el-table-column
            prop="id"
            label="编号"
            width="55px">
        </el-table-column>
        <el-table-column
            prop="name"
            label="职位"
            width="120px">
        </el-table-column>
        <el-table-column
            prop="createDate"
            label="创建时间"
            width="200px">
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button
                size="mini"
                @click="showEditView(scope.$index, scope.row)">编辑
            </el-button>
            <el-button
                size="mini"
                type="danger"
                @click="handleDelete(scope.$index, scope.row)">删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <el-button size="small" type="danger" style="margin-top: 8px" :disabled="this.multipleSelection.length === 0" @click="deleteBatch">批量删除</el-button>

    <el-dialog
        title="编辑职位"
        :visible.sync="dialogVisible"
        width="30%">
      <div>
        <span>职位名称</span>
        <el-input v-model="updatePos.name" size="small" class="updatePosInput"></el-input>
      </div>

      <span slot="footer" class="dialog-footer">
      <el-button size="small" @click="dialogVisible = false">取 消</el-button>
      <el-button size="small" type="primary" @click="doUpdate">确 定</el-button>
      </span>
    </el-dialog>

  </div>
</template>

<script>



export default {
  name: "PosMana",
  data() {
    return {
      pos: {
        name: ''
      },
      posData: [],
      dialogVisible: false,
      updatePos: {
        name: ''
      },
      multipleSelection: []
    }
  },
  mounted() {
    this.initPositions()
  },
  methods: {
    deleteBatch(){
      this.$confirm('此操作将永久删除' + this.multipleSelection.length + '条数据, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let ids = '?'
        this.multipleSelection.forEach(item => {
          ids += 'ids=' + item.id + '&'
        })
        this.deleteRequest('/system/basic/pos/' + ids).then(resp => {
          if (resp) {
            this.initPositions()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    handleSelectionChange(val){
      this.multipleSelection = val
    },
    doUpdate() {
      this.putRequest('/system/basic/pos/', {id:this.updatePos.id, name:this.updatePos.name }).then(resp => {
        if (resp) {
          this.initPositions()
          this.dialogVisible = false
        }
      })
    },
    addPosition() {
      if (this.pos.name) {
        this.postRequest('/system/basic/pos/', this.pos).then(resp => {
          if (resp) {
            this.initPositions()
            this.pos.name = ''
          }
        })
      } else {
        this.$message.error('职位名称不能为空！')
      }
    },
    showEditView(index, data) {
      Object.assign(this.updatePos,data)
      this.dialogVisible = true
    },
    handleDelete(index, data) {
      this.$confirm('此操作将永久删除[' + data.name + ']该职位, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/system/basic/pos/' + data.id).then(resp => {
          if (resp) {
            this.initPositions()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    initPositions() {
      this.getRequest('/system/basic/pos/').then(resl => {
        this.posData = resl
      })
    }
  }
}
</script>

<style scoped>

.addPosInput {
  width: 300px;
  margin-right: 8px;
}

.tablePos {
  margin-top: 10px;
}

.updatePosInput {
  width: 200px;
  margin-left: 8px;
}

</style>