<template>
  <div id="busenter">
    <mt-header fixed title="商家入驻">
      <a slot="left">
        <mt-button icon="back" @click="back">返回</mt-button>
      </a>
      <p slot="right" class="iconfont icon-tabshengji shengji" @click="gotoEimg"></p>
    </mt-header>
    <div class="con-wrapper">
      <div class="bus_con">
        <div class="bus_list">
          <span>公司法人：</span>
          <input type="text" v-model="legal_person" placeholder="请输入公司法人姓名" />
        </div>
        <div class="bus_list">
          <span>法人身份证：</span>
          <input type="text" v-model="legal_identity" placeholder="请输入法人身份证号码" />
        </div>
        <div class="bus_list">
          <span>银行账号：</span>
          <input type="text" v-model="bank_card" placeholder="请输入银行账户" />
        </div>
        <div class="bus_list">
          <span>开户行：</span>
          <input type="text" v-model="bank_name" placeholder="请输入开户行" />
        </div>
        <div class="bus_list">
          <span>推荐商邀请码：</span>
          <input type="text" v-model="invitation_code" placeholder="请输入推荐商邀请码" />
        </div>
        <div class="bus_list">
          <span>行业选择：</span>
          <select v-model="industry_id" class="selectpicker">
            <option v-for="(item,index) in industry" :key="index" :value="item.id">{{item.name}}</option>
          </select>
        </div>
        <div class="checket">
          <van-checkbox v-model="checked" id="checked" @click="check" checked-color="#EF6213"></van-checkbox>
          <p>
            我已阅读并同意
            <span @click="gotoArgument">《商家入驻协议》</span>
          </p>
        </div>
        <div class="button">
          <button class="accept" id="accept" @click="busEnter" disabled="disabled" type="default">确认</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Toast, Indicator } from "mint-ui";
export default {
  components: {
    Toast,
    Indicator
  },
  data() {
    return {
      checked: false,
      disabled: true,
      legal_person: "", //公司法人
      legal_identity: "", //法人身份证
      bank_card: "", //银行账户
      bank_name: "", //开户行
      industry_id: 1, //行业id
      invitation_code: "", //商家邀请码
      industry: [] //行业
    };
  },
  mounted: function() {
    this.getData();
    this.getIndustrylist();
  },
  //数据保存到session
  watch: {
    legal_person(val) {
      if (val || val != null || val != undefined) {
        sessionStorage.setItem("legal_person", val);
      }
    },
    legal_identity(val) {
      if (val || val != null || val != undefined) {
        sessionStorage.setItem("legal_identity", val);
      }
    },
    bank_card(val) {
      if (val || val != null || val != undefined) {
        sessionStorage.setItem("bank_card", val);
      }
    },
    bank_name(val) {
      if (val || val != null || val != undefined) {
        sessionStorage.setItem("bank_name", val);
      }
    },
    industry_id(val) {
      if (val || val != null || val != undefined) {
        sessionStorage.setItem("industry_id", val);
      }
    },
    invitation_code(val) {
      if (val || val != null || val != undefined) {
        sessionStorage.setItem("invitation_code", val);
      }
    }
  },
  methods: {
    back() {
      this.$router.go(-1); //返回上一层
    },
    //商家升级
    gotoEimg() {
      this.$router.push({
        path: "/busUp"
      });
    },
    //入驻协议
    gotoArgument() {
      this.$router.push({
        path: "/enterAgreement"
      });
    },
    //判断注册协议是否选择
    check() {
      let that = this;
      if (that.checked == false) {
        $("#accept").prop("disabled", false);
        $("#accept").css("background", "#EF6213");
      } else {
        $("#accept").prop("disabled", true);
        $("#accept").css("background", "#c6c6c6");
      }
    },
    // 刷新页面数据不消失
    getData() {
      this.legal_person = sessionStorage.getItem("legal_person");
      this.legal_identity = sessionStorage.getItem("legal_identity");
      this.bank_card = sessionStorage.getItem("bank_card");
      this.bank_name = sessionStorage.getItem("bank_name");
      this.invitation_code = sessionStorage.getItem("invitation_code");
      this.industry_id = sessionStorage.getItem("industry_id");
    },
    //商家入驻
    busEnter() {
      let that = this;
      let legal_person = that.legal_person;
      let legal_identity = that.legal_identity;
      let bank_card = that.bank_card;
      let bank_name = that.bank_name;
      let invitation_code = that.invitation_code;
      let industry_id = that.industry_id;
      if (!legal_person || legal_person == null) {
        Toast("请输入法人姓名");
      } else if (!/(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/.test(legal_identity)) {
        Toast("请输入正确的法人身份证号");
      } else if (!bank_card || bank_card == null) {
        Toast("请输入银行账号");
      } else if (!bank_name || bank_name == null) {
        Toast("请输入开户行名称");
      } else if (!invitation_code || invitation_code == null) {
        Toast("请输入商家邀请码");
      } else if (!industry_id || industry_id == null) {
        Toast("请选择行业");
      } else {
        Indicator.open({
          text: "提交中..."
        });
        that.$http
          .post("Ckshop/userReg", {
            token: sessionStorage.getItem("token"),
            legal_person: legal_person,
            legal_identity: legal_identity,
            bank_card: bank_card,
            bank_name: bank_name,
            invitation_code: invitation_code,
            industry_id: industry_id
          })
          .then(function(res) {
            Indicator.close();
            let code = res.data.code;
            if (code == 0) {
              Toast("提交成功，请等待审核");
              that.legal_person = sessionStorage.getItem("");
              that.legal_identity = sessionStorage.getItem("");
              that.bank_card = sessionStorage.getItem("");
              that.bank_name = sessionStorage.getItem("");
              that.invitation_code = sessionStorage.getItem("");
              that.industry_id = sessionStorage.getItem("");
              // that.$router.push({
              // 	path: '/'
              // })
            } else {
              Toast(res.data.msg);
            }
          })
          .catch(function(err) {
            Toast({
              message: "网络连接失败",
              position: "bottom",
              duration: 5000
            });
          });
      }
    },
    //获取区域列表
    getIndustrylist() {
      let that = this;
      that.$http
        .post("Ckshop/industryList", {})
        .then(function(res) {
          if (res.data.code == 0) {
            that.industry = res.data.data;
          }
        })
        .catch(function(err) {
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
#busenter {
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

.mint-header .shengji {
  font-size: 0.6rem;
}

.bus_con {
  width: 90%;
  margin: 0 auto;
  margin-top: 0.5rem;
}

.bus_con .bus_list {
  display: flex;
  flex-direction: column;
  padding: 0.2rem 0;
}

.bus_con .bus_list span {
  display: block;
  width: 100%;
  color: #333;
  font-size: 0.28rem;
  padding-bottom: 0.1rem;
}

.bus_con .bus_list input,
.bus_con .bus_list select {
  height: 0.8rem;
  border: 1px solid #c8c8c8;
  border-radius: 0.1rem;
  padding-left: 0.1rem;
  background: #fff;
}

.checket {
  display: flex;
  justify-content: center;
  font-size: 0.26rem;
  color: #555;
}

.checket span {
  color: #ef6213;
}
.checket p {
  padding-left: 0.1rem;
}

.bus_con button {
  position: relative;
  width: 6rem;
  left: 50%;
  margin-left: -3rem;
  height: 0.8rem;
  color: #fff;
  margin-top: 0.3rem;
  border: none;
  border-radius: 0.2rem;
  background: #ccc;
  font-size: 0.3rem;
}
</style>
