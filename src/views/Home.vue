<template>
  <div class="homepage">
    <div class="login-container">
      <img src="../assets/628e2b1228cfb_800.png" class="logo-img" />
      <el-form
        :model="formData"
        ref="ruleForm"
        label-width="100px"
        class="demo-ruleForm"
        label-position="top"
      >
        <el-form-item label="用户名" prop="name">
          <el-input
            v-model="formData.name"
            placeholder="请输入用户名"
          ></el-input>
        </el-form-item>
        <el-form-item label="用户id" prop="id">
          <el-input v-model="formData.id" placeholder="请输入用户Id"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input
            v-model="formData.password"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button size="large" @click="submit" class="login-btn"
            >登录</el-button
          >
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      formData: {
        id: "",
        name: "",
        password: "",
      },
    };
  },
  methods: {
    submit() {
      if (this.formData.name == "") {
        this.$message("请输入用户名");
        return;
      }

      if (this.formData.password == "") {
        this.$message("请输入密码");
        return;
      }

      if (this.formData.id == "") {
        this.$message("请输入密码");
        return;
      }

      this.$router.push({ name: "pannels", params: { ...this.formData } });
    },
  },
};
</script>

<style scoped>
.homepage {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.login-container {
  position: relative;
  width: 384px;
  background-color: white;
  padding: 60px;
  border-radius: 16px;
  background: #fbd5dd;
  box-sizing: border-box;
}
.logo-img {
  position: absolute;
  width: 250px;
  height: 165px;
  top: 0;
  right: 0;
}
v-deep .el-form-item__label {
  color: #3d3d3d !important;
}
.login-btn {
  width: 100%;
  background-color: #f3a4b4;
  color: #fff;
  margin-top: 10px;
}
.el-form-item {
  margin-bottom: 10px;
}
</style>
