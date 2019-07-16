<template>
  <div id="cardBag">
    <mt-header fixed title="卡包">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="con-wrapper">
      <div class="card_con">
        <div class="lingy_card">
          <div class="card_head">
            <span>领养卡</span>
            <span @click="lookAll">查看全部({{adoptNum}})</span>
          </div>
          <div class="card_list">
            <div
              class="card"
              v-for="(adoptitem,index) in adopt"
              :key="index"
              @click="gotoBlac(adoptitem.id)"
            >
              <div class="card-tit">
                <span>{{adoptitem.title}}</span>
                <span>{{adoptitem.number}}</span>
              </div>
              <h5>基地地址：{{adoptitem.address}}</h5>
              <div class="card_time">
                <span>领养时间：{{adoptitem.time}}</span>
              </div>
            </div>
          </div>
        </div>
        <div class="huiyuan_card">
          <div class="card_head">
            <span>会员卡</span>
            <h3 class="card_add" @click="gotoMem">+</h3>
          </div>
          <div class="card_list">
            <div class="card" @click="gotoMeDe">
              <div class="card-tit">
                <span>会员卡</span>
              </div>
              <h4>VIP</h4>
              <div class="card_time">
                <span>{{member_card.card_number}}</span>
              </div>
            </div>
          </div>
        </div>
        <div class="gulian_card">
          <div class="card_head">
            <span>谷联卡</span>
            <h3 class="card_add" @click="gotogul">+</h3>
          </div>
          <div class="card_list">
            <div class="card" @click="gotoGul">
              <div class="card-tit">
                <span>谷联卡</span>
              </div>
              <h4>谷联卡</h4>
              <div class="card_time">
                <span>{{gulian_card.card_number}}</span>
              </div>
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
      allAdopt: [], //全部领养卡
      adopt: [], //领养卡
      adoptNum: "", //领养卡个数
      member_card: [], //会员卡
      gulian_card: [], //谷联卡
      adoptIndex: "" //显示条数
    };
  },
  mounted: function() {
    let that = this;
    that.getAdoptCard();
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },

    gotoBlac(id) {
      let that = this;
      that.$router.push({
        path: "/blackDetail",
        query: {
          id: id
        }
      });
    },
    //查看全部
    lookAll() {
      let that = this;
      that.adopt = that.allAdopt; // 查看全部 赋值
    },
    //新办会员卡
    gotoMem() {
      this.$router.push({
        path: "/addmember",
        query: {
          cadindex: 0
        }
      });
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
    gotoMeDe() {
      this.$router.push({
        path: "/memberDetail"
      });
    },
    gotoGul() {
      this.$router.push({
        path: "/gulianDetail"
      });
    },
    //获取卡包
    getAdoptCard() {
      let that = this;
      Indicator.open({
        text: "加载中..."
      });
      that
        .$http({
          url: "card_bag/adoptCard",
          method: "post",
          data: {
            token: localStorage.getItem("token")
          }
        })
        .then(function(res) {
          if (res.data.code == 0) {
            //成功回调
            that.allAdopt = res.data.data.adopt; // 存全部卡
            for (let i = 0; i < res.data.data.adopt.length; i++) {
              if (i < 3) {
                that.adopt.push(res.data.data.adopt[i]); // 存进来时展示的
              }
            }
            that.member_card = res.data.data.member_card;
            that.gulian_card = res.data.data.gulian_card;
            that.adoptNum = that.allAdopt.length;
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
#cardBag {
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
  background: #ef6213;
}

.card_con {
  width: 90%;
  margin: 0 auto;
}

.card_head {
  display: flex;
  justify-content: space-between;
  padding: 0.3rem 0;
  font-size: 0.26rem;
}

.card_head span:nth-child(2) {
  font-size: 0.24rem;
}

.card {
  width: 96%;
  margin: 0 auto;
  padding: 0 0.1rem;
  background: #000000;
  border-radius: 0.1rem;
  color: #fff;
  margin-bottom: 0.2rem;
}

.card_huang {
  background: #eddaa8;
}

.card_jin {
  background: #d4b650;
}

.huiyuan_card .card {
  background: #ec7d37;
}

.huiyuan_card h4,
.gulian_card h4 {
  text-align: center;
  font-size: 0.36rem;
  font-weight: normal;
}

.huiyuan_card .card_add,
.gulian_card .card_add {
  font-size: 0.4rem;
  color: #ef6213;
}

.gulian_card .card {
  background: #55a532;
}

.card .card-tit {
  padding: 0.1rem 0;
  font-size: 0.26rem;
}

.card .card-tit span:nth-child(2) {
  margin-left: 1rem;
}

.card h5 {
  font-weight: normal;
  font-size: 0.24rem;
}

.card .card_time {
  padding: 0.16rem 0;
  text-align: right;
  font-size: 0.24rem;
}
</style>
