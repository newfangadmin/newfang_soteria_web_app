<template>
  <div class="navbar">
    <el-row :gutter="20">
      <el-col :span="2" class="logoContainer">
        <img class="logo" src="~@/assets/horizontal_white.png" />
      </el-col>
      <el-col :span="8" :offset="14">
        
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'navbar',
  data() {
    return {

    }
  },
  methods: {

  },
  mounted() {
    // eslint-disable-next-line no-console
  }
}
</script>

<style scoped>
  .navbar {
    height: 50px;
    overflow: hidden;
    position: relative;
    background: #222;
    color: #f2f2f2;
    border-bottom: 1px solid #e6e6e6;
  }

  .logo {
    height: 24px;
    margin: 12px;
    margin-left: 10px;
    color: #fff;
    font-weight: 600;
    line-height: 50px;
    font-size: 14px;
    vertical-align: middle;
  }
</style>