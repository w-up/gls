<template>
  <div id="fertilizer">
    <mt-header fixed title="种子">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
    </mt-header>
    <div class="con-wrapper">
      <div class="fertilizer_con">
        <div v-for="item in data" :key="item.id" class="fertilizer_list">
          <div class="fertilizer_img">
            <img :src="item.img" />
          </div>
          <div class="fertilizer_info">
            <div class="info_left">
              <span>{{item.name}}</span>
              <span>{{item.price}}</span>
            </div>
            <button @click="showDialog(item.id,item.price,item.name)">购买</button>
          </div>
        </div>
      </div>
    </div>
    <x-dialog v-model="lang_dlg" class="de_dialog lang_dialog" hide-on-blur>
      <div class="dialog">
        <span class="iconfont icon-tabguanbi" @click="closeDialog"></span>
        <h4>购买种子</h4>
        <div class="number">
          <span>类型</span>
          <span>{{name}}</span>
        </div>
        <div class="number">
          <span>价格</span>
          <span>{{price}}</span>
        </div>
        <div class="number">
          <span>数量:</span>
          <!-- <input type="number" v-model="num" name placeholder="请输入交易数量" /> -->
          <x-number title align v-model="num" button-style="round" :min="1"></x-number>
        </div>
        <div class="number" v-show="pass_hide">
          <span>交易密码</span>
          <input type="password" v-model="payment_password" name placeholder="请输入交易密码" />
        </div>
        <button @click="BuyFertilizer">确定</button>
      </div>
    </x-dialog>
  </div>
</template>

<script>
import { XNumber, XDialog } from "vux";
import { Toast, Indicator } from "mint-ui";
export default {
  components: {
    XNumber,
    XDialog,
    Toast,
    Indicator
  },
  data() {
    return {
      lang_dlg: false,
      data: [], //肥料列表数据
      price: "", //肥料价格
      activeId: "", //点击当前肥料id
      name: "", //肥料类型
      num: 1, // 数量
      payment_password: sessionStorage.getItem("tran_pass"),
      pass_hide: true, //密码隐藏
    };
  },
  created: function() {
    this.getFertilizerList();
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },
    //弹出确认弹窗
    showDialog: function(id, price, name) {
      var that = this;
      that.activeId = id;
      that.price = price;
      that.name = name;
      this.lang_dlg = true;
    },
    //关闭确认弹窗
    closeDialog: function() {
      this.lang_dlg = false;
    },
    //获取化肥
    getFertilizerList() {
      let that = this;
      Indicator.open({
        text: "数据加载中..."
      });
      that
        .$http({
          url: "Farm/fertilizerList",
          method: "post",
          data: {
            token: window.sessionStorage.getItem("token")
          }
        })
        .then(function(res) {
          Indicator.close();
          console.log(res);
          if (res.data.code == 0) {
            that.data = res.data.data;
          } else {
            Toast("获取信息失败");
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
    //购买肥料
    BuyFertilizer() {
      let that = this;
      let payment_password = that.payment_password;
      if (!payment_password || payment_password == null) {
        Toast("请输入支付密码");
      } else {
        that
          .$http({
            url: "Farm/fertilizerBuy",
            method: "post",
            data: {
              token: window.sessionStorage.getItem("token"),
              id: that.activeId,
              payment_password: payment_password,
              number: that.num
            }
          })
          .then(function(res) {
            console.log(res);
            var msg = res.data.msg;
            if (res.data.code == 0) {
              Toast("支付成功");
              that.lang_dlg = false;
              sessionStorage.setItem("tran_pass", payment_password);
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
      }
    }
  }
};
</script>

<style scoped="scoped">
#fertilizer {
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
  position: relative;
  background: #ef6213;
}

.fertilizer_con {
  width: 90%;
  margin: 0 auto;
  margin-top: 0.5rem;
}
.fertilizer_con .fertilizer_list {
  display: flex;
  align-items: center;
  margin-bottom: 0.3rem;
}
.fertilizer_con .fertilizer_list .fertilizer_img {
  width: 22%;
  background: #f7f7f7;
  text-align: center;
}
.fertilizer_con .fertilizer_list .fertilizer_img img {
  width: 66%;
}
.fertilizer_con .fertilizer_list .fertilizer_info {
  flex: 1;
  display: flex;
  margin-left: 0.3rem;
  justify-content: space-between;
  align-items: center;
}
.fertilizer_con .fertilizer_list .fertilizer_info .info_left {
  display: flex;
  flex-direction: column;
}
.fertilizer_con .fertilizer_list .fertilizer_info .info_left span {
  padding: 0.1rem 0;
  font-size: 0.3rem;
}
.fertilizer_con .fertilizer_list .fertilizer_info .info_left span:nth-child(2) {
  font-size: 0.24rem;
  color: #f2544b;
  font-weight: bold;
}
.fertilizer_con .fertilizer_list .fertilizer_info button {
  width: 1.2rem;
  height: 0.54rem;
  background: #259b24;
  border: none;
  border-radius: 0.1rem;
  color: #fff;
}
.dialog {
  width: 80%;
  margin: 0 auto;
  /* height: 4rem; */
  padding: 0.2rem 0 0.25rem;
}

.dialog span.iconfont {
  position: absolute;
  top: 0.2rem;
  right: 0.2rem;
}

.dialog h4 {
  font-size: 0.3rem;
  font-weight: normal;
  margin-bottom: 0.2rem;
}

.dialog .ktype {
  display: flex;
  padding: 0.1rem 0;
  font-size: 0.26rem;
}
.dialog .ktype span:nth-child(1) {
  padding: 0 0.5rem;
}

.dialog .haufei a {
  color: #ef6213;
}

.dialog .number {
  margin-top: 0.2rem;
  display: flex;
  /* justify-content: space-between; */
  font-size: 0.26rem;
  align-items: center;
}
.dialog .number span:nth-child(1) {
  width: 35%;
  text-align: right;
  padding-right: 5%;
}
.dialog .number input {
  width: 65%;
  border: 1px solid #e8e8e8;
  height: 0.6rem;
  padding-left: 0.1rem;
}

.dialog .number span:nth-last-child(1) {
  width: 65%;
  text-align: left;
}

.dialog button {
  width: 50%;
  height: 0.6rem;
  background: #ef6213;
  color: #fff;
  margin-top: 0.4rem;
  border: none;
  border-radius: 0.1rem;
}
</style>
