<template>
  <div id="farmdetail">
    <mt-header fixed title="红包明细">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="con-wrapper">
      <tab
        :line-width="2"
        bar-active-color="#ef6213"
        active-color="#ef6213"
        :scroll-threshold="5"
        v-model="redIndex"
      >
        <tab-item @click.native="navTap(0)">所领红包</tab-item>
        <tab-item @click.native="navTap(1)">实拆红包</tab-item>
      </tab>
      <div v-if="redIndex==0" class="detail_con">
        <div
          class="detail_list"
          v-for="(item,index) in haveList"
          :key="index"
          @click="gotoShare(item.id,item.count)"
        >
          <div class="detail_re">
            <span>{{item.name}}</span>
            <span>{{item.count}}个</span>
          </div>
          <div class="detail_time">{{item.time}}</div>
        </div>
      </div>
      <div v-if="redIndex==1" class="detail_con">
        <div class="detail_list" v-for="(item,index) in haveList" :key="index">
          <div class="detail_re">
            <span>{{item.name}}</span>
            <span>{{item.count}}个</span>
          </div>
          <div class="detail_time">{{item.time}}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Indicator, Toast } from "mint-ui";
import { Tab, TabItem } from "vux";

export default {
  components: {
    Tab,
    TabItem,
    Indicator,
    Toast
  },
  data() {
    return {
      redIndex: 0,
      haveList: [],
      type: "" //红包类型
    };
  },
  mounted: function() {
    let that = this;
    that.getHaveHongbao();
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },
    gotoShare(id, count) {
      this.$router.push({
        path: "/shareRed",
        query: {
          id: id,
          count: count
        }
      });
    },
    //选择相应的tab，初始化数据
    navTap(i) {
      let that = this;
      that.redIndex = i;
      that.haveList = [];
      that.getHaveHongbao();
    },
    //获取我的红包
    getHaveHongbao() {
      let that = this;
      // 类型：0所领红包，1实拆红包
      that.type = that.redIndex == 0 ? 0 : 1;
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "hong_bao/haveHongbao",
          method: "post",
          data: {
            token: localStorage.getItem("token"),
            type: that.type
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            //成功回调
            that.haveList = res.data.data;
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
#farmdetail {
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

#farmdetail .mint-tab-item-label {
  font-size: 0.3rem;
}

.mint-header {
  background: #ef6213;
}

.mint-navbar .mint-tab-item.is-selected {
  color: #ef6213;
  border-bottom: 2px solid #ef6213;
}

.detail_con {
  width: 90%;
  margin: 0 auto;
  margin-top: 0.1rem;
}

.detail_con .detail_list {
  border-bottom: 1px solid #e9e9e9;
  padding: 0 0.2rem;
  font-size: 0.26rem;
  padding: 0.2rem 0;
}

.detail_con .detail_list .nuum span:nth-child(2) {
  margin-right: 0.2rem;
}

.detail_con .detail_re {
  padding-bottom: 0.1rem;
  display: flex;
  justify-content: space-between;
}

.detail_con .detail_re span {
  color: #333;
}

.detail_con .detail_time {
  text-align: right;
  color: #777777;
}
</style>
