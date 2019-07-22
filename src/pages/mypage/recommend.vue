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
        <div class="link">
          <p>我的推广链接</p>
          <div>
            <p>{{invitation.url}}</p>
            <span
              v-clipboard:copy="invitation.url"
              v-clipboard:success="onCopy"
              v-clipboard:error="onError"
            >复制</span>
          </div>
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
    },
    //复制失败
    onError() {
      // 移动端走的失败
      // alert(navigator.userAgent);
      if (!/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
        if (/ baiduboxapp/i.test(navigator.userAgent)) {
          window.location.replace(
            "bdbox://utils?action=sendIntent&minver=7.4&params=%7B%22intent%22%3A%22weixin://%23wechat_redirect%23Intent%3Bend%22%7D"
          );
        } else {
          window.location.replace("weixin://");
        }
      } else {
        window.location.replace("weixin://");
      }
      Toast({
        message: "复制成功",
        position: "center",
        duration: 3000
      });
    },
    //复制成功
    onCopy() {
      // web走的成功
      // 邀请
      let that = this;
      if (!/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
        if (/ baiduboxapp/i.test(navigator.userAgent)) {
          window.location.replace(
            "bdbox://utils?action=sendIntent&minver=7.4&params=%7B%22intent%22%3A%22weixin://%23wechat_redirect%23Intent%3Bend%22%7D"
          );
        } else {
          window.location.replace("weixin://");
        }
      } else {
        window.location.replace("weixin://");
      }
      Toast({
        message: "复制成功",
        position: "center",
        duration: 3000
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
  width: 100%;
  padding: 0 0.2rem;
}

.black_list {
  font-size: 0.28rem;
  padding-top: 0.2rem;
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
.link {
  color: #555;
  padding-top: 0.2rem;
  font-size: 0.28rem;
}
.link > div {
  display: flex;
  display: -webkit-flex;
  justify-content: space-between;
  -webkit-justify-content: space-between;
}
.link > div > p {
  width: 80%;
  word-wrap: break-word;
  font-size: 0.28rem;
}
.link > div > span {
  color: #ffbe00;
  font-size: 0.28rem;
}
</style>
