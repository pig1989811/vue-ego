<template>
  <div>
    <main class="scroll-content" style="top:0">
      <!-- 头部 -->
      <section class="head-container">
        <div class="head-cover flex align-center just-between">
          <router-link :to="{name:'Update'}" tag="div">
            <img :src="getImgPath(member.imgHeadUrl)">
            <p class="vip-tag" v-if="member.isActiveMember=='1'">至尊VIP会员</p>
          </router-link>
          <div class="head-info flex-1">
            <div class="name">{{member.nickName||member.mobile}}
            </div>
          </div>
          <div class="setting" @click="navigate('/me/pwdmanage')">
            <span class="iconfont-large self-setting"></span>
          </div>
        </div>
      </section>
      <!-- 金额展示 -->
      <section class="money-container text-center">
        <p>{{fhbMoney}}</p>
        <p>可提现总额（元）</p>
      </section>
      <section class="money-container2 flex align-center text-center">
        <div class="money-item"  @click="navigate('/merchant/recommendhistory')">
          <p class="orange-color">{{member.directMemberMoney}}</p>
          <p>业务奖励</p>
        </div>
        <div class="money-item">
          <p class="orange-color">{{member.memberVoucherMoney}}</p>
          <p>兑换券</p>
        </div>
        <div class="money-item">
          <p class="orange-color">{{member.balanceMoney}}</p>
          <p>余额</p>
        </div>
        <div class="money-item">
          <p class="orange-color">{{member.consumptionMoney}}</p>
          <p>积分</p>
        </div>
      </section>
      <!-- 菜单 -->
      <div class="yd-grids-3">
        <div class="yd-grids-item" @click="goOrder(0)">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-dingdan" style="color:rgb(233,9,9);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>订单</span>
          </div>
        </div>
        <div class="yd-grids-item" @click="navigate('/me/qrcode')">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-qrcode" style="color: rgb(55,55,55);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>二维码</span>
          </div>
        </div>
        <div class="yd-grids-item" @click="goStore" v-if="+member.type==0">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-shangjia" style="color: rgb(222,113,8);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>商家入驻</span>
          </div>
        </div>
        <div class="yd-grids-item" @click="goStore" v-if="+member.type==1">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-shangjia" style="color: rgb(222,113,8);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>我是商家</span>
          </div>
        </div>
        <div class="yd-grids-item" @click="navigate(`/me/recommend?id=${member.id}`)">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-tuijianren" style="color: rgb(5,146,52);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>我推荐的人</span>
          </div>
        </div>
        <div class="yd-grids-item" @click="goWallet">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-wallet" style="color: rgb(224,16,66);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>钱包</span>
          </div>
        </div>
        <!-- <div class="yd-grids-item" @click="navigate('/merchant/upgrade')" v-if="member.type=='1' && +member.merchantType<3">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-zengsong" style="color: rgb(206,170,32);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>联盟商家升级</span>
          </div>
        </div> -->
        <!-- <div class="yd-grids-item" @click="navigate('/merchant/give2')" v-if="+member.merchantType>=3">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-zengsong" style="color: rgb(206,170,32);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>商家升级赠送</span>
          </div>
        </div> -->
        <!-- <div class="yd-grids-item" @click="navigate('/trade/bankcard')">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-yinxingqia" style="color: rgb(30,125,193);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>银行卡</span>
          </div>
        </div> -->
        <div class="yd-grids-item" @click="navigate('/address/index?source=5')">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-dizhi" style="color: rgb(227,9,8);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>地址管理</span>
          </div>
        </div>
        <div class="yd-grids-item" @click="showDialog = true">
          <div class="yd-grids-icon">
            <span class="iconfont-large self-kefu" style="color: rgb(35,91,204);"></span>
          </div>
          <div class="yd-grids-txt">
            <span>联系客服</span>
          </div>
        </div>
        <div class="yd-grids-item" @click="navigate('/me/updateApp')">
          <div class="yd-grids-icon">
            <img :src="getLocalImg('user.png')" alt="">
          </div>
          <div class="yd-grids-txt">
            <span>APP更新</span>
          </div>
        </div>
      </div>
      <!-- 退出按钮 -->
      <section class="btn-container">
        <button type="button" class="sign-btn" @click="signOut">退出登录</button>
      </section>
    </main>
    <footer-bar></footer-bar>
    <yd-popup v-model="showDialog" position="center" width="80%">
      <div class="tel-container text-center">
        <a href="tel:4001011553" class="tel-box flex align-center">
          <span class="iconfont-large self-dianhua"></span>
          <span class="tel-num">拨打:4001011553</span>
        </a>
        <yd-button type="danger" @click.native="showDialog=false" bgcolor="rgb(221,9,24)" color="#fff">返回</yd-button>
      </div>
    </yd-popup>
  </div>
