<template>
  <div id="proDetail">
    <mt-header fixed title="预定记录">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="con-wrapper">
      <div class="comm_nav">
        <van-tabs v-model="index" animated swipeable>
          <van-tab :title="'已预订'">
            <div class="comm_de">
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
                  <div class="yipin_list" v-for="(item,index) in shopList1" :key="index">
                    <div class="yipin_img">
                      <img :src="item.img" />
                    </div>
                    <div class="yipin_con">
                      <div>
                        <div>
                          <h4 class="yipin_title">{{item.title}}</h4>
                          <p class="spend">
                            <span>
                              单价:
                              <span>{{item.spend}} 元</span>
                            </span>
                            <span>数量:{{item.number}}</span>
                          </p>
                          <h5 class="yipin_time">预售时间:{{item.time}}</h5>
                          <h5 class="yipin_time">出售时间:{{item.sell_time}}</h5>
                        </div>
                        <span class="yipin_btn">已预订</span>
                      </div>
                      <h5
                        class="yipin_time"
                      >地址:{{item.province + item.city + item.area + item.address_detail}}</h5>
                    </div>
                  </div>
                </div>
                <load-more v-if="lif" :show-loading="load" tip="正在加载..."></load-more>
                <load-more v-if="nif" :show-loading="none" tip="没有更多数据了"></load-more>
              </van-pull-refresh>
            </div>
          </van-tab>
          <van-tab :title="'待收货'">
            <div class="comm_de">
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
                    <div class="yipin_list" v-for="(item,index) in shopList2" :key="index">
                      <div class="yipin_img">
                        <img :src="item.img" />
                      </div>
                      <div class="yipin_con">
                        <div>
                          <div>
                            <h4 class="yipin_title">{{item.title}}</h4>
                            <p class="spend">
                              <span>
                                单价:
                                <span>{{item.spend}} 元</span>
                              </span>
                              <span>数量:{{item.number}}</span>
                            </p>
                            <h5 class="yipin_time">预售时间:{{item.time}}</h5>
                            <h5 class="yipin_time">出售时间:{{item.sell_time}}</h5>
                          </div>
                          <span class="yipin_btn yipin_btn_active" @click="confirm(item.id)">确认收货</span>
                        </div>
                        <h5
                          class="yipin_time"
                        >地址:{{item.province + item.city + item.area + item.address_detail}}</h5>
                      </div>
                    </div>
                  </div>
                  <load-more v-if="lif" :show-loading="load" tip="正在加载..."></load-more>
                  <load-more v-if="nif" :show-loading="none" tip="没有更多数据了"></load-more>
                </van-pull-refresh>
              </div>
            </div>
          </van-tab>
          <van-tab :title="'已收货'">
            <div class="comm_de">
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
                    <div class="yipin_list" v-for="(item,index) in shopList3" :key="index">
                      <div class="yipin_img">
                        <img :src="item.img" />
                      </div>
                      <div class="yipin_con">
                        <div>
                          <div>
                            <h4 class="yipin_title">{{item.title}}</h4>
                            <p class="spend">
                              <span>
                                单价:
                                <span>{{item.spend}} 元</span>
                              </span>
                              <span>数量:{{item.number}}</span>
                            </p>
                            <h5 class="yipin_time">预售时间:{{item.time}}</h5>
                            <h5 class="yipin_time">出售时间:{{item.sell_time}}</h5>
                          </div>
                          <span class="yipin_btn">已收货</span>
                        </div>
                        <h5
                          class="yipin_time"
                        >地址:{{item.province + item.city + item.area + item.address_detail}}</h5>
                      </div>
                    </div>
                  </div>
                  <load-more v-if="lif" :show-loading="load" tip="正在加载..."></load-more>
                  <load-more v-if="nif" :show-loading="none" tip="没有更多数据了"></load-more>
                </van-pull-refresh>
              </div>
            </div>
          </van-tab>
        </van-tabs>
      </div>
    </div>
  </div>
</template>

