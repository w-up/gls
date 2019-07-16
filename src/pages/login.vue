<template>
  <div class="login">
    <mt-header fixed title="登录">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="logo">
      <img src="../assets/img/logo.jpg" />
    </div>
    <div class="login_con">
      <div class="login_info">
        <span class="iconfont icon-tabwode"></span>
        <input v-model="account" type="text" name="account" placeholder="请输入手机号" />
      </div>
      <div class="login_info">
        <span class="iconfont icon-tabsuo"></span>
        <input v-model="password" type="password" name="pass" placeholder="请输入登录密码" />
      </div>
      <div class="login_btn">
        <button @click="login">登录</button>
      </div>
      <div class="login_zhuce">
        <div class="zhuce">
          <span>还没有账号？</span>
          <span @click="gotoReg">立即注册</span>
        </div>
        <span @click="gotoFoget">忘记密码</span>
      </div>
    </div>
  </div>
</template>

<script>
import { Toast } from "mint-ui";
export default {
  components: {},
  data() {
    return {
      account: "",
      password: "",
      hint: true // 提示
    };
  },
  watch: {
    // password(val, oldVal) {
    // 	if (/^[A-Za-z0-9]+{6,}$/.test(val)) {
    // 		this.hint = true;
    // 	}
    // }
  },
  methods: {
    back() {
      if (sessionStorage.getItem("clickExit") == "true") {
        this.$router.push({
          path: "/"
        });
      } else {
        this.$router.go(-1);
      }
    },
    //登录
    login() {
      let that = this;
      let account = that.account;
      let password = that.password;
      if (!account || account == null) {
        Toast("请输入手机号码");
      } else if (
        account.trim().length != 11 ||
        !/^1[3|4|5|6|7|8|9]\d{9}$/.test(account)
      ) {
        Toast("手机号格式错误");
      } else if (!password || password == null) {
        Toast("密码不能为空");
      } else {
        that.$http
          .post("Register/userLogin", {
            account: account,
            password: password
          })
          .then(function(res) {
            let code = res.data.code;
            let msg = res.data.msg;
            let data = res.data.data;
            if (code == 0) {
              localStorage.setItem("token", data);
              localStorage.setItem("account", account);
              if (sessionStorage.getItem("clickExit") == "true") {
                that.$router.push({
                  path: "/"
                });
              } else {
                that.$router.go(-1);
              }
              sessionStorage.setItem("clickExit", true);
              Toast("登录成功");
            } else {
              Toast(msg);
            }
          })
          .catch(function(err) {
            Toast({
              message: "网络连接失败",
              position: "bottom",
              duration: 5000
            });
          });
      }
    },

    gotoFoget() {
      let that = this;
      that.$router.push({
        path: "/fogetpass"
      });
    },
    gotoReg() {
      let that = this;
      that.$router.push({
        path: "/register"
      });
    }
  }
};
</script>

<style scoped="scoped">
.header {
  width: 100%;
  height: 0.8rem;
  background: #ef6213;
  text-align: center;
  line-height: 0.8rem;
  color: #fff;
}
.logo {
  height: 3.4rem;
  width: 150%;
  margin-left: -25%;
  margin-top: 0.8rem;
  background: #ef6213;
  border-radius: 0 0 50% 50%;
  text-align: center;
}
.logo img {
  width: 1.6rem;
  border-radius: 50%;
  margin-top: 0.5rem;
}
.login_con {
  width: 90%;
  margin: 0 auto;
  margin-top: 1rem;
}
.login_info {
  width: 100%;
  height: 0.8rem;
  border: 1px solid #d6d6d6;
  border-radius: 0.1rem;
  line-height: 0.8rem;
  display: flex;
  align-items: center;
  margin-bottom: 0.5rem;
}
.login_info span {
  padding: 0 0.2rem;
  font-size: 0.4rem;
  color: #888;
}
.login_info input {
  flex: 1;
  height: 0.7rem;
  border: none;
}
.login_btn {
  margin: 0 auto;
}
.login_btn button {
  width: 100%;
  height: 0.8rem;
  background: #ef6213;
  border: none;
  color: #fff;
  border-radius: 0.1rem;
}
.login_zhuce {
  margin-top: 0.2rem;
  display: flex;
  justify-content: space-between;
  font-size: 0.26rem;
}
.login_zhuce .zhuce span:nth-child(2) {
  color: #ef6213;
}
</style>
