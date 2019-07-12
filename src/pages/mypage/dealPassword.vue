<template>
  <div id="dealPassword">
    <mt-header fixed title="交易密码">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="con-wrapper">
      <div class="rech_con">
        <div class="rech_list">
          <span>交易密码：</span>
          <input type="password" v-model="payment_password" placeholder="请输入至少6位数字和字母组合" />
        </div>
        <div class="rech_list">
          <span>确认密码：</span>
          <input type="password" v-model="repayment_password" placeholder="请再次输入您的交易密码" />
        </div>
        <mt-button type="default" @click="setDealPass">确认</mt-button>
      </div>
    </div>
  </div>
</template>

<script>
import { Indicator, Toast } from "mint-ui";

export default {
  components: {
    Indicator,
    Toast
  },
  data() {
    return {
      payment_password: "", //交易密码
      repayment_password: "" //确认密码
    };
  },
  mounted: function() {
    let that = this;
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },

    //设置交易密码
    setDealPass() {
      let that = this;
      if (!/^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{6,}$/.test(that.payment_password)) {
        Toast("密码输入有误");
      } else if (that.payment_password !== that.repayment_password) {
        Toast("两次密码不一致");
      } else {
        Indicator.open({
          text: "提交中..."
        });
        that
          .$http({
            url: "Personal/addPwd",
            method: "post",
            data: {
              token: window.sessionStorage.getItem("token"),
              payment_password: that.payment_password,
              repayment_password: that.repayment_password
            }
          })
          .then(function(res) {
            if (res.data.code == 0) {
              //成功回调
              Toast(res.data.msg);
            } else {
              //失败
              Toast(res.data.msg);
            }
            Indicator.close();
          })
          .catch(function(error) {
            Indicator.close();
            Toast({
              message: "网络连接失败",
              position: "bottom",
              duration: 5000
            });
          });
      }
    }
  }
};
</script>

<style scoped="scoped">
#dealPassword {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.con-wrapper {
  position: fixed;
  width: 100%;
  height: calc(100% - 40px);
  overflow-x: hidden;
  overflow-y: scroll;
  top: 40px;
}

.mint-header {
  background: #ef6213;
}

.mint-navbar .mint-tab-item.is-selected {
  color: #ef6213;
  border-bottom: 2px solid #ef6213;
}

.rech_con {
  width: 90%;
  margin: 0 auto;
  margin-top: 0.5rem;
}
.rech_con p {
  font-size: .24rem;
  color: #888;
  line-height: 1.8;
  text-align: right;
}
.rech_con .rech_list {
  display: flex;
  align-items: center;
  padding: 0.2rem 0;
}
.rech_con .rech_list span {
  width: 26%;
  color: #333;
  font-size: 0.28rem;
}
.rech_con .rech_list input {
  flex: 1;
  height: 0.7rem;
  border: 1px solid #c8c8c8;
  border-radius: 0.1rem;
  padding-left: 0.1rem;
}
.rech_con .mint-button {
  position: relative;
  width: 6rem;
  left: 50%;
  margin-left: -3rem;
  font-size: 0.3rem;
  height: 0.8rem;
  color: #fff;
  margin-top: 0.3rem;
  border-radius: 0.2rem;
  background: #ef6213;
}
</style>