</template>
<script>
import { mapState, mapGetters } from "vuex";
import HeaderTop from "components/header/index";
import FooterBar from "components/footer/index";
import CertModal from "components/common/CertModal";
import { mixin, getStore, removeStore,localImg } from "components/common/mixin";
export default {
  name: "Me",
  data() {
    return {
      oldBack: mui.back,
      showDialog: false
    };
  },
  components: { HeaderTop, FooterBar, CertModal },
  created() {},
  computed: {
    ...mapState([
      "account",
      "member",
      "fhbMoney"
    ]),
    ...mapGetters(["cartNum"])
  },
  mixins: [mixin,localImg],
  beforeRouteEnter(to, from, next) {
    next(vm => {
      vm.plusReady();
    });
  },
  beforeRouteLeave(to, from, next) {
    mui.back = this.oldBack;
    next();
  },
  activated() {
    if (this.$store.state.positions[this.$route.path]) {
      document.querySelector("main").scrollTop = this.$store.state.positions[
        this.$route.path
      ];
    }
    if (getStore("account") && getStore("account").length > 0) {
      this.$store.commit("SET_ACCOUNT", getStore("account"));
      this.$store.dispatch("getInfo");
      this.$store.dispatch("getFHB");
    } else {
      this.$router.push("/me/login");
    }
  },
  methods: {
    navigate(url) {
      this.$router.push(url);
    },
    goOrder(index) {
      this.$router.push({
        name: "MyOrder",
        params: { update: true },
        query: { id: index }
      });
    },
    goWallet() {
      this.$router.push({ name: "MyWallet", params: { update: true } });
    },
    goStore() {
      //入驻弹窗
      if (this.member.type == "0") {
        this.settle();
        return;
      }
      this.$router.push("/store/my");
    },
    settle() {
      this.$router.push({ path: "/store/settle-2" });
    },
    signOut() {
      removeStore("account");
      removeStore("storeId");
      removeStore("tips");
      this.$store.commit("SET_ACCOUNT", "");
      this.$store.commit("RECORD_BANK_List", []);
      this.$store.commit("RECORD_BANK_CARD", null);
      this.$store.commit("RECORD_MEMBER_INFO", null);

      this.$dialog.toast({
        mes: "退出成功",
        timeout: 1000,
        callback: () => {
          this.$router.replace("/me/login");
        }
      });
    }
  }
};
</script>
<style lang='less' scoped>
@import "../../style/mixin.less";
.head-container {
  background-color: @white;
}
.c1 {
  color: #f37370;
}

.c2 {
  color: #f8a24e;
}

.head-container {
  .head-cover {
    height: 1.6rem;
    padding: @pd;
    img {
      .wh(1rem,1rem);
      margin: 0 auto;
      z-index: 10;
      border-radius: 50%;
    }
    .vip-tag {
      color: @white;
      background-color: @red;
      font-size: 0.2rem;
      padding: 2px;
      border-radius: @pd;
    }
  }
  .head-info {
    margin-left: 0.1rem;
    font-size: 0.3rem;
  }
}

.btn-container {
  .pd;
  .sign-btn {
    display: block;
    width: 90%;
    margin: 0 auto;
    color: @white;
    background-color: @red;
    font-size: 0.34rem;
    text-align: center;
    padding: 0.2rem 0;
    border-radius: 5px;
  }
}

.tel-container {
  padding: @pd *2 @pd;
  background-color: @white;
  .tel-box {
    display: block;
    border-radius: 5px;
    border: 1px solid @red;
    background-color: @red;
    color: @white;
    padding: 0.1rem 0.2rem;
    .mg-v;
    .tel-num {
      margin-left: 0.1rem;
      font-size: 16px;
      vertical-align: 2px;
    }
  }
  button {
    margin-top: @pd * 2;
  }
}
.money-container {
  .pd;
  p:first-child {
    font-size: 0.8rem;
    font-weight: bold;
  }
}

.money-container2 {
  .pd-v;
  background-color: @white;
  border-bottom: 1px solid #f7f5f5;
  .money-item {
    width: 50%;
    margin: .1rem 0;
    p:first-child {
      font-size: 0.28rem;
    }
    p:last-child {
      font-size: 0.22rem;
    }
  }
}
</style>