<script>
import { Tab, LoadMore, TabItem, InlineXNumber } from "vux";
import { Indicator, Toast } from "mint-ui";
export default {
  components: {
    InlineXNumber,
    Toast,
    Tab,
    TabItem,
    LoadMore,
    Indicator
  },
  data() {
    return {
      index: 0,
      pageindex: 1,
      onFetching: false,
      bottomCount: 20,
      shopId: this.$route.query.storeId, //商品id
      shopList1: "", //列表
      shopList2: "", //列表
      shopList3: "", //列表
      load: false, //加载图标显示
      none: false, //加载图标隐藏
      lif: false, //正在加载中 显示
      nif: false, //没有更多数据了 隐藏
      loading: false, //下拉刷新
      isLoading: false //上拉加载更多
    };
  },
  mounted() {
    let that = this;
    that.getShopInfo(1);
    that.getShopInfo(2);
    that.getShopInfo(3);
  },
  methods: {
    back() {
      this.$router.push({
        path: "/yipinyidi"
      });
    },
    //获取 商品详情
    getShopInfo(status, p) {
      let that = this;
      Indicator.open({
        spinnerType: "fading-circle"
      });
      that
        .$http({
          url: "subscribe/subscribeLog",
          method: "post",
          data: {
            token: localStorage.getItem("token"),
            p: p,
            status: status
          }
        })
        .then(function(res) {
					that.lif = false;
					that.loading = false;
          that.isLoading = false;
					Indicator.close();
          if (res.data.code == 0) {
            //成功回调
            if (status == 1) {
              that.shopList1 = res.data.data.list;
            } else if (status == 2) {
              that.shopList2 = res.data.data.list;
            } else if (status == 3) {
              that.shopList3 = res.data.data.list;
            }
          } else {
            //失败
            Toast(res.data.msg);
          }
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
    //下拉刷新
    onRefresh() {
      let that = this;
      that.isLoading = true;
      that.loading = false;
      that.nif = false;
      that.pageindex = 1;
      that.commnetList = [];
      that.getShopInfo(that.index+1, 1);
    },
    //上拉加载更多
    loadMore() {
      let that = this;
      that.lif = true;
      that.pageindex++;
      that.getShopInfo(0);
    },
    confirm(id) {
      let that = this;
      Indicator.open({
        spinnerType: "fading-circle"
      });
      that
        .$http({
          url: "subscribe/setSubscribeReceive",
          method: "post",
          data: {
            token: localStorage.getItem("token"),
            id: id
          }
        })
        .then(function(res) {
					Indicator.close();
          if (res.data.code == 0) {
            //成功回调
          } else {
            //失败
            Toast(res.data.msg);
          }
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
#proDetail {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.con-wrapper {
  position: fixed;
  width: 100%;
  height: calc(100% - 0.8rem);
  overflow-x: hidden;
  overflow-y: scroll;
  top: 0.8rem;
}
.comm_nav {
  width: 100%;
  height: 100%;
}

.comd_img {
  width: 100%;
  height: auto;
}

.comm_de {
  width: 100%;
  padding: 0 0.2rem;
}

.yipin_list {
  margin: 0.2rem 0;
  display: flex;
  display: -webkit-flex;
  flex-direction: row;
  -webkit-flex-direction: row;
  justify-content: space-between;
  -webkit-justify-content: space-between;
  align-items: center;
  -webkit-align-items: center;
}
.yipin_img {
  width: 2rem;
  height: 2rem;
}
.yipin_img img {
  width: 2rem;
  height: 2rem;
}
.yipin_con {
  margin-left: 0.2rem;
  width: 100%;
  display: flex;
  display: -webkit-flex;
  flex-direction: column;
  -webkit-flex-direction: column;
  justify-content: space-between;
  -webkit-justify-content: space-between;
}
.yipin_con > div {
  display: flex;
  display: -webkit-flex;
  flex-direction: row;
  -webkit-flex-direction: row;
  justify-content: space-between;
  -webkit-justify-content: space-between;
  align-items: center;
  -webkit-align-items: center;
}
.yipin_con > div > div {
  display: flex;
  display: -webkit-flex;
  flex-direction: column;
  -webkit-flex-direction: column;
  justify-content: space-between;
  -webkit-justify-content: space-between;
}

.yipin_title {
  font-size: 0.28rem;
  color: #333;
}
.yipin_time {
  font-weight: normal;
  color: #555;
  line-height: 0.4rem;
  font-size: 0.24rem;
}
.yipin_btn {
  width: 1rem;
  height: 1rem;
  line-height: 1rem;
  font-size: 0.24rem;
  background-color: rgba(217, 217, 217, 1);
  color: rgba(255, 255, 255, 1);
  border-radius: 50%;
  text-align: center;
}
.yipin_btn_active {
  background-color: #ef6213;
  padding: 0.2rem;
  line-height: 0.3rem;
}
.spend span {
  font-size: 0.24rem;
  color: #666;
}

.spend span > span {
  padding-right: 0.2rem;
  font-size: 0.24rem;
  color: #ef6213;
}
</style>
