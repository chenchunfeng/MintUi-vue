<template>
  <div class="register">
    <mt-header title="注册" style="background:#26a2ff;color:#fff;">
      <router-link to="/login" slot="left">
        <mt-button icon="back"></mt-button>
      </router-link>
    </mt-header>
    <div class="formItem">
      <mt-field label=""  v-model="phoneNum" :state="phonStat" disableClear placeholder="手机号"></mt-field>
      <p><mt-field label="" placeholder="验证码" :state="telCodeStat" disableClear type="telCode" v-model="telCode"></mt-field>
        <span class="sendCode" @click="sendPhoCode" >发送验证码</span>
      </p>
      <mt-field label="" placeholder="请输入密码" :state="pasStat" type="password" disableClear v-model="password"></mt-field>
      <div class="subBut" @click="registerSub">完成</div>
      <div class="more">
        <router-link :to="{'path':'/login'}" class="forgetPass">已有账号去登录？去 <i>登陆</i></router-link>
      </div>
    </div>
  </div>
</template>
<script>
  import logApi from '../api/users'
  import { MessageBox } from 'mint-ui';

  export default {
    name: 'register',
    data () {
      return {
        phoneNum: '',
        telCode: '',
        mail: '',
        password: '',
        phonStat: '',
        telCodeStat: '',
        pasStat:''
      }
    },
    methods:{
      // 提交注册
      registerSub: function() {
        if (this.phonStat == 'success' && this.telCodeStat == 'success' && this.pasStat == 'success' ) {
          logApi.register({mobile:this.phoneNum, password:this.password,sms_code:this.telCode},(ret, err) => {
            if (err) {
              alert('注册失败！请稍后重试！')
            }else{
              this.$router.push('/login')
            }
          })
        } else {
          MessageBox('提示', '请输入正确的信息后重试！');
        }
      },
      // 发送验证码
      sendPhoCode: function(){
        if(this.phoneNum === '' || this.phonStat != 'success'){
          MessageBox('提示', '请输入正确的手机号后重试！');
          return false
        }
        logApi.sendCode({mobile:this.phoneNum},(ret, err) => {
          if (err) {
            //alert('用户名或密码错误！请稍后重试！')
          }else{
            this.telCode = ret.headers.debug
          }
        })
      }
    },
    watch:{
      phoneNum(val, oldval){
        if(/^1[34578]\d{9}$/.test(val)){
          this.phonStat = 'success'
        }else{
          this.phonStat = 'warning'
        }
      },
      telCode(val, oldval){
          if(/^[A-Za-z0-9]{6}$/.test(val)){
            this.telCodeStat = 'success'
          }else{
            this.telCodeStat = 'warning'
          }
        },
      password(val, oldval){
        if(/^[A-Za-z0-9]{6,18}$/.test(val)){
          this.pasStat = 'success'
        }else{
          this.pasStat = 'warning'
        }
      }
    },
    mounted:function(){
    },
    components: {
    }
  }
</script>

<style lang="scss">
  @import "../assets/baseScss";
  .register{
    .formItem{
      font-size: 18px;
      margin: 0 30px;
      p{position: relative}
      .mint-cell{
        background: transparent;
      }
      .mint-cell-wrapper{
        background: transparent;
        padding: 0;
        border-bottom: solid 1px $cl14;
      }
      .subBut{
        background: $cl0;
        color:$cl1;
        text-align: center;
        line-height: 40px;
        margin: 20px 0;
      }
      .more{
        position: relative;
        width: 100%;
        left: 0px;
        top: 40px;
        text-align: center;
        i{
          color: $cl0;
        }
        .forgetPass{
          color:$cl9;
        }
      }
      .sendCode{position: absolute;right: 0px;top:6px;display: inline-block;font-size: 14px;color:$cl0;border:solid 1px $cl0;padding: 5px;}
    }
  }
</style>
