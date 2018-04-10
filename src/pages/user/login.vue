<template>
  <div class="login-body">
    <div class="loginWarp"
         @keyup.enter="submit_form">
      <div class="login-title">
        <img src="./images/logo.png"/>
      </div>
      <div class="login-form">
        <el-form ref="form" :model="form" :rules="rules" label-width="0">
          <el-form-item prop="username" class="login-item">
            <el-input v-model="form.username" placeholder="请输入账户名：" class="form-input" :autofocus="true"></el-input>
          </el-form-item>
         <!-- <el-form-item prop="email" class="login-item">
            <el-input type="email" v-model="form.email" placeholder="请输入邮箱地址:" class="form-input"></el-input>
          </el-form-item> -->
          <el-form-item prop="password" class="login-item">
            <el-input type="password" v-model="form.password" placeholder="请输入账户密码：" class="form-input"></el-input>
          </el-form-item>
          <el-form-item class="login-item">
            <el-button size="large" icon="check" class="form-submit" @click="submit_form"></el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>
<script type="text/javascript">
  import {mapActions} from 'vuex'
  import {port_user, port_code} from 'common/port_uri'
  import {SET_USER_INFO} from 'store/actions/type'
  import {API_HOST} from '../../util/config'

  export default{
    data(){
      return {
        form: {
          username: null,
          password: null,
          email:null
        },
        rules: {
          username: [{required: true, message: '请输入账户名！', trigger: 'blur'}],
          password: [{required: true, message: '请输入账户密码！', trigger: 'blur'}]
        },
        //请求时的loading效果
        load_data: false
      }
    },
    methods: {
      ...mapActions({
        set_user_info: SET_USER_INFO
      }),
      //提交
      submit_form() {
        this.$refs.form.validate((valid) => {
          if (!valid) return false
          const user_data ={
              identifier:this.form.username,
              password:this.form.password,
          }
          console.log(this.form)
          const that = this
         // 登录提交
          this.$axios.post(API_HOST+"/auth/local",user_data).then((res)=>{
                // console.log(res.data)
                     that.$message.success("登录成功")
                     that.load_data =false
                    this.set_user_info({
                       user: res.data.user,
                       login: true,
                  })
                   localStorage.setItem("token",res.data.jwt)
                //    console.log(localStorage.getItem("token"))
                   setTimeout(that.$router.history.push({path: '/'}), 500)
                    console.log(this.$router.history)
                
          }).catch(({message})=>{
                that.$message.success("请输入正确的用户或者密码")
          })
           
        // 注册
        //   this.$axios.post("http://localhost:1337/auth/local/register",user_data).then((res)=>{
        //         console.log(res.data)
        //   }).catch(function(error){
		// 		console.log(error);
        //   })
          
        //   this.$fetch.api_user.login(this.form)
        //     .then(({data, msg}) => {
        //       this.set_user_info({
        //         user: data,
        //         login: true
        //       })
        //       console.log(msg,data)
        //       this.$message.success(msg)
        //       setTimeout(this.$router.push({path: '/'}), 500)
        //     })
        //     .catch(({code}) => {
        //       this.load_data = false
        //       if (code === port_code.error) {
        //         this.$notify.info({
        //           title: '温馨提示',
        //           message: '账号和密码都为：admin'
        //         })
        //       }
        //     })
        })
      }
    }
  }
</script>
<style lang="scss" type="text/scss" rel="stylesheet/scss">
  .login-body {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-image: url(./images/login_bg.jpg);
    background-repeat: no-repeat;
    background-position: center;
    background-size: cover;
    .loginWarp {
      width: 300px;
      padding: 25px 15px;
      margin: 100px auto;
      background-color: #fff;
      border-radius: 5px;
      .login-title {
        margin-bottom: 25px;
        text-align: center;
      }
      .login-item {
        .el-input__inner {
          margin: 0 !important;
        }
      }
      .form-input {
        input {
          margin-bottom: 15px;
          font-size: 12px;
          height: 40px;
          border: 1px solid #eaeaec;
          background: #eaeaec;
          border-radius: 5px;
          color: #555;
        }
      }
      .form-submit {
        width: 100%;
        color: #fff;
        border-color: #fdd856;
        background: #fdd856;
        &:active, &:hover {
          border-color: #fdd856;
          background: #fdd856;
        }
      }
    }
  }
</style>
