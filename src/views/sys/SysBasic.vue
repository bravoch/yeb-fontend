<template>
  <div>
    <el-tabs v-model="activeName" type="card">
      <el-tab-pane label="部门管理" name="DepMana"><DepMana></DepMana></el-tab-pane>
      <el-tab-pane label="职位管理" name="PosMana"><PosMana></PosMana></el-tab-pane>
      <el-tab-pane label="职称管理" name="JobMana"><JobMana></JobMana></el-tab-pane>
      <el-tab-pane label="奖惩规则" name="EcMana"><EcMana></EcMana></el-tab-pane>
      <el-tab-pane label="权限组" name="PermissMana"><PermissMana></PermissMana></el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>

const DepMana = () => import('../../components/sys/basic/DepMana')
const EcMana = () => import('../../components/sys/basic/EcMana')
const JobMana = () => import('../../components/sys/basic/JobMana')
const PermissMana = () => import('../../components/sys/basic/PermissMana')
const PosMana = () => import('../../components/sys/basic/PosMana')

export default {
  name: "SysBasic",
  data(){
    return{
      activeName: 'DepMana'
    }
  },
  components: {
    DepMana,
    EcMana,
    JobMana,
    PermissMana,
    PosMana
  }
}
</script>

<style scoped>

</style>