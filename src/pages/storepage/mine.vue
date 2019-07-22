<template>
  <div id="my">
    <mt-header fixed title="我的">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="con-wrapper">
      <div class="content">
        <div class="head-por">
          <img src="../../assets/img/logo.jpg" />
          <span>{{mineInfo.name}}</span>
          <span>{{mineInfo.phone}}</span>
        </div>
        <div class="my-content">
          <div class="jibie">
            <!-- <div class="daiyan">
              <img v-if="mineInfo.upgrade==1" src="../../assets/img/jinpai.png" />
              <span v-if="mineInfo.upgrade==0">普通代言人</span>
              <span v-if="mineInfo.upgrade==1">金牌代言人</span>
            </div>-->
            <span>商家推广码：{{mineInfo.invitation_code}}</span>
          </div>
          <div class="my-btn">
            <div class="my_con">
              <div class="btn_tit">
                <img src="../../assets/img/sd.png" />
                <span>谷分</span>
              </div>
              <span>{{mineInfo.gufen}}</span>
            </div>
            <div class="my_con">
              <div class="btn_tit">
                <img src="../../assets/img/bao.png" />
                <span>红包</span>
              </div>
              <span>{{mineInfo.hongbao}}</span>
            </div>
            <div class="my_con">
              <div class="btn_tit">
                <img src="../../assets/img/dengj.png" />
                <span>等级</span>
              </div>
              <div>
                <!-- 蓝钻 -->
                <span v-if="mineInfo.level < 4 && mineInfo.level > 0" class="blue-zuan"></span>
                <span v-if="mineInfo.level == 2 || mineInfo.level == 3" class="blue-zuan"></span>
                <span v-if="mineInfo.level ==3" class="blue-zuan"></span>
                <!-- 黄钻 -->
                <span v-if="mineInfo.level < 7 && mineInfo.level >= 4" class="yellow-zuan"></span>
                <span v-if="mineInfo.level < 7 && mineInfo.level > 4" class="yellow-zuan"></span>
                <span v-if="mineInfo.level == 6" class="yellow-zuan"></span>
                <!-- 蓝冠 -->
                <span v-if="mineInfo.level < 10 && mineInfo.level >= 7" class="blue-guan"></span>
                <span v-if="mineInfo.level < 10 && mineInfo.level > 7" class="blue-guan"></span>
                <span v-if="mineInfo.level == 9" class="blue-guan"></span>
                <!-- 皇冠 -->
                <span v-if="mineInfo.level < 13 && mineInfo.level >= 10" class="yellow-guan"></span>
                <span v-if="mineInfo.level < 13 && mineInfo.level > 10" class="yellow-guan"></span>
                <span v-if="mineInfo.level == 12" class="yellow-guan"></span>
                <!-- 金冠 -->
                <span v-if="mineInfo.level == 13" class="gold-guan"></span>
                <a>{{mineInfo.level}}</a>
              </div>
            </div>
          </div>

          <div class="balance">
            <div class="ba_tit" @click="gotoOrder">
              <h4>我的订单</h4>
              <img src="../../assets/img/more.png" />
            </div>
          </div>
          <div class="balance">
            <div class="ba_tit" @click="gotoAdd">
              <h4>地址管理</h4>
              <img src="../../assets/img/more.png" />
            </div>
          </div>
          <div class="balance">
            <div class="ba_tit" @click="gotoEnter">
              <h4>商家入驻</h4>
              <img src="../../assets/img/more.png" />
            </div>
          </div>
          <div class="balance">
            <div class="ba_tit" @click="gotoBusgr">
              <h4>我的商家团队</h4>
              <img src="../../assets/img/more.png" />
            </div>
          </div>
          <div class="balance">
            <div class="ba_tit" @click="gotoReturngf">
              <h4>商城返谷分记录</h4>
              <img src="../../assets/img/more.png" />
            </div>
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
      mineInfo: []
    };
  },
  mounted() {
    let that = this;
    that.getMineInfo();
  },
  methods: {
    //获取商城我的页面
    getMineInfo() {
      let that = this;
      Indicator.open({
        // text: "加载中...",
        //文字
        spinnerType: "fading-circle"
        //样式
      });
      that
        .$http({
          url: "Ckshop/myMsg",
          method: "post",
          data: {
            token: localStorage.getItem("token")
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            //成功回调
            that.mineInfo = res.data.data;
          } else {
            //失败
            Toast(res.data.msg);
          }
          Indicator.close();
        })
        .catch(function(error) {
          Indicator.close();
        });
    },
    back() {
      this.$router.go(-1); //返回上一层
    },
    gotoOrder() {
      this.$router.push({
        path: "/myorder",
        query: {
          isMine: 1
        }
      });
    },
    gotoAdd() {
      this.$router.push({
        path: "/address"
      });
    },
    gotoEnter() {
      this.$router.push({
        path: "/busEnter"
      });
    },
    gotoBusgr() {
      this.$router.push({
        path: "/mybusgroup"
      });
    },
    gotoReturngf() {
      this.$router.push({
        path: "/returnGrain"
      });
    }
  }
};
</script>

<style scoped="scoped">
.mint-header {
  position: relative;
  background: #ef6213;
}

.mint-header img {
  position: absolute;
  right: 0.3rem;
  top: 0.2rem;
  width: 0.44rem;
}

#my {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.con-wrapper {
  position: fixed;
  width: 100%;
  height: calc(100% - 90px);
  overflow-x: hidden;
  overflow-y: scroll;
  top: 40px;
}

.head-por {
  width: 100%;
  height: 3rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-size: 0.26rem;
}

.head-por img {
  width: 1.6rem;
  margin: 0.1rem 0;
}
.head-por span {
  color: #555;
}
.head-por span:nth-child(3) {
  margin-top: 0.1rem;
}

.my-content {
  width: 100%;
  padding: 0.2rem;
}

.my-content .my-btn {
  display: flex;
  justify-content: space-around;
}

.my-content .my-btn .mint-button {
  height: 0.54rem;
  font-size: 0.26rem;
  background: none;
}

.my-btn {
  margin-bottom: 0.3rem;
}

.my_con {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
}

.my_con .btn_tit {
  display: flex;
  align-items: center;
  margin-bottom: 0.1rem;
  font-size: 0.28rem;
}

.my_con img {
  width: 0.46rem;
  padding-right: 0.1rem;
}
.jibie {
  display: flex;
  justify-content: space-around;
  margin-bottom: 0.3rem;
  font-size: 0.26rem;
  color: #666;
}
.jibie .daiyan {
  color: #ef6213;
  display: flex;
  align-items: center;
}
.jibie .daiyan img {
  width: 0.46rem;
  height: 100%;
  margin-right: 0.1rem;
}
.balance {
  margin-top: 0.2rem;
  padding-bottom: 0.2rem;
  border-bottom: 1px solid #e9e9e9;
}

.name {
  position: relative;
}

.name img {
  width: 0.44rem;
}

.balance h4 {
  font-size: 0.28rem;
  font-weight: normal;
  color: #777;
  padding-bottom: 0.1rem;
}

.balance .ba_tit {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.balance .stat .stat-list span:nth-child(2) {
  padding-top: 4px;
}

.balance .ba_tit img {
  width: 0.32rem;
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
</style>
