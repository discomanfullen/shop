<template>
  <div class="container">
    <div class="login-box">
      <!-- 头像 -->
      <div class="login-head">
        <img src="../assets/logo.png" alt="">
      </div>
      <!-- 表单 -->
      <el-form class="login-form" ref="loginRef" :model="loginForm" :rules="rules">
        <el-form-item prop="username">
          <el-input v-model="loginForm.username" prefix-icon="iconfont icon-user" v-on:keyup.enter.native="login"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="loginForm.password" prefix-icon="iconfont icon-3702mima" v-on:keyup.enter.native="login"></el-input>
        </el-form-item>
        <el-form-item class="btns">
          <el-button type="primary" v-on:click="login">登录</el-button>
          <el-button @click="reset">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      // 表单数据
      loginForm: {
        username: 'admin',
        password: '123456'
      },
      // 输入框验证规则
      rules: {
        username: [
          { required: true, message: '用户名为必填项', trigger: 'blur' },
          { min: 2, max: 8, message: '用户名长度在2~8位', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '密码为必填项', trigger: 'blur' },
          { min: 3, max: 8, message: '用户名长度在3~8位', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    // 登录事件
    login: function () {
      this.$refs.loginRef.validate(async valide => {
        if (!valide) return false
        const { data: { data: { token }, meta: { msg, status } } } = await this.$axios.post('login', this.loginForm)
        if (status === 200) {
          this.$message.success(msg)
          // console.log(token)
          window.sessionStorage.setItem('token', token)
          this.$router.push('/home')
        } else this.$message.error(msg)
      })
    },
    // 重置事件
    reset: function () {
      this.$refs.loginRef.resetFields()
    }
  }
}
</script>

<style lang="less" scoped>
  .container {
    background-color: #2b5b6b;
    height: 100%;

    .login-box {
      height: 400px;
      width: 600px;
      background-color: #fff;
      position: relative;
      top: 50%;
      left: 50%;
      transform: translate(-50%,-50%);
      border-radius: 10px;

      .login-head {
        width: 100px;
        height: 100px;
        background-color: #fff;
        padding: 10px;
        border: 1px solid #eee;
        border-radius: 50%;
        box-shadow: 0 0 10px #ddd;
        position: absolute;
        left: 50%;
        transform: translate(-50%,-50%);

        img {
          width: 100%;
          height: 100%;
          border-radius: 50%;
          background-color: #eee;
        }
      }
      .login-form {
        position: absolute;
        bottom: 0;
        width: 100%;
        padding: 10px;
        box-sizing: border-box;

        .btns {
          display: flex;
          justify-content: flex-end;
          margin-top: 100px;
        }
      }
    }
  }
</style>
