<template>
  <div id="localshop">
    <mt-header fixed title="线下商户">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
      <p slot="right" @click="gotoPay">付款记录</p>
    </mt-header>
    <div class="con-wrapper">
      <div class="local_se">
        <select class="selectpicker" v-model="area_id" @change="selectChange($event)">
          <option
            v-for="(areaitem,index) in areaList"
            :key="index"
            :value="areaitem.id"
          >{{areaitem.name}}</option>
        </select>
        <div class="search">
          <input type="text" name="name" v-model="name" placeholder="请输入名称进行搜索" />
          <span class="iconfont icon-tabsearch" @click="search"></span>
        </div>
      </div>
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
            <div class="local_content">
              <div class="local">
                <div
                  class="content_list"
                  @click="showDialog(localitem.code,localitem.name)"
                  v-for="(localitem,index) in localshop"
                  :key="index"
                >
                  <img :src="localitem.img" />
                  <div class="con-title">
                    <h3>{{localitem.name}}</h3>
                    <div class="address">
                      <span class="iconfont icon-tabaddress"></span>
                      <span>{{localitem.address}}</span>
                    </div>
                  </div>
                </div>
                <!-- <div class="more">查看更多>></div> -->
              </div>
            </div>
          </div>
          <load-more v-if="lif" :show-loading="load" tip="正在加载..."></load-more>
          <load-more v-if="nif" :show-loading="none" tip="没有更多数据了"></load-more>
        </van-pull-refresh>
      </div>
    </div>
    <!-- 购买 -->
    <x-dialog v-model="lang_dlg" class="de_dialog lang_dialog" hide-on-blur>
      <div class="dialog">
        <span class="iconfont icon-tabguanbi" @click="closeDialog"></span>
        <h4>红包付款</h4>
        <div class="number">
          <span>红包金额(元)</span>
          <input type="text" v-model="money" name="money" placeholder="请输入红包金额" />
        </div>
        <div class="number">
          <span>商家识别号</span>
          <input type="text" v-model="code" name="code" disabled="disabled" />
        </div>
        <div class="number">
          <span>商家名称</span>
          <span>{{name1}}</span>
        </div>
        <div class="number">
          <span>交易密码</span>
          <input type="password" v-model="payment_password" name="pass" placeholder="请输入交易密码" />
        </div>
        <h5>备注：付款成功后请注意给线下商户展示</h5>
        <button @click="payMoney">确定</button>
      </div>
    </x-dialog>
  </div>
</template>

