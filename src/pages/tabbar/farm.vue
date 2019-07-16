<template>
  <div id="farm">
    <mt-header fixed title="在线农场">
      <span
        slot="right"
        class="iconfont icon-tabxiaoxitongzhitixinglingshenglingdang"
        @click="gotoRequest"
      >
        <a>{{fApply}}</a>
      </span>
    </mt-header>
    <div class="con-wrapper">
      <div class="fa_content">
        <div class="fa_info">
          <div class="fa_title">
            <div class="address">
              <span>当前区域</span>
              <span>{{area}}</span>
            </div>
            <div class="guli">
              <span>谷粒</span>
              <span>{{guli}}</span>
            </div>
          </div>
          <div class="fa_left">
            <div class="friend" @click="gotoFriend">
              <img src="../../assets/img/hy.png" />
              <img src="../../assets/img/hyte.png" />
            </div>
            <div class="gaunli" @click="gotoManage">
              <img src="../../assets/img/gl.png" />
              <img src="../../assets/img/gste.png" />
            </div>
            <div class="qiehuan" @click="changeLand">
              <img src="../../assets/img/qh.png" />
              <img src="../../assets/img/qhte.png" />
            </div>
          </div>
          <div class="fa_land"></div>
        </div>
        <div class="fa_bottom">
          <div class="buss" @click="gotoGame">
            <img src="../../assets/img/help.png" />
            <img src="../../assets/img/buss.png" />
          </div>
          <div class="mine" @click="gotoMyfarm">
            <img src="../../assets/img/wd.png" />
            <img src="../../assets/img/wod.png" />
          </div>
          <div class="jiaoyi" @click="gotoDeal">
            <img src="../../assets/img/jiaoy.png" />
            <img src="../../assets/img/jiaoyt.png" />
          </div>
        </div>
        <div class="stealingfood" @click="gotoSteal">
          <img src="../../assets/img/s.png" />
          <span>正在偷粮者</span>
        </div>
        <!-- 地 -->
        <div class="farm-list">
          <div>
            <div
              v-for="(item ,index) in data"
              :key="index"
              :class="{ 'huangFarm': item.land_type==1, 'heiFarm': item.land_type==2,'jinFarm': item.land_type==3}"
              class="greenFarm"
              @click="Plant(item.seed_id)"
            >
              <!-- 农作物名称 -->
              <p>{{item.seed_status}}</p>
              <!-- 农作物图片 -->
              <img v-if="item.seed_img" :src="item.seed_img" @click="seedDetail(item.id)" />
              <!-- 农作物 状态 -->
              <img
                v-if="item.status ==2"
                src="../../assets/img/s.png"
                @click="harvest(item.id)"
                alt
              />
            </div>
          </div>
        </div>
      </div>
    </div>
    <x-dialog v-model="lang_dlg" class="de_dialog lang_dialog" hide-on-blur>
      <div class="dialog">
        <span class="iconfont icon-tabguanbi" @click="closeDialog"></span>
        <p>不要着急!距离收获还有{{dataDetail}}0天，通过以下方式增快生长</p>
        <div class="fei">
          <img src="../../assets/img/sf.png" @click="fertilizer" />
          <img src="../../assets/img/js.png" @click="watering" />
        </div>
      </div>
    </x-dialog>
  </div>
