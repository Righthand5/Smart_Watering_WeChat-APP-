<template>
  <div>
    <div class="header">
      <div v-if="isLogin">
        <div class = "header-title">请登录</div>
        <div class = "header-info">please Login Your Account</div>
      </div>
      <div v-else>
        <div class = "header-title">请注册</div>
        <div class = "header-info">please Register Your Account</div>
      </div>
    </div>
    <div class="body">
      <div class="login-form">
        <van-field
          placeholder = "请输入你的用户名"
          :value = "inputUserName"
          @change="onUserNameChange"
          />
        <van-field
          type="password"
          placeholder = "请输入你的密码"
          :value = "inputPassWord"
          @change="onPassWordChange"
        />
        <div v-if="!isLogin">
          <van-field
            placeholder = "请输入你的联系方式"
            :value = "inputContect"
            @change="onContectChange"
          />
        </div>
      </div>
        <van-button
        slot = "button"
        round
        block
        color="#3d7ef9"
        @click="onClick"
        >{{isLogin?'登入':'注册'}}
        </van-button>
        <div class="other-option">
          <div @click="onOptionClick">
            <span>{{isLogin?'注册账户':'登入账户'}}</span>
          </div>
          <span style="margin: 0 3px">|</span>
          <div @click="onForgetClick">
            <span>忘记密码</span>
          </div>
        </div>
        <div class="copyright-wrapper">
          <span class="copyright">Power to Xyx.</span>
        </div>
      <van-dialog
        use-slot
        title="找回密码校验"
        :show="showFindPW"
        show-cancel-button
        transition="fade"
        @confirm="onFindPWConfirm"
        @cancel="onFindPWCancel"
      >
        <van-field
          label="手机号"
          title-width="60px"
          placeholder = "请输入你的联系方式"
          :value = "inputContect"
          @change="onContectChange"
        />
      </van-dialog>
      <van-dialog
        use-slot
        title="重置密码"
        :show="showRestPW"
        show-cancel-button
        transition="fade"
        @confirm="onResetPWConfirm"
        @cancel="onResetPWCancel"
      >
        <van-field
          label="用户名"
          title-width="60px"
          :value = "inputUserName"
          readonly
        />
        <van-field
          label="新密码"
          type = "password"
          title-width="60px"
          placeholder = "请输入你的新密码"
          :value = "inputPassWord"
          @change="onPassWordChange"
        />
      </van-dialog>
      <van-toast id="van-toast"/>
      </div>
  </div>
</template>
<script>
import Toast from "../../../dist/wx/vant-weapp/dist/toast/toast";
export default {
  data() {
    return {
      isLogin:true,
      inputUserName:"",
      inputPassWord:"",
      inputContect:"",
      showFindPW:false,
      showRestPW:false
    }
  },
  methods:{
    onContectChange(event){
      var that = this
      that.$set(that,"inputContect",event.mp.detail)
    },
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
      Toast.loading({
        duration:0,//持续展示Toast
        forbidClick:true,//不允许操作被打断
        message:that.isLogin?"登入中...":"注册中..."
      })
      setTimeout(()=>{
        if(!that.isLogin){
          wx.setStorage({
            key:"user",
            data:{
              username:that.inputUserName,
              password:that.inputPassWord,
              contect:that.inputContect,
            },
            success(res){
              console.log(res)
              console.log("注册成功!")
              Toast.success('注册成功')
            },
            fail(res){
              console.log(res)
              console.log('注册失败!')
              Toast.success('注册失败!')
            }
          })
        }else{
          wx.getStorage({
            key: 'user',
            success (res) {
              console.log(res.data)
              if(that.inputUserName === res.data.username && that.inputPassWord=== res.data.password){
                Toast.success('登入成功')
                setTimeout(()=>{
                  wx.switchTab({
                    url: '/pages/index/main'
                  })
                },500)
              }else {
                Toast.fail('用户名或密码错误')
              }
            },
            fail(res){
              console.log(res)
              Toast.fail('数据库暂无注册用户')
            }
          })
        }
      },1000)
    },
    onOptionClick(event){
      var that = this
      console.log(that)
      that.isLogin = !that.isLogin;
      console.log(`当前处于${that.isLogin}`)
    },
    onForgetClick(event){
      var that = this
      that.showFindPW = true

    },
    onFindPWConfirm(event){
      var that = this;
      wx.getStorage({
        key: 'user',
        success (res) {
          console.log(res.data)
          if(that.inputContect === res.data.contect){
            that.inputUserName = res.data.username
            that.showRestPW = true
          }else {
            Toast.fail('无用户信息')
            that.inputContect=""
          }
        },
        fail(res){
          console.log(res)
          Toast.fail('数据库暂无注册用户')
          that.inputContect=""
        }
      })
    },
    onResetPWConfirm(event){
      var that = this;
      wx.setStorage({
        key:"user",
        data:{
          username:that.inputUserName,
          password:that.inputPassWord,
          contect:that.inputContect,
        },
        success(res){
          console.log(res)
          console.log("密码重置成功!")
          Toast.success('密码重置成功')
        },
        fail(res){
          console.log(res)
          console.log('密码重置失败!')
          Toast.success('密码重置失败!')
        }
      })
    },
    onResetPWCancel(event){
      var that = this
      that.showRestPW = false;
      that.inputUserName = "";
      that.inputPassWord = "";
      that.inputContect = "";

    },
    onFindPWCancel(event){
      var that = this;
      that.showFindPW = false;
      that.inputContect = "";
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
      margin-top: 30px;
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
