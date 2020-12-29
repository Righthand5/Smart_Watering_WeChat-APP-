<template>
  <div>
    <div class="header">
      <div class = "header-title">请登录</div>
      <div class = "header-info">please Login Your Account</div>
    </div>
    <div class="body">
      <div class="login-form">
        <van-field
          placeholder = "请输入你的用户名"
          :value = "inputUserName"
          @change="onUserNameChange"
          />
        <van-field
          placeholder = "请输入你的密码"
          :value = "inputPassWord"
          @change="onPassWordChange"
        />
      </div>
        <van-button
        slot = "button"
        round
        block
        color="#3d7ef9"
        @click="onClick"
        >登入
        </van-button>
        <div class="other-option">
          <span>注册账户</span>
          <span style="margin: 0 3px">|</span>
          <span>忘记密码</span>
        </div>
        <div class="copyright-wrapper">
          <span class="copyright">Power to Xyx.</span>
        </div>
      </div>
  </div>
</template>

<script>


export default {

  data() {
    return {
      inputUserName:""
    }
  },
  methods:{
    onUserNameChange(event){
      var that = this
      that.$set(that,"inputUserName",event.mp.detail)
    },
    onPassWordChange(event){
      var that = this
      that.$set(that,"inputPassWord",event.mp.detail)
    },
    onClick(event){
      var that = this;
      console.log("登入按钮被点击了！")
    }

  }
}
</script>

<style lang="scss" scoped>
.header {
  height: 100px;
  padding: 25px 0;
  background-color: #3d7ef9;
  .header-title {
    font-size: 28px;
    font-weight: 500;
    color: #fff;
    margin-left: 20px;
  }
  .header-info {
    font-size: 14px;
    color: #fff;
    margin-left: 20px;
    }
  }
  .body{
    padding:20px;
    margin-top: -20px;
    border-radius:15px 15px 0 0;
    background-color: #fff;
    .login-form{
      margin-bottom: 30px;
    }
    .other-option{
      display: flex;
      justify-content: center;
      color:#9f9f9f
    }
    .copyright-wrapper{
      display: flex;
      justify-content: center ;
      .copyright{
        color:#d6d6d6;
        position: fixed;
        bottom: 50px;
      }
    }
}
</style>
