<template>
  <div class="container vel-flex">
    <div class="form-wrap">
      <h1 class="form-title">登录</h1>
      <el-form :model="loginForm" ref="loginForm" class="login-form">
        <el-form-item label="昵称" prop="name">
          <el-input v-model="loginForm.nickName" placeholder="请输入昵称"></el-input>
        </el-form-item>
        <div class="flex-form">
          <el-form-item label="头像背景色">
            <el-color-picker
              v-model="loginForm.defaultColor"
              show-alpha
              :predefine="predefineColors">
            </el-color-picker>
          </el-form-item>
          <div class="preview-wrap">
            <div class="preview-avatar vel-flex" :style="{background:avatarBgColor}">
              <p class="preview-name overflow-txt">{{loginForm.nickName}}</p>
            </div>
          </div>
        </div>
        <el-form-item>
          <el-button type="primary" @click="onSubmit" class="login-btn">确认</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data () {
    return {
      loginForm: {
        nickName: '',
        defaultColor: 'rgba(255, 165, 0, 0.6)'
      },
      predefineColors: [
        '#ff4500',
        '#ff8c00',
        '#ffd700',
        '#90ee90',
        '#00ced1',
        '#1e90ff',
        '#c71585',
        'rgba(255, 69, 0, 0.68)',
        'rgb(255, 120, 0)',
        'hsv(51, 100, 98)',
        'hsva(120, 40, 94, 0.5)',
        'hsl(181, 100%, 37%)',
        'hsla(209, 100%, 56%, 0.73)',
        '#c7158577'
      ]
    }
  },
  computed: {
    avatarBgColor () {
      return this.loginForm.defaultColor
    }
  },
  methods: {
    onSubmit (e) {
      this.$router.push({name: 'Main'})
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .container{
    width: 100vw;
    height: 100vh;
    position: relative;
  }
  .form-wrap{
    background: #fff;
    padding: 40px 120px;
    box-sizing: border-box;
    border-radius: 6px;
    box-shadow:0 1px 10px 1px rgba(0,0,0,0.1);
    .form-title{
      text-align: center;
      font-weight: bold;
    }
  }
  .login-form{
    width: 240px;
  }
  .flex-form{
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .preview-avatar{
    width: 100px;
    height: 30px;
    border-radius: 6px;
    position: relative;
    padding: 0 10px;
    box-sizing: border-box;
    &::before{
      content: '头像预览';
      position: absolute;
      left: 0;
      top: -43px;
      font-size: 14px;
      color: #606266;
      line-height: 40px;
      box-sizing: border-box;
    }
    .preview-name{
      width: 100%;
      color: #fff;
      text-align: center;
    }
  }
  .login-btn{
    width: 100%;
  }
</style>
