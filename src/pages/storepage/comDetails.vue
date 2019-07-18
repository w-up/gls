<template>
  <div id="comdetails">
    <mt-header fixed :title="name">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
      <a id="tore-right" slot="right">
        <svg
          t="1562572508084"
          class="icon shop_cart"
          @click="gotoCar"
          viewBox="0 0 1024 1024"
          version="1.1"
          xmlns="http://www.w3.org/2000/svg"
          p-id="2723"
          width="200"
          height="200"
        >
          <path
            d="M347.136 783.36q19.456 0 36.864 7.168t30.72 19.968 20.48 30.208 7.168 36.864-7.168 36.864-20.48 30.208-30.72 20.48-36.864 7.68q-20.48 0-37.376-7.68t-30.208-20.48-20.48-30.208-7.168-36.864 7.168-36.864 20.48-30.208 30.208-19.968 37.376-7.168zM773.12 785.408q19.456 0 37.376 7.168t30.72 19.968 20.48 30.208 7.68 36.864-7.68 36.864-20.48 30.208-30.72 20.48-37.376 7.68-36.864-7.68-30.208-20.48-20.48-30.208-7.68-36.864 7.68-36.864 20.48-30.208 30.208-19.968 36.864-7.168zM945.152 203.776q28.672 0 44.544 7.68t22.528 18.944 6.144 24.064-3.584 22.016-12.8 37.888-22.016 62.976-24.064 68.096-17.92 53.248q-13.312 40.96-33.792 56.832t-50.176 15.872l-34.816 0-66.56 0-87.04 0-95.232 0-253.952 0 15.36 92.16 516.096 0q49.152 0 49.152 41.984 0 20.48-9.728 35.328t-38.4 14.848l-49.152 0-95.232 0-117.76 0-119.808 0-98.304 0-56.32 0q-20.48 0-34.304-9.216t-23.04-24.064-14.848-32.256-8.704-32.768q-1.024-6.144-5.632-29.696t-11.264-58.88-14.848-78.848-16.384-87.552q-19.456-103.424-44.032-230.4l-76.8 0q-15.36 0-25.6-7.68t-16.896-18.432-9.216-23.04-2.56-22.528q0-20.48 13.824-33.792t37.376-13.312l22.528 0 20.48 0 25.6 0 34.816 0q20.48 0 32.768 6.144t19.456 15.36 10.24 19.456 5.12 17.408q2.048 8.192 4.096 23.04t4.096 30.208q3.072 18.432 6.144 38.912l700.416 0z"
            fill="#ffffff"
            p-id="2724"
          />
        </svg>
        <svg
          t="1562572794722"
          class="icon shop_cart"
          @click="gotoMine"
          viewBox="0 0 1024 1024"
          version="1.1"
          xmlns="http://www.w3.org/2000/svg"
          p-id="4402"
          width="200"
          height="200"
        >
          <path
            d="M512 64h248.9v248.9c0 137.5-111.4 248.9-248.9 248.9S263.1 450.4 263.1 312.9 374.5 64 512 64zM288 611.6h448c96.2 0 174.2 78 174.2 174.2v24.9c0 82.5-66.9 149.3-149.3 149.3H263.1c-82.5 0-149.3-66.9-149.3-149.3v-24.9c-0.1-96.2 77.9-174.2 174.2-174.2z"
            fill="#ffffff"
            p-id="4403"
          />
        </svg>
        <!-- <mt-button class="button-style" icon="aa iconfont icon-tabgouwuche2" @click="gotoCar"></mt-button>
        <mt-button class="button-2 button-style" icon="aa iconfont icon-tabwode" @click="gotoMine"></mt-button>-->
      </a>
    </mt-header>
    <div class="con-wrapper">
      <div class="comd_img">
        <mt-swipe :auto="4000">
          <mt-swipe-item v-for="(item,index) in shopinfo.show_img" :key="index">
            <img :src="item" />
          </mt-swipe-item>
        </mt-swipe>
        <!-- <img :src="shopinfo.show_img" /> -->
      </div>
      <div class="comd_con">
        <div class="comd_info">
          <h4>
            <span>{{shopinfo.price}}元红包+{{shopinfo.integral}}谷分</span>
            <inline-x-number :min="1" v-model="num"></inline-x-number>
          </h4>
          <h5>{{shopinfo.name}}</h5>
          <div class="specific" v-show="shopTypeList.length != 0">
            <p>规格</p>
            <div class="com_ch">
              <span
                :class="item.title == shopType?'active_span':''"
                v-for="item in shopTypeList"
                :key="item.title"
                @click="chooseTypeFun(item.title)"
              >{{item.title}}</span>
            </div>
          </div>
          <h6>商家地址：{{shopinfo.address}}</h6>
        </div>
      </div>
      <div class="comm_nav">
        <van-tabs v-model="index" animated>
          <van-tab :title="'详情'">
            <div v-show="index==0" class="comm_de" v-html="shopinfo.details_img">
              <!-- <img :src="shopinfo.details_img" /> -->
            </div>
          </van-tab>
          <van-tab :title="'评价('+count+')'">
            <div v-show="index==1" class="comm_de">
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
                    <div class="comment comd_con">
                      <div class="box2">
                        <div
                          class="comment_list"
                          v-for="(comItem,listId) in commnetList"
                          :key="listId"
                        >
                          <div class="com_title">
                            <span>{{comItem.name}} {{comItem.phone}}</span>
                            <span>{{comItem.time}}</span>
                          </div>
                          <div class="star">
                            <van-rate v-model="comItem.score" readonly />
                          </div>
                          <p>{{comItem.text}}</p>
                          <div class="com_img">
                            <img
                              v-for="imgItem in comItem.img"
                              @click="srcShowFun(imgItem.id, listId)"
                              :src="imgItem.src"
                              :key="imgItem.id"
                            />
                          </div>
                        </div>
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
        <!-- <tab
          :line-width="2"
          bar-active-color="#ef6213"
          active-color="#ef6213"
          :scroll-threshold="1.5"
        >
          <tab-item selected @on-item-click="index=0">详情</tab-item>
          <tab-item @on-item-click="index=1">评价({{count}})</tab-item>
        </tab>
        <div v-show="index==0" class="comm_de">
          <img :src="shopinfo.details_img" />
        </div>
        <div v-show="index==1" class="comm_de">
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
                <div class="comment comd_con">
                  <div class="box2">
                    <div class="comment_list" v-for="(comItem,listId) in commnetList" :key="listId">
                      <div class="com_title">
                        <span>{{comItem.name}} {{comItem.phone}}</span>
                        <span>{{comItem.time}}</span>
                      </div>
                      <div class="star">
                        <van-rate v-model="comItem.score" readonly />
                      </div>
                      <p>{{comItem.text}}</p>
                      <div class="com_img">
                        <img
                          v-for="imgItem in comItem.img"
                          @click="srcShowFun(imgItem.id, listId)"
                          :src="imgItem.src"
                          :key="imgItem.id"
                        />
                      </div>
                    </div>
                  </div>
                </div>
              </div>
              <load-more v-if="lif" :show-loading="load" tip="正在加载..."></load-more>
              <load-more v-if="nif" :show-loading="none" tip="没有更多数据了"></load-more>
            </van-pull-refresh>
          </div>
        </div>-->
      </div>
    </div>
    <van-image-preview
      v-model="srcShow"
      :images="images"
      :startPosition="srcIndex"
      @change="onChange"
      :loop="true"
    >
      <template v-slot:srcIndex>第{{ srcIndex }}页</template>
    </van-image-preview>
    <div class="com_bottom">
      <button @click="addShopCar">加入购物车</button>
      <button @click="gotoClose">立即购买</button>
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
      shopId: this.$route.query.storeId, // 商品id
      name: sessionStorage.getItem("shopName") || "商品详情",
      shopinfo: {}, //商品信息
      popupVisible: true,
      num: 1, //购买数量
      commnetList: [], //评价列表
      count: "", //评价个数
      load: true, //加载图标显示
      none: false, //加载图标隐藏
      lif: true, //正在加载中 显示
      nif: false, //没有更多数据了 隐藏
      loading: false, //下拉刷新
      isLoading: false, //上拉加载更多
      srcShow: false, //图片预览
      srcIndex: 0, //图片页面索引
      startPosition: 0, //图片预览起始位置索引
      images: [], //图片地址
      shopTypeList: [], //商品类型
      shopType: "" // 选中类型
    };
  },
  mounted() {
    let that = this;
    that.getShopInfo();
    that.getComment();
  },
  methods: {
    gotoCar() {
      this.$router.push({
        path: "/shoppingCar"
      });
    },
    gotoMine() {
      this.$router.push({
        path: "/mine"
      });
    },
    //获取 商品详情
    getShopInfo() {
      let that = this;
      Indicator.open({
        // text: "加载中...",
        //文字
        spinnerType: "fading-circle"
        //样式
      });
      that
        .$http({
          url: "Ckshop/goods_detail",
          method: "post",
          data: {
            id: that.shopId
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            //成功回调
            that.shopinfo = res.data.data;
            that.shopTypeList = res.data.data.specs; //类型
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
    chooseTypeFun(index) {
      this.shopType = index;
    },
    srcShowFun(index, listId) {
      // 图片预览
      this.srcShow = true;
      this.srcIndex = index - 1;
      this.startPosition = index - 1;
      this.images = [];
      for (let i = 0; i < this.commnetList[listId].img.length; i++) {
        this.images.push(this.commnetList[listId].img[i].src);
      }
    },
    onChange(index) {
      this.srcIndex = index;
    },
    //下拉刷新
    onRefresh() {
      let that = this;
      that.isLoading = true;
      that.loading = false;
      that.nif = false;
      that.pageindex = 1;
      that.commnetList = [];
      that.getComment(1);
    },
    //上拉加载更多
    loadMore() {
      let that = this;
      that.lif = true;
      that.pageindex++;
      that.getComment(0);
    },
    //获取 评价
    getComment(i) {
      let that = this;
      if (i) {
        that.lif = true;
      }
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "Ckshop/evaluateList",
          method: "post",
          data: {
            id: that.shopId,
            p: that.pageindex
          }
        })
        .then(function(res) {
          that.lif = false;
          that.isLoading = false;
          if (res.data.code == 0) {
            //成功回调
            if (res.data.data.list != "") {
              that.commnetList = res.data.data.list;
              for (let i = 0; i < that.commnetList.length; i++) {
                that.commnetList[i].score = Number(that.commnetList[i].score);
              }
            } else {
              that.nif = true;
              that.loading = true;
            }
            that.count = res.data.data.count; //评价数
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
    // 返回上一页
    back() {
      this.$router.go(-1); //返回上一层
    },
    // 加入购物车
    addShopCar() {
      let that = this;

      if (that.shopType == "") {
        Toast("请选择规格");
      } else {
        Indicator.open({
          spinnerType: "fading-circle"
        });
        that
          .$http({
            url: "Goods/addShopping",
            method: "post",
            data: {
              token: localStorage.getItem("token"),
              goods_id: that.shopId,
              number: that.num,
              shopType: that.shopType // 规格
            }
          })
          .then(function(res) {
            if (res.data.code == 0) {
              //成功回调
              Toast({
                message: "购物车添加成功！",
                position: "bottom",
                duration: 2000
              });
            } else {
              //失败
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
    },
    //立即购买
    gotoClose() {
      let that = this;
      var arr = [
        {
          goods_id: that.shopinfo.id, //商品id
          img: that.shopinfo.details_img, //商品图片
          integral: that.shopinfo.integral, //股分
          price: that.shopinfo.price, //红包
          number: that.num, //数量
          name: that.shopinfo.name, //名字
          shopType: that.shopType // 规格
        }
      ];
      sessionStorage.setItem("orderList", JSON.stringify(arr));
      if (that.shopType == "") {
        Toast("请选择规格");
      } else {
        that.$router.push({
          path: "/closeAccount"
        });
      }
    }
  }
};
</script>

<style scoped="scoped">
#comdetails {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.shop_cart {
  width: 0.8rem;
  height: 0.8rem;
  padding: 0.2rem;
}

.con-wrapper {
  position: fixed;
  width: 100%;
  height: calc(100% - 0.8rem);
  overflow-x: hidden;
  overflow-y: scroll;
  top: 0.8rem;
  padding-bottom: 0.8rem;
  z-index: 99;
}

.mint-header {
  position: relative;
  background: #ef6213;
}

.comd_img {
  width: 100%;
  height: auto;
}

.comd_img img {
  width: 100%;
  height: auto;
}

.comd_con {
  width: 90%;
  margin: 0 auto;
}

.comd_con .comd_info h4 {
  display: flex;
  justify-content: space-between;
  font-size: 0.26rem;
  padding: 0.2rem 0;
  color: #ef6213;
  font-weight: normal;
}

.comd_con .comd_info h4 span {
  display: inline-block;
  line-height: 35px;
}

.comd_con .comd_info >>> .vux-number-input {
  height: 35px !important;
  padding: 2px 2px !important;
}

.comd_con .comd_info >>> .vux-number-selector {
  height: 35px !important;
  line-height: 30px !important;
}

.comd_con .comd_info h5 {
  font-size: 0.28rem;
  padding-bottom: 0.1rem;
  border-bottom: 1px solid #e9e9e9;
}

.specific {
  display: flex;
  padding: 0.1rem;
  border-bottom: 1px solid #e9e9e9;
}

.specific p {
  font-size: 0.28rem;
  color: #666;
  margin: 0.1rem 0;
  width: 0.7rem;
}

.specific .com_ch {
  width: 91%;
  padding: 0 0.1rem 0;
}

.com_ch span {
  display: inline-block;
  font-size: 0.28rem;
  color: #333;
  padding: 0 0.1rem;
  border: 1px solid #e5e5e5;
  border-radius: 0.1rem;
  margin: 0.05rem;
}

.com_ch .active_span {
  border: 1px solid #ef6213;
  color: #ef6213;
}

.comd_info h6 {
  line-height: 0.44rem;
  color: rgba(16, 16, 16, 1);
  font-size: 0.3rem;
  font-weight: normal;
  padding: 0.1rem 0;
}

.comm_de {
  width: 100%;
  margin: 0 auto;
  height: auto;
}

.comm_de img {
  width: 100%;
  height: auto;
}

.comment {
  padding-bottom: 1rem;
}

.comment_list .com_title {
  display: flex;
  justify-content: space-between;
  padding: 0.2rem 0;
  font-size: 0.28rem;
}

.comment_list .com_title span:nth-child(2) {
  font-size: 0.24rem;
  color: #888;
}

.star span {
  font-size: 0.4rem;
  color: #ef6213;
}

.comment_list p {
  padding: 0.1rem 0;
  font-size: 0.26rem;
  line-height: 0.4rem;
}

.com_img {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
}

.com_img img {
  width: 48%;
  height: 100%;
  margin-bottom: 0.1rem;
}

.com_bottom {
  z-index: 99;
  width: 100%;
  position: fixed;
  bottom: 0;
  height: 0.8rem;
  display: flex;
  background: #fff;
}

.com_bottom button {
  display: inline-block;
  width: 50%;
  text-align: center;
  line-height: 0.8rem;
  font-size: 0.28rem;
  border: none;
  background: transparent;
}

.com_bottom button:nth-child(1) {
  border-top: 1px solid #ef6213;
  color: #ef6213;
}

.com_bottom button:nth-child(2) {
  background: #ef6213;
  color: #fff;
}
</style>