<script>
import { XDialog, LoadMore } from "vux";
import { Indicator, Toast } from "mint-ui";
export default {
  components: {
    Indicator,
    Toast,
    XDialog,
    LoadMore
  },
  data() {
    return {
      pageindex: 1,
      area_id: 1, //区域id
      name: "", //商铺名称
      name1: "", //
      localshop: [], //商铺数据
      areaList: [], //区域列表
      lang_dlg: false, //购买弹窗
      payment_password: "", //支付密码
      money: "", //金额
      code: "", //商户识别码
      load: true, //加载图标显示
      none: false, //加载图标隐藏
      lif: true, //正在加载中 显示
      nif: false, //没有更多数据了 隐藏
      loading: false, //下拉刷新
      isLoading: false //上拉加载更多
    };
  },
  mounted: function() {
    let that = this;
    that.getLocalShop();
    that.getAreaList();
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },
    gotoPay() {
      this.$router.push({
        path: "/payrecord"
      });
    },
    //选择区域
    selectChange(i) {
      let that = this;
      that.area_id = Number(i.target.value);
      that.loading = false;
      that.nif = false;
      that.pageindex = 1;
      that.localshop = [];
      that.getLocalShop(1);
    },
    //弹出确认弹窗
    showDialog: function(code, name) {
      var that = this;
      that.code = code;
      that.name1 = name;
      that.lang_dlg = true;
    },
    //关闭确认弹窗
    closeDialog: function() {
      var that = this;
      that.lang_dlg = false;
      that.money = "";
    },
    //下拉刷新
    onRefresh() {
      let that = this;
      that.isLoading = true;
      that.loading = false;
      that.nif = false;
      that.pageindex = 1;
      that.localshop = [];
      that.getLocalShop(1);
    },
    //上拉加载更多
    loadMore() {
      let that = this;
      that.lif = true;
      that.pageindex++;
      that.getLocalShop(0);
    },
    //获取线下商户
    getLocalShop(i) {
      let that = this;
      if (i) {
        that.lif = true;
      }
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "Subscribe/underShop",
          method: "post",
          data: {
            area_id: that.area_id,
            name: that.name,
            p: that.pageindex
          }
        })
        .then(function(res) {
          that.lif = false;
          that.isLoading = false;
          if (res.data.code == 0) {
            //成功回调
            if (res.data.data.list != "") {
              that.localshop = that.localshop.concat(res.data.data.list);
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
    //搜索
    search() {
      let that = this;
      that.loading = false;
      that.nif = false;
      that.pageindex = 1;
      that.localshop = [];
      that.getLocalShop(1);
    },
    //获取地址列表
    getAreaList() {
      let that = this;
      that
        .$http({
          url: "Register/areaList",
          method: "post"
        })
        .then(function(res) {
          if (res.data.code == 0) {
            //成功回调
            that.areaList = res.data.data;
          } else {
            //失败
            Toast(res.data.msg);
          }
        })
        .catch(function(error) {
          Toast({
            message: "网络连接失败",
            position: "bottom",
            duration: 5000
          });
        });
    },
    //付款
    payMoney() {
      let that = this;
      if (!that.money || that.money == null) {
        Toast("请输入红包金额");
      } else if (!that.payment_password || that.payment_password == null) {
        Toast("请输入交易密码");
      } else {
        that
          .$http({
            url: "Subscribe/underShopActive",
            method: "post",
            data: {
              token: localStorage.getItem("token"),
              code: that.code,
              money: that.money,
              payment_password: that.payment_password
            }
          })
          .then(function(res) {
            if (res.data.code == 0) {
              //成功回调
              Toast("付款成功");
              that.closeDialog();
              that.payment_password = "";
            } else {
              //失败
              Toast(res.data.msg);
            }
          })
          .catch(function(error) {
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

<style>
#localshop {
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
  top: 0.8rem;
}

.mint-header {
  background: #ef6213;
}
#localshop p {
  right: 1rem;
}
.mint-toast {
  z-index: 99999 !important;
}
.local_se {
  display: flex;
  width: 90%;
  margin: 0 auto;
  padding: 0.2rem 0;
  position: relative;
}
.local_se select {
  width: 22%;
  height: 0.7rem;
  border: 1px solid #c8c8c8;
  border-radius: 0.1rem;
  color: #333;
}

.local_se .search {
  width: 76%;
}

.local_se .search input[type="search"]::-webkit-search-cancel-button {
  -webkit-appearance: none;
}

.local_se .search input {
  margin-left: 0.2rem;
  width: 100%;
  height: 0.7rem;
  border: none;
  border: 1px solid #c8c8c8;
  border-radius: 0.1rem;
  padding-left: 0.1rem;
  font-size: 0.26rem;
}

.local_se .search span {
  position: absolute;
  top: 0.4rem;
  right: 0.1rem;
  color: #ef6213;
}
.scroll_div {
  width: 100%;
  height: 100%;
  width: 90%;
  margin: 0 auto;
  margin-top: 0.1rem;
}
/* .van-pull-refresh{
		 height: 100%;
	} */
.local {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

.local .content_list {
  padding: 0.3rem 0;
  width: 48%;
}

.local .content_list img {
  width: 100%;
}

.local .content_list .con-title {
  padding: 0.2rem 0;
}

.local .content_list .con-title h3 {
  font-size: 0.28rem;
  font-weight: 600;
}

.local .content_list .address {
  padding: 0.1rem 0;
  font-size: 0.26rem;
  color: #ef6213;
}

.local .more {
  margin: 0.8rem 0;
  color: #555;
  font-size: 0.28rem;
}
#localshop .dialog {
  width: 90%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  padding: 0.3rem 0;
}

#localshop .dialog span.iconfont {
  position: absolute;
  top: 0.2rem;
  right: 0.2rem;
}

#localshop .dialog h4 {
  font-size: 0.3rem;
  font-weight: normal;
}

#localshop .dialog .haufei {
  padding-top: 0.4rem;
  font-size: 0.28rem;
}

#localshop .dialog .haufei a {
  color: #ef6213;
}

#localshop .dialog .number {
  margin: 0.2rem 0;
  display: flex;
  font-size: 0.26rem;
  align-items: center;
}
#localshop .dialog h5 {
  padding-bottom: 0.2rem;
  font-size: 0.24rem;
  font-weight: normal;
  color: #ef6213;
}
#localshop .dialog .number span {
  width: 30%;
  text-align: start;
}
#localshop .dialog .number input {
  width: 70%;
  border: 1px solid #e8e8e8;
  height: 0.6rem;
  padding-left: 0.1rem;
  background: #fff;
}

#localshop .dialog button {
  width: 50%;
  height: 0.6rem;
  background: #ef6213;
  color: #fff;
  margin: 0 auto;
  /* margin: 0.4rem 0; */
  border: none;

  border-radius: 0.1rem;
}

/* 暂无数据 */
#no-data {
  width: 100%;
  text-align: center;
  padding-top: 2rem;
  transition: all 0.2s;
}

#no-data > img {
  width: 40%;
  height: auto;
}
</style>