</template>
<script>
import { XDialog } from "vux";
import { Toast } from "mint-ui";
export default {
  components: {
    XDialog,
    Toast
  },
  data() {
    return {
      data: [], //土地列表
      guli: "", //谷粒
      area: "", //当前区域
      lang_dlg: false, //种子详情 是否展示
      dataDetail: "", //农作物详细信息
      type: 1, //土地类型：1黄土地，2黑土地，3金土地
      activeId: "", //点击当前土地id
      fApply: "", //是否有好友申请
      seedId: window.localStorage.getItem("seed_id") //种子id
    };
  },
  created: function() {
    this.getFame();
    this.getfriendApply();
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },
    //我的好友
    gotoFriend() {
      this.$router.push({
        path: "/friend"
      });
    },
    //游戏商城
    gotoGame() {
      this.$router.push({
        path: "/gamemall"
      });
    },
    //我的农场
    gotoMyfarm() {
      this.$router.push({
        path: "/myfarm"
      });
    },
    //农场管理
    gotoManage() {
      this.$router.push({
        path: "/management"
      });
    },
    //好友请求
    gotoRequest() {
      this.$router.push({
        path: "/friendrequest"
      });
    },
    //交易
    gotoDeal() {
      this.$router.push({
        path: "/deal",
        query: {
          dealindex: 2
        }
      });
    },
    //正在偷粮
    gotoSteal() {
      this.$router.push({
        path: "/stealingfood"
      });
    },
    //获取首页数据
    getFame() {
      let that = this;
      that
        .$http({
          url: "Farm/index",
          method: "post",
          data: {
            token: window.sessionStorage.getItem("token")
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            that.data = res.data.data.land;
            that.guli = res.data.data.guli;
            that.area = res.data.data.area;
          } else {
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
    //是否有好友申请
    getfriendApply() {
      let that = this;
      that
        .$http({
          url: "Farm/friendApply",
          method: "post",
          data: {
            token: window.sessionStorage.getItem("token")
          }
        })
        .then(function(res) {
          var msg = res.data.msg;
          if (res.data.code == 0) {
            that.fApply = res.data.data;
          } else {
            Toast(msg);
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
    //切换土地
    changeLand() {
      let that = this;
      var type = that.type == 1 ? 2 : that.type == 2 ? 3 : 1;
      that
        .$http({
          url: "Farm/landList",
          method: "post",
          data: {
            token: window.sessionStorage.getItem("token"),
            type: type
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            that.type = type;
            that.data = res.data.data;
          } else {
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
    //查看种子详情
    seedDetail(id) {
      let that = this;
      that.activeId = id;

      that
        .$http({
          url: "farm_user/seedDetails",
          method: "post",
          data: {
            token: window.sessionStorage.getItem("token"),
            id: id
          }
        })
        .then(function(res) {
          var msg = res.data.msg;
          if (res.data.code == 0) {
            that.lang_dlg = true;
            that.dataDetail = res.data.data / 86400;
            that.dataDetail = that.dataDetail.toFixed(2);
          } else {
            Toast(msg);
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
    //自己施肥
    fertilizer() {
      let that = this;
      that
        .$http({
          url: "farm_user/fertilizer",
          method: "post",
          data: {
            token: window.sessionStorage.getItem("token"),
            id: that.activeId
          }
        })
        .then(function(res) {
          var msg = res.data.msg;
          if (res.data.code == 0) {
            Toast("施肥成功");
            that.lang_dlg = false;
          } else {
            Toast(msg);
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
    //浇水
    watering() {
      let that = this;
      that
        .$http({
          url: "farm_user/watering",
          method: "post",
          data: {
            token: window.sessionStorage.getItem("token"),
            id: that.activeId
          }
        })
        .then(function(res) {
          var msg = res.data.msg;
          if (res.data.code == 0) {
            Toast("浇水成功");
            that.lang_dlg = false;
          } else {
            Toast(msg);
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
    //收货种子
    harvest(id) {
      let that = this;
      that
        .$http({
          url: "farm_user/harvest",
          method: "post",
          data: {
            token: window.sessionStorage.getItem("token"),
            id: id
          }
        })
        .then(function(res) {
          var msg = res.data.msg;
          if (res.data.code == 0) {
            Toast("成功获得种子");
            that.getFame();
          } else {
            Toast(msg);
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
    //种地
    Plant(id) {
      let that = this;
      if (id !== undefined) {
        // Toast("这块土地已经种植了");
        return;
      } else {
        that
          .$http({
            url: "my_farm/seedActive",
            method: "post",
            data: {
              token: window.sessionStorage.getItem("token"),
              id: that.seedId
            }
          })
          .then(function(res) {
            var msg = res.data.msg;
            if (res.data.code == 0) {
              Toast("种植成功");
              that.getFame();
            } else {
              if (msg == "不能为空" || msg == "没有该种子") {
                Toast("请到(我的-种子选择种子进行种植)");
              } else {
                Toast(msg);
              }
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
    },
    //关闭确认弹窗
    closeDialog: function() {
      var that = this;
      this.lang_dlg = false;
      this.payment_password = "";
    }
  }
};
</script>

<style scoped="scoped">
#farm {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

#farm span.iconfont {
  font-size: 0.5rem;
  color: #fbb58e;
  position: relative;
  text-align: center;
}
#farm span.iconfont a {
  display: block;
  position: absolute;
  top: -0.1rem;
  right: -0.1rem;
  padding: 2px 4px;
  background: #fff;
  border-radius: 50%;
  font-size: 0.26rem;
  color: red;
}
.con-wrapper {
  position: fixed;
  width: 100%;
  height: calc(100% - 90px);
  overflow-x: hidden;
  overflow-y: scroll;
  top: 40px;
}

.fa_content {
  height: 100%;
  width: 100%;
  background: url("../../assets/img/bg-g.png") no-repeat center center;
  background-size: 105%;
  position: relative;
}

.mint-header {
  background: #ef6213;
}

.fa_info {
  width: 94%;
  margin: 0 auto;
  padding-top: 0.2rem;
}

.fa_title {
  /* width: 3.2rem; */
  height: 1.2rem;
  border-radius: 1rem;
  background: rgba(0, 0, 0, 0.4);
  font-size: 0.26rem;
  padding: 0.1rem 0.4rem;
  color: #fff;
}

.fa_title .address,
.fa_title .guli {
  padding: 4px 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.fa_title .address span:nth-child(2), .fa_title .guli span:nth-child(2) {
  background: #ef6213;
  padding: 1px 6px;
  border-radius: 0.1rem;
}

.fa_left {
  width: 16%;
  display: inline-block;
}

.fa_left .friend,
.fa_left .gaunli,
.fa_left .qiehuan {
  display: inline-block;
  padding: 0.16rem 0;
  width: 0.64rem;
  display: inline-block;
}

.fa_left .friend {
  padding-top: 0.4rem;
}

.friend img,
.gaunli img,
.qiehuan img {
  display: block;
  width: 100%;
}

.stealingfood {
  position: fixed;
  bottom: 3rem;
  left: 0.3rem;
  display: flex;
  align-items: center;
}

.stealingfood img {
  width: 0.6rem;
}

.stealingfood span {
  font-size: 0.26rem;
  color: #fff;
  padding-left: 0.1rem;
}

.fa_bottom {
  position: fixed;
  width: 100%;
  bottom: 1.2rem;
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.fa_bottom .buss,
.fa_bottom .mine,
.fa_bottom .jiaoyi {
  width: 0.64rem;
}

.fa_bottom img {
  display: block;
  width: 100%;
}

/* 土地 */
.farm-list {
  position: absolute;
  top: 2.4rem;
  left: 0;
  right: 0;
  bottom: 0;
  margin: auto;
  width: 5.4rem;
  height: 5.4rem;
  transform: rotateX(50deg) rotate(-45deg);
  background: url("../../assets/img/td.png") no-repeat 0 0 /100% 100%;
  padding: 0.4rem;
}

.farm-list > div {
  width: 100%;
  height: 100%;
  font-size: 0;
  line-height: 0;
}

.farm-list > div > div {
  width: 25%;
  height: 25%;
  float: left;
  position: relative;
  text-align: center;
}
.greenFarm {
  background: url(../../assets/img/wkk.png) no-repeat 0 0 /100% 100%;
}
.heiFarm {
  background: url(../../assets/img/hei.png) no-repeat 0 0 /100% 100%;
}

.huangFarm {
  background: url(../../assets/img/huang.png) no-repeat 0 0 /100% 100%;
}

.jinFarm {
  background: url(../../assets/img/jin.png) no-repeat 0 0 /100% 100%;
}

.farm-list > div > div > p {
  position: relative;
  -webkit-transform: rotate(45deg);
  transform: rotate(45deg);
  top: -0.54rem;
  left: 0.78rem;
  font-size: 0.32rem;
  line-height: 0.4rem;
  color: #fff;
  z-index: 10;
}

.farm-list > div > div > img:nth-child(2) {
  position: absolute;
  top: 0;
  left: 50%;
  right: 0;
  bottom: 65%;
  margin: auto;
  width: 0.8rem;
  height: 1.4rem;
  transform: rotate(45deg);
  z-index: 2;
  border: 0;
}

.bigImg {
  width: 50px !important;
  height: 86px !important;
}

.farm-list > div > div > img:nth-child(3) {
  position: absolute;
  top: 0;
  left: 63%;
  right: 0;
  bottom: 25%;
  margin: auto;
  width: 0.62rem;
  height: 0.64rem;
  transform: rotate(45deg);
  z-index: 10;
}
.dialog {
  width: 80%;
  margin: 0 auto;
  height: 4rem;
  padding-top: 0.2rem;
}

#farm .dialog span {
  position: absolute;
  top: 0.2rem;
  right: 0.2rem;
  font-size: 0.3rem;
}
.dialog p {
  margin-top: 0.5rem;
  font-size: 0.3rem;
  text-align: start;
}
.dialog .fei img {
  margin: 0.3rem 0.3rem;
}
.dialog .fei img:nth-child(1) {
  width: 0.8rem;
}
.dialog .fei img:nth-child(2) {
  width: 1.5rem;
}

/* .farm-list > div > div > img:nth-child(3) {
  position: absolute;
  top: 0;
  left: 50%;
  right: 0;
  bottom: 50%;
  margin: auto;
  width: 50px;
  height: 86px;
  transform: rotate(45deg);
  z-index: 2;
} */
</style>
