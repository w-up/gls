<template>
  <div id="storehome">
    <!-- 头部 -->
    <mt-header fixed title="创客商城"></mt-header>
    <!-- 轮播 -->

    <div class="con-wrapper">
      <div class="swipe">
        <mt-swipe :auto="4000">
          <mt-swipe-item v-for="(item,index) in banner" :key="index">
            <img :src="item">
          </mt-swipe-item>
        </mt-swipe>
        <div class="search">
          <input type="search" placeholder="请输入店铺名称" v-model="name">
          <button :disabled="name == ''" @click="setIndustryData(industry_id)">
            <i class="iconfont icon-tabsearch"></i>
          </button>
        </div>
      </div>
      <!-- 内容 -->
      <div class="content">
        <div class="nav">
          <ul :style="{width: percentage + '%'}">
            <li :class="{'shop-key-active': industry_id == ''}" :style="{width: 100 / (shopKey.length + 1) + '%'}" @click="getShopData">
              <a>全部</a>
            </li>
            <li
              v-for="(item,index) in shopKey"
              :key="index"
              :class="{'shop-key-active': industry_id == item.id}"
              :style="{width: 100 / (shopKey.length + 1) + '%'}"
              @click="setIndustryData(item.name, item.id)"
            >
              <a>{{item.name}}</a>
            </li>
          </ul>
        </div>
        <div class="content-con">
          <div
            class="content_list"
            @click="gotoStore(item)"
            v-for="(item,index) in shopList"
            :key="index"
          >
            <img :src="item.img">
            <div class="con-title">
              <h3>{{item.name}}</h3>
              <!-- 蓝钻 -->
              <span v-if="item.level < 4 && item.level > 0" class="blue-zuan"></span>
              <span v-if="item.level == 2 || item.level == 3" class="blue-zuan"></span>
              <span v-if="item.level ==3" class="blue-zuan"></span>
              <!-- 黄钻 -->
              <span v-if="item.level < 7 && item.level >= 4" class="yellow-zuan"></span>
              <span v-if="item.level < 7 && item.level > 4" class="yellow-zuan"></span>
              <span v-if="item.level == 6" class="yellow-zuan"></span>
              <!-- 蓝冠 -->
              <span v-if="item.level < 10 && item.level >= 7" class="blue-guan"></span>
              <span v-if="item.level < 10 && item.level > 7" class="blue-guan"></span>
              <span v-if="item.level == 9" class="blue-guan"></span>
              <!-- 皇冠 -->
              <span v-if="item.level < 13 && item.level >= 10" class="yellow-guan"></span>
              <span v-if="item.level < 13 && item.level > 10" class="yellow-guan"></span>
              <span v-if="item.level == 12" class="yellow-guan"></span>
              <!-- 金冠 -->
              <span v-if="item.level == 13" class="gold-guan"></span>
            </div>
          </div>
        </div>
      </div>
      <div id="no-data" v-if="shopList.length == 0">
        <img src="../../assets/img/nodata.png">
      </div>
      <div style="height:50px;"></div>
    </div>
  </div>
</template>

