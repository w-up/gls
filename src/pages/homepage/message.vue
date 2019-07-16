<template>
  <div id="message">
    <mt-header fixed title="平台资讯">
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
        v-model="messageIndex"
      >
        <tab-item selected @on-item-click="setmessageIndex(0)">新闻</tab-item>
        <tab-item @on-item-click="setmessageIndex(1)">报道</tab-item>
        <tab-item @on-item-click="setmessageIndex(2)">公告</tab-item>
      </tab>
      <div class="scroll_div">
        <van-pull-refresh
          v-model="isLoading"
          pulling-text="下拉刷新"
          loosing-text="释放更新"
          loading-text="正在加载..."
          @refresh="onRefresh"
        >
          <div
            class="div"
            v-infinite-scroll="loadMore"
            infinite-scroll-disabled="loading"
            infinite-scroll-distance="10"
            infinite-scroll-immediate-check="false"
          >
            <div v-if="messageIndex==0" class="tab-swiper vux-center">
              <div class="news">
                <div
                  class="news_list"
                  @click="gotoNewDetail(messageitem)"
                  v-for="(messageitem,index) in messageList"
                  :key="index"
                >
                  <div class="news_img">
                    <img :src="messageitem.img" />
                  </div>
                  <div class="news_info">
                    <p>{{messageitem.title}}</p>
                  </div>
                </div>
              </div>
              <div class="activity_news">
                <h5>活动专区</h5>
                <!-- <load-more v-if="lif" :show-loading="load" tip="正在加载..."></load-more>
                <load-more v-if="nif" :show-loading="none" tip="没有更多数据了"></load-more>-->
                <div
                  class="news_list"
                  @click="gotoNewDetail(actitem)"
                  v-for="(actitem,index) in activityList"
                  :key="index"
                >
                  <div class="news_img">
                    <img :src="actitem.img" />
                  </div>
                  <div class="news_info">
                    <p>{{actitem.title}}</p>
                  </div>
                </div>
              </div>
            </div>
            <div v-if="messageIndex==1" class="tab-swiper vux-center">
              <div class="news">
                <div
                  class="news_list"
                  @click="gotoNewDetail(messageitem)"
                  v-for="(messageitem,index) in messageList"
                  :key="index"
                >
                  <div class="news_img">
                    <img :src="messageitem.img" />
                  </div>
                  <div class="news_info">
                    <p>{{messageitem.title}}</p>
                  </div>
                </div>
              </div>
            </div>
            <div v-if="messageIndex==2" class="tab-swiper vux-center">
              <div class="news">
                <div
                  class="news_list"
                  @click="gotoNewDetail(messageitem)"
                  v-for="(messageitem,index) in messageList"
                  :key="index"
                >
                  <div class="news_img">
                    <img :src="messageitem.img" />
                  </div>
                  <div class="news_info">
                    <p>{{messageitem.title}}</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <load-more v-if="lif" :show-loading="load" tip="正在加载..."></load-more>
          <load-more v-if="nif" :show-loading="none" tip="没有更多数据了"></load-more>
        </van-pull-refresh>
      </div>

      <!-- 	<load-more tip="加载更多"></load-more> -->
    </div>
  </div>
</template>

<script>
import { Indicator, Toast } from "mint-ui";
import { Tab, TabItem, LoadMore } from "vux";
export default {
  components: {
    Tab,
    TabItem,
    Indicator,
    Toast,
    LoadMore
  },
  data() {
    return {
      messageIndex: 0,
      pageindex: 1, // 第一页
      messageList: [], //新闻资讯
      activityList: [], //活动
      load: true, //加载图标显示
      none: false, //加载图标隐藏
      lif: true, //正在加载中 显示
      nif: false, //没有更多数据了 隐藏
      loading: false, //下拉刷新
      isLoading: false, //上拉加载更多
      messageTitle: []
    };
  },
  mounted: function() {
    let that = this;
    that.getMessageList();
    that.getActivityList();
  },
  activated() {
    this.$nextTick(() => {
      this.messageIndex = 0;
      this.pageindex = 1;
      this.lif = true;
      this.nif = false;
      this.loading = false;
      this.getMessageList(1);
    });
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },
    gotoNewDetail(item) {
      let that = this;
      window.sessionStorage.setItem("message_type", item.type);
      window.sessionStorage.setItem("news_id", item.id);
      that.$router.push({
        path: "/newsDetail",
        query:{
        	storeId:item.id
        }
      });
    },
    setmessageIndex(i) {
      let that = this;
      that.messageIndex = i;
      that.loading = false;
      that.nif = false;
      that.pageindex = 1;
      that.messageList = [];
      that.getMessageList(1);
    },
    // 下拉刷新
    onRefresh() {
      let that = this;
      that.isLoading = true;
      that.loading = false;
      that.nif = false;
      that.pageindex = 1;
      that.messageList = [];
      that.getMessageList(0);
    },
    //上拉加载更多
    loadMore() {
      let that = this;
      that.lif = true;
      that.pageindex++;
      that.getMessageList();
      that.getActivityList(); //活动
    },
    //获取新闻资讯
    getMessageList(i) {
      let that = this;
      if (i) {
        that.lif = true;
      }
      let typem = that.messageIndex == 0 ? 1 : that.messageIndex == 1 ? 2 : 3;
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "Subscribe/noticeList",
          method: "post",
          data: {
            type: typem,
            p: that.pageindex
          }
        })
        .then(function(res) {
          that.lif = false;
          that.isLoading = false;
          if (res.data.code == 0) {
            //成功回调
            if (res.data.data.list != "") {
              that.messageList = that.messageList.concat(res.data.data.list);
              console.log(that.messageList);
            } else {
              that.nif = true;
              that.loading = true;
            }
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
    //获取活动
    getActivityList(i) {
      let that = this;
      if (i) {
        that.lif = true;
      }
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "Subscribe/noticeList",
          method: "post",
          data: {
            type: 4
          }
        })
        .then(function(res) {
          that.lif = false;
          if (res.data.code == 0) {
            //成功回调
            if (res.data.data.list != "") {
              that.activityList = that.activityList.concat(res.data.data.list);
            } else {
              that.nif = true;
            }
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
#message {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.con-wrapper {
  position: fixed;
  width: 100%;
  height: calc(100% - 50px);
  overflow-x: hidden;
  overflow-y: scroll;
  top: 0.8rem;
}

.mint-header {
  background: #ef6213;
}

.scroll_div {
  width: 100%;
  height: 100%;
  width: 90%;
  margin: 0 auto;
  margin-top: 0.1rem;
}
.news {
  height: 100%;
}
.activity_news {
  margin-top: 0.3rem;
}

.news_list {
  margin: 0.3rem 0;
  display: flex;
  justify-content: space-between;
}

.news_list .news_img {
  width: 24%;
}

.news_list .news_img img {
  width: 100%;
}

.news_list .news_info {
  margin-left: 0.2rem;
  font-size: 0.26rem;
}
</style>
