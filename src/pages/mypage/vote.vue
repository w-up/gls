<template>
  <div id="vote">
    <mt-header fixed title="投票">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="con-wrapper">
      <div class="vote_img">
        <img src="../../assets/img/6.jpg" />
      </div>
      <div class="vote_num">
        <div>
          <span>我的票数</span>
          <span>{{number}}票</span>
        </div>
        <div>
          <span>当前排名</span>
          <span>{{sort}}</span>
        </div>
      </div>
      <div class="vote_link">
        <h5>我的投票链接</h5>
        <div class="link">
          <span>{{url}}</span>
          <span v-clipboard:copy="url" v-clipboard:success="onCopy" v-clipboard:error="onError">分享</span>
        </div>
      </div>
      <div class="vote_con">
        <div class="vote_list" v-for="(item,index) in ranking" :key="index">
          <span>{{item.name}} {{item.phone}}</span>
          <span>{{item.number}}票</span>
          <span @click="subVote(item.id)">投票</span>
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
      ranking: [], //排名
      sort: [], //当前排名
      number: [], //我的票数
      url: "" //链接
    };
  },
  mounted: function() {
    let that = this;
    that.getRanding();
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },
    //新办谷联卡
    gotogul() {
      this.$router.push({
        path: "/addmember",
        query: {
          cadindex: 1
        }
      });
    },
    //获取我的投票
    getRanding() {
      let that = this;
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "adopt_randing/randingPage",
          method: "post",
          data: {
            token: localStorage.getItem("token")
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            //成功回调
            that.ranking = res.data.data.ranking;
            that.sort = res.data.data.sort;
            that.number = res.data.data.number;
            that.url = res.data.data.url;
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
    //投票
    subVote(id) {
      let that = this;
      Indicator.open({
        text: "提交中..."
      });
      that
        .$http({
          url: "adopt_randing/voteActive",
          method: "post",
          data: {
            token: localStorage.getItem("token"),
            id: id
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            //成功回调
            Toast(res.data.msg);
            that.getRanding();
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
      Toast({
        message: "链接复制成功，请粘贴到微信分享",
        position: "center",
        duration: 3000
      });
    },
    //复制成功
    onCopy() {
      // web走的成功
      let that = this;
      window.location.replace("weixin://");
      Toast({
        message: "链接复制成功，请粘贴到微信分享",
        position: "center",
        duration: 3000
      });
    }
  }
};
</script>

<style scoped="scoped">
#vote {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.con-wrapper {
  position: fixed;
  width: 100%;
  height: calc(100% - .8rem);
  overflow-x: hidden;
  overflow-y: scroll;
  top: .8rem;
}

.mint-header {
  position: relative;
  background: #ef6213;
}

.vote_img {
  width: 100%;
}

.vote_img img {
  width: 100%;
}

.vote_con {
  width: 100%;
  padding: 0 0.2rem;
}
.vote_num {
  display: flex;
  justify-content: space-around;
  padding: 0.2rem 0;
}
.vote_num > div {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.vote_num > div span:nth-child(2) {
  padding-top: 0.2rem;
  color: #ef6213;
}
.vote_link {
  width: 100%;
  padding: 0 0.2rem;
}
.vote_link h5 {
  font-weight: normal;
  font-size: 0.28rem;
}
.vote_link .link {
  padding: 0.2rem 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 0.26rem;
}
.vote_link .link span:nth-child(1) {
  width: 80%;
  word-wrap: break-word;
}
.vote_link .link span:nth-child(2) {
  color: #ef6213;
}
.vote_list {
  font-size: 0.28rem;
  display: flex;
  justify-content: space-between;
  padding: 0.2rem 0;
  align-items: center;
}

.vote_list span:nth-child(1) {
  width: 40%;
}
.vote_list span:nth-child(2) {
  color: #ef6213;
}
.vote_list span:nth-child(3) {
  color: #ef6213;
  padding: 0.08rem 0.2rem;
  border: 1px solid #ef6213;
  border-radius: 0.1rem;
}
</style>
