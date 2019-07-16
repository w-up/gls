<template>
  <div id="cover">
    <mt-header fixed title="推广有礼">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="con-wrapper">
      <div class="black_con">
        <div class="black_list">
          <span>我的区域</span>
          <span>{{invitation.area}}</span>
        </div>
        <div class="black_list">
          <span>我的邀请码</span>
          <span>{{invitation.invitation_code}}</span>
        </div>
        <div class="black_list">
          <span>我的二维码</span>
          <img :src="invitation.invitation_img" />
        </div>
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
      invitation: [] //推广有礼
    };
  },
  mounted: function() {
    let that = this;
    that.getInvitation();
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },
    //获取推广有礼
    getInvitation() {
      let that = this;
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "Personal/invitation",
          method: "post",
          data: {
            token: localStorage.getItem("token")
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            //成功回调
            that.invitation = res.data.data;
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
};
</script>

<style scoped="scoped">
#cover {
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
  position: relative;
  background: #ef6213;
}

#cover .mint-header p {
  position: absolute;
  right: 0.3rem;
  top: 0.26rem;
  width: 0.44rem;
}

.black_con {
  width: 90%;
  margin: 0 auto;
  margin-top: 0.3rem;
}

.black_list {
  font-size: 0.28rem;
  padding: 0.1rem 0;
  color: #555;
  display: flex;
}

.black_list span:nth-child(1) {
  display: inline-block;
  width: 26%;
}
.black_list img {
  width: 50%;
  height: 50%;
}
</style>