<script>
import { Swipe, SwipeItem, Indicator } from "mint-ui";
export default {
  components: {
    "mt-swipe": Swipe,
    "mt-swipe-item": SwipeItem
  },
  data() {
    return {
      banner: [], //轮播图
      shopKey: [], //店铺筛选条件（关键字）
      shopList: [], //店铺列表
      percentage: "", // 店铺列表动态宽
      industry_id: "", //行业ID
      name: "" //搜索名称
    };
  },
  mounted() {
    let that = this;
    that.getShopData();
  },
  methods: {
    // 获取创客商城首页信息
    getShopData() {
      let that = this;
      Indicator.open({
        // text: "加载中...",
        //文字
        spinnerType: "fading-circle"
        //样式
      });
      that
        .$http({
          url: "Ckshop/index",
          method: "post",
					 data: {
					  token: localStorage.getItem("token")
					}
        })
        .then(function(res) {
          Indicator.close();
          if (res.data.code == 0) {
            //成功回调
            that.banner = res.data.data.banner;
            that.shopKey = res.data.data.industry;
            that.shopList = res.data.data.shop.list;
            that.industry_id = "";
            that.name = "";
            if (that.shopKey.length>=4) {
              that.percentage = 20 * (that.shopKey.length + 1);
            } else {
              that.percentage = 100;
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
    // 选择行业
    setIndustryData(name, id) {
      let that = this;
      Indicator.open({
        // text: "加载中...",
        //文字
        spinnerType: "fading-circle"
        //样式
      });
      that
        .$http({
          url: "Ckshop/shopList",
          method: "post",
          data: {
            name: "",
            industry_id: id
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            that.industry_id = id;
            that.name = "";
            that.shopList = res.data.data.list;
          } else {
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
    // 进入店铺
    gotoStore(item) {
      this.$router.push({
        path: "/store",
        query: {
          name: item.name,
          storeId: item.id
        }
      });
    }
  }
};
</script>
<style scoped="scoped">
#storehome {
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.con-wrapper {
  position: fixed;
  width: 100%;
  height: calc(100% - 1.8rem);
  overflow-x: hidden;
  overflow-y: scroll;
  top: .8rem;
}
#storehome .mint-header {
  background: #ef6213;
}

/* 轮播图 */
.mint-swipe {
  width: 100%;
  height: 4.4rem;
}
.mint-swipe img {
  display: block;
  width: 100%;
  height: 100%;
}

.swipe .search {
  position: absolute;
  top: 0.1rem;
  left: 0;
  right: 0;
  margin: auto;
  width: 6rem;
  text-align: center;
}
.swipe input::-webkit-input-placeholder {
  color: red;
  text-align: center;
}
.swipe input::-moz-placeholder {
  /* Mozilla Firefox 19+ */
  color: red;
  text-align: center;
}
.swipe input::-moz-placeholder {
  /* Mozilla Firefox 4 to 18 */
  color: red;
  text-align: center;
}
.swipe input::-ms-input-placeholder {
  /* Internet Explorer 10-11 */
  color: red;
  text-align: center;
}
.swipe .search input[type="search"]::-webkit-search-cancel-button {
  -webkit-appearance: none;
}
.swipe .search button i {
  display: inline-block;
  font-size: 0.28rem;
  line-height: 0.6rem;
}
.swipe .search button:disabled {
  background: transparent;
  color: #ef6213;
}
.content .nav {
  width: 95%;
  margin: 0 auto;
  overflow-x: scroll;
}
.content .nav ul {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  text-align: center;
  white-space: nowrap;
}
.content .nav ul li {
  padding: 0.3rem 0;
  /* width: 20%; */
  font-size: 0.28rem;
  border-bottom: 1px solid #e9e9e9;
}
.content .nav ul li a {
  color: #999;
}
.content-con {
  display: flex;
  width: 96%;
  margin: 0 auto;
  flex-wrap: wrap;
  justify-content: space-between;
}
.content_list {
  margin-top: 0.15rem;
  width: 49%;
  line-height: 0;
}
.content_list img {
  width: 100%;
  height: 3rem;
  border-radius: 0.1rem 0.1rem 0 0;
}
.content_list .con-title {
  padding: 0.2rem 0.1rem;
  border: 1px solid #eee;
  border-top: none;
  border-radius: 0 0 0.1rem 0.1rem;
}
.content_list .con-title h3 {
  font-size: 0.28rem;
  font-weight: 600;
  display: inline-block;
  line-height: 0.4rem;
  vertical-align: top;
}
.content_list .con-title > span {
  display: block;
  line-height: 0.4rem;
  float: right;
  width: 0.4rem;
  height: 0.4rem;
}
.content_list .con-title img {
  width: 0.48rem;
  height: 0.48rem;
}
.content_list .con-title2 {
  margin-top: 0.1rem;
  display: flex;
  align-items: center;
  justify-content: space-around;
}
.content_list .con-title2 h3 {
  font-size: 0.24rem;
  font-weight: normal;
  color: #ef6213;
}
.content_list .con-title2 img {
  width: 0.36rem;
  height: 0.36rem;
}
.blue-zuan {
  background: url(../../assets/img/lz.png) no-repeat center center / 0.34rem
    0.28rem;
}
.yellow-zuan {
  background: url(../../assets/img/hz.png) no-repeat center center / 0.34rem
    0.28rem;
}
.blue-guan {
  background: url(../../assets/img/lg.png) no-repeat center center / 0.34rem
    0.28rem;
}
.yellow-guan {
  background: url(../../assets/img/hg.png) no-repeat center center / 0.34rem
    0.28rem;
}
.gold-guan {
  background: url(../../assets/img/jg.png) no-repeat center center / 0.34rem
    0.28rem;
}
.shop-key-active {
  border-color: #ef6213 !important;
}
.shop-key-active > a {
  color: #ef6213 !important;
}
body >>> .mint-indicator-mask {
  background: rgba(0, 0, 0, 0.5);
}
/* 暂无数据 */
#no-data {
  width: 100%;
  text-align: center;
  padding-top: 1rem;
  transition: all 0.2s;
}
#no-data > img {
  width: 40%;
  height: auto;
}
</style>
